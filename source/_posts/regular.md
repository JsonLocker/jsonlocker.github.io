---
title: regular
date: 2021-08-07 15:16:00
categories: Javascript
tags:
    - javascript
    - regular
---

正则表达式 的前瞻后顾

1. 前瞻

查找hrms前面的haorooms,表示我们需要匹配hrms的前面

```javascript
haorooms(?=hrms) 
```

2. 后顾

查找hrms后面的haorooms

```javascript
(?<=hrms)haorooms 
```

3. 负前瞻

查找后面不是hrms的haorooms,

```javascript
haorooms(?!hrms) 
```

4. 负后顾

查找前面不是hrms的haorooms

```javascript
(?<!=hrms)haorooms 
```
