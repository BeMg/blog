---
title: "Go goroutine"
date: 2018-08-27T11:16:03+08:00
draft: false
---

## 前言

這篇文章會包含 goroutine 的一些用法與需要注意的要點。

在這個問題上，我們需要釐清的重點在於

* goroutine 
* channel 
* select 
* waitgroup 
* timeout

`goroutine`是 golang 中一個重要的 feature，概念上就是從程式語言的層次上支援 coroutine 的功能。可以在低成本(不論是硬體或者是程式撰寫的部份)的情況下大量的使用平行化。

從最近使用的感想上來說，可以理解為，被指定由`goroutine`方法執行的程式區塊，是被直接丟進另外一個`task pool` 中等待電腦去執行它。至於 golang 是如何實現讓這些大量的 task 去榨乾所有的運算支援大概可以再寫一篇文章來討論。

被放出去的執行的程式，我們可以使用 channel 作為傳遞資料的管道，也可以作為一種 wait 的方法。當有多個 coroutine 同時在背景執行時，且每個 coroutine 個別的結果需要分別處理時，就可以利用 select 去接 channel 並且分開來處理。

當不需要分開處理又需要等待所有的coroutine執行完畢。就簡單的使用 sync.waitgroup 去執行與等待。

在每個執行的程式之中，有些程式可能會卡死，原因很多，像是網路不通阿。要取得的東西太大...總之，如果讓整個程式就卡在這裡的話，就沒有加速的意義了。所以需要一個timeout來限制最長的執行時間，如果時間到 coroutine 卻還沒有執行完成，那就進行其他的處理。

## 範例 Goroutine and Channel 

```go
func main() {
    
    c1 := make(chan int)
    go func () {
        c1 <- do_something_computer1() 
    }()
    
    c2 := make(chan int)
    go func () {
        c2 <- do_something_computer2()
    }()
    
    result := <-c1 + <-c2
}
```

- 調用 goroutine 的關鍵字為 `go` 只能加在一個 method/function 調用之前(待驗證)。用途就是將這個 func 令其在丟進 task pool。
- channel 為goroutine之間溝通的管道，我們可以利用其來達成 wait 的效果。
  - channel 的宣告為在變數之前加上 chan，例：chan int
  - channel 的使用上會用 <-，由變數的位置來決定是取用還是寫入
- 系統會等到c1與c2都執行完成才會結束。

> 如果不 wait，執行過程不會等待 goroutine 執行結束與否就直接 exit

## 範例 waitgroup, select and timeout

```go
func main() {
    var wg snyc.waitgroup
    for i, v := range list_of_string {
        wg.Add(1)
        go func (s string, wg *sync.waitgroup) {
            do_something(s)
            wg.Done()
        }(v, wg)
    }
    c1 = make(chan int)
    go func () {
        wg.Wait()
        close(c1)
    }()
    
    select {
    case <- c1:
        fmt.Printf("Success.\n")
    case <- time.After(100 * time.Second):
        fmt.Printf("Timeout\n")
    }
}
```







