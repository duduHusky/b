---
title: "Flarum语言选择显示Default问题"
date: 2019-12-06
draft: false
tags: ["flarum"]
categories: ["PHP"]
---

安装多个语言包之后，在前台或后台显示界面会出现Default选择项

**解决办法:**

> /path to your flarum project/vendor/flarum/core/src/Locale/LocaleServiceProvider.php
> 注释第31行代码:`$locales->addLocale($this->getDefaultLocale(), 'Default');`

---
> 解决!

---

> 新的更新问题已经解决