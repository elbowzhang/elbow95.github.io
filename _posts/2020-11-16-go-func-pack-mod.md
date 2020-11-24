---
layout: post
title: Go函数、包、模块
date: '2020-11-16'
categories: [Go]
---

### Go 函数

- 函数为一段实现特定功能的代码

```go
func functionname(parametername type) returntype{
    // function body
}
```

- 形式参数可以多个如`(para1, para2 int)`

- 返回参数可以多个如`(int, int)`

- 有名返回值`(retval1, retval2 int)`
```go
func rectProps(length, width float64)(area, perimeter float64){
    area = length * width
    perimeter = (length + width) * 2
    // 此时可以隐式返回
    return
}
```

#### 空白标识符(Blank Identifier)`_`

- 对于上文中的`rectProps`函数，可以在调用时使用`_`忽略返回值`perimeter`，比如`area, _ := rectProps(10.8, 5.6)`


### Go包(Packages)

- 由于在单个文件中实现所有源码不具有扩展性，这时使用包会很有帮助

- 包用于组织Go源代码，以提高可重用性和可读性。包是位于同一个目录下的所有Go源文件的集合。包提供了代码隔离，因此可以很容易的维护Go项目。

#### main函数和main包

- 每个可执行的Go程序都需要包含main函数，该函数是可执行程序的入口，main函数需要定义在main包中。

- `package packagename`表示该源文件隶属于包`packagename`，需要写在每个go源文件的第一行

#### init函数

- Go中的每个包可以包含一个`init`函数，该函数不能有任何参数和返回值，同时不能在源码中进行显式调用。它会在包初始化的时候自动调用。
 - 该函数用于进行初始化工作，也可以用于在执行之前验证程序的正确性。

- 包的初始化顺序为：
 - 初始化包级别的变量
 - 调用`init`函数，同一个包可以有多个`init`函数，调用顺序为他们呈现给编译期的顺序

- 如果一个包中导入了另外的包，被导入的包会首先初始化

- 同一个包只会被初始化一次，即使被导入多次

### Go模块(Module)

- Go模块是Go包的集合，自定义包的导入路径是从Go模块的名称派生的，即`import modulename/packagename`。

- 程序所有使用的第三方包将和版本一起出现在`go.mod`文件中，当创建一个新模块时，将创建该文件。

- 属于包的源文件应该放在各自的单独文件夹内，在Go中，使用与包相同的名称来命名该文件夹。

- 如果向在包外访问包内的函数，需要大写该函数名的首字母。


