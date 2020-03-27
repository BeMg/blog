---
title: "Llvm Trick"
date: 2019-08-19T18:50:01+08:00
draft: false
---

## 前言

LLVM 太多神秘的小技巧了，整理成一篇文章以外之後忘記。

本篇文章主要以需求導向，根據某個需求要如何用LLVM工具達成，

## LLVM Tools Usage

### 啟用Subtarget

這裡以 RISC-V 為例。
 
```
llc --march=riscv32 --mattr=+M,+F file.ll
```

## Backend

### SDTypeProfile

一個 SDNode 的 Type，很多時候會發生 Instruction can not be select 的根本原因就是因為型態對不上。

```Cpp
// llvm/include/llvm/Target/TargetSelectionDAG.td
// SDTypeProfile - This profile describes the type requirements of a Selection
// DAG node.
class SDTypeProfile<int numresults, int numoperands,
                    list<SDTypeConstraint> constraints> {
  int NumResults = numresults;
  int NumOperands = numoperands;
  list<SDTypeConstraint> Constraints = constraints;
}
```

第一個是回傳值，第二個是參數，第三個是說明需要的型態為何。

常用的型態紀錄如下

```Cpp
// SDTCisVT - The specified operand has exactly this VT.
class SDTCisVT<int OpNum, ValueType vt> : SDTypeConstraint<OpNum> {
  ValueType VT = vt;
}

class SDTCisPtrTy<int OpNum> : SDTypeConstraint<OpNum>;

// SDTCisInt - The specified operand has integer type.
class SDTCisInt<int OpNum> : SDTypeConstraint<OpNum>;

// SDTCisFP - The specified operand has floating-point type.
class SDTCisFP<int OpNum> : SDTypeConstraint<OpNum>;

// SDTCisVec - The specified operand has a vector type.
class SDTCisVec<int OpNum> : SDTypeConstraint<OpNum>;
```