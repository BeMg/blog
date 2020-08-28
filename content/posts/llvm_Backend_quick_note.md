---
title: "Llvm_Backend_quick_note"
date: 2018-11-28T15:21:28+08:00
draft: true
---

# LLVM Backend 速記

東西太多，先記一下。

## CodeGen

流程：

LLVM IR -> SelectionDAG -> MachineDAG -> MachineInst -> MCinst

> SelectionDAG 與 MachineDAG 之間的差異在於是否為 Target dependency，之間轉換的行為都會在 XXXSelLowering.cpp 中發生。

> MachineInst 這一層會包含 虛擬暫存器與物理暫存器。所以Register allocation 也會出現在這一層。

- LLVM IR -> SelectionDAG
  - 這一層我還沒有找到在哪裡發生，不過現階段看起來應該是 Target Independency 的。暫時可以不用去理會它。
- SelectionDAG -> MachineDAG
  - 沒有處理好就會 ERROR，直接中止編譯過程。
  - `XXXSelLowering.cpp` 做這方面的處理，整個檔案超級大。
- 

