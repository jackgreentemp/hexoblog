---
title: ti_scrollview
date: 2016-08-03 22:19:49
tags: Titanium
categories: Titanium知识点
---
# Titanium ScrollView做成widget时的一些局限

在开发中遇到这样的需求：输入身高/体重时，需要使用可以滑动的刻度尺输入，素材只有一张图片（有刻度，没有刻度文字）

开发思路就是在一个ScrollView中，放入n张图片，然后在图片上添加文字label

写demo的时候在ios和android上一切正常，标尺滑动也非常流畅，但当这个组件封装成widget之后，在ios上滑动非常不流畅，经过反复验证，发现是在scroll listener中修改label的text时导致的，按照ti与native交互的原理，问题应该是scroll事件触发太频繁，修改UI的任务太多，导致响应慢。但是非widget方式却不存在这个问题，莫非是require会导致性能下降？真正的原因还不得而知。

尝试了使用_.defer，没什么改善

最后为了实现功能，只好将更新UI的代码放在了dragend事件的监听中