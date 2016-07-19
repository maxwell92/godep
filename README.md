Godep使用教程
==============================================

Godep是golang的一个第三方包管理工具,用于处理golang的包依赖关系以及构建过程的工具.


#### 安装Godep(Mac OSX)
----------------------------------------------

官方的说法是通过如下命令:

```bash
go get github.com/tools/godep
```

但是安装过程需要依赖golang.org/x/tools底下的包,golang.org已经被墙,安装不成功.

可以从[这里](godep)下载二进制文件,然后将文件拷贝到/usr/local/go/bin/godep,在终端下执行:

```bash
sudo cp godep /usr/local/go/bin/
```

测试godep是否安装成功:

```bash
godep

Godep is a tool for managing Go package dependencies.

Usage:

        godep command [arguments]

The commands are:

    save     list and copy dependencies into Godeps
    go       run the go tool with saved dependencies
    get      download and install packages with specified dependencies
    path     print GOPATH for dependency code
    restore  check out listed dependency versions in GOPATH
    update   update selected packages or the go version
    diff     shows the diff between current and previously saved set of dependencies
    version  show version info

Use "godep help [command]" for more information about a command.
```

打印上面的信息就说明godep安装成功了.

#### 创建Golang项目
----------------------------------------------

Godep需要将它所管理的项目放入版本仓库中,这是必要条件.所以,我们先在github创建一个golang项目,可以直接clone下来:

```bash
git clone https://github.com/lth2015/godep.git
```

查看godep项目的目录结构

```bash
cd godep
tree

.
├── README.md
├── pkg
│   └── darwin_amd64
│       └── xx.org
│           └── dep.a
└── src
    ├── wiselyman.org
    │   └── app
    │       ├── app
    │       └── main.go
    └── xx.org
        └── dep
            └── external.go

8 directories, 5 files
```

有兴趣可以看看main.go和external.go的实现,很简单,不在赘述.

#### 使用godep
----------------------------------------------

设置GOPATH和GOBIN环境变量

```bash
cd godep

export GOPATH=$PWD
export GOBIN=$PWD/bin
```

使用godep构建golang项目

```bash
pwd

/Users/yp-tc-m-2655/IdeaProjects/godep
```

在项目的根目录下运行godep save命令
```bash
godep save
```

src的同级目录下生成Godep的文件夹

```bash
godep save
tree

.
├── Godeps
│   ├── Godeps.json
│   ├── Readme
│   └── _workspace
├── README.md
└── src
    ├── wiselyman.org
    │   └── app
    │       ├── app
    │       └── main.go
    └── xx.org
        └── dep
            └── external.go

7 directories, 6 files
```

构建golang二进制文件

```bash
cd src/wiselyman.org/app

godep go run main.go
```

src/wiselyman.org/app目录下会生产一个二进制可执行文件
```bash
ls 
main    main.go
```

godep还可以跟其他go命令结合使用

```bash
godep go run main.go
godep go build
godep go install
godep go test
....
```

使用godep go install会在src的同级目录bin下生成一个二进制文件,最后的文件和目录的结构如下:

```bash
tree

.
├── Godeps
│   ├── Godeps.json
│   ├── Readme
│   └── _workspace
├── README.md
├── bin
│   └── app
├── pkg
│   └── darwin_amd64
│       └── xx.org
│           └── dep.a
└── src
    ├── wiselyman.org
    │   └── app
    │       ├── main
    │       └── main.go
    └── xx.org
        └── dep
            └── external.go

11 directories, 8 files
```

#### 致谢
----------------------------------------------

本文转自[汪云飞记录本](http://wiselyman.iteye.com/blog/2171562),感谢作者!
