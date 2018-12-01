---
title: "Llvm_pointer_type"
date: 2018-12-01T12:21:14+08:00
draft: true
---

# LLVM pointer type 解析

寫個作業發現居然沒有對於 LLVM pointer type 的詳細說明，難怪沒有人要做 compiler，文件都那麼不友善。明明 pointer 是這麼常用的到東西。一堆 dereference 的星星。

本文基於C language 對於 llvm IR 的轉換進行介紹。

## 目錄

-  名詞定義
  - pointer type
  - reference
  - dereference

- LLVM IR

## 名詞定義

### Pointer type

在C語言中，我們使用 pointer 會是一個指向某一個 address 的 variable。

```c
int *p;
int x;
p = &x;
```

### Reference

每個變數都有值(value)跟位址(address)，pointer type 只是剛好 value 是某一個 address。所謂的 Reference 就是指用一個關鍵字去取得一個特定變數的address。

```c
int *p;
printf("%p\n", &p);
```

### dereference

這個操作是指 reference 的反向，如果可以從一個變數取得它的位址，那麼也可以從一個位址取得它的值。

```c
int *p;
int x = 10;
p = &x;
printf("%d\n", *p);
```

## LLVM IR

但是以上的各種名詞定義都僅僅應用在C語言層級。LLVM IR 更像是一種組合語言，對於組合語言來說，就沒有什麼 Reference、dereference。需要關注的，只有三種指令。

- alloca
  - 宣告一段空間給變數
- load
  - 從某個address取得其值
- store
  - 將某個值放入某個address

如此是不是可以得到一種直覺的對應。

- Load -> Reference
- Store -> dereference

當然實際上還要再更複雜一點。接下來我們來看一下這個LLVM IR範例。

```c
  // This is C source code
  int x, y, *p, **pp;
  p = &x;
  pp = &p;
  *pp = &y;
  *p = 3;
  **pp = *p;

  // This is LLVM IR transform by clang
  %1 = alloca i32, align 4
  %2 = alloca i32, align 4
  %3 = alloca i32*, align 8
  %4 = alloca i32**, align 8
  
  store i32* %1, i32** %3, align 8
  
  store i32** %3, i32*** %4, align 8
  
  %5 = load i32**, i32*** %4, align 8
  store i32* %2, i32** %5, align 8
  
  %6 = load i32*, i32** %3, align 8
  store i32 3, i32* %6, align 4
  
  %7 = load i32*, i32** %3, align 8   
  %8 = load i32, i32* %7, align 4     
  %9 = load i32**, i32*** %4, align 8 
  %10 = load i32*, i32** %9, align 8  
  store i32 %8, i32* %10, align 4
```

我們可以在C語言層級重新看待這段程式碼。

```
**pp - *pp - pp - &pp
```

- 當star數與實際宣告型態相同時，變數是原始型態

```
i32 %1    -> x
i32 %2    -> y
i32 * %3  -> p
i32 ** %4 -> pp
```

- 當多一個star的時候為reference，當少一個星時候為dereference，但是實務上只會有多一個星的狀況發生。
  - 因為 reference 是去取得一個constant的位址，dereference 是去取得一個動態的值。
  - 前者可以亂來，後者要靠指令。