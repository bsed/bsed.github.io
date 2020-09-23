---
title: vue 返回顶部源码
date: 2019-03-27 10:00:00
updated: 2020-01-16 13:37:39
tags: 
- 闭包
- JS基础增强
categories: 
- js

---
> 这个组件的实现思路也比较简单，核心是监听window的scroll事件，在页面滚动的过程中获取 `window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop` 值，并将这个值跟一个临界值比较，返回一个布尔值，再将这个布尔值赋值给组件的一个watch属性`showTop`来动态控制回到顶部按钮是否显示。

代码如下:


<!--more-->


```javascript
<template>
  <div>
    <img class="back_to_top" v-show="showTop" src="../../assets/back2top.png" @click="scrollToY(0, 1500, 'easeInOutQuint')" />
  </div>
</template>
<script>
window.requestAnimFrame = (function () {
    return window.requestAnimationFrame ||
          window.webkitRequestAnimationFrame ||
          window.mozRequestAnimationFrame ||
          function (callback) {
              window.setTimeout(callback, 1000 / 60)
          }
})()

export default {
    name: 'BackToTop',
    data () {
        return {
            scrollTop: 0
        }
    },
    methods: {
        scrollToY (scrollTargetY, speed, easing) {
            // scrollTargetY: the target scrollY property of the window
            // speed: time in pixels per second
            // easing: easing equation to use

            let scrollY = window.scrollY || document.documentElement.scrollTop
            scrollTargetY = scrollTargetY || 0
            speed = speed || 2000
            easing = easing || 'easeOutSine'
            let currentTime = 0
            // min time .1, max time .8 seconds
            let time = Math.max(0.1, Math.min(Math.abs(scrollY - scrollTargetY) / speed, 0.8))

            // easing equations from https://github.com/danro/easing-js/blob/master/easing.js
            let easingEquations = {
                easeOutSine: function (pos) {
                    return Math.sin(pos * (Math.PI / 2))
                },
                easeInOutSine: function (pos) {
                    return (-0.5 * (Math.cos(Math.PI * pos) - 1))
                },
                easeInOutQuint: function (pos) {
                    if ((pos /= 0.5) < 1) {
                        return 0.5 * Math.pow(pos, 5)
                    }
                    return 0.5 * (Math.pow((pos - 2), 5) + 2)
                }
            }

            // add animation loop
            function tick () {
                currentTime += 1 / 60

                let p = currentTime / time
                let t = easingEquations[easing](p)

                if (p < 1) {
                    window.requestAnimFrame(tick)
                    window.scrollTo(0, scrollY + ((scrollTargetY - scrollY) * t))
                } else {
                    window.scrollTo(0, scrollTargetY)
                }
            }

            // call it once to get started
            tick()
        },
        getScrollTop () {
            this.scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
        }
    },
    computed: {
        showTop: function () {
            return this.scrollTop > 500
        }
    },
    mounted () {
        window.addEventListener('scroll', this.getScrollTop)
    }
}
</script>

<style lang="less" scoped>
.back_to_top {
    position: fixed;
    z-index: 999;
    bottom: 1.8rem;
    right: .26rem;
    width: 102px;
    height: 102px;
    overflow: hidden;
}
</style>
```