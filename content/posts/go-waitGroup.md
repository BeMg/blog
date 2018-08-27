---
title: "Go goroutine"
date: 2018-08-27T11:16:03+08:00
draft: true
---

## 前言

這篇文章會包含

`waitgroup`是當你想要一次執行一定數量的 goroutine 並且等到這些 goroutine 執行結束。

在這個問題上，我們需要釐清的重點在於

* goroutine 
* channel 
* select 
* waitgroup 
* timeout

## Goruntine 概念

`goroutine`是 golang 中一個重要的 feature，概念上就是從程式語言的層次上支援 coroutine 的功能。可以在低成本(不論是硬體或者是程式撰寫的部份)的情況下大量的使用平行化。

從最近使用的感想上來說，可以理解為，被指定由`goroutine`方法執行的程式區塊，是被直接丟進另外一個`task pool` 中等待電腦去執行它。



