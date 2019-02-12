# go语言学习总结

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
- 引用类型 map
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

**http包**如下图:

![image](https://github.com/qiujianglong/learning/blob/master/go/png/包.jpg)

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

```text

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

```text

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


