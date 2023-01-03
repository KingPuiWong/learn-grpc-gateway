# grpc-gateway

<aside>
📌 根据grpc-gateway的官方文档进行开发,记录一下过程和遇到的问题,以及是如何解决的
# 文档

[Tutorials](https://grpc-ecosystem.github.io/grpc-gateway/docs/tutorials/)

# grpc-gate存在的原因

We all know that gRPC is not a tool for everything. There are cases where we still want to provide a traditional HTTP/JSON API. The reasons can range from maintaining backward-compatibility to supporting programming languages or clients not well supported by gRPC. But writing another service just to expose an HTTP/JSON API is quite a time consuming and tedious task.

So is there any way to code just once, but provide APIs in both gRPC and HTTP/JSON at the same time?

The answer is Yes. — 来自官方文档

我们都知道，GRPC不是万能的工具。在某些情况下，我们仍然希望提供传统的HTTP/JSON API。原因很多，从保持向后兼容性到支持GRPC不能很好支持的编程语言或客户端。但是，仅仅为了公开一个HTTP/JSON API而编写另一个服务是一项相当耗时且乏味的任务。

那么，有没有办法只编写一次代码，而同时提供GRPC和HTTP/JSON的API呢？

# 过程

过程主要是跟着文档的Tutorials走,这里就不做具体记录了,很简单

# 遇到的问题和解决的方案

```sql
protoc -I ./proto \
   --go_out ./proto --go_opt paths=source_relative \
   --go-grpc_out ./proto --go-grpc_opt paths=source_relative \
   ./proto/helloworld/hello_world.proto
```

使用以上命令的时候,发现错误

```sql
protoc-gen-go: unable to determine Go import path for "helloworld/hello_world.proto"
```

解决方案,在proto文件添加这一行

```sql
option go_package = "grpc_gateway/proto/helloworld";
```

<aside>
📌 相关链接:[https://developers.google.com/protocol-buffers/docs/reference/go-generated](https://developers.google.com/protocol-buffers/docs/reference/go-generated)
Adding gRPRC-Gateway annotions to an existing proto file时,

添加红框代码时,爆红

![Untitled](https://s2.loli.net/2023/01/03/LxWlCgE1AjdNBt2.png)

解决方案:

![Untitled](https://s2.loli.net/2023/01/03/XS8Od6tcCkUVRMF.png)

去掉automatically框,如果还是不行,将导航1的三个go get 资源,重新执行一次

使用protoc生成代码时,需要去grpc-gateway的代码拷贝到自己的项目中,按照下图的google\api创建目录,将annotation.proto和http.proto拷贝到自己项目中

![Untitled](https://s2.loli.net/2023/01/03/Qc8uKOAWsNEB4lX.png)

项目代码的链接:[https://github.com/googleapis/googleapis/tree/master/google/api](https://github.com/googleapis/googleapis/tree/master/google/api)

拷贝好了之后,按protoc命令去执行,发现报这个错误**google/protobuf/descriptor.proto: File not found**

解决方案:

在自己的项目创建protobuf命令,将descriptor.proto也拷贝进去,然后发现执行时,报****‘protoc-gen-grpc-gateway‘ 不是内部或外部命令的问题****

到本地的go path相关目录,找到grpc-ecosystem的目录,进入protoc-gen-grpc-gateway目录,执行

![Untitled](https://s2.loli.net/2023/01/03/h7QzyACWNi2SKXg.png)

```sql
go build
go install
```

到$GOPATH/bin的目录,就会生成相应的exe文件

![Untitled](https://s2.loli.net/2023/01/03/FyOeKGDH2NAchm9.png)