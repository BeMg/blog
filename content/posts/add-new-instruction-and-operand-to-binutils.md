---
title: "Add New Instruction and Operand to Binutils"
date: 2018-08-29T13:04:05+08:00
draft: true
---

## 前言

意圖修改 assembly，令其支援一個原本沒有的指令，這需要讓 assembly 知道你新的指令的格式為何。而你的格式可以定義在 gcc 的 binutils/opcode 中。會根據各種機器的 target 而有所不同。越研究越凌亂，故在此紀錄一下新增指令的過程。

- New instruction to gas
- operand
- parse operand 
- operand behavior

## 加個新指令到組譯器



## Operand 細節

## Operand 怎麼被 parse 的

## Operand 行為定義



## 參考資料

*  [Google Group](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/sL_OHXYj3LY/Gsm6sBc9BQAJ)
  * 第一個回答很有參考價值，雖然我在花了三天研究完才發現這個回答。
* [Binutils Porting Guide To A New Target/Processor Architecture](https://sourceware.org/binutils/binutils-porting-guide.txt)
* 



