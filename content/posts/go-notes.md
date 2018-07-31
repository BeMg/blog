---
title: "Go Notes"
date: 2018-07-31T20:38:40+08:00
draft: true
---

## 前言

簡單的紀錄一下 golang

- 基本語法
- 爬蟲相關
- 使用心得

## 基本語法

```c
// 註解的形式與c相同，雙重單斜線為單行註解。
/* 
此為多行註解
*/

// 使用外部函式庫
// 備註：沒有用到卻宣告會導致錯誤。
import (
    "fmt"
	"io"
	"io/ioutil"
	"net/http"
	"os"
	"strconv"
)

// 變數宣告
// 一樣，沒有使用卻宣告會導致錯誤。
var a [type] = value
a := value // 最常用
var a = value

// 據說會自行推理型態。

// 陣列宣告
var a [5]int;

// 切片(slice)宣告
var a []int = {1, 2 ,3, 4};
a = append(a, 5);
a[1:2] // get [2];


// 就是 c 的 printf
// golang 習慣函數的頭一個字母為大寫，似乎是隱藏了一些慣例，只要是首字大寫變為 public method 或者 variable。
//                                                ^ 待確認
fmt.Printf("Hello world\n")

yee := 87
fmt.Printf("Yee : %d\n", yee)

if [condition] {
    // condition is true then do something
} else {
    // condition is false then do something
}

for i:=0; i<10; i++ {
    // from 0 to 9
}

// 也有方便好用的迭代
for i, v := range [something can be iterated] {
    // in case int array
    // i will be index, v will be value
}

// _ 是一個特殊的變數，代表直接拋棄，大概就跟/dev/null差不多。
_ = 5

```