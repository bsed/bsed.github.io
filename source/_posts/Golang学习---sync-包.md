---
title: "Golang学习 - sync 包"
categories: [ "Golang" ]
tags: [ "golang","sync" ]
draft: false
slug: "golang-learning-sync-pack"
date: "2016-05-28 14:21:00"
---

## 临时对象池

Pool 用于存储临时对象，它将使用完毕的对象存入对象池中，在需要的时候取出来重复使用，目的是为了避免重复创建相同的对象造成 GC 负担过重。其中存放的临时对象随时可能被 GC 回收掉（如果该对象不再被其它变量引用）。

从 Pool 中取出对象时，如果 Pool 中没有对象，将返回 nil，但是如果给 Pool.New 字段指定了一个函数的话，Pool 将使用该函数创建一个新对象返回。

Pool 可以安全的在多个例程中并行使用，但 Pool 并不适用于所有空闲对象，Pool 应该用来管理并发的例程共享的临时对象，而不应该管理短寿命对象中的临时对象，因为这种情况下内存不能很好的分配，这些短寿命对象应该自己实现空闲列表。

Pool 在开始使用之后，不能再被复制。


<!--more-->


```golang
type Pool struct {
	// 创建临时对象的函数
	New func() interface{}
}

// 向临时对象池中存入对象
func (p *Pool) Put(x interface{})

// 从临时对象池中取出对象
func (p *Pool) Get() interface{}

```
## 单次执行

Once 的作用是多次调用但只执行一次，Once 只有一个方法，Once.Do()，向 Do 传入一个函数，这个函数在第一次执行 Once.Do() 的时候会被调用，以后再执行 Once.Do() 将没有任何动作，即使传入了其它的函数，也不会被执行，如果要执行其它函数，需要重新创建一个 Once 对象。

Once 可以安全的在多个例程中并行使用。

```golang
// 多次调用仅执行一次指定的函数 f
func (o *Once) Do(f func())
```

```golang
// 示例：Once
func main() {
	var once sync.Once
	onceBody := func() {
		fmt.Println("Only once")
	}
	done := make(chan bool)
	for i := 0; i < 10; i++ {
		go func() {
			once.Do(onceBody) // 多次调用只执行一次
			done <- true
		}()
	}
	for i := 0; i < 10; i++ {
		<-done
	}
}

// 输出结果：
// Only once
```
## 互斥锁

互斥锁用来保证在任一时刻，只能有一个例程访问某对象。Mutex 的初始值为解锁状态。Mutex 通常作为其它结构体的匿名字段使用，使该结构体具有 Lock 和 Unlock 方法。

Mutex 可以安全的在多个例程中并行使用。

```golang
// Locker 接口包装了基本的 Lock 和 UnLock 方法，用于加锁和解锁。
type Locker interface {
    Lock()
    Unlock()
}

// Lock 用于锁住 m，如果 m 已经被加锁，则 Lock 将被阻塞，直到 m 被解锁。
func (m *Mutex) Lock()

// Unlock 用于解锁 m，如果 m 未加锁，则该操作会引发 panic。
func (m *Mutex) Unlock()
```
```golang
// 示例：互斥锁
type SafeInt struct {
	sync.Mutex
	Num int
}

func main() {
	count := SafeInt{}
	done := make(chan bool)
	for i := 0; i < 10; i++ {
		go func(i int) {
			count.Lock() // 加锁，防止其它例程修改 count
			count.Num += i
			fmt.Print(count.Num, " ")
			count.Unlock() // 修改完毕，解锁
			done <- true
		}(i)
	}
	for i := 0; i < 10; i++ {
		<-done
	}
}

// 输出结果（不固定）：
// 2 11 14 18 23 29 36 44 45 45
```

## 读写互斥锁

RWMutex 比 Mutex 多了一个“读锁定”和“读解锁”，可以让多个例程同时读取某对象。RWMutex 的初始值为解锁状态。RWMutex 通常作为其它结构体的匿名字段使用。

