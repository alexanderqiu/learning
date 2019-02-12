# go语言学习总结

<!-- TOC -->

- [go语言学习总结](#go语言学习总结)
    - [go语言介绍](#go语言介绍)
        - [优势](#优势)
        - [高性能语言](#高性能语言)
        - [内置并发机制](#内置并发机制)
            - [goroutine](#goroutine)
            - [channel](#channel)
        - [类型系统](#类型系统)
        - [内存管理](#内存管理)
        - [start](#start)
    - [打包与工具链](#打包与工具链)
        - [包](#包)
            - [main包](#main包)
            - [导入](#导入)
        - [init函数](#init函数)
            - [示例](#示例)
        - [常用go工具指令](#常用go工具指令)
    - [依赖管理](#依赖管理)
        - [第三方依赖管理工具](#第三方依赖管理工具)
    - [数组切片与类型](#数组切片与类型)
        - [数组](#数组)
            - [概念](#概念)
            - [数组声明和初始化](#数组声明和初始化)
        - [多维数组](#多维数组)
            - [二维数组](#二维数组)
        - [数组传递](#数组传递)
        - [切片](#切片)
            - [切片的创建与初始化](#切片的创建与初始化)
        - [切片增长](#切片增长)
        - [迭代切片](#迭代切片)
        - [多维切片](#多维切片)
        - [切片传递](#切片传递)
    - [映射](#映射)
        - [创建和初始化](#创建和初始化)
        - [传递映射](#传递映射)
    - [类型](#类型)
        - [用户定义类型](#用户定义类型)
        - [基于int64声明一个新类型](#基于int64声明一个新类型)
        - [方法](#方法)
        - [go语言类型](#go语言类型)
        - [公开或未公开标识符](#公开或未公开标识符)

<!-- /TOC -->

## go语言介绍
### 优势

- [高性能语言](#高性能语言)
- [内置并发机制](#内置并发机制)
- [类型系统简单高效](#类型系统)
- [自带垃圾回收器](#内存管理)

### 高性能语言

1. 更智能的编译器, 编译时只关注被直接引用的库, 提高了编译速度
2. 静态语言类型, 但是有动态语言的感觉, 写起来高效, 编译期间能捕获类型错误

### 内置并发机制

#### goroutine

- **goroutine**是**轻量级**的**并行**程序执行路径,与线程类似,但是占用内存远少于线程,需要更少的代码
- **goroutine**可以与**其他goroutine**并行执行,与会与**主程序**并行执行
- **go语言**自动在配置的一组逻辑处理器上调度执行,逻辑处理器绑定在操作系统线程上
- 一次可以启动成千上万个goroutine

#### channel

- **通道**是一种数据结构
- 保证**同一时刻**只会有**一个goroutine**修改数据,解决全局变量或者共享内存问题

### 类型系统

- **灵活的**、**无继承**、**组合模式**复用代码
- 内置类型 **int** **string**
- 引用类型 数组 切片 映射
- 小类型通过组合方式构建成大类型
- 接口用于描述类型的行为,如果一个类型实现了某个接口,这个类型的实例可以存储在接口类型实例中,不需要额外声明
- 以大写字母开头的标识符公开, 程序可以直接访问公开的表示符
- 类型初始化
  - 数值类型 0
  - 布尔类型 false
  - 字符串类型 ''
  - 指针类型 nil

### 内存管理

> go语言与java一样拥有自带的垃圾回收机制,管理内存,使得程序员能够更加专注于代码开发
> 将内存管理交给编译器去处理

### start
```go

package main

import "fmt"

func main() {
	fmt.Printf("hello world")
}

```
## 打包与工具链
### 包
- go语言的都会组成若干组文件,每组文件被称为一个包
- 内置包不需要全路径,其他包需要写明全路径
- 报名清晰简洁、全部小写(有利于开发频繁输入包名)

**http包**如下:

```text

net/http/
    cgi/
    cookiejar/
        testdata/
    fcgi/
    httptest/
    httputil/
    pprof/
    testdata/

```


#### main包
- 命名为main的包有特殊含义
- main()函数位于main包下,作为程序入口
- main包所在的目录的名称为二进制可执行文件的文件名

#### 导入

- **导入标准库** GOPATH目录下查找
```go

package main

import (
	"fmt"
	"strings"
	"github.com/pkg/errors"
)

```

- **远程导入** 指定地址 github地址

```go

package main

import (
	"github.com/pkg/errors"
)

```

- **重命名导入** 两个相同名称的包名

```gotemplate

package main

import (
	"fmt"
	myfmt "mylib/fmt"
)

```

- 导入的包必须被使用,否则编译时会报错

- **空白标识符** 下划线标识符(_) 用于忽略导入的包

```go

package main

import (
	_ "fmt"
	
)

```
### init函数
> init函数在main函数之前被调用,用于设置包、初始化变量和引导工作

####示例
**将数据库驱动注册到sql包中**

```gotemplate

package pg

import (
	"database/sql"
)

func init()  {
	sql.Register("postgres", new(MysqlDriver))
}

```

### 常用go工具指令

```text

go build - 编译生成可执行文件
go clean - 清理可执行文件
go run - 先编译可执行文件、后执行
go vet - 帮助开发人员检测代码常见错误
go fmt - 自动格式化指定的源代码文件
go doc tar - 终端获取帮助文档
godoc -http=:8080 - 浏览器通过localhost:8080查看文档

```

## 依赖管理

### 第三方依赖管理工具
- godep
- vender
- gb(与go get不兼容)

## 数组切片与类型

### 数组

#### 概念
> 数组是切片与映射的基础
>
> 数组是一个长度固定的数据类型,用于存储相同类型的元素连续块(**连续分配** **元素相同** **固定快速索引到元素**)

#### 数组声明和初始化

  - 声明一个数组，并设置为零值
  ```gotemplate
  var array [5] int<br/>
  ```
  
  - 使用数组字面量声明数组

  ```gotemplate
  array =: [5]int{10,20,30,40,50}
  ```

  - ...替代数组长度,容量由初始值决定
  ```gotemplate
  array =: [...]int{10,20,30,40,50}
  ```
  
  - 声明数组并指定特定元素的值
  ```gotemplate
  // 声明一个有 5 个元素的数组
  // 用具体值初始化索引为 1 和 2 的元素 // 其余元素保持零值  
  array := [5]int{1: 10, 2: 20}
  ```
  
  - 修改指定元素的值
  ```gotemplate
  array[2] = 35
  ```  

  - 访问指针数组的元素
  ```gotemplate
  // 声明包含 5 个元素的指向整数的数组
  // 用整型指针初始化索引为 0 和 1 的数组元素
  array := [5]*int{0: new(int), 1: new(int)}
    
  // 为索引为0和1的元素赋值 
  *array[0] = 10 
  *array[1] = 20
  ```

  - 数组赋值给另外一个数组
  ```gotemplate
  // 声明第一个包含 5 个元素的字符串数组 var array1 [5]string
  // 声明第二个包含 5 个元素的字符串数组
  // 用颜色初始化数组
  array2 := [5]string{"Red", "Blue", "Green", "Yellow", "Pink"}
    
  // 把 array2 的值复制到 array1 (元素类型与长度都一致才能赋值)
  array1 = array2
  ```
  
  - 把一个指针数组赋值给另一个
  ```gotemplate
  //把一个指针数组赋值给另一个
  // 声明第一个包含 3 个元素的指向字符串的指针数组  
  var array1 [3]*string
    
  // 声明第二个包含 3 个元素的指向字符串的指针数组
  // 使用字符串指针初始化这个数组
  array2 := [3]*string{new(string), new(string), new(string)}
    
  // 使用颜色为每个元素赋值 *array2[0] = "Red" *array2[1] = "Blue" *array2[2] = "Green"
  // 将 array2 复制给 array1 
  array1 = array2
  ```

### 多维数组
#### 二维数组

- 二维数组的声明与初始化
```text
// 声明一个二维整型数组，两个维度分别存储4个元素和2个元素
var array1 [4][2]int
    
// 使用数组字面量来声明并初始化一个二维整型数组
array2 := [4][2]int{{10, 11}, {20, 21}, {30, 31}, {40, 41}}
    
// 声明并初始化外层数组中索引为1和3的元素 
array3 := [4][2]int{1: {20, 21}, 3: {40, 41}}
    
// 声明并初始化外层数组和内层数组的单个元素 
array4 := [4][2]int{1: {0: 20}, 3: {1: 41}}
```
**多维数组如下图**
![image](https://github.com/qiujianglong/learning/blob/master/go/png/多维数组.jpg)

- 二维数组的赋值
```gotemplate
// 声明两个不同的二维整型数组 
var array1 [2][2]int  
var array2 [2][2]int
  
// 为每个元素赋值 
array2[0][0] = 10 
array2[0][1] = 20 
array2[1][0] = 30 
array2[1][1] = 40
  
// 将 array2 的值复制给 
array1 array1 = array2
```

- 使用索引为变量赋值
```gotemplate
// 将 array1 的索引为 1 的维度复制到一个同类型的新数组里 
var array3 [2]int = array1[1]
    
// 将外层数组的索引为 1、内层数组的索引为 0 的整型值复制到新的整型变量里 
var value int = array1[1][0]
```
### 数组传递
- 值传递(分配内存、赋值)
```gotemplate
//声明一个需要8 MB的数组
var array [1e6]int
    
// 将数组传递给函数 foo foo(array)
// 函数 foo 接受一个 100 万个整型值的数组 
func foo(array [1e6]int) {
}
```

- 指针传递(改变指针指向)
```gotemplate
//分配一个需要8 MB的数组
var array [1e6]int
    
// 将数组的地址传递给函数 
foo foo(&array)
    
// 函数 foo 接受一个指向 100 万个整型值的数组的指针 
func foo(array *[1e6]int) {
}
```

### 切片
> 切片是一种**数据结构**,便于使用和管理数据集合
>
> 切片对底层数组进行了抽象
>
> 动态数据,按需自增或者缩小

**底层结构**
![image](https://github.com/qiujianglong/learning/blob/master/go/png/slice.jpg)

#### 切片的创建与初始化

- 使用长度声明一个字符串切片
```gotemplate
// 创建一个字符串切片
// 其长度和容量都是5个元素 
slice := make([]string, 5)
```

- 使用长度和容量声明整型切片
```gotemplate
// 创建一个整型切片
// 其长度为3个元素，容量为5个元素 
slice := make([]int, 3, 5)
```

- 使用索引声明切片
```gotemplate
// 创建字符串切片
// 使用空字符串初始化第100个元素 
slice := []string{99: ""}
```

- 声明数组和声明切片的不同
```gotemplate
// 创建有3个元素的整型数组
array := [3]int{10, 20, 30}
    
// 创建长度和容量都是3的整型切片 
slice := []int{10, 20, 30}
```

- 创建 nil 切片
```gotemplate
var slice []int
```

- 声明空切片
```gotemplate
// 使用 make 创建空的整型切片
slice := make([]int, 0)
    
// 使用切片字面量创建空的整型切片 
slice := []int{}
```

- 使用切片字面量来声明切片
```gotemplate
// 创建一个整型切片
// 其容量和长度都是5个元素
slice := []int{10, 20, 30, 40, 50}
    
// 改变索引为1的元素的值 
slice[1] = 25
```

- 使用切片创建切片
```gotemplate
// 创建一个整型切片
// 其长度和容量都是5个元素
slice := []int{10, 20, 30, 40, 50}
    
// 创建一个新切片
// 其长度为2个元素，容量为4个元素 
newSlice := slice[1:3]
    
//计算新的长度和容量,源切片长度为5
长度: 3 - 1 = 2 
容量: 5 - 1 = 4
    
//修改新切片内容,原始切片也会被更改
// 修改 newSlice 索引为1的元素
// 同时也修改了原来的slice的索引为2的元素 
newSlice[1] = 35
```
- 切片索引越界
```gotemplate
// 创建一个整型切片
// 其长度和容量都是5个元素
slice := []int{10, 20, 30, 40, 50}
    
// 创建一个新切片
// 其长度为2个元素，容量为4个元素 
newSlice := slice[1:3]
    
// 修改newSlice索引为3的元素
// 这个元素对于newSlice来说并不存在 
newSlice[3] = 45
    
Runtime Exception:
panic: runtime error: index out of range
```

### 切片增长
- 使用append向切片增加元素
```gotemplate
// 创建一个整型切片
// 其长度和容量都是5个元素
slice := []int{10, 20, 30, 40, 50}
    
// 创建一个新切片
// 其长度为2个元素，容量为4个元素 
newSlice := slice[1:3]
    
// 使用原有的容量来分配一个新元素
// 将新元素赋值为60
newSlice = append(newSlice, 60)
```

- 使用append同时增加切片的长度和容量
```gotemplate
// 创建一个整型切片
// 其长度和容量都是 4 个元素
slice := []int{10, 20, 30, 40}
 
// 向切片追加一个新元素
// 将新元素赋值为 50
newSlice := append(slice, 50)
```

- 使用切片字面量声明一个字符串切片
```gotemplate
// 创建字符串切片
// 其长度和容量都是 5 个元素
source := []string{"Apple", "Orange", "Plum", "Banana", "Grape"}
```

- 使用索引创建切片
```gotemplate
// 将第三个元素切片,并限制容量
// 其长度为1个元素，容量为2个元素 slice := source[2:3:4]
  
对于 slice[i:j:k] 或 [2:3:4]
长度: j – i 或 3 - 2 = 1 
容量: k – i 或 4 - 2 = 2
```

- 将一个切片追加到另一个切片
```gotemplate
// 创建两个切片，并分别用两个整数进行初始化 s1 := []int{1, 2}
s2 := []int{3, 4}
    
// 将两个切片追加在一起，并显示结果 
fmt.Printf("%v\n", append(s1, s2...))
  
Output:[1 2 3 4]
```

### 迭代切片
- 使用for range迭代切片
```gotemplate
// 创建一个整型切片
// 其长度和容量都是 4 个元素
slice := []int{10, 20, 30, 40}
 
// 迭代每一个元素，并显示其值
for index, value := range slice {
} fmt.Printf("Index: %d Value: %d\n", index, value)
    
Output:
Index: 0 Value: 10 Index: 1 Value: 20 Index: 2 Value: 30 Index: 3 Value: 40
```

- 使用空白标识符(下划线)来忽略索引值
```gotemplate
// 创建一个整型切片
// 其长度和容量都是 4 个元素
slice := []int{10, 20, 30, 40}
    
// 迭代每个元素，并显示其值
for _, value := range slice {
  fmt.Printf("Value: %d\n", value) 
}
  
Output:
Value: 10
Value: 20
Value: 30
Value: 40
```

- 使用传统的for循环对切片进行迭代
```gotemplate
// 创建一个整型切片
// 其长度和容量都是 4 个元素
slice := []int{10, 20, 30, 40}
  
// 从第三个元素开始迭代每个元素
for index := 2; index < len(slice); index++ {
  fmt.Printf("Index: %d Value: %d\n", index, slice[index]) 
}
  
Output:
Index: 2 Value: 30 Index: 3 Value: 40
```

### 多维切片
- 创建一个整型切片的切片
```text
slice := [][]int{{10}, {100, 200}}
```
**如图**
![image](https://github.com/qiujianglong/learning/blob/master/go/png/multislice.jpg)

- 多维切片增加值
```text
// 创建一个整型切片的切片
slice := [][]int{{10}, {100, 200}}
  
// 为第一个切片追加值为20的元素 
slice[0] = append(slice[0], 20)
```

### 切片传递
> 函数间以值的方式传递切片,切片值很小,复制成本低

```gotemplate
// 分配包含100万个整型值的切片
slice := make([]int, 1e6)
  
// 将slice传递到函数 
foo slice = foo(slice)
  
// 函数 foo 接收一个整型切片，并返回这个切片 
func foo(slice []int) []int {
  ...
  return slice
}
```

## 映射
> 映射是一种数据结构，用于存储一系列无序的键值对
> 
> 映射是一个集合，可以使用类似处理数组和切片的方式迭代映射中的元素
>
> 所有操作都要先选择一个桶。把操作映射时指定的键传给映射的散列函数,就能选中对应的桶

### 创建和初始化
- 使用make声明映射
```gotemplate
// 创建一个映射，键的类型是string，值的类型是int
dict := make(map[string]int)
  
// 创建一个映射，键和值的类型都是string
// 使用两个键值对初始化映射
dict := map[string]string{"Red": "#da1337", "Orange": "#e95a22"}
```

- 声明一个存储字符串切片的映射
```gotemplate
// 创建一个映射，使用字符串切片作为值
dict := map[int][]string{}
```

- 为映射赋值
```gotemplate
// 创建一个空映射，用来存储颜色以及颜色对应的十六进制代码
colors := map[string]string{}
    
// 将 Red 的代码加入到映射 
colors["Red"] = "#da1337"
```

- 使用range迭代映射
```gotemplate
// 创建一个映射，存储颜色以及颜色对应的十六进制代码 
colors := map[string]string {
    "AliceBlue":   "#f0f8ff",
    "Coral":       "#ff7F50",
    "DarkGray":    "#a9a9a9",
    "ForestGreen": "#228b22",
}
    
// 显示映射里的所有颜色
for key, value := range colors {
  fmt.Printf("Key: %s Value: %s\n", key, value) 
}
```

- 从映射中删除一项
```gotemplate
// 删除键为 Coral 的键值对 
delete(colors, "Coral")
    
// 显示映射里的所有颜色
for key, value := range colors {
    fmt.Printf("Key: %s Value: %s\n", key, value) 
}
```

### 传递映射
> 函数间传递映射,对映射修改后,所有对映射的引用都会被修改

## 类型

### 用户定义类型
- 自定义类型
```gotemplate
type user struct {
    name          string
    email         string
    ext           int
    privileged    boolean
}
```

- 声明初始化
```gotemplate
// 声明一个为user类型的变量,并且初始化零值
var bill user
```

- 结构字面量来声明一个结构类型的变量
```gotemplate
//声明user类型的变量，并初始化所有字段 
lisa := user {
  name: "Lisa",
  email: "lisa@email.com",
  ext: 123, 
  privileged: true, 
}
```

- 不使用字段名，创建结构类型的值
```gotemplate
//声明user类型的变量
lisa := user{"Lisa", "lisa@email.com", 123, true}
```

- 嵌套结构
```gotemplate
// admin需要一个user类型作为管理者，并附加权限
type admin struct {
    person user
    level string
}
  
//声明admin类型的变量 
fred := admin{
  person: user{
    name: "Lisa",
    email:  "lisa@email.com",
    ext:  123,
    privileged: true,
  },
  level: "super",
}
```

### 基于int64声明一个新类型
**type Duration int64**

Duration 是一种描述时间 间隔的类型，单位是纳秒(ns)

### 方法
关键字**func**和方法名之间增加了一个参数
```gotemplate
func (u user) notify() {
  ...
}
```

- 使用变量调用
```gotemplate
var bill user
bill.notify()
```

- 使用指针调用
```gotemplate
lisa := &user{"Lisa", "lisa@email.com"}
lisa.notify()

    
(*lisa).notify()
```

### go语言类型
- 内置类型
  1. 数值类型
  2. 字符串类型
  3. 布尔类型
  
- 引用类型
  1. 切片
  2. 映射
  3. 通道
  4. 接口
  5. 函数
  
- 结构类型
  
  可以用来描述一组数据值,自定义
  
- 接口
  
  接口用来定义行为的类型, 这些被定义的行为不由接口直接实现
  
- 实现
  
  如果用户定义的类型实现了某个接口类型声明的一组方法，那么这个用户定义的类型的值就可以赋给这个接口类型的值。
  这个赋值会把用户定义的类型的值存入接口类型的值。
  对接口值方法的调用会执行接口值里存储的用户定义的类型的值对应的方法
  
- 多态
  
  通过定义入参的不同结构类型,实现多态
  
### 公开或未公开标识符
> 需要使用某种规则来控制声明后的标识符的可见性,通过判断首字母大小写
- 公开标识符 首字母大写
- 非公开标识符 首字母小写

