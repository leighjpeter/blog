---
title: Protocol Buffers
date: 2019-08-06T15:45:29+08:00
draft: false

keywords: ["Protobuf"]
description: "Protobuf"
tags: ["Go"]
categories: ["Go"]
author: "leighj"
url: /2019/08/06/protobuf.html

weight: 10

---

> Protocol Buffers - a language-neutral, platform-neutral, extensible way of serializing structured data for use in communications protocols, data storage, and more.

<!--more-->

## 什么是Protocol buffers
+ 一种灵活，高效，自动化的机制，用于序列化结构化数据 - 类似XML，但更小，更快，更简单。 可以定义数据的结构化结构，然后使用特殊生成的源代码轻松地将结构化数据写入和读取各种数据流，并使用各种语言。 甚至可以更新数据结构，而不会破坏根据“旧”格式编译的已部署程序。

## 官方的例子

```go
// [START declaration]
syntax = "proto3";
package tutorial;

import "google/protobuf/timestamp.proto";
// [END declaration]

// [START messages]
message Person {
  string name = 1;
  int32 id = 2;  // Unique ID number for this person.
  string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    string number = 1;
    PhoneType type = 2;
  }

  repeated PhoneNumber phones = 4;
https://developers.google.com/protocol-buffers/docs/downloads
  google.protobuf.Timestamp last_updated = 5;
}

// Our address book file is just one of these.
message AddressBook {
  repeated Person people = 1;
}
// [END messages]
```
**每个 message 类型都有一个或者多个带有唯一编号的字段, 每个字段都有对应的名称和值类型**

每个元素上的“= 1”，“= 2”标记标识该字段在二进制编码中使用的唯一“标记”。标签号1-15需要少于一个字节来编码而不是更高的数字，因此作为优化，您可以决定将这些标签用于常用或重复的元素，将标签16和更高版本留给不太常用的可选元素。重复字段中的每个元素都需要重新编码标记号，因此重复字段特别适合此优化。

如果未设置字段值，则使用默认值：数字类型为零，字符串为空字符串，bools为false。

对于嵌入式消息，默认值始终是消息的“默认实例”或“原型”，其中没有设置其字段。调用访问器以获取尚未显式设置的字段的值始终返回该字段的默认值。

如果重复一个字段，该字段可以重复任意次数（包括零）。重复值的顺序将保留在protocol buffers中。将重复字段视为动态大小的数组。

## 编译protocol buffers

