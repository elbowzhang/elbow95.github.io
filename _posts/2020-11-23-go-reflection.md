---
layout: post
title: Go中的反射
date: '2020-11-23'
categories: [Go]
---


### Reflection

- 反射机制是指程序在运行的时候检查其变量和值并找到其类型的能力。


```go
// interface{}的值在运行时确定
func createQuery(q interface{}) string{

}
```

#### 反射package

- 反射package实现了运行时反射，有助于识别底层的具体类型和`interface{}`的值。

- `interface{}`的具体类型由`reflect.Type`表示，底层值由`reflect.Value`表示，有两个函数分别返回他们，这两种类型时创建查询生成器的基础。

- `Type`表示`interface{}`的实际类型，`Kind`表示类型

```
o := order{
    ordId:  456,
    customerId: 56,
}
Type: main.order
Kind: struct
Value: {456,56}
```

- `NumField()`方法返回结构中的字段数，`Field()`方法返回第i个字段的`reflect.Value`

- `Int()`方法和`String()`方法将相应的`reflect.Value`值转义为int64和string

#### 该不该使用反射

- 在Go语言中，反射是一种非常强大且先进的概念，应该谨慎使用，使用反射编写清晰和可维护的代码非常困难，应该尽可能的避免使用它，并在绝对必要的时候使用。


