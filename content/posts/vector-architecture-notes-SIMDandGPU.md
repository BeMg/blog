---
title: "Vector Architecture Notes SIMDandGPU"
date: 2018-08-18T20:55:53+08:00
draft: true
---

## 前言

對於現行的 SIMD(Single instruction multiply data) 架構進行比較，主要有以下：

- Vector instructions
- SIMD instructions
- GPU

主要的內容取至 computer architecture a quantitative approach chp4.1-4

## Vector instructions

請看[這篇](https://bemg.github.io/blog/posts/vector-architecture-notes-1/)

## SIMD instructions for multimedia

此處的 SIMD instruction 主要是為了 Multimedia 所設計。跟現實中所謂的 SIMD 的定義有一些微妙的差異。如果直接講結論的話，應當可以是這一類 SIMD 為 vector instruction 的 subset。是為了硬體的複雜度與效能上做出的取捨。

> 大多數的 graphics system 會分別使用 8-bits 去表達三原色，在使用一個額外的 8-bits 表達透明度。剛好用完一個 32-bits。
>
> audio sample (聲音取樣) 通常被表達為 8-bits 或者 16-bits

知名的 SIMD for multimedia 

- MMX
- SSE
- AVX

> 上述皆是 x86 下的產物，似乎可以額外做一篇筆記了。

### 指令

指令的部份一次都支援 256-bits 同時的運算。稍微進行一些換算可以得出。

可以一次執行

- 32 個 8-bits
- 16 個 16-bits
- 8 個 32-bits
- 4 個 64-bits

相較於 vector extension 動不動就是上百個來說確實小多了。

| Instruction category  |
| --------------------- |
| Unisgned add/subtract |
| Maximum/minimum       |
| Average               |
| Shift right/left      |
| Floating point        |

> Floating point 沒有一個 element 是 8-bits。所以這部份從 16-bits 開始。細節的部份就之後在探討。

### 與Vector 的差異

- Register 數量更少
- 指令運作的長度固定
  - 所以要在多少 width 的資料上進行操作是取決於指令
- 沒有 stride, gather-scatter 等 load/store method
- 沒有 mask

絕大多數在 VMIPS 中可以看到用以增進效能的奇招都被移除了。這些功能的減少，會導致編譯器更難以產生出 SIMD code (這裡的SIMD應當是指標題的SIMD)。接下來就是要解釋這樣做的理由。

### Why SIMD instruction

- 更簡單的實作
- 比較便宜
- 更少額外的狀態(little extar state)
  - 我猜是指 CSR
  - 畢竟長度都被固定了，沒有什麼好記狀態的了
- Vector 需要大量的 memory bandwidth 支撐
- 不會遭遇到 memory access 會產生的 page fault
  - 我想應該就是 cache miss
  - 一次 data 的讀寫不會超過一個 page size (憑記憶的 cache 知識，待查證)
- 定長的特性，可以更好的制定 media standard (課本片面之詞)  

### Example

![](https://i.imgur.com/dQqf7o8.png)

- 基本上就是在進行 `Y[i] = a*X[i] + Y[i]`
- `.4D` 這一個後綴是只一次對 4 個 element 進行
- 這個舉例是用 MIPS 作為指令的基礎，所以並非實際上的指令集用法

## GPU
