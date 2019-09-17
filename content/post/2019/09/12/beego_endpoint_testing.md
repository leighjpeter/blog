---
title: "Beego - Endpoint Testing"
date: 2019-09-12T09:47:01+08:00
draft: false

keywords: ["Beego testing AppPath"]
description: "beego testing"
tags: ["Go"]
categories: ["Go"]
author: "leighj"
url: /2019/09/12/beego_endpoint_testing.html

weight: 9

---

> Beego - Endpoint Testing

<!--more-->


## 问题：通常如果有DB或者Redis的初始化，都会遇到DB,Redis的初始化失败，或者找不到.conf路径等问题


首先beego自带一个测试用例案例 /tests/default_test.go

```go

package test

import (
    _ "github.com/leighjpeter/go-learning/myapp/routers"
    "net/http"
    "net/http/httptest"
    "path/filepath"
    "runtime"
    "testing"

    "github.com/astaxie/beego"
    . "github.com/smartystreets/goconvey/convey"
)

func init() {
    _, file, _, _ := runtime.Caller(0)
    apppath, _ := filepath.Abs(filepath.Dir(filepath.Join(file, ".."+string(filepath.Separator))))
    beego.TestBeegoInit(apppath)
}

// TestBeego is a sample to run an endpoint test
func TestBeego(t *testing.T) {
    r, _ := http.NewRequest("GET", "/", nil)
    w := httptest.NewRecorder()
    beego.BeeApp.Handlers.ServeHTTP(w, r)

    beego.Trace("testing", "TestBeego", "Code[%d]\n%s", w.Code, w.Body.String())

    Convey("Subject: Test Station Endpoint\n", t, func() {
        Convey("Status Code Should Be 200", func() {
            So(w.Code, ShouldEqual, 200)
        })
        Convey("The Result Should Not Be Empty", func() {
            So(w.Body.Len(), ShouldBeGreaterThan, 0)
        })
    })
}

```

apppath :  /path/to/my/project

再看beego.TestBeegoInit()

```go

// TestBeegoInit is for test package init
func TestBeegoInit(ap string) {
    path := filepath.Join(ap, "conf", "app.conf")
    os.Chdir(ap)
    InitBeegoBeforeTest(path)
}

// InitBeegoBeforeTest is for test package init
func InitBeegoBeforeTest(appConfigPath string) {
    if err := LoadAppConfig(appConfigProvider, appConfigPath); err != nil {
        panic(err)
    }
    BConfig.RunMode = "test"
    initBeforeHTTPRun()
}

```

这里默认配置 conf/app.conf

如果自定义了配置文件

```go
- conf
    - mysql
        - mysql.conf
- conf
    - redis
        - redis.conf
```

## 在test中如何读取自定义配置文件的内容呢？

只需要修改 /tests/default_test.go 添加 **beego.LoadAppConfig()**

```go
func init() {
    _, file, _, _ := runtime.Caller(0)
    apppath, _ := filepath.Abs(filepath.Dir(filepath.Join(file, ".."+string(filepath.Separator))))
    beego.TestBeegoInit(apppath)
    beego.LoadAppConfig("ini", apppath+"/conf/mysql/mysql.conf")
}

```

## 遗留问题

比如/path/to/my/project/libs的init(), 执行 go test -v, 打印 RunMode 输出 prod，而不是 test。
执行 bee run 正常

```go

func init() {
    println(beego.BConfig.RunMode) // prod 而不是test
}

```

[之前有人提问issue，但是没有给出明确的答案](https://github.com/astaxie/beego/issues/1057)

[参考](https://stackoverflow.com/questions/36983078/beego-endpoint-testing)
