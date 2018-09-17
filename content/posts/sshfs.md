---
title: "Sshfs"
date: 2018-09-17T13:55:00+08:00
draft: true
---

# sshfs

## 前言

這是一個與遠端檔案同步的解決方案，最大的特色為伺服器端不需要進行任何的設定。

功能上是可以在本地同步修改遠端的檔案或者文件，表現的手法是將遠端的資料夾視為一個本地的資料夾，這樣就可以直接像是在修改本地資料一樣修改位於遠端的資料。

## ubuntu

```c
apt-get install sshfs
sshfs [username]@[server ip]:[remote dir] [local dir]
fusermount -u [local dir]
```

以上三個指令分別是代表安裝、使用與解除掛載。

## windows

這個就沒有像 ubuntu 一樣方便的，需要一些進階的輔助程式。

* [sshfs-win](https://github.com/billziss-gh/sshfs-win)
* [winfsp](https://github.com/billziss-gh/winfsp/releases/tag/v1.3)
* [siriKai]([SiriKali](https://mhogomchungu.github.io/sirikali/))

前兩個是必須的，第三個為GUI的操控界面用起來可以更的心應手。

都裝一裝，開 siriKai 起來進行設定。

- create volume
  - sshfs
    - remote address -> 就是 [usernmae]@[server ip]:[remote dir]
    - Mount point path -> 隨便選一個，沒有它會建立
- Add and done
- Favorites
  - 點你剛剛加入的volume
  - 輸入密碼



