---
title: "Vue笔记-批量删除list中数据"
date: 2020-05-19T20:34:47+08:00
categories: ["Vue"]
tags: ["Vue"]
author: CoderLeftEar
draft: true
---

# Vue笔记



## 使用some方法查找需要删除的数据

```
this.list.some((item, i) => {
    if (item.id == id) {
        this.list.splice(i, 1)
        return true;
    }
})
```

## 使用findIndex方法

```
var index = this.list.findIndex(item => {
    if (item.id == id){
        return true;
    }
})

this.list.splice(index, 1)
```

vue中增加一个搜索同步按钮功能
![img](https://img2018.cnblogs.com/blog/648026/201901/648026-20190111173347427-657448188.png)

![img](https://img2018.cnblogs.com/blog/648026/201901/648026-20190111173401378-1094231757.png)

![img](https://img2018.cnblogs.com/blog/648026/201901/648026-20190111173408202-1261168603.png)

![img](https://img2018.cnblogs.com/blog/648026/201901/648026-20190111173413857-1768474284.png)

在vue中使用过滤器处理对应的数据；主要采用管道符'|'

![img](https://img2018.cnblogs.com/blog/648026/201901/648026-20190111175136889-121075226.png)