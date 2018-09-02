---
\title: "What Is Fixed Point"
date: 2018-08-31T12:30:33+08:00
draft: true
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

## 參考資料

- [Fixed-point arithmetic](https://en.wikipedia.org/wiki/Fixed-point_arithmetic)

