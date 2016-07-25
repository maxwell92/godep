##一个使用godep的例子##

在README.md里面已经了解到了godep的基本概念，这里将通过一个简单的例子来说明godep的基本使用，这个例子用到了git以及golang，所以在开始之前，需要了解版本控制(VCS)的概念、git的基本使用以及golang包的概念。

-----------

##误区##
之前对godep存在理解上的误区，现在知道了它主要用来管理golang开发所需要的*第三方包*，而在下面的例子里，被管理的本质上是自己开发的包，从内容上看，两者并无差异；从包管理的角度来看，两者还是有一定的差异：对于自有代码版本的控制，一定是有的，协作团队间会比较清楚；而对于第三方包，由于协作的团队都需要使用go get命令来获取，容易出现两个团队获取的版本不一致而带来的混乱。

通过go get获取的包一般是存放在$GOPATH/src下。下面以github.com/gorilla/mux包为例，说明一下godep管理第三方包的情况：

首先go get github.com/gorilla/mux下载这个包，并切换到该目录$GOPATH/src/github.com/gorilla/mux下。已经知道godep可以依赖于git，所以godep的相关操作一定是发生在拥有.git的路径下，mux路径刚好符合。此时运行godep save，会提示有个github.com/gorilla/context包找不到：

![](http://7xiwbf.com1.z0.glb.clouddn.com/godepnotfound.png)

可以通过grep github.com/gorilla/context * 发现是context_gorilla.go和context_gorilla_test.go需要这个包。这时需要使用godep get github.com/gorilla/context下载这个包，之后再次godep save，并通过ls -R可以查看目录结构：

![](http://7xiwbf.com1.z0.glb.clouddn.com/godepls.png)

通过cat命令查看Godeps/Godeps.json查看文件的内容为:

![](http://7xiwbf.com1.z0.glb.clouddn.com/godepnew.png)

这里将context视为mux包的第三方包进行了管理




*July 25th Updated*

-----------

##准备##
查看本地GOPATH的值：
![](http://7xiwbf.com1.z0.glb.clouddn.com/gopath.png)

在这个路径之外，创建目录godepexample，并在里面创建两个子目录app和src，结构如下：

![](http://7xiwbf.com1.z0.glb.clouddn.com/tree.png)

在app目录下，我们编辑一个main.go，内容如下：

```
package main
import (
    "fmt"
    foo "lab"
)

func main() {
    fmt.Println(foo.Bigger(1, 2))
}

```

在src目录下创建子目录lab，并在lab下编辑一个foo.go，内容如下：
```
package foo 
func Bigger(a, b int) int {
    if a > b {
        return a
    } else {
        return b
    }
}

```

切换到app目录，运行go run main.go，会看到下面的显示：

![](http://7xiwbf.com1.z0.glb.clouddn.com/notingopath.png)

找不到包，那么将该包路径添加到GOPATH，GOPATH=$GOPATH:`pwd`。这里如果将lab没有放在src里面，可能仍会找不到包。

正确添加包路径后，再次运行go run main.go就会看到正确的输出结果了。



##添加版本记录##
godep是版本控制工具，但它仍需要别的工具来支持，不采用会怎么样？这里采用git进行版本控制。git应该初始化哪个路径呢？

不采用：

![](http://7xiwbf.com1.z0.glb.clouddn.com/withoutgit.png)

git应该初始化包路径为./src/lab，那么要先对这个路径git init; git add .; git commit -m "xxx"的操作，仅将本次改动提交到本地仓库。通过git log命令可以看到本次操作的记录：

![](http://7xiwbf.com1.z0.glb.clouddn.com/godepgitlog.png)


然后返回godepexample目录下，执行godep save -r ./src/lab记录版本，执行成功结果如下：

![](http://7xiwbf.com1.z0.glb.clouddn.com/godepsucc.png)

查看Godeps/Godeps.json的内容，发现里面rev和刚才git log里面的commit值一致：

![](http://7xiwbf.com1.z0.glb.clouddn.com/Godeps.png)

表明本次记录成功。

这里要注意，godep save 后面跟的是要做版本控制的路径，什么都不写的话默认当前目录。

##更新版本记录##

我们对src/lab/foo.go进行一点修改，例如添加函数:

```
func Add(a, b int) int {
    return a + b
}
```

如果想更新版本记录的话，首先仍需要在src/lab里面提交git，

![](http://7xiwbf.com1.z0.glb.clouddn.com/dirtyworkplace.png)

然后再执行godep update，后面跟的是待更新的包，这里只写lab就可以，如果切换到src/lab下面执行godep update是无效的。所以仍然回到godepexample路径下执行godep update lab进行更新：

![](http://7xiwbf.com1.z0.glb.clouddn.com/2update.png)

这里即使回到godepexample执行godep update src/lab也不对，它要求被更新的必须是json文件里的名字，如下：

![](http://7xiwbf.com1.z0.glb.clouddn.com/updateerror.png)

*July 21th Updated*
