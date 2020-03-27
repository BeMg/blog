---
title: "Fcitx 輸入法與新酷音"
date: 2018-02-25T12:04:05+08:00
draft: false
---

在ubuntu底下有內建輸入法ibus，但是其使用上的體驗與在windows上的新注音輸入法有比較大的隔閡，用起來不是很習慣。在尋找更好的輸入法時，有嘗試過gcin與Rime。下面分別說說他們的缺點。

- gcin
    - 這個輸入法沒有辦法自動學習正確的詞彙，以及一開始預設的字典挺小的。用起來時常會打錯字，需要細心的一一修正，用起來不是很方便。
- rime
    - 在字典的自動修正上與新詞彙的學習上都較為良好，但是在輸入法的使用上，rime並不會限制你的輸入，也就是說就算你輸入了一連串完全沒有待選字的注音，它還是會讓你繼續輸入，很大程度上要求你字字輸入清楚，用起來壓力很大。


## fcitx

於是乎我就找到了另外一款兼具gcin輸入方式與rime的詞彙精準度的輸入法 新酷音。下面簡單的介紹如何安裝與使用。

### 安裝

```
sudo apt-get install fcitx fcitx-chewing fcitx-anthy
```

- fcitx 是輸入法框架
- chewing 是新酷音
- anthy 是一款日文輸入法

### 設定

System setting -> language support -> language tab -> Keyboard input method system -> 調整為fcitx

如果沒有跳出fctix，請重新登入系統。

開啟 fcitx 的 configue 加入，fcitx-chewing與fcitx-anthy。

### 操作

- `ctrl-space` fcitx的啟用與關閉
    - 常用於切換成英文輸入
- `ctrl-shift` 在fcitx啟動的情況下，循環切換設定的輸入法

