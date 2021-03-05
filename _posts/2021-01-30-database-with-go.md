---
layout: post
title: 'Go访问数据库'
date: '2021-01-30'
categories: [Go]
---

## 访问数据库

- 对于许多Web应用程序来说，数据库都是核心所在，数据库可以用来存储想要查询或者修改的所有信息。

- Go中没有内置的驱动支持任何的数据库，但是Go定义了`database/sql`接口，用户可以基于驱动接口开发相应数据库的驱动

## database/sql接口

- Go没有提供数据库驱动，而是为开发数据库驱动定义了一些标准接口，开发者可以根据定义的接口来开发相应的数据库驱动。这样在进行数据库迁移的时候，不需要进行任何修改。

### sql.Register

- 在`database/sql`中定义，该函数用来注册数据库驱动，当第三方开发者开发数据库驱动时，都会实现自身的`init`函数，在`init`函数中调用`sql.Register(name string, driver driver.Driver)`函数完成驱动的注册。
```go
// go-sqlite3驱动
func init(){
    sql.Register("sqlite3", &SQLiteDriver{})
}
// 在database/sql内部通过一个map来存储用户定义的相应驱动
// 因此通过该注册函数可以同时注册多个数据库驱动，只要不重复
var drivers = make(map[string]driver.Driver)
drivers[name] = driver
```

### driver.Driver

- Driver是一个数据库驱动的接口，定义了一个method:`Open(name string)`，该方法返回一个数据库的`Conn`接口
```go
type Driver interface{
    Open(name string) (Conn, error)
}
```
- 返回的`Conn`只能用来进行一次goroutine的操作，也就是无法将该`Conn`应用于多个goroutine
```go
// 以下代码可能会使Go不知道某个操作是由哪个goroutine发起的，从而导致数据混乱，进而产生错误
go goroutineA(Conn)
go goroutineB(Conn)
```

- 第三方驱动都会定义这个函数，他会解析name参数来获取相关数据库的连接信息，解析完成后，使用该信息初始化一个Conn并返回它。

### diver.Conn

- Conn是一个数据库连接的接口定义，定义了多个方法，并且一个Conn只能应用在一个goroutine里
```go
type Conn interface{
    // Prepare函数返回与当前连接相关的执行sql语句的准备状态，可以进行查询、删除等操作
    Prepare(query string) (Stmt, error)
    // Close函数关闭当前的连接，执行释放连接拥有的资源等清理工作。因为驱动实现了`database/sql`里建议的`conn poll`，所以不同再实现缓存conn之类的内容
    Close() error
    // Begin函数返回一个代表事务处理的Tx，通过它可以进行查询、更新等操作，或者对事物进行回滚、递交等
    Begin() (Tx, error)
}
```

### driver.Stmt

- Stmt是一种准备好的状态，和Conn相关联，而且只能应用于一个goroutine中，不能应用于多个goroutine
```go
type Stmt interface{
    // Close函数关闭当前的连接状态，但是如果当前正在执行query，query还是有效，并返回rows数据
    Close() error
    // NumInput函数返回当前预留参数的个数，当返回>=0时数据库驱动就会智能检查调用者的参数。当数据库驱动包不知道预留参数的时候，返回-1
    NumInput() int
    // Exec函数执行Prepare准备好的sql，传入参数执行update/insert等操作，返回Result数据
    Exec(args []Value) (Result, error)
    // Query函数执行Prepare准备好的sql，传入需要的参数执行select操作，并返回rows结果集
    Query(args []Value) (Rows, error)
}
```

### driver.Tx

- 事务处理一般只有两个过程，提交或者回滚。数据库驱动中也只需要实现这两个函数即可
```go
type Tx interface{
    // 提交事务
    Commit() error
    // 回滚事务
    Rollback() error
}
```

### driver.Execer

- 这是一个Conn可选实现的接口
```go
type Execer interface{
    Exec(query string, args []Value) (Result, error)
}
```

- 如果这个接口没有定义，那么在调用DB.Exec时就会首先调用Prepare返回Stmt，然后执行Stmt的Exec，最后关闭Stmt


### driver.Result

- 执行update/insert等操作返回的结果接口定义
```go
type Result interface{
    // LastInsertId函数返回数据库执行插入操作得到的自增ID号
    LastInsertId() (int64, error)
    // RowsAffected函数返回执行update/insert等操作影响的数据条目数
    RowsAffected() (int64, error)
}
```

