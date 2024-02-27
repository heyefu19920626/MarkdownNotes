# Go基础

- [..](catalog.md)

- [Go基础](#go基础)
  - [Go 函数](#go-函数)
    - [函数作为实参](#函数作为实参)
    - [函数闭包](#函数闭包)
    - [函数方法](#函数方法)
  - [defer](#defer)
  - [Struct Tag](#struct-tag)

## 编译

1. 在Windows系统编译Linux上的可运行程序
```go
# cmd下执行
SET CGO_ENABLED=0
set GOARCH=amd64
set GOOS=linux
# 编译命令
go build main.go
 
# 编译成exe windows下默认exe
SET CGO_ENABLED=1
SET GOOS=windows
SET GOARCH=amd64
# 编译命令
go build main.go
```

## Go 函数

定义

```go
func function_name( [parameter list] ) [return_types] {
   函数体
}
```

### 函数作为实参

```go
package main
import "fmt"

// 声明一个函数类型
type cb func(int) int

func main() {
    testCallBack(1, callBack)
    testCallBack(2, func(x int) int {
        fmt.Printf("我是回调，x：%d\n", x)
        return x
    })
}

func testCallBack(x int, f cb) {
    f(x)
}

func callBack(x int) int {
    fmt.Printf("我是回调，x：%d\n", x)
    return x
}
```

### 函数闭包

Go 语言支持匿名函数，可作为闭包。匿名函数是一个"内联"语句或表达式。匿名函数的优越性在于可以直接使用函数内的变量，不必申明,返回的是另一个函数

```go
package main

import "fmt"

func getSequence() func() int { i:=0
   return func() int { i+=1
     return i  
   }
}

func main(){
   /* nextNumber 为一个函数，函数 i 为 0 */
   nextNumber := getSequence()  

   /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
   fmt.Println(nextNumber()) //1
   fmt.Println(nextNumber()) //2
   fmt.Println(nextNumber()) //3
   
   /* 创建新的函数 nextNumber1，并查看结果 */
   nextNumber1 := getSequence()  
   fmt.Println(nextNumber1()) //1
   fmt.Println(nextNumber1()) //2
}
```

### 函数方法

Go 语言中同时有函数和方法。一个方法就是一个包含了接受者的函数，接受者可以是命名类型或者结构体类型的一个值或者是一个指针。所有给定类型的方法属于该类型的方法集。语法格式如下：

```go
func (variable_name variable_data_type) function_name() [return_type]{
   /* 函数体*/
}
```

示例：

```go
package main

import (
   "fmt"  
)

/* 定义结构体 */
type Circle struct {
  radius float64
}

func main() {
  var c1 Circle
  c1.radius = 10.00
  fmt.Println("圆的面积 = ", c1.getArea())
}

//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64 {
  //c.radius 即为 Circle 类型对象中的属性
  return 3.14 * c.radius * c.radius
}
```

## defer

defer后面的函数在defer语句所在的函数执行结束的时候会被调用

1. 如何让defer函数在宿主函数的执行中间执行
   1. 将函数拆分
   2. 使用匿名函数,在匿名函数中使用defer
2. 多个defer的执行顺序
   1. 后定义的defer先执行
3. defer函数参数的计算时间点
   1. defer函数的参数是在defer语句出现的位置做计算的，而不是在函数运行的时候做计算的
4. 在defer语句里面使用多条语句
   1. 使用函数或匿名函数
5. defer函数会影响宿主函数的返回值
   1. 对于传递的引用会影响返回值，如果只是值传递不会
   2. 如下示例，传递的是i的地址，在返回i的值给r后，又修改了i地址的内容，所以i变为300，r还是100
```go
func foo(i *int) int{
    *i += 100
    defer func(){
        *i += 200
    }()
    return *i
}
func main(){
   i := 0
   fmt.Printf("i = %d\n", i)
   r := foo(&i)
   fmt.Printf("i = %d; r = %d", i, r)
}
// 输出为:
// i = 0
// i = 300; r = 100
```

## Struct Tag

Struct tag 用来将结构体转化为json,Struct能被转化的字段都是首字母大写的字段

1. tag中标识的名称将称为json数据中key的值
2. tag可以设置为\`json:"-"\`来表示本字段不转换为json数据，即使这个字段名首字母大写
   1. 如果想要json key的名称为字符"-"，则可以特殊处理\`json:"-,"\`，也就是加上一个逗号
3. 如果tag中带有,omitempty选项，那么如果这个字段的值为0值，即false、0、""、nil等，这个字段将不会转换到json中
4. 如果字段的类型为bool、string、int类、float类，而tag中又带有,string选项，那么这个字段的值将转换成json字符串