Mutex 可以安全的在多个例程中并行使用。

```golang
// Lock 将 rw 设置为写锁定状态，禁止其他例程读取或写入。
func (rw *RWMutex) Lock()

// Unlock 解除 rw 的写锁定状态，如果 rw 未被写锁定，则该操作会引发 panic。
func (rw *RWMutex) Unlock()

// RLock 将 rw 设置为读锁定状态，禁止其他例程写入，但可以读取。
func (rw *RWMutex) RLock()

// Runlock 解除 rw 的读锁定状态，如果 rw 未被读锁顶，则该操作会引发 panic。
func (rw *RWMutex) RUnlock()

// RLocker 返回一个互斥锁，将 rw.RLock 和 rw.RUnlock 封装成了一个 Locker 接口。
func (rw *RWMutex) RLocker() Locker
```

## 组等待

WaitGroup 用于等待一组例程的结束。主例程在创建每个子例程的时候先调用 Add 增加等待计数，每个子例程在结束时调用 Done 减少例程计数。之后，主例程通过 Wait 方法开始等待，直到计数器归零才继续执行。

```golang
// 计数器增加 delta，delta 可以是负数。
func (wg *WaitGroup) Add(delta int)

// 计数器减少 1
func (wg *WaitGroup) Done()

// 等待直到计数器归零。如果计数器小于 0，则该操作会引发 panic。
func (wg *WaitGroup) Wait()
```

```golang
// 示例：组等待
func main() {
	wg := sync.WaitGroup{}
	wg.Add(10)
	for i := 0; i < 10; i++ {
		go func(i int) {
			defer wg.Done()
			fmt.Print(i, " ")
		}(i)
	}
	wg.Wait()
}

// 输出结果（不固定）：
// 9 3 4 5 6 7 8 0 1 2
```


## 条件等待

条件等待通过 Wait 让例程等待，通过 Signal 让一个等待的例程继续，通过 Broadcast 让所有等待的例程继续。

在 Wait 之前应当手动为 c.L 上锁，Wait 结束后手动解锁。为避免虚假唤醒，需要将 Wait 放到一个条件判断循环中。官方要求的写法如下：
```golang
c.L.Lock()
for !condition() {
    c.Wait()
}
// 执行条件满足之后的动作...
c.L.Unlock()
```
Cond 在开始使用之后，不能再被复制。

type Cond struct {
    L Locker // 在“检查条件”或“更改条件”时 L 应该锁定。
} 

// 创建一个条件等待
func NewCond(l Locker) *Cond

// Broadcast 唤醒所有等待的 Wait，建议在“更改条件”时锁定 c.L，更改完毕再解锁。
func (c *Cond) Broadcast()

// Signal 唤醒一个等待的 Wait，建议在“更改条件”时锁定 c.L，更改完毕再解锁。
func (c *Cond) Signal()

// Wait 会解锁 c.L 并进入等待状态，在被唤醒时，会重新锁定 c.L
func (c *Cond) Wait()

```golang
// 示例：条件等待
func main() {
	condition := false // 条件不满足
	var mu sync.Mutex
	cond := sync.NewCond(&mu)
	// 让例程去创造条件
	go func() {
		mu.Lock()
		condition = true // 更改条件
		cond.Signal()    // 发送通知：条件已经满足
		mu.Unlock()
	}()
	mu.Lock()
	// 检查条件是否满足，避免虚假通知，同时避免 Signal 提前于 Wait 执行。
	for !condition {
		// 等待条件满足的通知，如果收到虚假通知，则循环继续等待。
		cond.Wait() // 等待时 mu 处于解锁状态，唤醒时重新锁定。
	}
	fmt.Println("条件满足，开始后续动作...")
	mu.Unlock()
}

// 输出结果：
// 条件满足，开始后续动作...
```