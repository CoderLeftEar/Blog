---
title: "Js短路运算符"
date: 2020-11-12T09:44:32+08:00
author: CoderLeftEar
tags: ["js"]
categories: ["js"]
draft: true
---

# Js短路运算符

## ||

从前往后算，false || false || 1 => 1 ;



## &&

false && clg("It is false"); => "It is false";

true && clg("It is true") => true;