### driver.Rows

- Rows是执行查询返回的结果集接口定义
```go
type Rows interface{
    // Columns函数返回数据库表中的字段信息，这个返回的slice和sql查询的字段一一对应，而不是返回整个表的所有字段
    Columns() []string
    // Close函数用来关闭Rows迭代器
    Close() error
    // Next函数用来返回下一条数据，并把数据赋值给dest。dest中的元素必须是driver.Value的值，除了string，返回的数据里所有的string都必须转换为[]byte。如果最后没有数据了，Next函数返回io.EOF
    Next(dest []Value) error
}
```

### driver.RowsAffected

- RowsAffected其实就是一个int64的别名，但是它是西安了Result接口，用来底层实现Result的表示方式
```go
type RowsAffected int64
func (RowsAffected) LastInsertId()(int64, error)
func (v RowsAffected) RowsAffected()(int64, error)
```

### driver.Value

- Value是一个空接口，可以容纳任何的数据
```go
type Value interface{}
```

- driver的Value是驱动必须能够操作的Value，Value要么是nil，要么是下面的任意一种
```
int64
float64
bool
[]byte
string
time.Time
```

### driver.ValueConverter

- ValueConverter接口定义了如何把一个普通值转化成driver.Value的接口
```go
type ValueConverter interface{
    ConvertValue(v interface{}) (Value, error)
}
```

- 在开发的数据库驱动包里面实现这个接口的函数在很多地方会使用到，这个ValueConverter有很多好处
    - 转化driver.Value到数据库表相应的字段，例如int64的数据如何转化成数据库表uint16字段
    - 把数据库查询结果转化成driver.Value值
    - 在Scan函数里面如何把driver.Value值转化成用户定义的值

### driver.Valuer

- Valuer接口定义了返回一个driver.Value的方式
```go
type Valuer interface{
    Value() (Value, error)
}
```

- 很多类型都实现了这个Value方法，用来自身与driver.Value的转化

### database/sql

- `database/sql`在`database/sql/driver`提供的接口基础上定义了一些更高级的方法，用来简化数据库操作，同时内部还建议性的实现了一个`conn pool`
```go
type DB struct{
    driver      driver.Driver
    dsn         string
    mu          sync.Mutex
    // 一个简单的连接池
    freeConn     []*driverConn
    closed      bool
}
```

## Mysql数据库

- 查看GORM操作

## NOSQL数据库操作

- NoSQL(Not Only SQL)，指的是非关系型的数据库。

### redis

- redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string、list、set和zset。

- 使用`https://github.com/garyburd/redigo`驱动的实例代码为：
```go
package main
import (
	"fmt"
	"os"
	"os/signal"
	"syscall"
	"time"
	"github.com/garyburd/redigo/redis"
)
var (
	// Pool redis.Pool
	Pool *redis.Pool
)
func init() {
	redisHost := ":6379"
	Pool = newPool(redisHost)
	close()
}
func newPool(server string) *redis.Pool {
	return &redis.Pool{
		MaxIdle:     3,
		IdleTimeout: 240 * time.Second,
		Dial: func() (redis.Conn, error) {
			c, err := redis.Dial("tcp", server)
			if err != nil {
				return nil, err
			}
			return c, err
		},
		TestOnBorrow: func(c redis.Conn, t time.Time) error {
			_, err := c.Do("PING")
			return err
		},
	}
}
func close() {
	c := make(chan os.Signal, 1)
	signal.Notify(c, os.Interrupt)
	signal.Notify(c, syscall.SIGTERM)
	signal.Notify(c, syscall.SIGKILL)
	go func() {
		<-c
		Pool.Close()
		os.Exit(0)
	}()
}
// Get func
func Get(key string) ([]byte, error) {
	conn := Pool.Get()
	defer conn.Close()
	var data []byte
	data, err := redis.Bytes(conn.Do("GET", key))
	if err != nil {
		return data, fmt.Errorf("error get key %s: %v", key, err)
	}
	return data, err
}
// Set func
func Set(key, value string) error {
	conn := Pool.Get()
	defer conn.Close()
	err := conn.Send("SET", key, value)
	if err != nil {
		return err
	}
	return nil
}
func main() {
	Set("test1", "test1")
	test, err := Get("test1")
	fmt.Println(test, err)
}
```

