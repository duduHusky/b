---
title: "Vue中使用axios发送请求"
date: 2019-12-07
draft: false
tags: ["vue", "axios"]
categories: ["前端"]
---

### 首先前端部分
``` javascript
let instance = axios.create({
    baseURL: 'http://api.xxx.com',
    headers: {
        'Access-Control-Allow-Origin': '*',
        'Content-Type': 'application/json',
    }
})
```

### 后端PHP部分
``` php
public function behaviors() {
        return ArrayHelper::merge([
            [
                'class' => Cors::className()
            ],
        ], parent::behaviors());
}
```

主要有几点注意：

1. 对于跨域请求浏览器一般不会发送身份凭证信息。如果要发送凭证信息，需要设置 XMLHttpRequest 的 withCredentials 属性为 true：withCredentials: true。此时要求服务器的响应信息中携带 Access-Control-Allow-Credentials: true，否则响应内容将不会返回.
2. 对于携带身份凭证的请求，服务器不得设置 Access-Control-Allow-Origin 的值为“*”。因为请求头携带了 Cookie 信息。要将 Access-Control-Allow-Origin 的值设置为xxx
3. 另外，响应头中也携带了 Set-Cookie 字段，尝试对 Cookie 进行修改。如果操作失败，将会抛出异常


> 跨域请求想要带上 cookies 必须在请求头里面加上
> crossDomain: true
> withCredentials: true
> 后端需要添加`Access-Control-Allow-Credentials: true`

---
> 顺便->Yii2添加解析json数据
> ```php
> 'request' => [
>      'parsers' => [
>         'application/json' => 'yii\web\JsonParser',
>       ]
> ],
> ```