---
title: "Read Source Note"
date: 2018-08-22T19:15:37+08:00
draft: true
---

## 前言

紀錄一些讀一些 source code 的筆記



## riscv-gun-toolchain

- riscv assembler parse operand的部份在 `riscv-binutils-gdb/gas/config/tc-riscv.c`
  - 492行開始
- md_begin()
  - 一開始的設定在此，包含 register 的 declare
  - assembly 開始時會先執行一次進行設定。 

## 理解 MASK and MATCH

這兩者都是為了建立一個完整指令的編碼而生

- MATCH是確認所有已經被確定的值
  - opcode
  - funct
- MASK
  - 將可以被確定的編碼設為1
  - 目的是為了確認是否完全吻合預設好的 encoding





