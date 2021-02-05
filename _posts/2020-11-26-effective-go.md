---
layout: post
title: Effective Go
date: '2020-11-25'
categories: [Go]
---

### 格式化问题

- Go中可以使用`gofmt`命令对Go文件进行格式化处理，并且保留注释同时在必要时候重新格式化注释

- 缩进：`gofmt`使用制表符`tab`进行缩进

- 行长：Go没有行长限制，但不宜过长影响阅读


### 注释

- Go使用`C-style`风格注释代码`//`，`/*...*/`

- 每个包都应当在包名字前有一个包注释，对于多文件包，包注释仅需要出现在一个文件中，任何一个都可以。包注释应当介绍包，并提供与包整体有关的信息。

- 在包中，顶级声明之前的任何注释都将用作该声明的doc注释。程序中的每个导出（大写）名称都应当带有文档注释

- Doc注释最好作为完整的句子使用，第一句应该是单句摘要并且以声明的名称开头

```go
// Compile parses a regular expression and returns, if successful,
// a Regexp that can be used to match against text.
func Compile(str string) (*Regexp, error){}
```

- 如果每个文档注释都以其描述的内容名称开头，则可以使用go工具中的`doc`命令通过`grep`运行输出，例如忘记了`Compile`函数名称但是正在寻找解析函数可以使用

```cmd
// 使用以下命令可以查找含有单词parse的所有注释
go doc -all regexp | grep -i parse
```

### 名称

- 包内名称的外部可见性取决于其首字母是否为大写。

- 当一个包被导入之后，包名就会成为内容的访问器

- 包的名称应当简洁明了并且易于理解，按照惯例，包应当以小写单个单词来命名，并且不应当使用下划线或者驼峰记法。


