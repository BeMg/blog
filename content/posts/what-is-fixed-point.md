---
\title: "What Is Fixed Point"
date: 2018-08-31T12:30:33+08:00
draft: false
---

## 前言

因為在閱讀 ISA 的文件時，對於常常出現的名詞不是十分清楚。主要將重點把在：

- fixed point 
- float point

## Fixed Point

### 結構

fixed point 由兩個部份組合

- Integer part
- Scaling factor part
  - 通常用 10 或者 2 的指數形式表達 

>  例如：1.23 在 fixed point 中可以被表達為，1230 與 1/1000。

### 操作

如果要轉換從一個 scaling factor 到另外一個不相同的 scaling factor。最簡單的方法就是 fixed point 還原成真正的數值，然後再 scaling 一次。我們可以定義一下各種數值，然後用數學公式來表示這種情況。

- 令 I 為原始值的 Integer part
- 令 N 為 目標值
- 令 R 為原始值的 scaling factor
- 令 S 為目標值的 scaling factor

$$
N = I * \frac{R}{S}
$$

如果 R 與 S 並非可以整除的關係的話，則必須考慮 Round 的問題。

> Round 好像沒有一個統一的中文翻譯可以用，其含意在於因為各種原因沒有辦法精確的描述一個數值而採用近似的方式表達。Round 就是指從精確到近似的過程。

- 加減法
  - 如果有相同的 scaling factor 則直接加減 Integer part 即可
  - 不同 scaling factor 則利用上述轉換 scaling factor 的技巧統一 scaling factor
- 乘法
  - 兩個 part 都相乘
- 除法
  - 兩個 part 都相除
  - 很容易就會遇到 round 的問題

這邊實際還有一個 Saturation arithmetic 的議題，雖然我不大清楚為什麼需要這個功能，但還是在這邊簡述一下。 Saturation 就是讓數值運算時有上限與下限，使其觸碰到邊界時不會發生 overflow/underflow。就讓它卡死在邊界上。

### 實際上的數值表達形式

實際上在程式中使用 fixed point，scaling factor 存在於指令之中，Integer part 則是 encoding 在 memory。

其表達形式許多種，但是最泛用的形式為 `Qm.n`

- m 代表 Integer part 的 bit 數
- n 代表 scaling factor 的數值

Example:

- 指令為 Q8.2 

  - 有八個 bits
  - 要對其 scaling (1/2)

- 所以對於 `0b11010100` 

- $$
  \frac{1}{2^2} * (2^2 + 2^4 + 2^6 + 2^7) = 53
  $$


> 預設為 unsigned，signed 的就是將其 integer part 用 signed 的方式解讀。

除此之外也有 Qn 的形式，直接使用系統預設的數值表達長度(8, 16, 32 bits...)，只紀錄下 scaling factor 的部份。



## 參考資料

- [Fixed-point arithmetic](https://en.wikipedia.org/wiki/Fixed-point_arithmetic)
- [Fixed-Point Arithmetic: An Introduction](https://courses.cs.washington.edu/courses/cse467/08au/labs/l5/fp.pdf)
- [Saturation arithmetic](https://en.wikipedia.org/wiki/Saturation_arithmetic)

