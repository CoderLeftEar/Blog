---
title: "IsPC"
date: 2020-06-02T16:30:56+08:00
author: CoderLeftEar
categories: ["js"]
tags: ["js"]
draft: true
---



# 判断是PC还是Phone

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

