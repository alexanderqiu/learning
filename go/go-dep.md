# go包管理工具之dep

go包管理工具, 对于新人来说不是太友好, 不像maven那样, 只需要maven clean install容易理解与便捷使用
一开始GOROOT与GOPATH就会让新人很不理解, 因此包管理工具需要从理解GOROOT和GOPATH开始 

## GOROOT

> **GOROOT**是go语言默认安装路径, 默认是/usr/local/go, 编译时从GOROOT查找SDK的系统库

**如果有多个版本, 可以通过GOROOT指定**

    export GOROOT=$HOME/go1.11.5

## GOPATH

> **GOPATH**表示本项目与外部项目引用外部项目的代码的路径

GOPATH下有三个目录

- src go编译时查找代码的路径
- bin gvt gopm gopkgs等命令行工具
- pkg 编译生成的lib文件存储的地方

## 内部依赖

docker/go-docker/client.go项目中的内部引用

```gotemplate
import(
  "docker.io/go-docker/api/types"
  "docker.io/go-docker/api/types/versions"
  "docker.io/go-docker/api"
)
```
以上三个都是内部, 当项目在编译时, 就会去查找GOPATH下src中的go-docker的内部依赖包
最终在$GOPATH/src/docker.io/go-docker找到需要的client.go的代码

## 外部依赖

当我们本地开发项目引用github.com, k8s.io, docker.io等外部依赖时, 通过go get获取的外部依赖项目的代码
会存储在$GOPATH/src/github, 在import时也会通过GOPATH路径找到外部依赖

## 为什么需要包管理工具

从上面的不管是内部依赖还是外部依赖, 我们都可以通过GOPATH/src下面定位到项目需要的内部或者外部的依赖包
但是其中有个很严重的问题, 类似于maven中的snapshot包, 当我们从新打包时, 会用最新的代码进行打包。因此,
如果我们依赖的包进行了修改时, 就会影响到我们当前的项目。

为了规避这个问题, 出现了很多包管理工具, 今天我们来谈谈常见的包管理工具

- dep
- godep
- govendor
- glide

## dep

**dep**是google官方的包管理工具

### 安装或升级

macos用户通过homebrew进行安装

    $ brew install dep
    $ brew upgrade dep
  
如果想破解, 可以通过go get方式

    $ go get -u github.com/golang/dep/cmd/dep

### dep使用

工程源文件(src) + 依赖规则(Gopkg.toml) **->** 生成依赖项信息(Gopkg.lock) **->** 获取依赖项到指定目录(缓存和vendor)

**流程图**

![image](https://github.com/alexanderqiu/learning/blob/master/go/png/tools/depflow.png)

**使用demo**
```bash

# 创建一个目录
mkdir -p $GOPATH/src/github.com/alexanderqiu/example
  
# 进入该目录
cd $GOPATH/src/github.com/alexanderqiu/example
  
# dep初始化
dep init
  
# 查看dep初始化后的变化, 发现多出了一个vendor空文件夹与两个文件
ls
  
Gopkg.lock Gopkg.toml vendor/
  
# 根目录添加main.go
  
package main
  
import "fmt"
  
func main() {
  
	fmt.Println("hello example")
  
}
  

# 添加依赖
dep ensure -add github.com/kubernetes/client-go

```

#### Gopkg.lock

**Gopkg.lock**是项目依赖的完整快照,主要分为[[projects]]、[[solve-meta]]两部分, 包含依赖的名称、分支、源路径、版本等相关信息

```text
[[projects]]
  digest = "1:a129b47022db3310826e5ad6ab980205cb5540ad45403170ed5577b2186d760e"
  name = "github.com/kubernetes/client-go"
  packages = ["."]
  pruneopts = "UT"
  revision = "1195e3a8ee1a529d53eed7c624527a68555ddf1f"
  version = "v1.5.1"

[solve-meta]
  analyzer-name = "dep"
  analyzer-version = 1
  input-imports = ["github.com/kubernetes/client-go"]
  solver-name = "gps-cdcl"
  solver-version = 1
```
  

#### Gopkg.toml

**Gopkg.toml**是几种管理dep行为的规则声明

- **dependency rules 依赖性规则** 
  
  约束和覆盖允许用户指定哪些版本的依赖项是可以接收的, 以及从何处

- **Package graph rules 包图规则**
 
  必须和忽略用户通过包含或者排除导入路径来操作导入图

- **metadata 元数据** 

  dep将忽略的键值对的用户定义映射
  
- **prune 修改**

  修剪设置确定哪些文件和目录可以被认为是不必要的, 自动从vendor中删除
 
- **noverify 跳过验证**

  noverify是跳过vendor验证的项目根列表
  
**Gopkg.toml**

```text
# Gopkg.toml example
#
# Refer to https://golang.github.io/dep/docs/Gopkg.toml.html
# for detailed Gopkg.toml documentation.
#
# required = ["github.com/user/thing/cmd/thing"]
# ignored = ["github.com/user/project/pkgX", "bitbucket.org/user/project/pkgA/pkgY"]
#
# [[constraint]]
#   name = "github.com/user/project"
#   version = "1.0.0"
#
# [[constraint]]
#   name = "github.com/user/project2"
#   branch = "dev"
#   source = "github.com/myfork/project2"
#
# [[override]]
#   name = "github.com/x/y"
#   version = "2.4.0"
#
# [prune]
#   non-go = false
#   go-tests = true
#   unused-packages = true


[prune]
  go-tests = true
  unused-packages = true

[[constraint]]
  name = "github.com/kubernetes/client-go"
  version = "1.5.1"
```


此时, example项目通过dep获取到外部依赖, 并且进行了管理

**项目结构如下:**

![image](https://github.com/alexanderqiu/learning/blob/master/go/png/tools/example.png)

### dep指令介绍

**dep init** 主要是初始化项目的时候使用

当使用init后, 会在项目中生成一个空vendor文件夹与两个Gopkg.lock Gopkg.toml文件

**dep ensure** 安装更新项目依赖

    dep ensure -update 更新锁住的版本以及所有其他的依赖
    dep ensure -update github.com/xxx/xxx 指定更新
    dep ensure -add xxx 添加依赖到项目中
    
### dep图形化

#### 安装graphviz

    brew install graphviz
    
#### 使用图形化

通过指令生成图形化依赖并且打开Preview进行预览

    dep status -dot | dot -T png | open -f -a /Applications/Preview.app



