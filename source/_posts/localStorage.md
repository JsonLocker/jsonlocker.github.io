---
title: localStorage
date: 2021-08-02 08:52:34
categories: Javascript
tags:
    - javascript
    - Localstorage
---

### 1. Set expiry time(TTL) for LocalStorage

```javascript
function setWithExpiry(key, value, ttl){
    const new = new Date()

    const item = {
    	value : value
	expiry : new.getTime() + ttl 
    }

    localstorage.setItem(key, JSON.stringify(item))
}

```


### 2. Get items from LocalStorage

```
function getWithExpiry(key){
    const itemStr = localstorage.getItem(key)
    
    if(!itemStr){
    	return null
    }

    const item = JSON.parse(itemStr)
    const now = new Date()
    if( now.getTime() > item.expiry ){
    	localStorage.removeItem(key)
    }
    return item.value
}

```
