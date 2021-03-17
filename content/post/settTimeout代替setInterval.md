---
title: "SettTimeout代替setInterval"
date: 2021-03-17T13:56:02+08:00
draft: true
---

```js
function Timer() {
  this.timeID = null
  this.func = null
}

Timer.prototype.repeat = function(func, ms) {
  if (this.func === null) {
    this.func = func
  }

  // 确保一个 Timer 实例只能重复一个 func
  if (this.func !== func) {
    return
  }

  this.timeID = setTimeout(() => {
    func()
    this.repeat(func, ms)
  }, ms)
}

Timer.prototype.clear = function() {
  clearTimeout(this.timeID)
}

const a = () => console.log('a')

const b = () => console.log('b')

const timer = new Timer()

timer.repeat(a, 1000)
timer.repeat(b, 1000) // 不会定时执行 b
```

