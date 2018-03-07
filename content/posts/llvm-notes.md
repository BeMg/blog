---
title: "LLVM 筆記 (更新中)"
date: 2018-02-26T19:03:58+08:00
draft: true
---

## 目錄

- 前言
- 架構
- 實驗
- 參考資料

## 前言

LLVM是一個完整的編譯器基礎建設(compiler infrastructure)，與`GNU GCC`相比，最大的差異在於大量的將compile的過程高度模組化，可以進行任意的抽換。從 source code 到 machine code，每一個階段都有相對應的module。

## 安裝(build from source)

```bash
apt-get update
apt-get install sudo subversion python-dev g++ cmake
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
cmake ..
make
```

## 架構

### compiler 架構

在常見的compiler中，常見這類三段式的設計。
![](https://i.imgur.com/miah8lO.png)

- FrontEnd
    - 將Source code 轉換為 IR or AST
    - 包含lexer、parser
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


## 參考資料