+ 下载安装[protoc](https://developers.google.com/protocol-buffers/docs/downloads)的编译器

+ 安装 Go protocol buffers plugin

```go
go get -u github.com/golang/protobuf/protoc-gen-go

```

+ 执行命令

```go
protoc -I=$SRC_DIR --go_out=$DST_DIR $SRC_DIR/addressbook.proto

```

就会自动生成一个 addressbook.pb.go 的文件

如果出现以下错误：

```go
Import "google/protobuf/timestamp.proto" was not found or had errors.

```

解决方法：

```
//查看protoc

$ which protoc
/Users/Dev/go/bin/protoc

然后把protoc压缩包中的include文件夹放到/Users/Dev/go/下即可

```

## 例子一

list_person.go

```go

package person

import (
    "fmt"
    "github.com/golang/protobuf/proto"
    pb "github.com/leighjpeter/go-learning/example-protobuf/tutorial"
    "io"
    "io/ioutil"
    "log"
    "os"
)

func writePerson(w io.Writer, p *pb.Person) {
    fmt.Fprintln(w, "Person ID:", p.Id)
    fmt.Fprintln(w, "  Name:", p.Name)
    if p.Email != "" {
        fmt.Fprintln(w, "  E-mail address:", p.Email)
    }
    for _, pn := range p.Phones {
        switch pn.Type {
        case pb.Person_MOBILE:
            fmt.Fprint(w, "  Mobile phone #: ")
        case pb.Person_HOME:
            fmt.Fprint(w, "  Home phone #: ")
        case pb.Person_WORK:
            fmt.Fprint(w, "  Work phone #: ")
        }
        fmt.Fprintln(w, pn.Number)
    }
}

func listPeople(w io.Writer, book *pb.AddressBook) {
    for _, p := range book.People {
        writePerson(w, p)
    }
}

func main() {
    if len(os.Args) != 2 {
        log.Fatalf("Usage: %s ADDRESS_BOOL_FILE\n", os.Args[0])
    }
    fname := os.Args[1]

    in, err := ioutil.ReadFile(fname)
    if err != nil {
        log.Fatalln("Error reading file:", err)
    }

    book := &pb.AddressBook{}
    if err := proto.Unmarshal(in, book); err != nil {
        log.Fatalln("Failed ti parse address book:", err)
    }
    listPeople(os.Stdout, book)
}

```

list_person_test.go

```go
package person

import (
    "bytes"
    pb "github.com/leighjpeter/go-learning/example-protobuf/tutorial"
    "strings"
    "testing"
)

func TestWritePersonWritesPerson(t *testing.T) {
    buf := new(bytes.Buffer)
    // [START populate_proto]
    p := pb.Person{
        Id:    1234,
        Name:  "John Doe",
        Email: "jdoe@example.com",
        Phones: []*pb.Person_PhoneNumber{
            {Number: "555-4321", Type: pb.Person_HOME},
        },
    }
    // [END populate_proto]
    writePerson(buf, &p)
    got := buf.String()
    want := `Person ID: 1234
  Name: John Doe
  E-mail address: jdoe@example.com
  Home phone #: 555-4321
`
    if got != want {
        t.Errorf("writePerson(%s) =>\n\t%q, want %q", p.String(), got, want)
    }
}

func TestListPeopleWritesList(t *testing.T) {
    buf := new(bytes.Buffer)
    in := pb.AddressBook{People: []*pb.Person{
        {
            Name:  "John Doe",
            Id:    101,
            Email: "john@example.com",
        },
        {
            Name: "Jane Doe",
            Id:   102,
        },
        {
            Name:  "Jack Doe",
            Id:    201,
            Email: "jack@example.com",
            Phones: []*pb.Person_PhoneNumber{
                {Number: "555-555-5555", Type: pb.Person_WORK},
            },
        },
        {
            Name:  "Jack Buck",
            Id:    301,
            Email: "buck@example.com",
            Phones: []*pb.Person_PhoneNumber{
                {Number: "555-555-0000", Type: pb.Person_HOME},
                {Number: "555-555-0001", Type: pb.Person_MOBILE},
                {Number: "555-555-0002", Type: pb.Person_WORK},
            },
        },
        {
            Name:  "Janet Doe",
            Id:    1001,
            Email: "janet@example.com",
            Phones: []*pb.Person_PhoneNumber{
                {Number: "555-777-0000"},
                {Number: "555-777-0001", Type: pb.Person_HOME},
            },
        },
    }}
    listPeople(buf, &in)
    want := strings.Split(`Person ID: 101
  Name: John Doe
  E-mail address: john@example.com
Person ID: 102
  Name: Jane Doe
Person ID: 201
  Name: Jack Doe
  E-mail address: jack@example.com
  Work phone #: 555-555-5555
Person ID: 301
  Name: Jack Buck
  E-mail address: buck@example.com
  Home phone #: 555-555-0000
  Mobile phone #: 555-555-0001
  Work phone #: 555-555-0002
Person ID: 1001
  Name: Janet Doe
  E-mail address: janet@example.com
  Mobile phone #: 555-777-0000
  Home phone #: 555-777-0001
`, "\n")
    got := strings.Split(buf.String(), "\n")
    if len(got) != len(want) {
        t.Errorf(
            "listPeople(%s) =>\n\t%q has %d lines, want %d",
            in.String(),
            buf.String(),
            len(got),
            len(want))
    }
    lines := len(got)
    if lines > len(want) {
        lines = len(want)
    }
    for i := 0; i < lines; i++ {
        if got[i] != want[i] {
            t.Errorf(
                "listPeople(%s) =>\n\tline %d %q, want %q",
                in.String(),
                i,
                got[i],
                want[i])
        }
    }
}

```

```go

go test -v

=== RUN   TestWritePersonWritesPerson
--- PASS: TestWritePersonWritesPerson (0.00s)
=== RUN   TestListPeopleWritesList
--- PASS: TestListPeopleWritesList (0.00s)
PASS
ok      command-line-arguments  0.008s

```

## 例子二：一个简单的tcp连接
userinfo.proto

```go
syntax = "proto3";  //指定版本，必须要写（proto3、proto2）  
package proto;

enum FOO 
{ 
    X = 0; 
};

//message是固定的。UserInfo是类名，可以随意指定，符合规范即可
message UserInfo{
    string message = 1;   //消息
    int32 length = 2;    //消息大小
    int32 cnt = 3;      //消息计数
}
```

client/client_protobuf.go

```go

package main

import (
    "bufio"
    "fmt"
    "github.com/golang/protobuf/proto"
    stProto "github.com/leighjpeter/go-learning/example-protobuf/proto"
    "net"
    "os"
)

func main() {
    conIP := "localhost:7000"
    var conn net.Conn
    var err error

    //建立连接
    conn, err = net.Dial("tcp", conIP)
    if err != nil {
        fmt.Println("connect fail")
        return
    }
    fmt.Println("connect", conIP, "success")
    defer conn.Close()
    //发送消息
    cnt := 1
    sender := bufio.NewScanner(os.Stdin)
    for sender.Scan() {
        stSend := &stProto.UserInfo{
            Message: sender.Text(),
            Length:  *proto.Int(len(sender.Text())),
            Cnt:     *proto.Int(cnt),
        }
        //protobuf编码
        pData, err := proto.Marshal(stSend)
        if err != nil {
            panic(err)
        }

        //发送
        conn.Write(pData)
        if sender.Text() == "stop" {
            return
        }
        cnt++
    }
}

```

server/server_protobuf.go

```go
package main

import (
    "fmt"
    "github.com/golang/protobuf/proto"
    stProto "github.com/leighjpeter/go-learning/example-protobuf/proto"
    "net"
    "os"
)

func readMessage(conn net.Conn) {
    defer conn.Close()
    buf := make([]byte, 4096, 4096)
    for {
        cnt, err := conn.Read(buf)
        if err != nil {
            panic(err)
        }
        stReceive := &stProto.UserInfo{}
        pData := buf[:cnt]
        //protobuf解码
        err = proto.Unmarshal(pData, stReceive)
        if err != nil {
            panic(err)
        }

        fmt.Println("receive", conn.RemoteAddr(), stReceive)
        if stReceive.Message == "stop" {
            os.Exit(1)
        }
    }
}
func main() {
    //
    listener, err := net.Listen("tcp", "localhost:7000")
    if err != nil {
        fmt.Println("listen fail")
        return
    }
    for {
        conn, err := listener.Accept()
        if err != nil {
            panic(err)
        }
        fmt.Println("new connect", conn.RemoteAddr())
        go readMessage(conn)
    }
}
```

```go
go run clent/client_protobuf.go
go run server/server_protobuf.go


client:connect localhost:7000 success
server:new connect 127.0.0.1:58905

client:hello
server:receive 127.0.0.1:58905 message:"hello" length:5 cnt:1

```
