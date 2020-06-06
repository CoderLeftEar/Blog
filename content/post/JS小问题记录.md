---
title: "JS小问题记录"
date: 2020-06-02T16:30:56+08:00
author: CoderLeftEar
categories: ["js"]
tags: ["js"]
draft: true
---

# JS小问题记录

## 1.判断是PC还是Phone

```javascript
function isPC() {
    var userAgentInfo = navigator.userAgent;
    var Agents = ["Android", "iPhone", "SymbianOS", "Windows Phone", "iPad", "iPod"];
    var flag = true;
    for (var v = 0; v < Agents.length; v++) {
        if (userAgentInfo.indexOf(Agents[v]) > 0) {
            flag = false;
            break;
        }
    }
    return flag;
}

var isPCOrPhone = isPC();

// 判断是手机端还是电脑端
if(isPCOrPhone) {
    $("#app").css("width", "1200px");
    $(".top_title .container").css("width", "1200px");
} else {
    $("#app").css("width", "100%");
    $(".top_title .container").css("width", "100%");
}
```

## 2.setInterval、setTimeout

```js
// 每隔5秒循环执行
setInterval(fun1, 5000);
function fun1() {
    if(...) {
        console.log("服务断开");
        alert("服务断开");
        clearInterval(_setIntervalIsDisconnect);
    }
}

/** 代替setInterval */
// 5秒后执行一次
setTimeout(fun2, 5000);
function fun2() {
    if(...) {
        alert("服务断开");
        console.log("服务断开");
    }
	// 5秒后执行一次
    setTimeout(arguments.callee, 5000);
}
```




