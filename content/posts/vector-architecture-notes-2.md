---
title: "Vector Architecture Notes 2 - RISC-V Vector extension"
date: 2018-08-05T22:49:37+08:00
draft: true
---

## 前言

對於 Vector Architecture 做一下筆記。對於各種不同的 ISA 的 vector extension 進行比較。

- computer architecture a quantitative approach 課本筆記 -> 實際上就是 VMIPS
- **riscv vector extension tutorial**
- arm ISA vector extension

## RISC-V vector extension

規格還在制定中，只有一些 draft 等級的東西在，姑且暫時用這些資料作為參考。

- 硬體架構
- 指令集分類
- 範例

> 也就是說很有可能之後通通改掉，直接白研究了 QQ


## Complement vector extension instruction

根據[2]，我們可以列出所有的指令如下：

- operation
  - 用途 
- body
  - 指令的代號
- encoding
  - 是否已經被編碼
  - 是某編碼是根據[這份資料](https://i.imgur.com/mvJoaeb.png)
- instruction detail
  - 各種派生指令

### Vector Memory Instructions

| Operation                       | Body | Encoding | Instructions Detail                                          |
| ------------------------------- | ---- | -------- | ------------------------------------------------------------ |
| vector load                     | vl   | Yes      | vlb, vlbu, vlh, vlhu, vlw, vlwu, vld, vflh, vflw, vfld       |
| vector load, strided            | vls  | Yes      | vlsb, vlsbu, vlsh, vlshu, vlsw, vlswu, vlsd, vflsh, vflsw, vflsd |
| vector load, indexed (gather)   | vlx  | Yes      | vlxb, vlxbu, vlxh, vlxhu, vlxw, vlxwu, vlxd, vflxh, vflxw, vflxd |
| vector store                    | vs   | Yes      | vsb, vsh, vsw, vsd                                           |
| vector store, strided           | vss  | Yes      | vssb, vssh, vssw, vssd                                       |
| vector store, indexed (scatter) | vsx  | Yes      | vsxb, vsxh, vsxw, vsxd                                       |
| vector store, indexed, unorder  | vsxu | No       | vsxub, vsxuh, vsxuw, vsxud                                   |

### Vector Integer Instructions

| Operation         | Body | Encoding | Instructions                                      |
| ----------------- | ---- | -------- | ------------------------------------------------- |
| add               | vadd | Yes      | vadd, vaddi, vaddw, vaddiw                        |
| subtract          | vsub | Yes      | vsub, vsubw                                       |
| multiply          | vmul | No       | vmul, vmulh, vmulhsu, vmulhu                      |
| widening multiply | ???  | No       | vmulwdn                                           |
| divide            | vdiv | No       | vdiv, vdivu, vrem, vremu                          |
| shift             | vs   | No       | vsll, vslli, vsra, vsrai, vsrl, vsrli             |
| logical           | ???  | No       | vand, vandi, vor, vori, vxor, vxori               |
| compare           | ???  | No       | vseq, vslt, vsltu                                 |
| fixed point       | vcli | No       | vclipb, vclipbu, vcliph, vcliphu, vclipw, vclipwu |

### Vector Floating Point Instructions

| Operation | Body    | Encoding | Instructions                                                 |
| --------- | ------- | -------- | ------------------------------------------------------------ |
| add       | vfadd   | No       | vfadd.h, vfadd.s, vfadd.d                                    |
| substract | vfsub   | No       | vfsub.h, vfsub.s, vfsub.d                                    |
| multiply  | vfmul   | No       | vfmul.h, vfmul.s, vfmul.d                                    |
| divide    | vfdiv   | No       | vfdiv.h, vfdiv.s, vfdiv.d                                    |
| sign      | vfsgn   | No       | vfsgn{j,jn,jx}.h, vfsgn{j,jn,jx}.s, vfsgn{j,jn,jx}.d         |
| max       | vfmax   | No       | vfmax.h, vfmax.s, vfmax.d                                    |
| min       | vfmin   | No       | vfmin.h, vfmin.s, vfmin.d                                    |
| compare   | ???     | No       | vfeq.h, vfeq.s, vfeq.d, vltq.h, vflt.s, vflt.d, vfle.h, vfle.s, vfle.d |
| sqrt      | vfsqrt  | No       | vfsqrt.h, vfsqrt.s, vfsqrt.d                                 |
| class     | vfclass | No       | vfclass.h, vflcass.s, vfclass.d                              |

### Vector  Floating Point Multiply Add

| operation    | instructions                          |
| ------------ | ------------------------------------- |
| add          | vfmadd.h, vfmadd.s, vfmadd.d          |
| sub          | vfmsub.h, vfmsub.s, vfmsub.d          |
| widening add | vfmaddwdn.h, vfmaddwdn.s, vfmaddwdn.d |
| widening sub | vfmsubwdn.h, vfmsubwdn.s, vfmsubwdn.d |

### Vector Convert

| From Integer to Float |                      |
| --------------------- | -------------------- |
| To Half               | vfcvt.h.i, vfcvt.h.u |
| To Single             | vfcvt.s.i, vfcvt.s.u |
| To Double             | vfcvt.d.i, vfcvt.d.u |

| From Float to Vemaxw Integer |                                 |
| ---------------------------- | ------------------------------- |
| To Signed                    | vfcvt.i.h, vfcvt.i.s, vfcvt.i.d |
| To Unsigned                  | vfcvt.u.h, vfcvt.u.s, vfcvt.u.d |

| From Float to Float |                      |
| ------------------- | -------------------- |
| To Half             | vfcvt.h.s, vfcvt.h.d |
| To Single           | vfcvt.s.h, vfcvt.s.d |
| To Double           | vfcvt.d.h, vfcvt.d.s |

| From Vemaxw Int to Narrow Int |               |
| ----------------------------- | ------------- |
| To Byte                       | vcvt.{b,bu}.i |
| To Half                       | vcvt.{h,hu}.i |
| To Word                       | vcvt.{w,wu}.i |

### Vector Data Movement

| Operation              | Instruction                | action              |
| ---------------------- | -------------------------- | ------------------- |
| insert gpr into vector | vins vd, rs1, rs2          | vd[rs2] = rs1       |
| insert fp into vector  | vins vd, fs1, rs2          | vd[rs2] = fs1       |
| extract velem to gpr   | vext rd, rs1, rs2          | rd = vs1[rs2]       |
| extract velem to fp    | vext fd, vs1, rs2          | fd = vs1[rs2]       |
| vector-vector merge    | vmerge vd, vs1, vs2, vm    | mask picks src      |
| vector-gpr merge       | vmergex vd, rs1, vs2, vm   | mask picks src      |
| vector-fp merge        | vmergef vd, fs1, vs2, vm   | mask picks src      |
| vector register gather | vrgather vd, vs1, vs2, vm  | vd[i] = vs1[vs2[i]] |
| Gpr splat/bcast        | vsplatx vd, rs1            | Vd[0..MAXVL] = rs1  |
| fpr splat/bcast        | vsplatf vd, fs1            | Vd[0..MAXVL] = fs1  |
| vector slide down      | vslidedwn vd, vs1, rs2, vm | vd[i] = vs1[rs2+i]  |
| vector slide up        | vslideup vd, vs1, rs2, vm  | vd[rs2+i] = vs1[i]  |

### Vector Mask Operations

| Operation                  | Instruction       |
| -------------------------- | ----------------- |
| Find first set bit in mask | Vmfirst rd, vs1   |
| Mask pop count             | Vmpopc rd, vs1    |
| Count preceding mask bits  | Vmiota vd, vm     |
| Flag before first          | Vmfbf vd, vs1, vm |
| Flag including first       | Vmfif vd, vs1, vm |

> 很明顯可以發現絕大多數的指令都沒有被編碼，這 proposal 也是pro個辛酸的。跟沒有講差不多，更何況還語帶保留的說很有可能會改。

看看就好。

## Register Design

暫存器在 RISC-V vector extension 中主要分為兩種。

- Data vector register
- Control and State Register (CSR)

### Vector Data Register 

- v0-31
- MAXVL 由實作決定
- 資料的型態 -> 就指令來看，整數跟浮點數用同一組Register (待研究)

### CSR 

- vcfg (WARL->Wrtie Any Values, Reads Legal Values)
  - vregmax (8 bits)：控制 Vector data register 的數量 (disable 與否)
    - 舉例來說，如果其值為0，則 vector unit disable
    - 其值為2，則 v0, v1 啟用
    - 所以實際上 Vector Data Register 保留了可以擴充到 256 個空間
  - vemaxw (3 bits)：設定單一個 element 有多寬
    - ![](https://i.imgur.com/AkauTw2.png)
  - vtypeen (1 bit)：啟動 type extension (功能待補)
- vxcfg
  - vxcm (1 bit)：fixed point clip mode 
    - ![](https://i.imgur.com/LIWuEIx.png)
  - vxrm (2 bits)：fixed point rounding mode
    - ![](https://i.imgur.com/F1HIfM3.png)
- vl
  - 設定目前有效的 vector length
  - 可以使用 `vsetvl` 進行設定

所有的 CSR，都可以藉由 `vconfig` 進行同一設定。

用法：`vconfig imm`

> vdisable 寄生在 vconfig 上面，等效於 `vconfig 0`



## 參考資料

1. [riscv-v-spec](https://github.com/riscv/riscv-v-spec)
2. [The RISC-V Vector ISA Tutorial](https://drive.google.com/file/d/1fWyhzi5XsjrdamAPZISKH7RwRgc93Q9o/view?usp=sharing)
3. [The RISC-V Vector ISA Update](https://drive.google.com/file/d/164QVrxV5OW4peSvhP6j1POkFOYQ4t6oT/view?usp=sharing)