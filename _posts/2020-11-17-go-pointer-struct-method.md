---
layout: post
title: Go中的指针、结构体和方法
date: '2020-11-17'
categories: [Go]
---

### Pointer

- `*T`表示指向类型T的指针，`&`用于获取变量的地址

- `var b *int`，b未初始化，为nil值

- `pointer := new(type)`用于创建指针，并开辟一块新内存，初始化为默认值

- `*pointer`：解引用

- 可以作为参数传递或者返回

- 可以返回局部变量的指针，这是因为Go语言会自动进行转义分析，并在地址离开局部范围时自动在堆上分配新内存。

- 不要将指向数组的指针作为函数参数传递，惯用做法是使用切片传递

- Go语言不支持指针运算


### struct

- 用户定义的类型，表示字段的集合，可以用于将数据分组为一个单位而不是将每个数据都做为单独的值。

```go
type Employee struct{
    fristname   string
    lastname    string
    age         int
}

emp1 := Employee{
    firstname:  "sum"
    lastname:   "anderson"
    age:        15
}

emp1 := Emplyee{"sum", "anderson", 15}
```

- 匿名结构体：可以声明结构而不创建新的数据类型

```go
emp1 := struct{
    firstname   string
    lastname    string
    age         int
}{
    firstname:  "sum"
    lastname:   "anderson"
    age:        15
}
```

- 使用`.`运算符访问结构体对象内的字段

- 定义一个结构体对象但是不初始化的对象为零值（nil）

- 可以使用指针指向一个结构体`(*emp1).firstname  相当于  emp1.firstname`

- 匿名字段：使用仅包含类型而不包含字段名称的字段来创建的结构

```go
type Person struct{
    string
    int
}
```

- 即使匿名字段没有显式名称，但在默认情况下，匿名字段的名称是其类型的名称

- 结构体可以嵌套

- Promoted Fields(提升字段)，属于结构体中的匿名结构体中的字段称为提升字段，可以像访问结构体字段一样访问匿名结构体字段

- 结构体内小写字母开头的字段无法被外部包访问到

- 结构体是值类型，如果每个字段都是可以比较的，则他们之间也是可以比较的。如果两个结构体变量的对应字段相等，则可以认为他们相等。


### Method方法

- 方法是具有特殊接收类型的函数，该接收类型可以是结构类型或者非结构类型

```go
// 声明方式
func (t Type) methodname(parameter list){

}

// 调用方法
t.methodname(...)

```

- Go语言拥有方法的原因：
 - Go不是一个单纯的OOP语言，没有类的概念，方法允许对类似于类的类型的行为进行逻辑分组。
 - 可以在不同类型上定义具有相同名称的方法，但是不允许具有相同名称的函数

#### 指针方法和值方法

- 指针方法和值方法之间的区别在于，指针接收内部作的更改会影响到调用者，值接收不会影响

```go
// 值接收形式
func (e Employee) changename(newname string){
}

// 指针接收形式
func (e *Employee) changeage(newage int){
}

// 值接收内部更改无法影响到调用者
e.changename("")

// 指针接收内部更改会影响到调用者
(&e).changeage("")
```

- 值方法可以同时接收指针或者值，函数不可以

- 指针方法也可以同时接收指针或者值，函数也不可以




