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

## vsetvl 系列指令

```
vsetvli	rd, rs1, vsewi	# rd = new vl, rs1 = AVL, vsewi = new SEW setting
vsetvl	rd, rs1, rs2	# rd = new vl, rs1 = AVL, rs2 = vsew value 
```

接下來如果當 `vl` 超過 `vlmax` 時，我們可以有以下的特性

- vl = AVL if AVL <= VLMAX
- vl >= ceil(AVL / 2) if  VLMAX < AVL < 2 * VLMAX // 很奇怪，沒有確定到某一個值。我猜應該是等於
- vl = VLMAX if AVL >= 2 * VLMAX

## Register Grouping and vlmul field

可以根據 vlmul 這一個 CSR(應該是)，去串起多個 Vector register，可以直接的增加 vector length 的長度。

```
 vlmul  #vregs  vnames   VLMAX
 00         32   v0-v31  VBITS/SEW
 01         16   v0-v15  2*VBITS/SEW
 10          8   v0-v7   4*VBITS/SEW
 11          4   v0-v3   8*VBITS/SEW
```

而 register 被組合起來的結構大致上長這樣

```
vlmul
01      [v0,v16],[v1,v17],...,[v15,v31]
10      [v0,v8,v16,v24],[v1,v9,v17,v24],...,[v7,v15,v23,v31]
11      [v0,v4,v8,v12,v16,v20,v24],[v1,v5,v9,v13,v17,v21,v24],...,[v3,...,v31]
```

> 00 就是原本的狀態

