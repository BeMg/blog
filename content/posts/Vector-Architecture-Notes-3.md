---
title: "Vector Architecture Notes 3 - x84"
date: 2018-09-02T17:57:41+08:00
draft: true
---

## 前言  

對於 Vector Architecture 做一下筆記。對於各種不同的 ISA 的 vector extension 進行比較。

- computer architecture a quantitative approach 課本筆記 -> 實際上就是 VMIPS
- riscv vector extension tutorial
- **x86**
  - MMX
  - SSE
  - AVX
- arm ISA vector extension

## X86 SIMD history

這個系列指令是由 intel 所開發，順序如下：

```c
MMX -> SSE -> AVX
```

皆是基於前者再進行擴充。分類上皆是 SIMD 指令集。

至於彼此之間有沒有被取代的問題還需要再研究。

## MMX

有一些資料稱呼這個指令集的全名為 Matrix Math eXtensions。不過這個不是很重要的問題。

### 暫存器

### 指令

## SSE

## AVX