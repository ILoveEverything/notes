# Go-gRPC

## 快速开始

本指南通过一个简单的工作示例帮助您开始在 Go 中使用 gRPC。

### 先决条件

- [Go](https://golang.org)最新版或[稳定版](https://golang.org/doc/devel/release)中的任意一个

  有关安装说明，请参阅 Go 的[入门](https://golang.org/doc/install) 指导。

- [协议缓冲区](https://developers.google.com/protocol-buffers)编译器, `protoc`[版本 3](https://developers.google.com/protocol-buffers/docs/proto3).

  有关安装说明，请参阅[协议缓冲区编译器安装](https://grpc.io/docs/protoc-installation/)。

- 协议编译器的**Go 插件**：

  1.使用以下命令为 Go 安装协议编译器插件：

  ```shell
  $ go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.26
  $ go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.1
  ```

  2.更新您的，`PATH`以便`protoc`编译器可以找到插件：

  ```shell
  $ export PATH="$PATH:$(go env GOPATH)/bin"
  ```

### 获取示例代码

示例代码是[grpc-go](https://github.com/grpc/grpc-go) 的一部分回购。

1.[将 repo 下载为 zip 文件](https://github.com/grpc/grpc-go/archive/v1.35.0.zip) 并解压缩它，或克隆 repo：

```shell
$ git clone -b v1.35.0 https://github.com/grpc/grpc-go
```

2.切换到`helloworld`示例目录：

```shell
$ cd grpc-go/examples/helloworld
```

### 运行示例

自`examples/helloworld`目录：

1.编译并执行服务器代码：

```shell
$ go run greeter_server/main.go
```

2.从不同的终端编译并执行客户端代码以查看客户端输出：

``` shell
$ go run greeter_client/main.go
Greeting: Hello world
```

恭喜！您刚刚使用 gRPC 运行了一个客户端-服务器应用程序

### 更新 gRPC 服务

在本节中，您将使用额外的服务器方法更新应用程序。gRPC 服务是使用[协议缓冲区](https://developers.google.com/protocol-buffers)定义的. 要了解有关如何在`.proto`文件中定义服务的更多信息，请参阅[基础教程](https://grpc.io/docs/languages/go/basics/)。现在，您只需要知道服务器和客户端存根都有一个`SayHello()`RPC 方法，该方法`HelloRequest`从客户端获取 参数并`HelloReply`从服务器返回一个，并且该方法定义如下：

```protobuf
//定义服务
service Greeter {
  //定义方法
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

//请求信息
message HelloRequest {
  string name = 1;
}

//响应信息
message HelloReply {
  string mess~~~~````~~~~age = 1;
}
```

打开`helloworld/helloworld.proto`并添加一个`SayHelloAgain()`具有相同请求和响应类型的新方法：

```protobuf
//定义服务
service Greeter {
  //定义方法
  rpc SayHello (HelloRequest) returns (HelloResponse) {}
  rpc SayHelloAgain (HelloRequest) returns (HelloResponse) {}
}

//请求信息
message HelloRequest {
  string name = 1;
}

//响应信息
message HelloResponse {
  string message = 1;
}
```

记得保存文件！

### 重新生成 gRPC 代码

在您可以使用新的服务方法之前，您需要重新编译更新的 `.proto`文件。

仍在`examples/helloworld`目录中时，运行以下命令：

```shell
$ protoc --go_out=. --go_opt=paths=source_relative --go-grpc_out=. --go-grpc_opt=paths=source_relative helloworld/helloworld.proto
```
这将重新生成helloworld/helloworld.pb.go和helloworld/helloworld_grpc.pb.go文件，其中包含：
- 代码填充，序列化和检索`HelloReques`t和 `HelloResponse`信息类型。

- 生成客户端和服务器代码。
### 更新并运行应用程序

您已经重新生成了服务器和客户端代码，但您仍然需要在示例应用程序的人工编写部分中实现和调用新方法。

#### 更新服务器

打开`greeter_server/main.go`并向其添加以下函数：

```go
func (s *server) SayHelloAgain(ctx context.Context, in *pb.HelloRequest) (*pb.HelloResponse, error) {
        return &pb.HelloResponse{Message: "Hello again " + in.GetName()}, nil
}
```

#### 更新客户端

打开`greeter_client/main.go`以在`main()`函数体的末尾添加以下代码 ：

```go
r, err = c.SayHelloAgain(ctx, &pb.HelloRequest{Name: name})
if err != nil {
        log.Fatalf("could not greet: %v", err)
}
log.Printf("Greeting: %s", r.GetMessage())
```

请记住保存您的更改。

#### 运行

像以前一样运行客户端和服务器。从`examples/helloworld`目录执行以下命令：

1.运行服务器：

```shell
$ go run greeter_server/main.go
```

2.从另一个终端运行客户端。这一次，添加一个名称作为命令行参数：

```sh
$ go run greeter_client/main.go Alice
```

您将看到以下输出：

```sh
Greeting: Hello Alice
Greeting: Hello again Alice
```

## 基础教程

#### Go 中 gRPC 的基本教程介绍。

本教程提供了 Go 程序员使用 gRPC 的基本介绍。

通过浏览此示例，您将学习如何：

- 在`.proto`文件中定义服务。
- 使用协议缓冲区编译器生成服务器和客户端代码。

- 使用 Go gRPC API 为您的服务编写一个简单的客户端和服务器。

假设您已阅读[gRPC 简介](https://grpc.io/docs/what-is-grpc/introduction/)并熟悉 [协议缓冲区](https://developers.google.com/protocol-buffers/docs/overview). 请注意，本教程中的示例使用协议缓冲区语言的 proto3 版本：您可以在[proto3 语言指南中](https://developers.google.com/protocol-buffers/docs/proto3)找到更多信息和 [Go 生成的代码指南](https://developers.google.com/protocol-buffers/docs/reference/go-generated).

### 为什么要使用 gRPC？

我们的示例是一个简单的路线映射应用程序，它允许客户端获取有关其路线上的要素的信息、创建路线摘要，并与服务器和其他客户端交换路线信息（例如交通更新）。

使用 gRPC，我们可以在一个`.proto`文件中定义一次我们的服务，并以 gRPC 支持的任何语言生成客户端和服务器，而这些语言又可以在从大型数据中心内的服务器到您自己的平板电脑的环境中运行 - 之间通信的所有复杂性gRPC 为您处理不同的语言和环境。我们还获得了使用协议缓冲区的所有优势，包括高效的序列化、简单的 IDL 和轻松的界面更新

### 设置

您应该已经安装了生成客户端和服务器接口代码所需的工具——如果还没有，请参阅[快速入门](https://grpc.io/docs/languages/go/quickstart/)的[先决条件](https://grpc.io/docs/languages/go/quickstart/#prerequisites)部分以获取设置说明

### 获取示例代码

示例代码是[grpc-go 的一部分](https://github.com/grpc/grpc-go)回购

1.[将 repo 下载为 zip 文件](https://github.com/grpc/grpc-go/archive/v1.35.0.zip) 并解压缩它，或克隆 repo：

```sh
$ git clone -b v1.35.0 https://github.com/grpc/grpc-go
```

2.切换到示例目录：

```sh
$ cd grpc-go/examples/route_guide
```

### 定义服务

我们的第一步（你会从[gRPC 简介](https://grpc.io/docs/what-is-grpc/introduction/)中了解到）是使用[协议缓冲区](https://developers.google.com/protocol-buffers/docs/overview)定义 gRPC服务和方法的请求和响应类型 . 完整`.proto`文件请参见 [routeguide/route_guide.proto](https://github.com/grpc/grpc-go/blob/master/examples/route_guide/routeguide/route_guide.proto).

要定义服务，请`service`在 .proto 文件中指定一个命名服务：

```proto
service RouteGuide {
   ...
}
```

然后`rpc`在服务定义中定义方法，指定它们的请求和响应类型。gRPC 可以让你定义四种服务方法，都在`RouteGuide`服务中使用：

- 一个简单的 RPC，客户端向服务器发送请求并等待响应返回，就像普通的函数调用一样。

```proto
//获取给定位置的特征
rpc GetFeature(Point) returns (Feature) {}
```

- 一个服务器端的流式RPC，客户端向服务器发送一个请求，并获得一个流来读回一连串的消息。客户端从返回的流中读取信息，直到没有更多的信息。正如你在我们的例子中看到的，你通过在响应类型前放置`stream`关键字来指定一个服务器端的流式方法。

```proto
//结果是流式的，而不是一次性返回
rpc ListFeatures(Rectangle) returns (stream Feature) {}
```

- 一个客户端的流式RPC，客户端写了一连串的消息并把它们发送到服务器，同样使用一个提供的流。一旦客户端完成了消息的写入，它就等待服务器读取所有的消息并返回其响应。你通过在请求类型前放置`stream`关键字来指定一个客户端流方法。

```proto
//请求参数是流式的,不是一次性发送
rpc RecordRoute(stream Point) returns (RouteSummary) {}
```

- 一个双向的流式RPC，双方都使用一个读写流发送一连串的消息。这两个流独立运行，因此客户和服务器可以按照他们喜欢的顺序进行读写：例如，服务器可以等待收到所有客户的消息，然后再写它的响应，或者它可以交替地读一个消息然后写一个消息，或者其他一些读写的组合。每个流中的消息的顺序被保留下来。你通过在请求和响应前放置`stream`关键字来指定这种类型的方法。

```proto
//请求和返回都为流式传输
rpc RouteChat(stream RouteNote) returns (stream RouteNote) {}
```

我们的`.proto`文件还包含协议缓冲区的消息类型定义，用于我们服务方法中使用的所有请求和响应类型--例如，这里是`Point`消息类型。

```proto
//消息类型类似与go语言中的结构体
message Point {
  int32 latitude = 1;
  int32 longitude = 2;
}
```

### 生成客户端和服务器代码

接下来，我们需要根据我们的`.proto` 服务定义生成 gRPC 客户端和服务器接口。我们使用`protoc`带有特殊 gRPC Go 插件的协议缓冲区编译器来做到这一点。这类似于我们在[快速入门中](https://grpc.io/docs/languages/go/quickstart/)所做的。

从`examples/route_guide`目录中，运行以下命令：

```sh
$ protoc --go_out=. --go_opt=paths=source_relative --go-grpc_out=. --go-grpc_opt=paths=source_relative routeguide/route_guide.proto
```

运行此命令会在[routeguide 中](https://github.com/grpc/grpc-go/blob/master/examples/route_guide/routeguide)生成以下文件 目录：

- `route_guide.pb.go`，其中包含用于填充、序列化和检索请求和响应消息类型的所有协议缓冲区代码。

- `route_guide_grpc.pb.go`其中包含以下内容：
  - 客户端使用服务中定义的方法调用的接口类型`RouteGuide`。
  - 服务器要实现的接口类型，以及`RouteGuide`服务中定义的方法。

### 创建服务器

首先让我们看看我们如何创建`RouteGuide`服务器。如果您只对创建 gRPC 客户端感兴趣，则可以跳过本节并直接转到[创建客户端](https://grpc.io/docs/languages/go/basics/#client)（尽管无论如何您可能会觉得它很有趣！）。

使我们的`RouteGuide`服务发挥作用有两个部分：

- 实现由我们的服务定义生成的服务接口：做我们服务的实际“工作”。
- 运行 gRPC 服务器来侦听来自客户端的请求并将它们分派给正确的服务实现。

您可以`RouteGuide`在[server/server.go ](https://github.com/grpc/grpc-go/tree/master/examples/route_guide/server/server.go)中找到我们的示例服务器 . 让我们仔细看看它是如何工作的。

#### 实现 RouteGuide

如您所见，我们的服务器有一个`routeGuideServer`结构类型，它实现了生成的`RouteGuideServer`接口：

```go
type routeGuideServer struct {
        ...
}
...

func (s *routeGuideServer) GetFeature(ctx context.Context, point *pb.Point) (*pb.Feature, error) {
        ...
}
...

func (s *routeGuideServer) ListFeatures(rect *pb.Rectangle, stream pb.RouteGuide_ListFeaturesServer) error {
        ...
}
...

func (s *routeGuideServer) RecordRoute(stream pb.RouteGuide_RecordRouteServer) error {
        ...
}
...

func (s *routeGuideServer) RouteChat(stream pb.RouteGuide_RouteChatServer) error {
        ...
}
...
```

##### 简单的RPC

该`routeGuideServer`工具我们所有的服务方法。我们先看最简单的类型，`GetFeature`，它只是`Point`从客户端获取 a并在 a 中从其数据库返回相应的特征信息`Feature`。

```go
func (s *routeGuideServer) GetFeature(ctx context.Context, point *pb.Point) (*pb.Feature, error) {
  for _, feature := range s.savedFeatures {
    if proto.Equal(feature.Location, point) {
      return feature, nil
    }
  }
  // No feature was found, return an unnamed feature
  return &pb.Feature{Location: point}, nil
}
```

该方法为 RPC 和客户端的`Point` 协议缓冲区请求传递一个上下文对象。它返回一个`Feature`带有响应信息和`error`. 在该方法中，我们`Feature` 使用适当的信息填充，然后将`return`它与`nil`错误一起告诉 gRPC 我们已经完成了对 RPC 的处理并且`Feature`可以将 返回给客户端。

##### 服务器端流式RPC

现在让我们看看我们的流式 RPC 之一。`ListFeatures`是服务器端流式RPC，所以我们需要`Feature`向我们的客户端发回多个s。

```go
func (s *routeGuideServer) ListFeatures(rect *pb.Rectangle, stream pb.RouteGuide_ListFeaturesServer) error {
  for _, feature := range s.savedFeatures {
    if inRange(feature.Location, rect) {
      if err := stream.Send(feature); err != nil {
        return err
      }
    }
  }
  return nil
}
```

如您所见，这次我们不是在我们的方法参数中获取简单的请求和响应对象，而是获取一个请求对象（`Rectangle`我们的客户端想要在其中找到`Feature`s）和一个特殊的 `RouteGuide_ListFeaturesServer`对象来编写我们的响应。

在该方法中，我们填充`Feature`需要返回的尽可能多的对象，并`RouteGuide_ListFeaturesServer`使用其`Send()`方法将它们写入。最后，就像在我们的简单 RPC 中一样，我们返回一个`nil`错误来告诉 gRPC 我们已经完成了响应的写入。如果此调用中发生任何错误，我们将返回一个非`nil`错误；gRPC 层会将其转换为适当的 RPC 状态以在线发送。

##### 客户端流式RPC

现在让我们看一个稍微复杂一点的东西：客户端流方法`RecordRoute`，我们`Point`从客户端获取一个s流并返回一个`RouteSummary`包含他们旅行信息的单个流。如您所见，这次该方法根本没有请求参数。相反，它获取一个`RouteGuide_RecordRouteServer`流，服务器可以使用它来读取*和*写入消息 - 它可以使用其`Recv()` 方法接收客户端消息并使用其方法返回其单个响应`SendAndClose()`。

```go
func (s *routeGuideServer) RecordRoute(stream pb.RouteGuide_RecordRouteServer) error {
  var pointCount, featureCount, distance int32
  var lastPoint *pb.Point
  startTime := time.Now()
  for {
    point, err := stream.Recv()
    if err == io.EOF {
      endTime := time.Now()
      return stream.SendAndClose(&pb.RouteSummary{
        PointCount:   pointCount,
        FeatureCount: featureCount,
        Distance:     distance,
        ElapsedTime:  int32(endTime.Sub(startTime).Seconds()),
      })
    }
    if err != nil {
      return err
    }
    pointCount++
    for _, feature := range s.savedFeatures {
      if proto.Equal(feature.Location, point) {
        featureCount++
      }
    }
    if lastPoint != nil {
      distance += calcDistance(lastPoint, point)
    }
    lastPoint = point
  }
}
```

在方法主体中，我们使用`RouteGuide_RecordRouteServer`'s`Recv()`方法重复读取客户端对请求对象（在本例中为 a `Point`）的请求， 直到没有更多消息：服务器需要检查`Recv()`每次调用后返回的错误。如果是`nil`，则流仍然良好，可以继续阅读；如果`io.EOF`消息流已经结束并且服务器可以返回其`RouteSummary`. 如果它有任何其他值，我们将“按原样”返回错误，以便 gRPC 层将其转换为 RPC 状态。

##### 双向流式RPC

最后，让我们看看我们的双向流 RPC `RouteChat()`。

```go
func (s *routeGuideServer) RouteChat(stream pb.RouteGuide_RouteChatServer) error {
  for {
    in, err := stream.Recv()
    if err == io.EOF {
      return nil
    }
    if err != nil {
      return err
    }
    key := serialize(in.Location)
                ... // look for notes to be sent to client
    for _, note := range s.routeNotes[key] {
      if err := stream.Send(note); err != nil {
        return err
      }
    }
  }
}
```

这次我们得到一个`RouteGuide_RouteChatServer`流，就像在我们的客户端流示例中一样，可用于读取和写入消息。但是，这一次我们通过我们的方法的流返回值，而客户端仍在将消息写入其消息流。

这里的读写语法与我们的客户端流方法非常相似，除了服务器使用流的`Send()`方法而不是 `SendAndClose()`因为它正在写入多个响应。尽管每一方总是按照写入的顺序获取对方的消息，但客户端和服务器都可以按任意顺序读写——流完全独立运行。

#### 启动服务器

一旦我们实现了所有方法，我们还需要启动一个 gRPC 服务器，以便客户端可以实际使用我们的服务。以下代码段显示了我们如何为我们的`RouteGuide`服务执行此操作：

```go
flag.Parse()
lis, err := net.Listen("tcp", fmt.Sprintf("localhost:%d", *port))
if err != nil {
  log.Fatalf("failed to listen: %v", err)
}
var opts []grpc.ServerOption
...
grpcServer := grpc.NewServer(opts...)
pb.RegisterRouteGuideServer(grpcServer, newServer())
grpcServer.Serve(lis)
```

要构建和启动服务器，我们：

1. 指定我们要使用监听使用的客户端请求的端口：`lis, err := net.Listen(...)`。
2. 使用`grpc.NewServer(...)`.
3. 向 gRPC 服务器注册我们的服务实现。
4. `Serve()`使用我们的端口详细信息调用服务器以进行阻塞等待，直到进程被终止或被`Stop()`调用。

### 创建客户端

在本节中，我们将着眼于为我们的`RouteGuide` 服务创建 Go 客户端。您可以在[grpc-go/examples/route_guide/client/client.go ](https://github.com/grpc/grpc-go/tree/master/examples/route_guide/client/client.go)中查看我们完整的示例客户端代码 .

#### 创建连接体

要调用服务方法，我们首先需要创建一个 gRPC*通道*与服务器进行通信。我们通过将服务器地址和端口号传递给`grpc.Dial()`如下来创建它 ：

```go
var opts []grpc.DialOption
...
conn, err := grpc.Dial(*serverAddr, opts...)
if err != nil {
  ...
}
defer conn.Close()
```

您可以`DialOptions`在`grpc.Dial`服务需要身份验证凭据（例如，TLS、GCE 凭据或 JWT 凭据）时使用它们。该`RouteGuide`服务不需要任何凭据。

设置gRPC*通道后*，我们需要一个客户端连接体来执行 RPC。我们使用从示例文件生成`NewRouteGuideClient`的`pb`包提供的方法来获取它`.proto`。

```go
client := pb.NewRouteGuideClient(conn)
```

#### 调用服务方法

现在让我们看看我们如何调用我们的服务方法。请注意，在 gRPC-Go 中，RPC 以阻塞/同步模式运行，这意味着 RPC 调用等待服务器响应，并且将返回响应或错误。

##### 简单的RPC

调用简单的 RPC`GetFeature`几乎和调用本地方法一样简单。

```go
feature, err := client.GetFeature(context.Background(), &pb.Point{409146138, -746188906})
if err != nil {
  ...
}
```

如您所见，我们在之前获得的连接体上调用了该方法。在我们的方法参数中，我们创建并填充了一个请求协议缓冲区对象（在我们的例子中 `Point`）。我们还传递了一个`context.Context`对象，它允许我们在必要时更改 RPC 的行为，例如超时/取消正在运行的 RPC。如果调用没有返回错误，那么我们可以从第一个返回值中读取来自服务器的响应信息。

```go
log.Println(feature)
```

##### 服务器端流式RPC

这里是我们调用服务器端流方法的`ListFeatures`，它返回一个流`Feature`。如果您已经阅读了[创建服务器，](https://grpc.io/docs/languages/go/basics/#server)其中的一些内容可能看起来很熟悉 - 流式 RPC 在双方都以类似的方式实现。

```go
rect := &pb.Rectangle{ ... }  // initialize a pb.Rectangle
stream, err := client.ListFeatures(context.Background(), rect)
if err != nil {
  ...
}
for {
    feature, err := stream.Recv()
    if err == io.EOF {
        break
    }
    if err != nil {
        log.Fatalf("%v.ListFeatures(_) = _, %v", client, err)
    }
    log.Println(feature)
}
```

就像在简单的 RPC 中一样，我们向方法传递一个上下文和一个请求。然而，我们得到的不是一个响应对象，而是一个 `RouteGuide_ListFeaturesClient`. 客户端可以使用 `RouteGuide_ListFeaturesClient`流来读取服务器的响应。

我们使用`RouteGuide_ListFeaturesClient`'s`Recv()`方法重复读取服务器对响应协议缓冲区对象（在本例中为 a `Feature`）的响应， 直到没有更多消息：客户端需要在每次调用后检查`err`返回的错误 `Recv()`。如果`nil`，则流仍然良好，可以继续阅读；如果是，`io.EOF`则消息流已结束；否则肯定有一个 RPC 错误，它通过`err`.

##### 客户端流式RPC

客户端流方法`RecordRoute`类似于服务器端方法，除了我们只向方法传递一个上下文并`RouteGuide_RecordRouteClient`返回一个 流，我们可以使用它来写入*和* 读取消息。

```go
// Create a random number of random points
r := rand.New(rand.NewSource(time.Now().UnixNano()))
pointCount := int(r.Int31n(100)) + 2 // Traverse at least two points
var points []*pb.Point
for i := 0; i < pointCount; i++ {
  points = append(points, randomPoint(r))
}
log.Printf("Traversing %d points.", len(points))
stream, err := client.RecordRoute(context.Background())
if err != nil {
  log.Fatalf("%v.RecordRoute(_) = _, %v", client, err)
}
for _, point := range points {
  if err := stream.Send(point); err != nil {
    log.Fatalf("%v.Send(%v) = %v", stream, point, err)
  }
}
reply, err := stream.CloseAndRecv()
if err != nil {
  log.Fatalf("%v.CloseAndRecv() got error %v, want %v", stream, err, nil)
}
log.Printf("Route summary: %v", reply)
```

该`RouteGuide_RecordRouteClient`有一个`Send()`，我们可以用它来发送请求到服务器的方法。一旦我们使用 完成将客户端的请求写入流`Send()`，我们需要调用`CloseAndRecv()`流以让 gRPC 知道我们已完成写入并期待收到响应。我们从`err`返回的`CloseAndRecv()`. 如果状态为`nil`，则来自的第一个返回值`CloseAndRecv()`将是有效的服务器响应。

##### 双向流式RPC

最后，让我们看看我们的双向流 RPC `RouteChat()`。与 的情况一样`RecordRoute`，我们只向方法传递一个上下文对象并返回一个流，我们可以使用它来写入和读取消息。然而，这一次我们通过我们方法的流返回值，而服务器仍在将消息写入*它们的*消息流。

```go
stream, err := client.RouteChat(context.Background())
waitc := make(chan struct{})
go func() {
  for {
    in, err := stream.Recv()
    if err == io.EOF {
      // read done.
      close(waitc)
      return
    }
    if err != nil {
      log.Fatalf("Failed to receive a note : %v", err)
    }
    log.Printf("Got message %s at point(%d, %d)", in.Message, in.Location.Latitude, in.Location.Longitude)
  }
}()
for _, note := range notes {
  if err := stream.Send(note); err != nil {
    log.Fatalf("Failed to send a note: %v", err)
  }
}
stream.CloseSend()
<-waitc
```

这里的读写语法与我们的客户端流方法非常相似，除了`CloseSend()`我们在完成调用后使用流的方法。尽管每一方总是按照写入的顺序获取对方的消息，但客户端和服务器都可以按任意顺序读写——流完全独立运行。

### 试试看！

从`examples/route_guide`目录执行以下命令：

1.运行服务器：

```sh
$ go run server/server.go
```

2.从另一个终端运行客户端：

```sh
$ go run client/client.go
```

你会看到这样的输出：

```nocode
Getting feature for point (409146138, -746188906)
name:"Berkshire Valley Management Area Trail, Jefferson, NJ, USA" location:<latitude:409146138 longitude:-746188906 >
Getting feature for point (0, 0)
location:<>
Looking for features within lo:<latitude:400000000 longitude:-750000000 > hi:<latitude:420000000 longitude:-730000000 >
name:"Patriots Path, Mendham, NJ 07945, USA" location:<latitude:407838351 longitude:-746143763 >
...
name:"3 Hasta Way, Newton, NJ 07860, USA" location:<latitude:410248224 longitude:-747127767 >
Traversing 56 points.
Route summary: point_count:56 distance:497013163
Got message First message at point(0, 1)
Got message Second message at point(0, 2)
Got message Third message at point(0, 3)
Got message First message at point(0, 1)
Got message Fourth message at point(0, 1)
Got message Second message at point(0, 2)
Got message Fifth message at point(0, 2)
Got message Third message at point(0, 3)
Got message Sixth message at point(0, 3)
```

## ALTS认证

Go 中使用应用层传输安全性 (ALTS) 的 gRPC 身份验证概述。

### 概述

应用层传输安全（ALTS）是谷歌开发的一种相互认证和传输加密系统。它用于保护 Google 基础架构内的 RPC 通信。ALTS 类似于双向 TLS，但经过设计和优化以满足 Google 生产环境的需求。有关更多信息，请查看 [ALTS 白皮书](https://cloud.google.com/security/encryption-in-transit/application-layer-transport-security).

gRPC 中的 ALTS 具有以下特点：

- 使用 ALTS 作为传输安全协议创建 gRPC 服务器和客户端。
- ALTS 连接受到端到端的隐私和完整性保护。
- 应用程序可以访问对等信息，例如对等服务帐户。
- 客户端授权和服务器授权支持。
- 最少的代码更改以启用 ALTS。

gRPC 用户可以将他们的应用程序配置为使用 ALTS 作为传输安全协议，只需几行代码。

请注意，如果应用程序在[Google Cloud Platform](https://cloud.google.com/)上运行，则 ALTS 功能齐全 . ALTS 可以在任何具有可插拔[ALTS 握手服务的](https://github.com/grpc/grpc/blob/7e367da22a137e2e7caeae8342c239a91434ba50/src/proto/grpc/gcp/handshaker.proto#L224-L234)平台上运行 .

### 具有 ALTS 传输安全协议的 gRPC 客户端

gRPC 客户端可以使用 ALTS 凭据连接到服务器，如以下代码摘录所示：

```go
import (
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials/alts"
)

altsTC := alts.NewClientCreds(alts.DefaultClientOptions())
conn, err := grpc.Dial(serverAddr, grpc.WithTransportCredentials(altsTC))
```

### 带有 ALTS 传输安全协议的 gRPC 服务器

gRPC 服务器可以使用 ALTS 凭据来允许客户端连接到它们，如下所示：

```go
import (
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials/alts"
)

altsTC := alts.NewServerCreds(alts.DefaultServerOptions())
server := grpc.NewServer(grpc.Creds(altsTC))
```

### 服务器授权

gRPC 具有使用 ALTS 的内置服务器授权支持。使用 ALTS 的 gRPC 客户端可以在建立连接之前设置预期的服务器服务帐户。然后，在握手结束时，服务器授权保证服务器身份与客户端指定的服务帐户之一匹配。否则，连接失败。

```go
import (
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials/alts"
)

clientOpts := alts.DefaultClientOptions()
clientOpts.TargetServiceAccounts = []string{expectedServerSA}
altsTC := alts.NewClientCreds(clientOpts)
conn, err := grpc.Dial(serverAddr, grpc.WithTransportCredentials(altsTC))
```

### 客户端授权

成功连接后，对等信息（例如，客户端的服务帐户）存储在[AltsContext 中](https://github.com/grpc/grpc/blob/master/src/proto/grpc/gcp/altscontext.proto). gRPC 提供了一个用于客户端授权检查的实用程序库。假设服务器知道预期的客户端身份（例如，`foo@iam.gserviceaccount.com`），它可以运行以下示例代码来授权传入的 RPC。

```go
import (
  "google.golang.org/grpc"
  "google.golang.org/grpc/credentials/alts"
)

err := alts.ClientAuthorizationCheck(ctx, []string{"foo@iam.gserviceaccount.com"})
```

## 生成代码参考

此页面描述了使用[grpc 插件](https://godoc.org/github.com/golang/protobuf/protoc-gen-go/grpc)生成的代码以`protoc-gen-go` 编译时`.proto`使用的文件`protoc`。

您可以`.proto`在[Service definition](https://grpc.io/docs/what-is-grpc/core-concepts/#service-definition)中的文件中找到如何定义 gRPC 服务。

**线程安全**：请注意，客户端 RPC 调用和服务器端 RPC 处理程序*是线程安全的*，旨在在并发 goroutine 上运行。但还要注意，对于*单个流*，传入和传出数据是双向但串行的；因此，例如*单个流*不支持*并发读取*或*并发写入*（但读取*与*写入安全并发）。

### 生成的服务器接口上的方法

在服务器端，每一个`service Bar`在`.proto`函数中文件的结果：

```go
func RegisterBarServer(s *grpc.Server, srv BarServer)
```

应用程序可以定义`BarServer`接口的具体实现，并`grpc.Server`通过使用此函数将其注册到实例（在启动服务器实例之前）。

#### 单项传输方法

这些方法在生成的服务接口上具有以下签名：

```go
Foo(context.Context, *MsgA) (*MsgB, error)
```

在这个上下文中，`MsgA`是从客户端发送的 protobuf 消息，`MsgB`是从服务器发回的 protobuf 消息。

#### 服务器流传输方法

这些方法在生成的服务接口上具有以下签名：

```
Foo(*MsgA, <ServiceName>_FooServer) error
```

在此上下文中，`MsgA`是来自客户端的单个请求，该`<ServiceName>_FooServer`参数表示服务器到客户端的`MsgB`消息流。

`<ServiceName>_FooServer`具有嵌入式`grpc.ServerStream`和以下接口：

```go
type <ServiceName>_FooServer interface {
	Send(*MsgB) error
	grpc.ServerStream
}
```

服务器端处理程序可以通过此参数的`Send`方法向客户端发送 protobuf 消息流。服务器到客户端流的流结束是由`return`处理程序方法的引起的。

#### 客户端流方法

这些方法在生成的服务接口上具有以下签名：

```
Foo(<ServiceName>_FooServer) error
```

在此上下文中，`<ServiceName>_FooServer`可用于读取客户端到服务器的消息流和发送单个服务器响应消息。

`<ServiceName>_FooServer`具有嵌入式`grpc.ServerStream`和以下接口：

```go
type <ServiceName>_FooServer interface {
	SendAndClose(*MsgA) error
	Recv() (*MsgB, error)
	grpc.ServerStream
}
```

服务器端处理程序可以重复调用`Recv`此参数，以便从客户端接收完整的消息流。一旦到达流的末尾就`Recv`返回`(nil, io.EOF)`。来自服务器的单个响应消息是通过调用`SendAndClose`此`<ServiceName>_FooServer`参数上的方法发送的。请注意，`SendAndClose`必须调用一次且仅调用一次。

#### 双向流式传输方法

这些方法在生成的服务接口上具有以下签名：

```
Foo(<ServiceName>_FooServer) error
```

在此上下文中，`<ServiceName>_FooServer`可用于访问客户端到服务器消息流和服务器到客户端消息流。 `<ServiceName>_FooServer`具有嵌入式`grpc.ServerStream`和以下接口：

```go
type <ServiceName>_FooServer interface {
	Send(*MsgA) error
	Recv() (*MsgB, error)
	grpc.ServerStream
}
```

服务器端处理程序可以重复调用`Recv`此参数以读取客户端到服务器的消息流。 一旦到达客户端到服务器流的末尾就`Recv`返回`(nil, io.EOF)`。响应服务器到客户端的消息流是通过重复调用`Send`此`ServiceName>_FooServer`参数上的方法来发送的。服务器到客户端流的流结束由`return`bidi 方法处理程序的指示。

### 生成的客户端接口上的方法

用于客户端的使用，每一个`service Bar`中`.proto`的文件也导致功能：`func BarClient(cc *grpc.ClientConn) BarClient`，它返回的具体实现`BarClient`接口（在此具体的实施也住在所生成的`.pb.go`文件中）。

#### 单项传输方法

这些方法在生成的客户端存根上具有以下签名：

```go
(ctx context.Context, in *MsgA, opts ...grpc.CallOption) (*MsgB, error)
```

在此上下文中，`MsgA`是从客户端到服务器的单个请求，并`MsgB`包含从服务器发回的响应。

#### 服务器流方法

这些方法在生成的客户端存根上具有以下签名：

```go
Foo(ctx context.Context, in *MsgA, opts ...grpc.CallOption) (<ServiceName>_FooClient, error)
```

在此背景下，`<ServiceName>_FooClient`代表服务器到客户端`stream`的`MsgB`消息。

此流具有嵌入式`grpc.ClientStream`接口和以下接口：

```go
type <ServiceName>_FooClient interface {
	Recv() (*MsgB, error)
	grpc.ClientStream
}
```

当客户端调用`Foo`存根上的方法时，流开始。然后，客户端可以`Recv`在返回的`<ServiceName>_FooClient` *流*上重复调用该方法，以读取服务器到客户端的响应流。一旦服务器到客户端的流被完全读取，这个`Recv`方法就会返回`(nil, io.EOF)`。

#### 客户端流方法

这些方法在生成的客户端存根上具有以下签名：

```go
Foo(ctx context.Context, opts ...grpc.CallOption) (<ServiceName>_FooClient, error)
```

在此背景下，`<ServiceName>_FooClient`代表客户机到服务器`stream`的`MsgA`消息。

`<ServiceName>_FooClient`具有嵌入式`grpc.ClientStream`和以下接口：

```go
type <ServiceName>_FooClient interface {
	Send(*MsgA) error
	CloseAndRecv() (*MsgB, error)
	grpc.ClientStream
}
```

当客户端调用`Foo`存根上的方法时，流开始。然后，客户端可以`Send`在返回的`<ServiceName>_FooClient`流上重复调用该方法，以发送客户端到服务器的消息流。`CloseAndRecv`此流上的方法必须调用一次且仅一次，以便关闭客户端到服务器的流并从服务器接收单个响应消息。

#### 双向流式传输方法

这些方法在生成的客户端存根上具有以下签名：

```go
Foo(ctx context.Context, opts ...grpc.CallOption) (<ServiceName>_FooClient, error)
```

在此上下文中，`<ServiceName>_FooClient`表示客户端到服务器和服务器到客户端的消息流。

`<ServiceName>_FooClient`具有嵌入式`grpc.ClientStream`和以下接口：

```go
type <ServiceName>_FooClient interface {
	Send(*MsgA) error
	Recv() (*MsgB, error)
	grpc.ClientStream
}
```

当客户端调用`Foo`存根上的方法时，流开始。然后，客户端可以`Send`在返回的`<SericeName>_FooClient`流上重复调用该方法，以发送客户端到服务器的消息流。客户端还可以重复调用`Recv`此流以接收完整的服务器到客户端消息流。

服务器到客户端流的流结束由流`(nil, io.EOF)`的`Recv`方法的返回值指示。客户端到服务器流的流结束可以通过调用`CloseSend`流上的方法从客户端指示

### 包和命名空间

当使用`protoc`调用编译器时`--go_out=plugins=grpc:`，`proto package`to Go 包翻译的工作方式与`protoc-gen-go`不使用`grpc`插件时使用插件时相同。

因此，例如，如果`foo.proto`声明自己在 in 中`package foo`，那么生成的`foo.pb.go`文件也将在 Go 包中`foo`。

