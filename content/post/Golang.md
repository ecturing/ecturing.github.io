---
title: Golang
date: 2022-03-12 14:44:05
draft: true
categories:
- 书籍笔记
tags:
- Go
- 后端
---
# Golang

## 安装环境

- 在官网安装Go的安装环境

- 在终端中输入以下命令

  ```shell
  go env -w GO111MODULE=on
  go env -w GOPROXY=https://goproxy.io,direct
  ```

  执行命令添加工具代理，不然在Visual Studio Code中下载失败

- Visual Studio Code按住Ctrl+Shift+P 选择Go : install/update tools

### go多文件编译

> Go.mod是Golang1.11版本新引入的官方包管理工具用于解决之前没有地方记录依赖包具体版本的问题，方便依赖包的管理。

1. 启用go mod

   ```shell
   go env -w GO111MODULE=on 
   ```

2. 创建go mod 项目

   ```shell
   go mod init moudleName #moudleName为模块名
   ```

3. 使用go mod的项目文件引用办法
   在项目中使用

   ```go
   import "moudleName/packageName" //moudleName为模块名，packageName为包名
   ```

   

## 学习

### 语言结构

示例

```go
package main

import "fmt"

func main() {
   fmt.Println("Hello, World!")
}
```

> **注意**
> 	当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 protected ）

函数名首字母小写即为 **private** :

```go
func getId() {}
```

函数名首字母大写即为 **public** :

```go
func Printf() {}
```

关于包，以下几点：

-  文件名与包名没有直接关系，不一定要将文件名与包名定成同一个。
-  文件夹名与包名没有直接关系，并非需要一致。
-  同一个文件夹下的文件只能有一个包名，否则编译报错。

### 基础语法

> **行分隔符**
> 在 Go 程序中，一行代表一个语句结束。每个语句不需要像 C 家族中的其它语言一样以分号 ; 结尾，因为这些工作都将由 Go 编译器自动完成。如果你打算将多个语句写在同一行，它们则必须使用 ; 人为区分，但在实际开发中我们并不鼓励这种做法。

> **标识符**标识符用来命名变量、类型等程序实体。一个标识符实际上就是一个或是多个字母(A~Z和a~z)数字(0~9)、下划线_组成的序列，但是第一个字符必须是字母或下划线而不能是数字。

格式化字符串

```go
package main

import (
    "fmt"
)

func main() {
   // %d 表示整型数字，%s 表示字符串
    var stockcode=123
    var enddate="2020-12-31"
    var url="Code=%d&endDate=%s"
    var target_url=fmt.Sprintf(url,stockcode,enddate)
    fmt.Println(target_url)
}
```

Go 语言的包引入一般为: **项目名/包名**

```go
import "test/controllers"
```

方法的调用为: **包名.方法名()**

```go
controllers.Test()
```

### 数据类型

`布尔型`：布尔型的值只可以是常量 true 或者 false。一个简单的例子：var b bool = true。

`数字类型`：整型 int 和浮点型 float32、float64，Go 语言支持整型和浮点型数字，并且支持复数，其中位的运算采用补码。

`字符串类型`：字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 编码标识 Unicode 文本。

派生类型：

- (a) 指针类型（Pointer）
- (b) 数组类型
- (c) 结构化类型(struct)
- (d) Channel 类型
- (e) 函数类型
- (f) 切片类型
- (g) 接口类型（interface）
- (h) Map 类型

### 语言变量

```go
var identifier type
```

```go
var identifier1, identifier2 type
```

零值（未初始化）

- 数值类型（包括complex64/128）为 **0**

- 布尔类型为 **false**

- 字符串为 **""**（空字符串）

- 以下几种类型为 **nil**：
 ```go
  var a *int
  var a []int
  var a map[string] int
  var a chan int
  var a func(string) int
  var a error // error 是接口
 ```

> a := 50 或 b := false。
> a 和 b 的类型（int 和 bool）将由编译器自动推断。
> 这是使用变量的首选形式，但是它只能被用在函数体内，而不可以用于全局变量的声明与赋值。使用操作符 := 可以高效地创建一个新的变量，称之为初始化声明。

### 语言常量

