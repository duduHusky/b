---
title: "重置vue打印变量显示方式"
date: 2019-12-08
draft: false
tags: ["vue"]
categories: ["前端"]
---

在vue使用console.log()打印变量 会有多余不期望看到的属性
而且展开方式不友好
所以重置一个打印方式：

在main.js文件中添加一下代码
```javascript
Vue.prototype.print = (obj,type) => {
  type = type || "log";
  const log = JSON.parse(JSON.stringify(obj));
  console[type](log)
}
```

以后打印变量就可以直接使用
```javascript
this.print(obj)
//或者
this.print(obj,"error")
```