---
title: "Vector Architecture Notes 3 - x86"
date: 2018-09-02T17:57:41+08:00
draft: false
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

皆是基於前者再進行擴充。分類上皆是 SIMD 指令集。版本一大堆。

- MMX (Matrix Math Extension)
- SSE (Streaming SIMD Extension)
  - 2, 3, 4, 4.1, 4.2
- AVX (Advanced Vector Extension)
  - 2, 512

至於彼此之間有沒有被取代的問題還需要再研究。

## MMX

有一些資料稱呼這個指令集的全名為 Matrix Math extensions。不過這個不是很重要的問題。

主要是用於加速 media 與 communications 應用。

### 架構與暫存器

![](https://i.imgur.com/BAmVbSw.png)

-  8 個 64-bits width 的 Reigster -> 被稱呼為 MMX register
  - MM0~MM7
  - ![](https://i.imgur.com/Tx9ybIZ.png)
- 8 個 32-bits width 的 general-purpose register
  - 因為 MMX reigster 規定不行作為 address operand。
  - EAX, EBX, ECX, EDX, EBP, ESI, EDI, ESP
- 四種不相同的 data type
  - packed bytes (8 bits)
  - packed word (16 bits)
  - packed doubleword (32bits)
  - quadword (64 bits)
  - ![](https://i.imgur.com/nqrXLkP.png)

### 執行模型

- 大多數的指令的operand為 dest src1 src2
  - ![1535888169340](https://i.imgur.com/JhALka5.png)

> 有些說 operand 有兩個，有的說有三個，似乎是因為版本不同。兩個的版本是寫 dest 可以被當作 src2，運算完成後覆蓋原本的資料。

- Saturation and Wraparound mode
  - Saturation
    - 設定數值的上限與下限，當運算值超出時鎖死在上下限。
  - Wraparound
    - 就讓它overflow，只取需要的 low bits
  - ![](https://i.imgur.com/dEsjySW.png)

## 指令

![](https://i.imgur.com/O76Gq0Q.png)

- data transfer
  - MMX register 與 memory 之間資料傳遞
  - 只有 32bits 與 64 bits 兩種
- arithmetic
  - 加減乘除
  - 針對那一種 data type 那一種 saturation mode unsigned or signed 都寫在指令上了
  - 比較特殊的指令
    - PMULHW PMULLW (multiply packed signed integers and store high result)
      - 似乎是因為一定會爆，所以可以選說要前 16 bits 還是後 16 bits
      - ![](https://i.imgur.com/hpbF4Jz.png)
    - PMADDWD (multiply and add packed integers)
      - ![](https://i.imgur.com/ZK7HXlH.png)
- comparsion
  - 生成一個 mask 裡面放 0 或 1
  - 怎麼放待補
    - 整個 byte/word/doubleword 放 1/0
    - 或者 第一個 bits 放 1/0
- conversion
  - ![](https://i.imgur.com/5AIiXKI.png)
  - doubleword -> word
  - word -> byte
  - saturated 的截斷法
- unpacking
  - ![](https://i.imgur.com/LzuWYfs.png)
  - bytes -> word
  - word -> doubleword
- logical
  - and, or, xor, andn (疑？怎麼不是 nand)
- shift
- Empty MMX state instruction
  - 似乎跟 float-point FPU 的運作有關
  - 細節待補，暫時視為切換運算模式

## SSE

## AVX



## 參考資料

- [Intel® 64 and IA-32 Architectures Software Developer’s Manual vol 1](https://www.intel.com/content/www/us/en/architecture-and-technology/64-ia-32-architectures-software-developer-vol-1-manual.html)
- [Intel® 64 and IA-32 Architectures Software Developer’s Manual vol 2](https://www.intel.com.tw/content/www/tw/zh/architecture-and-technology/64-ia-32-architectures-software-developer-instruction-set-reference-manual-325383.html)
- [Intel® 64 and IA-32 Architectures Software Developer’s Manual vol 3](https://www.intel.com.tw/content/www/tw/zh/architecture-and-technology/64-ia-32-architectures-software-developer-system-programming-manual-325384.html)