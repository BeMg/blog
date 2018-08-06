---
title: "Vector Architecture Notes - (1) VMIPS"
date: 2018-08-05T12:07:48+08:00
draft: true
---

## 前言

對於 Vector Architecture 做一下筆記。對於各種不同的 ISA 的 vector extension 進行比較。

- **computer architecture a quantitative approach 課本筆記 -> 實際上就是 VMIPS**
- riscv vector extension tutorial
- arm ISA vector extension

## VMIPS

VMIPS 是課本作者針對於 MIPS，增加 vector instruction 的擴增板。描述了大致上的硬體架構與各種針對於效能議題上的探討。

- 硬體架構
- 指令集分類
- 範例
- 術語
- 改進

### 硬體架構

![](https://i.imgur.com/MqS7Hjz.png)

- Vector register
    - 預設有 8 個 vector register
    - 每個 register 有 64 個 element，每個 element 寬度為 64 bits
- Vector functional units
    - 專門對 vector register 進行加法、減法、乘法、除法、邏輯運算的元件。
    - 可以被 pipelined。
        - 這一點後面的章節會提到。就是可以先執行在 vector register 中的第一個 element，而不用等到全部的 element 都讀入 register 才開始執行。
- Vector load/store units
    - 在 register 與 memory 之間讀入/寫入資料。
    - 一樣是可以被 pipelined。
    - 也支援 scalar 的 load/store
- scalar register
    - 就是 MIPS 中原本的那些 register。

在此還有兩個特殊的 register 留待後續討論。

- VLR (vector length regist)
    - 處理 vector 長度變化問題。
    - strip mining
- VM (vector mask)
    - 處理 IF statement -> 有一些 element 可以不用被執行到。

### 指令集分類

![](https://i.imgur.com/iJma1jU.png)

- 數值運算
    - `op rd, rs1, rs2 // rd[i] = rs1[i] op rs2[i] for all i` 
    - ADD__.D
    - SUB__.D
    - MUL__.D
    - DIV__.D
- load/store
    - 普通的從出發點取 VLR 長度的資料 `op v1, r1`
        - LV
        - SV
    - 跳著取，為了滿足多維矩陣的存取 (column-base or row-base)
        - LVWS 
        - SVWS
    - 對於一個 vector register 中存放特定的 index，並且根據這個 index 去取得特定 memory 位置的值。(為了滿足稀疏矩陣)
        - LVI 
        - SVI
        - CVI
- 設定指令長度
    - MTCL
    - MFCL
- 設定mask
    - 生成 mask，結果會存在 VM 這個 register 裡面
        - S--VV.D -> vector 對 vector 
        - S--VS.D -> vector 對 scalar
    - 存放 VM
        - MVTM
        - MVFM

### 範例

```c
Y[i] = a*X[i] + Y[i] 
```

假設陣列 X 與 Y 的起點在 Rx 與 Ry

- 普通的指令
    - ![](https://i.imgur.com/VbbIKuI.png)
- 有 vector instruction 指令
    - ![](https://i.imgur.com/3zl5m5z.png)

### 術語

- chained
    - 就是上面提到的可以被 pipeline
    - 不用等到整個vector執行完成才能執行下一步
    - 可以一個接一個 element 進行
- convey
    - 特定的 code 區塊，用意在於計算程式執行效率
    - 根據 structual hazard 或者 data hazard 進行分隔
        - 碰到這兩個就不能 pipeline 了
    - 因為可以pipeline，執行時間大致可以視為 vector 長度 
- chime
    - 一個 convey 執行的時間

### 改進

主要就是在提昇效能

- 同一個 clock cycle 執行多個 element
- 程式的 array 長度並非 64 的倍數
    - array 的長度有可能會到 run time 才能確定
    - 需要 VLR 的原因
- 讓有 IF 的程式也可以向量化
    - 需要 MASK 的原因
- 加速資料讀寫
- 多維陣列支援
- 稀疏陣列支援 

## 參考資料

- computer architecture a quantitative approach Chp4
- computer architecture a quantitative approach Appendix G