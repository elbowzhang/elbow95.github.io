---
layout: post
title: Go中的Map和String
date: '2020-11-17'
categories: [Go]
---

### Map

- 创建方式：`make(map[key type]value type)`

```go
employeeSalary := make(map[string]int)
employeeSalary["steve"] = 12000

//等价于
employeeSalary := map[string]int{
    "steve": 12000,
}
```

- 和切片一样，map也是引用类型，当将map赋值给一个新值时，他们都指向同一个内部数据结构。

- 判断相等？map只可以和nil进行`==`判断

#### 零值map nil

- `var employeeSalary map[string]int`，当尝试向`employeeSalary`添加元素时会报错

#### 获取元素

- `value := map[key]`获取指定`key`的`value`

#### 检查key是否存在

- `value, ok := map[key]`

#### 遍历map

- `key, value := range map`：对于使用range遍历map来说，从映射中检索值的顺序不能保证每次执行遍历都相同，顺序也和添加元素的顺序无关

#### 删除元素

- `delete(map, key)`


### String

- 一个string是Go语言中的一部分字节，可以通过将一组字符括在双引号`""`来创建字符串

- `%s`为打印string的占位符，`%x`为打印string的16进制的占位符。

- `%c`为打印单字符的占位符，但是只能访问ascii编码的字符串，访问UTF-8编码字符串时可以能会发生错误。

- `rune`是一个go内置类型，是`int32`的别名，可以替换单纯使用`%c`会出错的问题

```go
func printChars(s string){
    runes := []rune(s)
    for i := 0; i < len(runes); i++{
        fmt.Printf("%c ", runes[i])
    }
}
```

- `rune`可以使用范围range遍历：`index, rune := range s`

- 使用`string()`可以从一组字节或者一组`rune`创建出字符串

- `RuneCountInString(s string)(n int)`函数可以返回字符串的字符长度

- `len(s)`函数可以返回字符串的字节长度

- `==`判断符可以判断两个字符串是否相等

- `+`符号可以连接字符串：`res := string1 + " " + string2`

- `Sprintf(...)`函数同样可以实现字符串连接的功能：`res := fmt.Sprintf("%s %s", string1, string2)`

- Go中的字符串在创建之后，无法更改；可以将字符串转换为一组Rune，更改完成之后再转换为新的字符串

```go
func mutate(s []rune) string{
    s[0] = 'a'
    return string(s)
}
```






