---
layout: post
title: First class function
date: 2020-11-23
categories: [Go]
---

### First Clas Function

- 一个支持first class function的语言允许将函数赋值给变量，作为参数传递给其他函数，还可以从函数返回

#### 匿名函数

- 可以将一个匿名函数赋值给一个变量，这些函数称为匿名函数，因为他们没有名称

```go
a := func(){
    fmt.Println("annoymous functions")
}
```

- 也可以像普通函数一样将参数传递给匿名函数

```go
func(n string){
    fmt.Println("welcome", n)
}("Gophers")
```

- `type add func(a int, b int) int` 用户定义的函数类型

#### 高阶函数

- 高阶函数的定义为至少满足以下一个条件的函数：
 - 接收一个或者多个函数作为参数输入
 - 返回一个函数

```go
// 将一个函数作为参数输入
func simple(a func(a, b int) int){
    fmt.Println(a(60,1))
}

// 返回一个函数
func simple() func(a, b int) int{
    f := func(a, b int) int{
        return a + b
    }
    return f
}
```


#### 闭包Closures

- 闭包是一个特殊的匿名函数，允许访问定义在函数体之外的变量。

```go
func main(){
    a := 5
    func(){
        // 这个匿名函数可以访问外部的a，这是一个闭包
        fmt.Println("a=",a)
    }()
}
```

- 每一个闭包都绑定到了其自己的周围变量

```go
func appendStr() func(string) string{
    t := "hello"
    // 闭包，绑定了变量t
    c := func(b string) string{
        t = t + " " + b
        return t
    }
    return c
}
```

