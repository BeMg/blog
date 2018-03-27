---
title: "LLVM 筆記 (更新中)"
date: 2018-02-26T19:03:58+08:00
draft: false
---

## 目錄

- 前言
- 架構
- 實驗
- 參考資料

## 前言

LLVM是一個完整的編譯器基礎建設(compiler infrastructure)，與`GNU GCC`相比，最大的差異在於大量的將compile的過程高度模組化，可以進行任意的抽換。從 source code 到 machine code，每一個階段都有相對應的module。

本篇文章雖然以LLVM 為標題，可是實際上多半都在整理編譯器的相關知識，畢竟LLVM大部分都是在實做這些編譯器的技術。

## 安裝

從原始碼開始從頭編譯

```bash
apt-get update
apt-get install -y sudo subversion python-dev g++ cmake
svn co http://llvm.org/svn/llvm-project/llvm/trunk llvm
cd llvm/tools
svn co http://llvm.org/svn/llvm-project/cfe/trunk clang
cd ../..
cd llvm/tools/clang/tools
svn co http://llvm.org/svn/llvm-project/clang-tools-extra/trunk extra
cd ../../../..
cd llvm/tools
svn co http://llvm.org/svn/llvm-project/lld/trunk lld
cd ../..
cd llvm
mkdir build
cd build
cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release ..
make
```

## 架構

### compiler 架構

在常見的compiler中，常見這類三段式的設計。
![](https://i.imgur.com/miah8lO.png)

- FrontEnd
    - 將Source code 轉換為 IR or AST
    - 包含lexer、parser
    - lexer： Regular Expression
    - parser：Context-free grammar -> ANTLR 之類的
- Optimizer
    - 針對於IR進行最佳化，以追求更快的效能或者節省資源。
    - 在有中間語言的情況下，不需要針對每一種target language撰寫優化。
    - 也不需要撰寫多種不同的語言轉換。
- BackEnd
    - 將IR 轉換為 target language
    - instruction selection
    - register allocation
    - instruction scheduling

而 LLVM 也是按照這三個階段的 compiler 設計為基準開發而成。

### LLVM 的 架構

Source code -> clang(lexer -> parser -> AST) -> LLVM IR -> LLVM bitcode -> object code(machine code) -> linker

### 實際跑一次看看

而用實際上的llvm的module，達成從

```
        clang            llvm-as                 lli
C code -------> llvm IR ---------> llvm bitcode -----> result
                          llc                 GNU ld
                llvm IR -------> object code --------> binary file
                        | llc                          GNU gcc
                        -------> naive assembly code ----------> binary file
```

```c
#include <stdio.h>
#include <string.h>

int main() {
    int a = 2 + 5;
    int b = a + 10 + 30;
    int c = a * b + 30 + a;
    
    char s[100];
    sprintf(s, "%d", c);
    puts(s);

    return 0;
}
```

> 執行結果為366

clang利用特定指令使其在編譯過程中停止，並且生成中間產物。

- `clang -emit-llvm -c test.c`
- `clang -emit-Optimizerllvm -S test.c`

![test.ll](https://i.imgur.com/bRy0JPr.png)

## Front End

Clang是llvm官方的front end，主要針對C, C++和Objective-C和等語言而作。最主要負責的工作為將source code 轉換成 AST (Abstract Syntax Tree)，在將其轉換為 llvm IR。

雖然說是前端，不過實際上的使用的時候可以加入多樣參數，完成編譯程式的每一個環節。

> 在編譯期間加入 -### 參數可以查看clang究竟調用了多少額外的工具

### AST

##Optimizer

### IR

### SSA

## Back End

### DAG







## 參考資料