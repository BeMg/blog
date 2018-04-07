---
title: "Selenium notes"
date: 2018-04-07T16:22:57+08:00
draft: false
---

## 前言

對於使用javascript動態產生的網頁來說，單純的抓取html會缺失許多資訊。我們可以透過selenium執行javascript，生成我們所需要的資訊。

selenium原本是被設計來進行自動化測試的，但是我們只需要它執行javascript的功能。

## 安裝

```shell
pip3 install selenium
```

在此使用chrome作為範例。

到[官網](https://sites.google.com/a/chromium.org/chromedriver/getting-started)下載chromedrive。並且將其加入至PATH中，或者在使用時指定其位置。

## 使用

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

# 加入參數
chrome_options = Options()
chrome_options.add_argument("--headless")

# 啟動
drive = webdriver.Chrome(chrome_options=chrome_options)

# 讀取網頁
drive.get(url)

# 取得source
drive.page_source


```