- 显式类型定义： `const b string = "abc"`
- 隐式类型定义： `const b = "abc"`

常量还可以用作枚举：

```go
const (
    Unknown = 0
    Female = 1
    Male = 2
)
```

常量可以用len(), cap(), unsafe.Sizeof()函数计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不过：
实例

```go
package main

import "unsafe"
const (
    a = "abc"
    b = len(a)
    c = unsafe.Sizeof(a)
)

func main(){
    println(a, b, c)
}
```

**iota**
特殊常量，可以认为是一个可以被编译器修改的常量。iota 在 const关键字出现时将被重置为 0(const 内部的第一行之前)，const 中每新增一行常量声明将使 iota 计数一次(iota 可理解为 const 语句块中的行索引)。

```go
package main

import "fmt"

func main() {
    const (
            a = iota   //0
            b          //1
            c          //2
            d = "ha"   //独立值，iota += 1
            e          //"ha"   iota += 1
            f = 100    //iota +=1
            g          //100  iota +=1
            h = iota   //7,恢复计数
            i          //8
    )
    fmt.Println(a,b,c,d,e,f,g,h,i)
}
```

```bash
0 1 2 ha ha 100 100 7 8
```


### 循环控制

for循环

1. 类C For循环

   ```go
   for init; condition; post { }
   ```

2. 类C while循环

   ```go
   for condition { }
   ```

3. range 循环

   ```go
   for key, value := range oldMap {
       newMap[key] = value
   }
   ```

### 函数

```go
func function_name( [parameter list] ) [return_types] {
   函数体
}
```

Go 函数可以返回多个值，例如：
实例

```go
package main

import "fmt"

func swap(x, y string) (string, string) {
   return y, x
}

func main() {
   a, b := swap("Google", "Runoob")
   fmt.Println(a, b)
}
```

Go语言函数作为实参

```go
package main

import (
   "fmt"
   "math"
)

func main(){
   /* 声明函数变量 */
   getSquareRoot := func(x float64) float64 {
      return math.Sqrt(x)
   }
   /* 使用函数 */
   fmt.Println(getSquareRoot(9))
}
```

回调

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
闭包
闭包是一种函数包含外部变量作用域的变量引用的特性。闭包由于会依赖于其他变量作用域的数据，可能会发生内存泄漏，使用时需注意。

###  数组

> 与其他语言不同，go语言数组传递为副本，非引用类型，会发生拷贝，造成效率低下，内存利用率低，推荐传入指针或使用切片

普通初始化

