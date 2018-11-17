---
title: "RISC V_Vector_extension"
date: 2018-11-17T16:23:15+08:00
draft: true
---

## 前言

這篇文章會描述，`RISC-V Vector Extension`的 SPEC，這份規格書仍然正在處於制定的階段，所以內容上很有可能會進行大翻修，所以現在這份就參考一下就好了。

## Vector Register

- 讓硬體提供者決定 Register 的長度，但是它會進行一些限制，以下兩者都要為 2 的次方
  - 一個 element 的最大寬度(width)
  - 一個 Vector 的最大長度(bits)
- v0-v31
- 每個 Register 的長度是固定的

> - float-point register 直接重疊在 vector register
>
> - Zfinx(float in X) 直接重疊在 integer register 

## Vector CSRs

- vsew
  - dynamic Standard Element Width
- vl
  - vector length
  - 指定指令所可以影響的 element 數量。
- vlmax
  - maximum vector length
  - vlmax 是被計算出來的
  - vlmax = \frac{vector bit}{element width}

以上這些 CSR 都只能用 vsetvl 系列指令去操作。

```
  Standard element width (SEW) encoding

  vsew  bits
  000     8
  001    16
  010    32
  011    64
  100   128
  101   256
  110   512
  111  1024
```

