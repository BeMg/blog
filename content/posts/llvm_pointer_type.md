---
ctitle: "Llvm_pointer_type"
date: 2018-12-01T12:21:14+08:00
draft: false
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

### STORE

```c
store i32* %1, i32** %3, align 8
// %1 -> int x
// %3 -> int *p
```

我們先回歸所謂 store 指令的定義，store 的本質為，**將一個 value ，存入一個 address 中**。剛好，pointer type 其中的 value 就是 address。所以很容易造成混淆。

我們來逐個 operand 進行分析。

```c
// %1 -> x
// x - &x
// 多一個star，向右一格
i32* %1 -> &x
```

```c
// %3 -> *p
// *p - p - &p
// 多一個star，向右一格
i32** %3 -> &p
```

但是我們還要考慮store的本質，所以我們是存值回 address of p。

```c
p = &x;
```

> 所以從分析的角度看來，store 第二個 operand 會導致向左一格。

### LOAD

接著來看看有