```go
var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

数组长度不确定

```go
var balance = [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

特殊

```go
//  将索引为 1 和 3 的元素初始化
   balance3 := [5]float32{1:2.0,3:7.0}  
   for k = 0; k < 5; k++ {
      fmt.Printf("balance3[%d] = %f\n", k, balance3[k] )
   }
```

多维数组初始化

```go
package main

import "fmt"

func main() {
    // Step 1: 创建数组
    values := [][]int{}

    // Step 2: 使用 append() 函数向空的二维数组添加两行一维数组
    row1 := []int{1, 2, 3}
    row2 := []int{4, 5, 6}
    values = append(values, row1)
    values = append(values, row2)

    // Step 3: 显示两行数据
    fmt.Println("Row 1")
    fmt.Println(values[0])
    fmt.Println("Row 2")
    fmt.Println(values[1])

    // Step 4: 访问第一个元素
    fmt.Println("第一个元素为：")
    fmt.Println(values[0][0])
}
```

```go
package main

import "fmt"

func main() {
    // Step 1: 创建数组
    values := [][]int{}

    // Step 2: 使用 append() 函数向空的二维数组添加两行一维数组
    row1 := []int{1, 2, 3}
    row2 := []int{4, 5, 6}
    values = append(values, row1)
    values = append(values, row2)

    // Step 3: 显示两行数据
    fmt.Println("Row 1")
    fmt.Println(values[0])
    fmt.Println("Row 2")
    fmt.Println(values[1])

    // Step 4: 访问第一个元素
    fmt.Println("第一个元素为：")
    fmt.Println(values[0][0])
}
```

### 指针

定义

```go
var var_name *var-type
```

go空指针

> 当一个指针被定义后没有分配到任何变量时，它的值为 nil。
>
> nil 指针也称为空指针。
>
> nil在概念上和其它语言的null、None、nil、NULL一样，都指代零值或空值。

### 结构体

定义

```go
type struct_variable_type struct {
   member definition
   member definition
   ...
   member definition
}
```

初始化

```go
varName := StructName{
    Field1Value,
    Field2Value,
    Field3Value,
    ...
}
```



>
> 结构体为值传递，需使用结构体指针将其改变为引用传递

结构体数据访问与c基本相通，指针类型与值类型访问均为**.**

### 切片

#### 定义切片

你可以声明一个未指定大小的数组来定义切片：

```go
var identifier []type
```

切片不需要说明长度。

或使用 **make()** 函数来创建切片:

```go
var slice1 []type = make([]type, len)
```

```go
make([]T, length, capacity) //T:类型,length：切片长度,capacity:指定容量(可选)
```

> 数组与切片的定义切勿混淆，不指定大小的数组就是切片

#### 切片初始化

```
s :=[] int {1,2,3 } 
```

直接初始化切片，**[]** 表示是切片类型，**{1,2,3}** 初始化值依次是 **1,2,3**，其 **cap=len=3**。

```
s := arr[:] 
```

初始化切片 **s**，是数组 arr 的引用。

```
s := arr[startIndex:endIndex] 
```

将 arr 中从下标 startIndex 到 endIndex-1 下的元素创建为一个新的切片。

```
s := arr[startIndex:] 
```

默认 endIndex 时将表示一直到arr的最后一个元素。

```
s := arr[:endIndex] 
```

默认 startIndex 时将表示从 arr 的第一个元素开始。

```
s1 := s[startIndex:endIndex] 
```

通过切片 s 初始化切片 s1。

```
s :=make([]int,len,cap) 
```

### Range

> Go 语言中 range 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对。

```go
package main
import "fmt"
func main() {
    //这是我们使用range去求一个slice的和。使用数组跟这个很类似
    nums := []int{2, 3, 4}
    sum := 0
    for _, num := range nums {
        sum += num
    }
    fmt.Println("sum:", sum)
    //在数组上使用range将传入index和值两个变量。上面那个例子我们不需要使用该元素的序号，所以我们使用空白符"_"省略了。有时侯我们确实需要知道它的索引。
    for i, num := range nums {
        if num == 3 {
            fmt.Println("index:", i)
        }
    }
    //range也可以用在map的键值对上。
    kvs := map[string]string{"a": "apple", "b": "banana"}
    for k, v := range kvs {
        fmt.Printf("%s -> %s\n", k, v)
    }
    //range也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
    for i, c := range "go" {
        fmt.Println(i, c)
    }
}
```

### Map

定义

```go
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
```

###  接口
接口本身有很多特性，接口和变量在初始化上非常类型，但是接口和变量有很大区别，最常见的就是error接口，接口有两个属性，一是类型数据，二是值。所以比如把普通类型变量如int a=4，那么转换为接口类型就是{int,4}。对于errors接口，不允许存在比如 var err error=nil。然后把err和nil对比，会发现不一样。因为err={error,nil}。

对于面向对象的接口设计，对于实现的结构体的值接收器可以为值类型和指针类型

定义

```go
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
   /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
   /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
   /* 方法实现*/
}
```

### 并发

### 协程
协程（goroutine）是golang提供的一种轻量级线程，采用多对多(多个协程对应内核多个线程)线程模型，对于协程最主要的是协程的启动和退出以及协程生命周期的管理，goroutine本身没有像Java语言提供对于线程生命周期时的各种函数调用，随用随关闭
1. 启动
   协程的启动使用go关键字
   ```go
   go func()
   ```
2. 退出
   对于以下函数对于的协程退出，当协程非阻塞时，函数返回协程退出
   ```go
   func Do(){
      // To Do
   }

   go Do()
   ```
3. 生命周期管理Context
   上面讲到，Golang本身没有提供任何关于协程生命周期的函数接口调用，对于阻塞式函数，Context提供了一种类似信号式的解决思路
### 管道
管道是golang提供的一种类似
