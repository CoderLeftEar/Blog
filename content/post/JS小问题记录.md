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

## 3.禁用console.log()

```js
var console = {};
console.log = function(){};
```

```js
window['console']['log'] = console.log() //打开默认设置
window['console']['log'] = function() {}; //关闭默认设置
```

## 4.大小转换

```js
getSize(size) {
    if (!size)
        return "";
    var num = 1024.00; //byte
    if (size < num)
        return size + "B";
    if (size < Math.pow(num, 2))
        return (size / num).toFixed(2) + "K";
    if (size < Math.pow(num, 3))
        return (size / Math.pow(num, 2)).toFixed(2) + "M";
    if (size < Math.pow(num, 4))
        return (size / Math.pow(num, 3)).toFixed(2);
    return (size / Math.pow(num, 4)).toFixed(2) + "T";
}
```

## 5.时间转换

```js
// 时间戳(单位秒)的只转为时分秒
formatDuring(timestamp) {
    var hours = parseInt(timestamp / (1000 * 60 * 60));
    var minutes = parseInt((timestamp % (1000 * 60 * 60)) / (1000 * 60));
    var seconds = parseInt((timestamp % (1000 * 60 * 60) % (1000 * 60)) / (1000));
    // return hours + "时" + minutes + "分" + seconds + "秒";
    return (hours == 0 ? "00:" : String(hours) + ":") 
        + (minutes == 0 ? "00:" : (minutes / 10 >= 1 ? String(minutes) : "0" + String(minutes)) + ":") 
        + (seconds == 0 ? "00" : seconds / 10 >= 1 ? String(seconds) : "0" + String(seconds));
},
```




