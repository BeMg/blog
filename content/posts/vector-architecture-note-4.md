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
  * ![](https://i.imgur.com/AJVRolu.png)
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
    * [denormals number](https://en.wikipedia.org/wiki/Denormal_number)
      * 大概就是值小到沒有辦法表示
    * 當發現 Denormal operand 時，當滿足以下條件
      * 處理器支援
      * mask bits 被設為 1
    * 在 operator 執行前，將 operand 設為 0
* MMX
  * MMX 的 register 
* General-purpose
  * 也是延續 MMX 的用途
* EFLAGS
  * 放置 comparsion operations 的結果

## 指令

* Data movement
* Arithmetic
* Logical
* Comparison
* Shuffle
* Conversion

總體而言，可以分成兩種執行模式

* Packed
  * ![](https://i.imgur.com/m1k2Bg2.png)
* scalar
  * ![](https://i.imgur.com/CKl94BM.png)

### Data movement

傳遞資料於

* XMM <-> XMM
* XMM <-> memory

全部的指令

* MOVAPS
  * aligned packed
* MOVUPS
  * unaligned packed
* MOVSS
  * scalar
* MOVLPS
  * low to low
* MOVHPS
  * high to high
* MOVLHPS
  * low to high
* MOVHLPS
  * high to low
* MOVMSKPS
  * to mask, take four bits from the most significant bit

### Arithmetic 

* ADDPS
* ADDSS
* MULPS
* MULSS
* DIVPS
* DIVSS
* RCPPS
* RCPSS
* SQRTPS
* SQRTSS
* RSQRTPS
* RSQRTSS
* MAXPS
* MAXSS
* MINPS
* MINSS

### logical

* ANDPS
* ANDNPS
* ORPS
* XORPS

### Comparision

相對應的位置，填滿 0 或者 1

* CMPPS
  * 可以選 equal, less than, less than equal, unordered, not equal, not less than, not less than or equal, ordered
* CMPSS
* COMISS
* UCOMISS
  * 這兩個會動到 EFLAGS

### Shuffle and unpacked

* SHUFPS
  * ![](https://i.imgur.com/sdz4n1Z.png)
  * 隨機的，是真的shuffle
* UNPCKHPS
  * unpacked, interleave, high
  * ![](https://i.imgur.com/svyMjan.png)
* UNPCKLPS
  * unpacked, interleave, low
  * ![](https://i.imgur.com/8A6eCoD.png)

### Conversion

浮點數與整數之間的轉換

* CVTPI2PS
  * packed,  integer to float-point
* CVTSI2SS
  * scalar, integar to float-point
* CVTPS2PI
  * packed, float-point to integar
* CVTSS2SI
  * scalar, float-point to integar

### SSE 64-bit SIMD Integar

* PAVGB
  * ![](https://i.imgur.com/8QXMSn9.png)
* PAVGW
* PEXTRW
  * MMX register to general-purpose register
* PINSRW
  * general-purpose register to MMX register
* PMAXUB
* PMINUB
* PMAXSW
* PMINSW
* PMOVMASKB
  * ![](https://i.imgur.com/NmZtpsh.png)
* PMULHUW
  * MMX 指令
* PSADBW
  * MMX 指令
* PSHUFW
  * MMX 指令

## Prefetch

可以利用下列指令對於機器進行一些關於資料的提示

### non-terporal hint

不進入 cache herarchy 直接存進 memory

* MOVNTQ
  * quadword
* MOVNTPS
  * packed float-point
* MASKMOVQ
  * 存特定的byte

### Temporal and Non-Temporal Data

* Temporal
  * 還會被用到的資料
* Non-Temporal
  * 讀完就不需要了，要是還存進 cache，就是一種在污染 cache 的行為(會使 cache 變慢)
  * cache 會充斥著一些不會再被存取的資料
  * 例：影片看一次就不用了
* 三種作法
  * write combining(WC)
  * write back(WB)
  * write through(WT)





