---
layout: post
title: Learn Go in Y Mins
date: '2020-12-20'
categories: [Go]
---


```go
// 单行注释
/* 多行
   注释 */

/* 构建标记(build tags)是以//+build开头的行注释
   并可以通过go build -tags="foo bar"命令执行
   构建标记放置在文件附近或者顶部的package子句之前
   然后是空白行或者其他行注释。 */

// +build prod, dev, test

// 每个源文件都以package子句开头
// Main 是一个特殊名称，用于生命可执行文件而不是库
package main

// Import 声明该文件总引用的库包
import (
	"fmt" // Go标准库中的包
	"io/ioutil"
	m "math" // 具有别名m的数学库
	"net/http"
	"os"
	"strconv"
	// 一个网络服务器
	// 操作系统的功能，例如使用文件系统
)

// main 是一个函数定义，是一个特殊的函数，是一个可执行程序的入口
func main() {
	// Println将一行输出到stdout
	// 该函数在fmt包中
	fmt.Println("Hello world!")

	// 调用此程序包中的另一个函数
	beyondHello()
}

// 函数的括号中是参数
// 如果没有参数，同样需要括号
func beyondHello() {
	var x int // 变量声明，变量在使用之前必须声明
	x = 3     // 变量赋值
	// 可以使用短声明:=，实现推断类型，声明并赋值
	y := 4
	// 返回两个值的函数
	sum, prod := learnMultiple(x, y)
	fmt.Println("sum:", sum, "prod:", prod)
	learnTypes()
}

/* 多行注释
一个函数可以有多个参数和多个返回值
x,y为参数，sum,prod为返回值
*/
func learnMultiple(x, y int) (sum, prod int) {
	return x + y, x * y
}

// 一些内置类型和字面值
func learnTypes() {
	// 短声明通常可以实现你的想法
	str := "Learn Go!" // string类型
	s2 := `A "raw" string literal
	can include line breaks.` // string类型
	// 非ASCII字面值，以UTF-8编码
	g := 'Σ'    // rune类型，int32的别名
	f := 3.1415 // float64类型
	c := 3 + 4i // complex128类型，内部使用两个float64实现

	// 带有初始化的var语法
	var u uint = 7 // 无符号，但实现与int一样
	var pi float32 = 22. / 7

	// 带有短声明的转换语法
	n := byte('\n') // byte是uint8的别名

	// 数组的大小在编译时确定
	var a4 [4]int                    // 一个包含4个int的数组，并初始化为0
	a5 := [...]int{3, 1, 5, 10, 100} // 初始化大小为5的数组

	// 数组具有值语义，操作和值相同
	a4Cpy := a4                    // a4_cpy是a4的副本，两个独立的实例
	a4Cpy[0] = 25                  // 只有a4_cpy改变了
	fmt.Println(a4Cpy[0] == a4[0]) // false

	// 切片具有动态大小。数组和切片各有优点
	// 但是切片的用例更加普遍
	s3 := []int{4, 5, 9}    // 相较于a5，没有省略号
	s4 := make([]int, 4)    // 分配4个整数的切片，并初始化为0
	var d2 [][]float64      // 仅声明，但未分配任何内容
	bs := []byte("a slice") // 类型转换语法

	// 切片（包括map和channel）都有引用语义
	s3Cpy := s3                    // 两个值指向同一个实例
	s3Cpy[0] = 0                   // 两个同事更新
	fmt.Println(s3Cpy[0] == s3[0]) // true

	// 由于切片是动态的，因此切片可以按需添加
	// 使用内置函数append()向切片添加元素
	// 第一个参数是要增加的切片，通常情况下就地更新
	s := []int{1, 2, 3}    // 一个长度为3的切片
	s = append(s, 4, 5, 6) // 添加3个元素，切片长度变为6
	fmt.Println(s)         // 切片更新为[1 2 3 4 5 6]

	// 在一个切片中追加另一个切片，而不是一个元素列表
	// 可以将引用传递给切片或切片字面值并带有省略号
	// 这意味着将该切片拆开并将其元素依次添加到切片中
	s = append(s, []int{7, 8, 9}...) // 第二个参数是一个切片字面值
	fmt.Println(s)                   // 切片更新为[1 2 3 4 5 6 7 8 9]

	p, q := learnMemory() // 声明p,q为指向int类型的指针
	fmt.Println(*p, *q)   // *解指针，打印两个值

	// map是一种可动态增长的关联数组类型，类似于hash或字典
	m := map[string]int{"three": 3, "four": 4}
	m["one"] = 1

	// 解决未使用变量错误
	_, _, _, _, _, _, _, _, _, _ = str, s2, g, f, u, pi, n, a5, s4, bs

	// 通常使用_来忽略函数的返回值之一
	file, _ := os.Create("output.txt")
	fmt.Fprint(file, "This is how you write to a file, by the way")
	file.Close()

	fmt.Println(s, c, a4, s3, d2, m)

	learnFlowControl()
}

// Go中的函数可以有命名返回值，在函数声明行中为返回的类型赋值一个名称
// 可以从函数中的多个点返回，并且只使用return关键字，而不使用其他任何东西
func learnNamedReturns(x, y int) (z int) {
	z = x * y
	return // z在这里是隐式的，因为在前面进行了命名
}

// Go具有垃圾回收机制，它有指针但是没有指针语法
// 有nil空指针，但是没有指针的自增和自减操作
// 与C/C++不同，或者和返回本地变量的地址也是安全的
func learnMemory() (p, q *int) {
	// p和q是指向int类型的指针
	p = new(int) // 内置函数new分配内存
	// 分配的int切片初始化为0，并且p不再是nil
	s := make([]int, 20) // 将20个整数分配为一个内存块
	s[3] = 7
	// 声明一个局部变量
	r := -2
	// 使用&符号返回对象的地址
	return &s[3], &r
}

func expensiveComputation() float64 {
	return m.Exp(10)
}

func learnFlowControl() {
	// if语句需要大括号，不需要小括号
	if true {
		fmt.Println("told ya")
	}
	// 格式化if-else语句
	if false {
		// Pout
	} else {
		// Gloat
	}

	// 使用switch实现链式if语句
	x := 42.0
	switch x {
	case 0:
	case 1, 2: // 可以在一个case中包含多个匹配
	case 42: // case不会向下落，但是可以使用fallthrough关键字
	case 43: // 不会达到这里
	default: // default语句可选
	}

	// switch允许判断类型而不仅仅是值
	var data interface{}
	data = ""
	switch c := data.(type) {
	case string:
		fmt.Println(c, "is a string")
	case int64:
		fmt.Printf("%d is an int64\n", c)
	default:
		// all other cases
	}

	// for语句不需要小括号，在for和if内部声明的变量是其内部的局部变量
	for x := 0; x < 3; x++ {
		fmt.Println("iteration", x)
	}
	// 这里的x还是上文的24
	// for是Go中唯一的循环语句
	for {
		break
		// continue
	}

	// 可以使用range遍历数组、切片、字符串、map或者通道
	// range返回一个（通道）或两个值（其他）
	for key, value := range map[string]int{"one": 1, "two": 2, "three": 3} {
		// 打印每一个组合
		fmt.Printf("key=%s, value=%d\n", key, value)
	}

	// 如果只需要值不需要键值
	for _, name := range []string{"Bob", "Bill", "Joe"} {
		fmt.Printf("Hello, %s\n", name)
	}

	// 与for相似，在if语句中使用:=意味着声明和赋值
	if y := expensiveComputation(); y > x {
		x = y
	}

	// 函数字面值是一个闭包
	xBig := func() bool {
		return x > 10000 // x在上面声明了
	}
	x = 99999
	fmt.Println("xBig:", xBig()) // true
	x = 1.3e3
	fmt.Println("xBig:", xBig()) // false

	// 函数字面值可以称为内联，并可以充当一个函数的参数
	// 1，函数字面值立即调用()
	// 2，结果类型与参数的预期类型匹配
	fmt.Println("Add + double two numbers: ",
		func(a, b int) int {
			return (a + b) * 2
		}(10, 2))

	// 跳转
	goto love
love:
	learnFunctionFactory()
	learnDefer()
	learnInterfaces()
}

func learnFunctionFactory() {
	// 下面的两个是相同的，但是第二个更实用
	fmt.Println(sentenceFactory("summer")("A beautiful", "day!"))

	d := sentenceFactory("summer")
	fmt.Println(d("A beautiful", "day!"))
	fmt.Println(d("A lazy", "afternoon!"))
}

// 装饰器在其他语言中很常见，在Go中也可以处理接收参数的函数字面值
func sentenceFactory(mystring string) func(before, after string) string {
	return func(before, after string) string {
		return fmt.Sprintf("%s %s %s", before, mystring, after)
	}
}

func learnDefer() (ok bool) {
	// defer语句将函数调用推送到一个列表上，这个列表上的调用在函数返回后执行
	defer fmt.Println("deferred statements execute in reverse (LIFO) order.")
	defer fmt.Println("\nThis line is being printed first because")
	// defer语句通常用于关闭文件
	return true
}

// Stringer 将定义为接口类型
type Stringer interface {
	String() string
}

// struct
type pair struct {
	x, y int
}

// 在类型pair上定义一个方法, pair实现了Stringer因为实现了接口中的所有方法
func (p pair) String() string {
	// p被称为接收者
	return fmt.Sprintf("(%d, %d)", p.x, p.y)
}

func learnInterfaces() {
	// 大括号语法是一个"struct字面值",:=语法声明p并将其初始化
	p := pair{3, 4}
	fmt.Println(p.String()) // 调用String方法
	var i Stringer
	i = p // pair实现了Stringer接口，因此合法
	// 调用String类型的i的String方法，输出与上面相同
	fmt.Println(i.String())
	// fmt包中的函数调用String方法来请求对象本身的可打印表示
	fmt.Println(p)
	fmt.Println(i)

	learnVariadicParams("great", "learning", "here!")
}

func learnVariadicParams(myStrings ...interface{}) {
	// 迭代可变参数的每个值
	// 下划线表示忽略数组的index参数
	for _, param := range myStrings {
		fmt.Println("param:", param)
	}

	// 传递可变值
	fmt.Println("params:", fmt.Sprintln(myStrings...))

	learnErrorHandling()
}

func learnErrorHandling() {
	// "ok"常被用来表示是否正常
	m := map[int]string{3: "three", 4: "four"}
	if x, ok := m[1]; !ok {
		// 由于1不存在与map中，所有ok为false
		fmt.Println("no one there")
	} else {
		// 如果存在，则x表示值
		fmt.Print(x)
	}

	// error值传达的不仅仅是是否ok，还包括关于这个错误的更多信息
	if _, err := strconv.Atoi("non-int"); err != nil {
		// 打印'strconv.ParseInt: parsing "non-int": invalid syntax'
		fmt.Println(err)
	}

	//
	learnConcurrency()
}

// c是一个通道，一个进程安全的传输对象
func inc(i int, c chan int) {
	// 当一个通道在左边时， <- 表示发送操作符
	c <- i + 1
}

// 使用inc来并行增加某些值
func learnConcurrency() {
	// 与前面创建切片的make函数相似，make函数可以分配和初始化sliece,map和channel
	c := make(chan int)
	// 开始三个并行协程，数字将并行增加
	go inc(0, c)
	go inc(10, c)
	go inc(-805, c)
	// 通道在左边时， <- 为接收操作符
	fmt.Println(<-c, <-c, <-c)

	cs := make(chan string)       // 一个字符串通道
	ccs := make(chan chan string) // 一个字符串通道的通道
	go func() { c <- 84 }()
	go func() { cs <- "wordy" }()

	// select语法和swich相似，但是每个case中包含一个通道操作
	// 每次从准备好通道中选择一个
	select {
	case i := <-c: // 接收到的值可以传递给一个变量
		fmt.Printf("it's a %T", i)
	case <-cs: // 接收到的值也可以忽略掉
		fmt.Println("it's a string")
	case <-ccs: // 空通道
		fmt.Println("didn't happen")
	}
	// 两个协程启动后，可以从c和cs通道获取值，另一个ccs通道会一直阻塞
	//
	learnWebProgramming()
}

// http包函数，启动一个网络服务器
func learnWebProgramming() {
	// ListenAndServe的第一个参数是监听的TCP的地址
	// 第二个参数是一个接口，特指http.Handler
	go func() {
		err := http.ListenAndServe(":8080", pair{})
		fmt.Println(err)
	}()

	requestServer()
}

// 实现pair的唯一方法，ServeHttp
func (p pair) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	// 使用http.ResponseWriter 方法提供数据
	w.Write([]byte("You learned Go in Y minites!"))
}

func requestServer() {
	resp, err := http.Get("http://localhost:8080")
	fmt.Println(err)
	defer resp.Body.Close()
	body, err := ioutil.ReadAll(resp.Body)
	fmt.Printf("\nWebServer said: '%s'", string(body))
}
```