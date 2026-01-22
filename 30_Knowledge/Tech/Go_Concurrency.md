---
type: knowledge
topic: Go
tags: [go, concurrency, goroutine, backend]
publish: true
last_updated: <% tp.file.last_modified_date() %>
---

# Go Concurrency: Goroutines & Channels

## 📌 Concept
จุดเด่นที่สุดของ Go คือ Concurrency Model ที่เบาและเขียนง่าย
- **Goroutine**: Thread ขนาดจิ๋ว (User-space thread) ใช้ RAM แค่ 2KB
- **Channel**: ท่อสื่อสารระหว่าง Goroutine (Don't communicate by sharing memory; share memory by communicating)
- **WaitGroup**: ตัวรอให้งานย่อยเสร็จให้หมด

## 💻 How it works / Code

### 1. Basic Goroutine & WaitGroup
```go
package main

import (
	"fmt"
	"sync"
	time	"time"
)

func worker(id int, wg *sync.WaitGroup) {
	defer wg.Done() // แจ้งว่าเสร็จแล้ว
	fmt.Printf("Worker %d starting\n", id)
	time.Sleep(time.Second)
	fmt.Printf("Worker %d done\n", id)
}

func main() {
	var wg sync.WaitGroup

	for i := 1; i <= 5; i++ {
		wg.Add(1) // เพิ่มจำนวนงานที่ต้องรอ
		go worker(i, &wg)
	}

	wg.Wait() // รอจนกว่าจะเหลือ 0
	fmt.Println("All workers done")
}
```

### 2. Buffered Channel (Worker Pool Pattern)
```go
func main() {
	jobs := make(chan int, 100)
	results := make(chan int, 100)

	// Start 3 workers
	for w := 1; w <= 3; w++ {
		go func(id int, jobs <-chan int, results chan<- int) {
			for j := range jobs {
				fmt.Println("worker", id, "processing job", j)
				time.Sleep(time.Second)
				results <- j * 2
			}
		}(w, jobs, results)
	}

	// Send jobs
	for j := 1; j <= 5; j++ {
		jobs <- j
	}
	close(jobs) // ปิดท่อส่ง (เพื่อให้ worker รู้ว่าหมดงานแล้ว)

	// Collect results
	for a := 1; a <= 5; a++ {
		<-results
	}
}
```

## 🔗 References
- [A Tour of Go: Concurrency](https://go.dev/tour/concurrency/1)
- [Go by Example](https://gobyexample.com/)
