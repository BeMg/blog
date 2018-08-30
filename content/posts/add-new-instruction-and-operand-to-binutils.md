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

新增指令需要的是如何讓 assembler 知道指令的

- name
- format
- encoding

此外還有一些像是，某一個指令是另外一個指令的 alais(別名)。有一些指令是寄生在其他指令底下(addi 與 mv)。有一些指令是一連串指令的 marco。但是從無到有的一個指令所需要的就是最一開始所提到的，name, format, encoding。

```c
const struct riscv_opcode riscv_opcodes[] =
{
   /* name,      isa,   operands, match, mask, match_func, pinfo.  */
	...
	{"vld",        "64I", "b,o(s)", MATCH_VLD, MASK_VLD, match_opcode, 0 },
    {0, 0, 0, 0, 0, 0, 0}
}
```

所有 assembler 所接受的指令都保存在 riscv-binutils-gdb/opcode/riscv-opc.c 中。當然，根據 ISA 的不同，檔案的名稱理所當然會不太相同。

我們所要關注的部份是 name, operands, match, mask, pinfo

- name 
  - 指令的名稱，怎麼取都可以
- operands
  - 指令的格式

## Operand 細節

## Operand 怎麼被 parse 的

## Operand 行為定義



## 參考資料

* [Google Group](https://groups.google.com/a/groups.riscv.org/forum/#!msg/sw-dev/sL_OHXYj3LY/Gsm6sBc9BQAJ)
  * 第一個回答很有參考價值，雖然我在花了三天研究完才發現這個回答。
* [Binutils Porting Guide To A New Target/Processor Architecture](https://sourceware.org/binutils/binutils-porting-guide.txt)


