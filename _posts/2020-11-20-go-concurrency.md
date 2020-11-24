---
layout: post
title: Go语言并发编程
date: '2020-11-20'
categories: [Go]
---

### Go并发编程

- GO是一个并发语言，而不是一个并行语言。

- 并发不是真正的同时运行，是指在单处理器上不同时间片处理不同任务，实现观感上的同时运行。并行是指真正的同时运行。

<div class="image-wrapper" style="text-align: center">
<img src="..\assets\post\2020-11-20\concurrency-parallelism-copy.png" alt="img" style="zoom:40%;" />
</div>

- 并行并不总是可以缩短执行时间，这是因为并行运行的组件可能必须相互通信。但在并发系统中，这种通信开销很低。


### 协程(Goroutines)

- 协程是与其他函数或者方法同时运行的函数或者方法。

- 协程可以认为是轻量级的线程，与线程相比，创建协程的成本更小，因此Go程序中同时运行数千个协程是很常见的。

#### 相对线程的优势

- 与线程相比，协程的代价更小，堆栈空间通常只有几kb，并且堆栈可以根据程序的需要进行扩展或者收缩；线程则必须指定堆栈大小并且固定堆栈大小

- 协程可以被多路复用到更少的OS线程，在具有数千个协程的程序中，可能只有一个线程。

- 协程使用通道(Channels)进行通信。通过设计通道可以防止在使用协程访问共享内存时发生争用情况。


#### 启动一个协程

- 当启动一个新的协程时，协程调用会立即返回，执行下一行代码，并且协程中的所有返回值都会被忽略。与函数不同的是，控制器并不会等待协程执行完成之后返回。

- 只有主协程正在运行的时候，其他协程才能运行，如果主协程终止，则程序就会终止。

#### 启动多个协程

- 使用`go ...`启动协程


### 协程通道(Channels)

- 可以将通道视为使用协程进行通信的管道。与水在管道中从一端流到另一端的方式类似，可以使用通道从一端发送数据并从另一端接收数据。

- 发送者可以关闭通道，以通知接收者该通道将不再发送任何数据。

- 接收者可以从通道接收数据时使用附加变量以检查通道是否已经关闭`v, ok := <- ch`


#### 声明通道

- `chan T`是一个类型`T`的通道

- 通道的零值是`nil`，`nil`通道没有任何用处，因此必须使用类似于地图和切片的make来定义通道。

#### 通道发送和接收

```go
// 从通道a中读取数据
data := <- a

// 向通道a写入数据
a <- data
```

- 箭头相对于通道的方向指定是向通道发送还是接收数据。

- 默认情况下，向通道发送和接收数据是阻塞的，当数据发送到通道时，发送语句会被阻塞，直到其他协程将数据从通道中读取出来。
 - 通道的这个属性可以帮助协程有效通信，而不需要使用其他编程语言中很常见的显式锁或者条件变量。

```go
func hello(done chan bool){
    fmt.Println("hwllo world")
    done <- true
}

func main(){
    done := make(chan bool)
    go hello(done)
    // 在某些协程将数据写入通道之前，该行读取通道代码会被阻塞
    <- done
    fmt.Println("main function")
}
```

- 使用通道可以解决竞态产生

```go
func increment(wg *sync.WaitGroup, ch chan bool){
    // 当缓冲通道里还有空间时可以进入临界区
    ch <- true
    x = x + 1
    // 从缓冲通道读出一个数据
    <- ch
    wg.Done()
}
// 创建一个容量为1的缓冲通道
ch := make(chan bool, 1)
```

#### 死锁

- 如果协程正在通过通道发送数据，应该有另一个协程正在接收数据，否则程序将在运行时因为死锁而panic

```go
func main(){
    ch := make(chan int)
    ch <- 5
}
```


#### 单向通道

- 单向通道只能发送或者接收数据的通道

- `make(chan<- int)`创建`send-only channel`，只能发送的通道，但是无法读取

- 应用场景：只有在限制使用的时候使用，可以将双向通道转换为仅发送或仅接收通道


### 缓冲通道和工作池

#### 缓冲通道(buffered channels)

- 创建带有缓冲区的通道，仅当缓冲区已满时才阻止向缓冲通道发送数据，同样，仅当缓冲区为空时才会阻止从缓冲通道接收数据。

- 缓冲通道创建方式：`ch := make(chan type, capacity)`，`capacity`的容量应该大于0，以使通道具有缓冲区。默认情况下，未缓冲通道的容量为0.

- 向缓冲通道中写入超出缓冲通道容量的数据时，会造成`DeadLock`

