---
layout: post
title: Go控制语句
date: '2020-11-16'
categories: [Go]
---

### if-else语句

```go
if condition1{

} else if condition2{
    // else if需要和大括号在同一行
} else{
    // else需要和大括号在同一行
}

```

#### 赋值if语句

```go
// assignment-statement语句会首先执行
if assignment-statement; condition{

}
```

- 在Go语言的哲学中，最好尽量避免代码中不必要的分支（比如if语句成立的时候应当直接返回，避免else分支）和缩进，同样还要尽早返回

```go
// 一般写法
func func1(){
    if num := 10; num % 2 == 0{
        fmt.Println(num, "is even")
    } else{
        fmt.Println(num, "is odd")
    }
}

// 在Go中通常写作
func func2(){
    num := 10
    if num % 2 == 0{
        fmt.Println(num, "is even")
        // 尽早返回
        return
    }
    // 避免不必要的分支
    fmt.Println(num, "is odd")
}
```

### for语句

- 在Go语言中的循环语句没有`while`关键字，只有`for`

- for循环可以嵌套

```go
for initialisation; condition; post{
    // 使用break关键字退出循环
    break

    // 使用continue进行下次循环
    continue

}
```

- 标签（label）

```go
func main(){
outer:
    for i := 0; i < 3; i++{
        for j := 1; j < 4; j++{
            fmt.Printf("i = %d, j = %d\n",i,j)
            if i == j{
                break outer
            }
        }
    }
}
```

### switch语句

- switch是条件语句，可以将其视为替换复杂if语句的惯用方式

- 不允许重复`case`

```go
func main(){
    switch expression{
    case 1:
        fmt.Println("1")
    // ...
    default:
        fmt.Println("default")
    }
}
```

- 允许在同一个`case`内有多个表达式

- 可以省略switch表达式，此时会判断case为true或者false

```go
func main(){
    num := 75
    switch{
    case num >= 0 && num <= 50:
        fmt.Println("num >= 0 && num <= 50")
    case num >= 51 && num <= 100:
        fmt.Println("num >= 51 && num <= 100")
    }
}
```

- `fallthrough`关键字：作为一个`case`的最后一条语句，符合该case时不退出，执行下一个case内的语句
 - 注意该关键字不能存在于switch中的最后一个case中
 - 即使下一个case的判断为false也会执行下一个case内部的语句

- `break`关键字：跳出switch





