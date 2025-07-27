---
title: "優雅停止程式2"
date: 2025-07-27T10:23:51+08:00
tags: ["知識",'go', "程式停止"]
categories: ["程式"]
draft: false
---
# Go 實作Demo

單一goroutine的優雅停止，當接收到中斷信號時，能夠讓程式在完成當前任務後正常退出，而不是強制終止。

```go
package main

import (
	"context"
	"fmt"
	"os"
	"os/signal"
	"syscall"
	"time"
)

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	sigs := make(chan os.Signal, 1)
	signal.Notify(sigs, syscall.SIGINT, syscall.SIGTERM)

	go func() {
		ticker := time.NewTicker(2 * time.Second)
		for {
			select {
			case <-sigs:
				fmt.Println("接收到中斷信號，開始停止任務")
				cancel()
				return
			case <-ticker.C:
				fmt.Println("正在處理任務...")
			}
		}
	}()

	fmt.Println("全部服務已啟動")
	select {
	case <-ctx.Done():
		fmt.Println("全部任務已停止，程式退出")
		return
	}
}
```
## 多個goroutine的優雅停止(server, worker, 訂閱, 排程, infinite loop...)

使用廣播訊號來通知所有goroutine停止，並用wg 計算所有goroutine結束後再退出程式。
```go
package main

import (
    "fmt"
    "os"
    "os/signal"
    "sync"
    "syscall"
    "time"
)

func main() {
    sigs := make(chan os.Signal, 1)
    signal.Notify(sigs, syscall.SIGINT, syscall.SIGTERM)
    done := make(chan struct{})
    var wg sync.WaitGroup
    // 多個不停止的併發(server, worker, 訂閱, 定期等)
    for i := 0; i < 5; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            ticker := time.NewTicker(2 * time.Second)
            defer ticker.Stop()
            for {
                select {
                case <-done:
                    fmt.Printf("接收到中斷信號，任務 %d 開始停止\n", id)
                    return
                case <-ticker.C:
                    fmt.Printf("任務 %d 正在處理...\n", id)
                }
            }
        }(i)
    }

    fmt.Println("全部服務已啟動")
    select {
    case <-sigs:
        close(done) // 廣播訊號給所有goroutine，如果只是單一goroutine，可以直接cancel
        wg.Wait()
		fmt.Println("全部任務已停止，程式退出")
        return
    }
}
```
