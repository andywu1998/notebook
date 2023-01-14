---
title: golang里的map并发读写实验
---

测试一下 并发读，并发写，并发读写
<!--more-->

# 并发读 不会panic
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	m := make(map[string]int)
	wg := sync.WaitGroup{}
	wg.Add(1)
	go func() {
		defer wg.Done()
		for {
			a := m["1"]
			fmt.Println(a)
		}
	}()
	go func() {
		defer wg.Done()
		for {
			a := m["2"]
			fmt.Println(a)
		}
	}()

	wg.Wait()
}

```

# 并发写 会出现panic
fatal error: concurrent map writes

```go 
package main

import (
	"sync"
)

func main() {
	m := make(map[string]int)
	wg := sync.WaitGroup{}
	wg.Add(1)
	go func() {
		defer wg.Done()
		for {
			m["1"] = 1
		}
	}()
	go func() {
		defer wg.Done()
		for {
			m["2"] = 2
		}
	}()

	wg.Wait()
}

```

# 并发读写 会出现panic
fatal error: concurrent map read and map write
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	m := make(map[string]int)
	wg := sync.WaitGroup{}
	wg.Add(1)
	go func() {
		defer wg.Done()
		for {
			m["1"] = 1
		}
	}()
	go func() {
		defer wg.Done()
		for {
			a := m["2"]
			fmt.Println(a)
		}
	}()

	wg.Wait()
}

```