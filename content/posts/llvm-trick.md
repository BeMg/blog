---
title: "Llvm Trick"
date: 2019-08-19T18:50:01+08:00
draft: false
---

## 前言

LLVM 太多神秘的小技巧了，整理成一篇文章以外之後忘記。

本篇文章主要以需求導向，根據某個需求要如何用LLVM工具達成，

## 啟用Subtarget

這裡以 RISC-V 為例。
 
```
llc --march=riscv32 --mattr=+M,+F file.ll
```

