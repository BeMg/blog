---
title: "Vector Architecture Note 4 - SSE"
date: 2018-09-25T09:31:45+08:00
draft: true
---

## 前言  

對於 Vector Architecture 做一下筆記。對於各種不同的 ISA 的 vector extension 進行比較。

- computer architecture a quantitative approach 課本筆記 -> 實際上就是 VMIPS
- riscv vector extension tutorial
- **x86**
  - MMX
  - **SSE**
  - AVX
- arm ISA vector extension

## Streaming SIMD Extension (SSE)

延續 MMX，後接 SSE2。

## 總覽

此處列出 SSE 主要的改進。(與 MMX 相比較)

* 更大的 Register -> XMM register
  * 64 bits -> 128 bits
  * store packed and scalar single-precision floating-point value (MMX 為純 interger)
* 向下支援
  * 以前的 MMX 程式都可以順利執行在 SSE 下
* 32 bits CSR **MXCSR**
  * rounding method
  * overflow and underflow exception
  * ...
  * 有指令可以儲存或讀取目前 MXCSR
* 通常 XMM 被視為四個 32 bits 的 float-point value
* 用指令手動 prefetch 與 控制 cache
  * 專門控制進出 XMM 的資料

## 架構

![](https://i.imgur.com/ymiP3o8.png)

* XMM
  * ![](https://i.imgur.com/IAPoUA5.png)
  * XMM0-XMM7
  * 128 bits
* MXCSR
  * ![](https://i.imgur.com/wUB9V8s.png)
  * 32 bits control and state register
  * Mask and Flag
    * 就是 exception 的開關
  * Rounding 
    * ![](https://i.imgur.com/w4QrPyo.png)
  * Flush-to-Zero
    * 出問題(underflow)就重設為零
  * Denormals-Are-Zeros
    * 
* MMX
  * MMX 的 register 
* General-purpose
  * 也是延續 MMX 的用途
* EFLAGS
  * 放置 comparsion operations 的結果