- 缓冲通道的`length`是指通道中当前拥有的数据，`capacity`是指通道容量


### 工作池(Worker Pool)

#### WaitGroup

- WaitGroup用于等待一个协程集合完成执行。直到所有的协程执行完毕，程序才会继续。

```go
// 必须传递wg指针给协程，否则无法修改原wg数据导致主协程无法获得当前协程状态
func process(i int, wg *sync.WaitGroup){
    time.Sleep(2 * time.Second)
    // 协程执行完毕之后调用Done方法减少协程数量
    wg.Done()
}

func main(){
    no := 3
    // 创建一个WaitGroup的零值
    var wg sync.WaitGroup
    for i := 0; i < no; i++{
        // WaitGroup的工作方式是使用计数器，使用Add方法增加协程数量
        wg.Add(1)
        go Process(i, &wg)
    }
    wg.Wait()
}
```

#### 工作池实现(线程池)

- 通常，工作池是一组等待分配人物的线程集合，一旦完成分配的任务，便可以再次用于下一个任务。

- 可以使用缓冲通道实现工作池。

- 工作池的主要功能为：
 - 创建协程池，这些协程在等待分配作业的输入缓冲通道上进行监听
 - 将作业添加到输入缓冲通道
 - 作业完成之后将结果写入输出缓冲通道
 - 从输出缓冲通道读取和打印结果

```go
package main

import{
    "fmt"
    "math/rand"
    "sync"
    "time"
}

type Job struct{
    id          int
    randomno    int
}

type Result struct{
    job         Job
    sumofdigits int
}

var jobs = make(chan Job, 10)
var results = make(chan Result, 10)

// 作业内容，计算每个数字的各位和
func digits(number int) int{
    sum := 0
    no := number
    for no != 0{
        digit := no % 10
        sum += digit
        no /= 10
    }
    return sum
}

// 工作协程，遍历作业缓冲通道
func worker(wg *sync.WaitGroup){
    for job := range jobs{
        output := Result{job, digits(job.randomno)}
        result <- output
    }
    wg.Done()
}

// 创建工作池协程
func createWorkerPool(noOfWorkers int){
    var wg sync.WaitGroup
    for i := 0; i < noOfWorkers; i++ {
        wg.Add(1)
        go worker(&wg)
    }
    wg.Wait()
    close(results)
}

// 分配作业，当分配完成之后关闭jobs缓冲通道
func allocate(noOfJobs int){
    for i := 0; i < noOfJobs; i++ {
        randomno := rand.Intn(999)
        job := Job{i, randomno}
        jobs <- job
    }
    close(jobs)
}

// 打印计算结果，从缓冲通道results中读取数据
func result(done chan bool){
    for result := range results{
        fmt.Printf("Job id %d, intput random no %d, sum of digits %d\n", result.job.id, result.job.randomno, result.sumofdigits)
    }
    done <- true
}

func main(){
    // 作业的数量    
    noOfJobs := 100
    // 启动分配作业协程
    go allocate(noOfJobs)

    // 启动打印计算结果协程
    done := make(chan bool)
    go result(done)

    // worker的数量
    noOfWorkers := 10
    // 创建工作池
    createWorkerPool(noOfWorkers)
    <- done
}

```

### select多路复用

- 用于从多个发送/接收通道操作中进行选择。select语句在发送/接收操作之一准备就绪之前将阻塞。

- 如果多个操作同时准备好了，则随即选择其中的一个，语法和switch相似，不同之处在于每个case语句都是通道操作。

- select的实际用法：选择最快的返回

- `default case`：用于当其他情况都没有准备就绪时，执行select语句的默认情况。通常用于防止select语句被阻塞。

- `deadLock`：`default case`可以解决`deadlock`

- 随机选择：当多个case同时准备好的时候，此时进行随即select


### Mutex互斥量

- 临界区(Critical section)：当程序并发运行的时候，修改共享资源部分的代码不应该被多个协程访问到，这段部分代码称为临界区。

- Mutex互斥量提供一种锁定机制，以确保在任何时间点只有一个协程正在运行代码的关键部分，以防止发生竞态。

- Mutex有两种方法，即锁定和解锁，在调用Lock和Unlock之间的任何代码都只能仅由一个协程执行，从而避免竞态发生。

- 如果一个协程已经持有该锁，并且如果有新的协程试图获取锁，则新的协程会被阻止，直到互斥锁解锁为止。


#### 通道和互斥量解决竞态

- 当协程需要彼此通信时使用通道解决竞态，而当只有一个协程应该访问代码的临界区时则使用互斥锁



