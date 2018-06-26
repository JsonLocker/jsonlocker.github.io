---
title: SQL_定时备份
date: 2018-06-25 15:20:36
categories: mysql
tags:
    - mysql
    - 定期备份
---

```shell
#!/bin/bash
today=date +%Y%m%d%H%m%M
oldday=date -d "-3 minutes" +%Y%m%d%H%M
echo today


