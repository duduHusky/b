---
title: "Gorm返回的JSON里时间格式"
date: 2019-12-13T14:51:47+08:00
draft: false
tags: ["gorm"]
categories: ["Gorm"]
---

Golang中使用gorm时，通过加入gorm.Model到自己的struct来定义一个model。 Gorm是这样定义的：
```golang
type Model struct {
    ID        uint `gorm:"primary_key"`
    CreatedAt time.Time
    UpdatedAt time.Time
}
```

当我们的API在接收到查询请求时返回一个model通过JSON形式返回给客户端，这时类型为time.Time的字段就会默认以RFC3339的格式返回，所有的这类字段的返回值都固定是`2006-01-02T15:04:05.999999999Z07:00`这种格式

```go
const (
    ANSIC       = "Mon Jan _2 15:04:05 2006"
    UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
    RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
    RFC822      = "02 Jan 06 15:04 MST"
    RFC822Z     = "02 Jan 06 15:04 -0700" // RFC822 with numeric zone
    RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
    RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
    RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // RFC1123 with numeric zone
    RFC3339     = "2006-01-02T15:04:05Z07:00"
    RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
    Kitchen     = "3:04PM"
    // Handy time stamps.
    Stamp      = "Jan _2 15:04:05"
    StampMilli = "Jan _2 15:04:05.000"
    StampMicro = "Jan _2 15:04:05.000000"
    StampNano  = "Jan _2 15:04:05.000000000"
)
```

原因是在go的time包中实现`json.Marshaler`接口时指定了使用RFC3339Nano这种格式
```go
// MarshalJSON implements the json.Marshaler interface.
// The time is a quoted string in RFC 3339 format, with sub-second precision added if present.
func (t Time) MarshalJSON() ([]byte, error) {
    if y := t.Year(); y < 0 || y >= 10000 {
        // RFC 3339 is clear that years are 4 digits exactly.
        // See golang.org/issue/4556#c15 for more discussion.
        return nil, errors.New("Time.MarshalJSON: year outside of range [0,9999]")
    }

    b := make([]byte, 0, len(RFC3339Nano)+2)
    b = append(b, '"')
    b = t.AppendFormat(b, RFC3339Nano)
    b = append(b, '"')
    return b, nil
}
```

所以在把model序列化为JSON的时候默认就会来调这个MarshalJSON方法把time.Time类型的字段都搞成这种格式，要想自定义这种格式只能重写这个接口方法，go中不允许在包外新增或重写方法 `cannot define new methods on non-local type[s],`，只能通过在外部定义别名或者内嵌结构体，再在这上面新加或重写方法。

别名方式：
```go
type JSONTime time.Time
```

内嵌方式（推荐）：
```go
type JSONTime struct {
    time.Time
}
```

需要注意别名方式只能使用原始类型的字段，不能使用其方法，只重写字段的时候可以考虑使用，建议使用内嵌方式，都可以使用 https://stackoverflow.com/questions/28800672/how-to-add-new-methods-to-an-existing-type-in-go

所以我们只需定义一个内嵌time.Time的结构体，并重写MarshalJSON方法，然后在定义model的时候把time.Time类型替换为我们自己的类型即可。但是在gorm中只重写MarshalJSON是不够的，只写这个方法会在写数据库的时候会提示delete_at字段不存在，需要加上database/sql的Value和Scan方法 https://github.com/jinzhu/gorm/issues/1611#issuecomment-329654638。

所以最后实现我们自己格式化的time类型JSONTime为：
``` go
package utils
import (
    "database/sql/driver"
    "fmt"
    "time"
)

// JSONTime format json time field by myself
type JSONTime struct {
    time.Time
}

// MarshalJSON on JSONTime format Time field with %Y-%m-%d %H:%M:%S
func (t JSONTime) MarshalJSON() ([]byte, error) {
    formatted := fmt.Sprintf("\"%s\"", t.Format("2006-01-02 15:04:05"))
    return []byte(formatted), nil
}

// Value insert timestamp into mysql need this function.
func (t JSONTime) Value() (driver.Value, error) {
    var zeroTime time.Time
    if t.Time.UnixNano() == zeroTime.UnixNano() {
        return nil, nil
    }
    return t.Time, nil
}

// Scan valueof time.Time
func (t *JSONTime) Scan(v interface{}) error {
    value, ok := v.(time.Time)
    if ok {
        *t = JSONTime{Time: value}
        return nil
    }
    return fmt.Errorf("can not convert %v to timestamp", v)
}
```

然后重新定义自己的Model：
``` go
type Model struct {
    ID        uint            `gorm:"primary_key" json:"id"`
    CreatedAt utils.JSONTime  `json:"createdAt"`
    UpdatedAt utils.JSONTime  `json:"updatedAt"`
}
```