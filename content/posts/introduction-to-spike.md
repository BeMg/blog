---
title: "Introduction to Spike"
date: 2018-08-01T10:32:07+08:00
draft: false
---

## 前言

最近花了大概兩個禮拜研究了 RISCV 的官方模擬器 (riscv-isa-sim) Spike。

內容包含

- 安裝
- 基本使用
- 如何加入新的指令

## 安裝

在使用 spike 之前，需要安裝 riscv-tools，所需要的工具大致如下

- **riscv-gnu-toolchain: RISC-V cross-compiler**
- riscv-fesvr: a "front-end" server that services calls between the host and target processors on the Host-Target InterFace 
- **riscv-isa-sim: ISA simulator, as kown as spike**
- **riscv-opcodes: the enumeration of all RISC-V opcodes executable by the simulator**
- riscv-pk: a proxy kernel that services system calls generated by code built and linked with the RISC-V Newlib port
- riscv-tests: a set of assembly tests and benchmarks

粗體的部份是使用 spike 會需要理解的部份。

```shell
git clone https://github.com/riscv/riscv-tools.git
cd riscv-tools
git submodule update --init --recursive
export RISCV=/path/to/install/riscv/toolchain
./build.sh
export $RISCV/bin to $PATH
```

> 覺得編譯速度不夠快可以去修改 build.sh 使用多核心


## 如何使用 spike

寫程式 -> 用 cross-over compiler 編譯出 riscv machine code -> 使用 spike 運行此執行檔

- 寫程式

```c
#include <stdio.h>
int main() {
    int a = 1*2*3*4;
    printf("%d\n", a);
    return 0;
}
```

- 使用cross-over compiler 編譯

```shell
riscv64-unknown-elf-gcc main.c -o main
```

- 使用 spike 運行此程式

```
spike pk main
```


## 如何加入新的指令

spike 是一個 riscv 的模擬器，最主要的目標就是在沒有支援 riscv ISA 的處理器上，也可以執行以 riscv machine code 為主的程式。方便程式開發與測試。其本身就是在定義一個符合 riscv 規格的處理器。有 register 有 memory 最大程度的重現真實CPU的行為。

> 當然，結果正確無誤比起流程正確無誤重要。

- 定義指令編碼與生成mask
- 描述指令行為
- 註冊進spike
- 測試新的指令

這一個章節會以 `vfadd.s` 作為範例

- 格式
    - `vfadd.s rd rs1 rs2`
- 讀取 rs1 與 rs2 的值，並且將加在一起的結果存在 rd。 
- 操作皆為浮點數

### 定義指令編碼與生成mask

首先必須定義好專屬於這個指令的編碼，這樣才可以在 spike 辨識出這個指令並且執行特定的行為。

```c
vfadd.s rd rs1 rs2 31..25=0 14..12=6 6..2=0x02 1..0=3
```

上述編碼會將其定義在 `riscv-tools/riscv-opcodes/opcodes`，這個檔案中存放有所有 spike 可以識別指令的編碼。理論上是不能有重覆的。

> 實際上有可能會出現某些指令寄生在其他指令身上的情況發生。舉例來說：`nop`就寄生在`addi`上。詳情可以查看規格書

接著使用 `parse-opcode` 這一個執行檔產生出相對應所撰寫編碼的十六進位表示。

```c
cat opcodes opcodes-rvc-pseudo opcodes-rvc opcodes-custom | ./parse-opcodes -c | grep "VFADD_S"
```

![](https://i.imgur.com/Gl3QMq2.png)

> 這邊是一個取巧的辦法，指印出所需要新增的那一個指令。當然你可以不使用 `grep` 直接輸出。

將這個結果加入至 `riscv-tools/riscv-isa-sim/riscv/encoding.h` 。

### 描述指令行為

所有行為的描述都建立在 `rsicv-tools/riscv-isa-sim/riscv/insn/[instruction name].h`。

實際上用於描述指令行為的語言是 c++，只不過 spike 用上了一堆 marco 讓它看起來很不像，這些 marco 主要是在替換調與 register 和 memory 互動的複雜性。多看幾次應該就可以感覺的出來這些 marco 實際上的行為。

一樣用 vfadd.s 作為例子

```c
vreg_t rst;
vreg_t rs1 = VRS1;
vreg_t rs2 = VRS2;
int vl = STATE.vl;

for(int i=0; i<vl; i++) {
    rst.f_data[i] = rs1.f_data[i] + rs2.f_data[i];
}
for(int i=vl; i<MAXVL; i++) {
    rst.f_data[i] = 0;
}

WRITE_VRD(rst);
```

### 註冊指令至spike

到 `riscv-tools/riscv-isa-sim/riscv/riscv.mk.in` 中，在`riscv_insn_list`下加入新的指令名稱。並且重新編譯一次 spike。


### 測試新指令

因為並沒有實際去修改編譯器，所以編譯程式的時候，編譯並不會自動生成剛剛新增的指令。而為了加入一個新的指令而重新編譯一次編譯器太花時間了。

> 一次大概 30 分鐘。

所以在這裡使用一個小技倆，可以繞過編譯器，直接加入新的指令至執行檔。這個技倆就是 `assembly inline`。

```c
asm __volatile__ (".word [instruction encoding]\n");
```

這一個部份的關鍵就是要如何生成出正確的指令格式。關鍵如下：

- 編碼
- 暫存器

這部份過於繁雜，在此不具體描述，僅提供一些想法。

- 先定義好所需要的變數，如此編譯器就會將這些變數分配給特定的暫存器
- 使用工具查看編譯器所產生的 `assembly` 看這些變數
- 自己轉換編碼，`bin to hex`
- 重新編譯一次程式


