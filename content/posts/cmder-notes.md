---
title: "Cmder Notes"
date: 2018-07-06T13:04:56+08:00
draft: false
---

## 前言

在 windows 系統底下，cmd(命令提示字元)是類似於 unix bash 的功能。可是 cmd 看起來實在是不太友善，無論是用法與字體，他的指令用法也是與 unix 有很大的差異。所以在於 windows 系統下，我通常會使用有 gui 的應用程式進行操作(例：git...)。

> 一點差異：顯示當其目錄的檔案 ls vs dir

不過大概在 2016 開始，微軟讓 windwos 10 可以支援 linux 的子系統，被稱為 `bash on windows`，裝起來就很像一個只有 bash 的虛擬機器。

cmder 就是在 cmd 上面在套一層看起來很漂亮的畫面。在搭配上 `bash on windows`。期許可以用起來感覺像是真的在 linux 下指令。

## 安裝

### bash on windows

* 開啟 控制台-開啟或關閉windows功能-適用於windows的linux子系統
* 下載 windows store 中的 ubuntu
* 啟動cmd
* 輸入bash -> 初次使用要進行安裝
* 此後只要輸入 bash 就可以啟動

### cmder

cmder 不用安裝，它就是一個資料夾，可以用dropbox還是google driver進行多台電腦同步。

* 去[官網](http://cmder.net/)下載
* 啟動 /bin/cmder.exe
* 啟動cmder就是啟動cmd -> 所以要打入bash進入 `bash on windows`
