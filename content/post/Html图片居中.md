---
title: "Html图片居中"
date: 2021-16-18T11:30:48+08:00
author: CoderLeftEar
categories: ["html", "css"]
tags: ["html", "css"]
draft: true
---

# Html图片居中

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .box {
      width: 120px;
      height: 180px;
      border: 2px solid rgb(118, 117, 117);
      border-radius: 4px;
      padding: 4px;
      overflow: hidden;
    }

    .image {
      width: 100%;
      height: 100%;
      background-image: url("https://dss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1089874897,1268118658&fm=26&gp=0.jpg");
      background-repeat: no-repeat;
      background-position: center center;
      background-size: cover;
    }
  </style>
</head>

<body>
  <h3>原图：</h3>
  <img src="https://dss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1089874897,1268118658&fm=26&gp=0.jpg" alt="">
  <h3>居中：</h3>
  <div class="box">
    <img src="https://dss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1089874897,1268118658&fm=26&gp=0.jpg"
      style="width: 100%; height: 100%; object-fit: cover;">
  </div>
  <div class="box">
    <div class="image"></div>
  </div>
</body>

<
```

