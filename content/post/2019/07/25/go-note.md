---
title: "Go Note"
date: 2019-07-25T11:11:24+08:00
lastmod: 2019-08-01T11:11:24+08:00
draft: false
keywords: ["golang","学习笔记"]
description: "golang学习笔记，基础知识，查漏补缺"
tags: ["Go"]
categories: ["Go"]
author: "leighj"
url: /2019/07/25/go-note.html

weight: 10


---


> golang笔记，基础知识，查漏补缺

<!--more-->

## Go 语言特性

+ 没有继承
+ 强制类型
+ 没有错误处理
+ 支持UTF-8

## Why Go

+ 学习曲线
+ 团队开发工具
+ 部署环境
+ 系统效能

## 基本类型

### **类型和变量**

|类型|长度|默认值|说明|
| ---- | ---- | ---- | ---- |
| bool | 1 | false |      |
| byte | 1 | 0 | uint8 |
| rune | 4 | 0 | |
| int,uint | 4\|8 | 0 |  |
| Int8,uint8 | 1 | 0 | 0~255,-128~127 |
| Int16,uint16 | 2 | 0 | 0~65535,-32768~32767 |
| Int32,uint32 | 4 | 0 | 0 ~ 42亿,-21亿~ 21亿 |
| Int64,uint64 | 8 | 0 | |
| float32 | 4 | 0.0 | |
| Float64 | 8 | 0.0 | |
| complex64 | 8 | | |
| complex128 | 16 | | |
| uintptr | 4\|8 | | 以存储指针的 uint32 或 uint64 整数 |
| array | | | 值类型 |
| string | | "" | utf8字符串，不可改变类型 |
| struct | | | 值类型 |
| slice | | nil | 引用类型(被引用的变量存储在堆区中) |
| map | | nil | 引用类型(被引用的变量存储在堆区中) |
| channel | | nil | 引用类型(被引用的变量存储在堆区中) |
| interface | | nil | 接口 |
| function | | nil | 函数 |

```go
// 关键字
break      default       func     interface   select
case       defer         go       map         struct
chan       else          goto     package     switch
const      fallthrough   if       range       type
continue   for           import   return      var

```

```go
//  内部预定义字段
内建常量: true false iota nil

内建类型: int int8 int16 int32 int64
        uint uint8 uint16 uint32 uint64 uintptr
        float32 float64 complex128 complex64
        bool byte rune string error

内建函数: make len cap new append copy close delete
        complex real imag
        panic recover

 
```


### **控制语句**

```go

// for 三种形式
s := "abc"
for i := 0; i < len(s); i++ {
    println(s[i])
}

n := len(s)
for n > 0 {
    println(s[n])
    n--
}

for { //类似while
    println(s)
}

# switch 三种表达式,不需要break
# 要执行后续case fallthrough
# 支持初始化表达式
switch switch_a := 1; {
case switch_a > 0:
    fmt.Println("a > 0")
    fallthrough
case switch_a == 1:
    fmt.Println("a = 1")
}

switch {
case condition1:
    ...
case condition2:
    ...
default:
    ...
}

switch result:=calculate() {
case result<0:
    ...
case result>0:
    ...
default:
    ...
}

# 跳转语句 goto，break，continue
# 三个语法都可以配合标签使用
LABEL:
    for L_a := 0; L_a < 10; L_a++ {
        for {
            fmt.Println(L_a)
            continue LABEL
        }
    }

    for {
        for L_a := 0; L_a < 10; L_a++ {
            fmt.Println(L_a)
        }
        goto LABEL1
    }
LABEL1:

 
```



### **数组(值类型)**

+ 初始化的方式

```go
var numArr01 [3]int = [3]int{1,2,3}
var numArr02 = [3]int{1,2,3}
var numArr03 = [...]int{1,2,3}
var numArr04 = [...]int{1:800,0:900,2:999}
strArr05 := [...]string{"a","b","c"}

 
```

+ 多维数组

```go
var arr [2][3]int
arr[1][1] = 10

var arr [2][3]int = [2][3]int{{1,2,3},{4,5,6}}
var arr [2][3]int = [...][3]int{{1,2,3},{4,5,6}}
var arr = [2][3]int{{1,2,3},{4,5,6}}
var arr = [...][3]int{{1,2,3},{4,5,6}}

const(
  WIDTH=10
  HEIGHT=20
)

type pixel int
var screen [WIDTH][HEIGHT]pixel

 
```

+ 指向数组的指针和指针数组

```go
var arr1 = new([5]int)
var arr2 [5]int
arr1的类型是*[5]int
arr2的类型是[5]int

# 指向数组的指针
a := [...]int{1,2,3,4,5}
var p *[5]int = &a

# 指针数组
x,y := 1,2
a := [...]*int{&x,&y}

 
```



### **切片 Slice(引用类型)**

```go
// 本质是一个数据结构(struct结构体)
type slice struct{
    ptr *[2]int
    len int
    cap int
}

 
```

```go
// 方式1
var intArr [5]int = [...]int{1,2,3,4,5,6}
slice := intArr[1:3] //左闭右开 slice = [2,3]
// 方式2
slice_a := make([]type,len,[cap])
// 方式3
slice_b := []int{1,2,3}
var slice_c []string = []string{"tom","leighj"}
//
var slice []int //创建一个空的slice，cap和len都是0
slice == nil //true
slice = []int{}
slice == nil // false


//slice 之间无法比较 不能使用==

//组成多维数据结构，内部的slice长度可以不一致，这一点和数组不同
twoD := make([][]int, 3)
for i := 0; i < 3; i++ {
    innerLen := i + 1
    twoD[i] = make([]int, innerLen)
    for j := 0; j < innerLen; j++ {
        twoD[i][j] = i + j
    }
}

 
```

+ slice定义完还不能使用，需要引用到一个数组或make一个空间

+ append

```go
//append底层原理：先创建数组阔容，将原来的元素拷贝到新数组，追加新的元素，然后slice重新引用到这个数组
var slice []int = []int{1,2,3}
slice = append(slice,4,5,6,7)
slice = append(slice,slice...)

 
```

```go
slice := []int{7, 9, 3, 5, 1}
x := min(slice...)
fmt.Printf("The minimum in the slice is: %d\n", x)

func min(s ...int) int {
    if len(s) == 0 {
        return 0
    }
    min := s[0]
    for _, v := range s {
        if v < min {
            min = v
        }
    }
        return min
}
// 切片重新分片，只能向后移，不能向前移
s1 = slice[1:3] // s1=[9,3]
s2 = s1[0:4] // s2=[9,3,5,1]

 
```


### **Map(引用类型)**

+ 声明

```go
var a map[string]string
// 在使用map前一定要先make，这里和数组不同。定义map的时候没有分配内存

 
```

+ make

```go
var map_a map[string]string
map_a = make(map[string]string,10)
map_b := make(map[int]string,10)
map_c := map[int]string{1: "a", 2: "b", 3: "c"}

// map
studentMap := make(map[string]map[string]string)
studentMap["st1"] = make(map[string]string)
studentMap["st1"]["name"] = "tom"
studentMap["st1"]["sex"] = "woman"
studentMap["st1"]["address"] = "北京"
fmt.Println(studentMap) // map[st1:map[sex:woman address:北京 name:tom]]
fmt.Println(studentMap["st1"]["address"]) // 北京

 
```

+ map的增删改查

  + Key-value形式存储数据，key不会重复，key-value是无序的
  + 键值对不存在时自动添加，使用delete()删除键值对

```go
// 判断key是否存在
val,ok := map1[key]
cities := make(map[string]string)
cities["no1"] = "北京"
cities["no1"] = "上海"
cities["no2"] = "杭州"
delete(map,"key")
// 查找
val,ok := cities["no2]
if ok {
    fmt.Printf("find %v",val)
}else{
    fmt.Print("not find")
}

 
```

+ slice of map

```go
var monsters []map[string]string
monsters = make([]map[string]string, 2) //slice需要make
if monsters[0] == nil {
    monsters[0] = make(map[string]string, 2) // map需要make
    monsters[0]["name"] = "lei"
    monsters[0]["age"] = "12"
}
if monsters[1] == nil {
    monsters[1] = make(map[string]string, 2) // map需要make
    monsters[1]["name"] = "tom"
    monsters[1]["age"] = "13"
}

// 初始化的容量是2，继续按下标添加会越界，用append解决动态添加的问题
newMonster := map[string]string{
    "name": "kely",
    "age":  "100",
}

monsters = append(monsters, newMonster)
fmt.Println(monsters)

 
```

+ Sort

```go
// 对map间接排序
map_a := map[int]string{1: "a", 2: "b", 3: "c", 4: "d", 5: "e"}
slice_a := make([]int, len(map_a))
i := 0
for k, _ := range map_a {
    slice_a[i] = k
    i++
}
sort.Ints(slice_a)
fmt.Println(slice_a)

// 转换map的key-value
map_1 := map[int]string{1: "a", 2: "b", 3: "c", 4: "d", 5: "e"}
map_2 := make(map[string]int)

for k, v := range map_1 {
    map_2[v] = k
}
fmt.Println(map_2)

 
```

+ value 更多的是使用struct

```go
type Stu struct{
    Name string
    Age int
    Add string
}

students := make(map[string]Stu,20)
stu1 := Stu{Name:"leighj",Age:12,Add:"aaa"}
stu2 := Stu{Name:"tom",Age:11,Add:"bbb"}
students["st1"] = stu1
students["st2"] = stu2

for k, v := range students {
    fmt.Printf("student's num=%v\n", k)
    fmt.Printf("student's name=%v\n", v.Name)
    fmt.Println()
}

 
```

+ Import!

```go
//当 map 因扩张重新哈希时，各键值项存储位置都会发改变。
//因此，map被设计成 not addressable。
//类似 m[1].name 这种期望透过原 value 指针修改成员的行为自然会被禁

type use struct{
    name string
}
m:= map[int]user{
    1:{"user1"},
}

m[1].name = "tom" // Error: cannot assign to m[1].name

// 正确的做法是完整替换value或者使用指针
u:= m[1]
u.name = "jack"
m[1] = u //替换value

m2 := map[int]*user{
    1:&user{"user1"},
}
m2[1].name = "jack" // 返回的是指针复制品。透过指针修改原对象是允许的

 
```



### **Struct(值类型)**

+ 结构体的所有字段在内存中是连续的
+ 结构间的赋值与比较，必须是完全相同的字段
+ 允许强制转换
+ struct的每个字段上，可以写上一个tag，该tag可以通过反射机制获取。常见的使用场景就是序
  列化和反序列化。

```go
type person struct{
    Name string
    Age int
    Scores [5]float64
    ptr *int
    slice []int
    map map[string]string
}
// 如果结构体的字段类型是： 指针，slice，map的零值都是nil，没有分配空间，实用的时候要先make

 
```

+ 嵌入结构

```go
// 匿名结构体
type human struct {
    Sex int
}
type techer struct {
    human
    Name string
    Age int
}
type student struct {
    human
    Name string
    Age int
}
a := teacher{Name:"joe",Age:19,human:human{Sex:0}}
a := student{Name:"joe",Age:19,human:human{Sex:1}}

 
```


+ 创建结构体变量时指定字段值

```go
type Student struct {
    Name string
    Age  int
}
var st1 = Student{"leighj", 30}
fmt.Println(st1) // {leighj 30}
var st2 = Student{Name: "leighj", Age: 30}
fmt.Println(st2) // {leighj 30}
// 返回结构体的指针类型
var st3 = &Student{Name: "leighj", Age: 30}
fmt.Println(*st3) // {leighj 30}

 
```

+ 用工厂模式解决 **没有构造函数** 的问题

```go
package model
type student struct{
    Name string
    Score float64
}

func NewStudent(name string,score float64) *student{
    return &student{
        Name:name,
        Score:score,
    }
}

package main

import("model")

vat st = model.NewStudent("leighj",30)
 
```

+ 类型断言

```go
val.(type)

for classifier(items ...interface{}){
    for i,x := range items{
        switch x.(type){
        case bool:
            fmt.Printf("param #%d is a bool\n", i)
        case float64:
            fmt.Printf("param #%d is a float64\n", i)
        case int, int64:
            fmt.Printf("param #%d is an int\n", i)
        case nil:
            fmt.Printf("param #%d is nil\n", i)
        case string:
            fmt.Printf("param #%d is a string\n", i)
        default:
            fmt.Printf("param #%d’s type is unknown\n", i)
        }
    }
}

 
```

+ 实现set数据结构

```go
var null struct{}
set := make(map[string]struct{})
set["a"] = null

 
```




### **字符串**

+ 底层是一个byte数组，因此string也可以进行切片处理

+ 遍历,修改  都需要  将其转换成[]rune或者[]byte，处理后，再转回string。

```go
s := "abc汉字"
for i := 0; i < len(s); i++ { // byte
    fmt.Printf("%c,", s[i])
}
fmt.Println()
for _, r := range s { // rune
    fmt.Printf("%c,", r)
}

// 无论哪种转换都会重现分配内存，并复制字节数组

 
```

+ 字符串转整数 strconv.Atoi()

+ 整数转字符串 strconv.Itoa()

+ 字符串转byte []byte("go")

+ []byte转字符串 string([]byte{97,98,99})

+ 10进制转2，8，16 strconv.FormatInt(i int64,base int) string

+ 字符串是否包含 strings.Contains(s,substr string) bool

+ 字符出现的次数 strings.Count(s,substr string) int

+ 不区分大小写的字符比较 strings.EqualFold(s,t string) bool

+ 字串在字符串第一次出现的位置 strings.Index(s,substr string) int

+ 字串在字符串最后出现的位置 strings.LastIndex(s,substr string) int

+ 分割字符串 strings.Split(s,sep string) []string

+ 替换 strings.Replace(s,old,new string, n int) string  如果n<0则表示全部替换

+ strings.ToLower

+ strings.TrimSpace,strings.Trim,strings.TrimLeft,strings.TrimRight

+ 前缀后缀 strings.HasPrefix,strings.HasSuffix

```go
func MakeAddSuffix(prefix string) func(string) string {
    return func(name string) string {
        if !strings.HasPrefix(name, prefix) {
            return prefix + name
        }
        return name
    }
}
```



### **时间**

- now := time.Now()

- 格式化时间函数Fomat  now.Format() !备注：2006-01-02 15:04:05 数字必须固定

- 获取当前时间戳 now.Unix(), now.UnixNano()

- time.LoadLocation(name string)

```go

var locales map[string]map[string]string
func date(formate string, t time.Time) string {
    year, month, day := t.Date()
    hour, min, sec := t.Clock()
    formate = strings.Replace(formate, "Y", "d", -1)
    formate = strings.Replace(formate, "m", "d", -1)
    formate = strings.Replace(formate, "d", "d", -1)
    formate = strings.Replace(formate, "H", "d", -1)
    formate = strings.Replace(formate, "M", "d", -1)
    formate = strings.Replace(formate, "S", "d", -1)
    return fmt.Sprintf(formate, year, month, day, hour, min, sec)
}

func msg(lang, key string) string {
    if v, ok := locales[lang]; ok {
        if v2, ok := v[key]; ok {
            return v2
        }
    }
    return ""
}

func main(){
    locales = make(map[string]map[string]string, 2)
    en := make(map[string]string, 10)
    cn := make(map[string]string, 10)
    en["time_zone"] = "America/Chicago"
    cn["time_zone"] = "Asia/Shanghai"
    locales["zh-CN"] = cn
    locales["en"] = en
    lang := "zh-CN"
    loc, _ := time.LoadLocation(msg(lang, "time_zone"))
    t := time.Now()
    t = t.In(loc)
    fmt.Println(t.Format(time.RFC3339)) // 2019-07-26T15:30:04+08:00

    en["date_format"] = "%Y-%m-%d %H:%M:%S"
    cn["date_format"] = "%Y年%m月%d日 %H时%M分%S秒"
    fmt.Println(date(msg(lang, "date_format"), t)) // 2019年7月26日 15时30分4秒

    // UTC时间戳默认+8个小时
    tt, _ := time.Parse("2006-01-02 15:04:05", "2019-08-01 00:00:00")
    fmt.Println(tt.Unix()) // 1564617600 = 2019-08-01 08:00:00

    loc, _ := time.LoadLocation("PRC")
    t, _ := time.ParseInLocation("2006-01-02 15:04:05", "2019-08-01 00:00:00", loc)
    fmt.Println(t.Unix()) // 1564588800 = 2019-08-01 00:00:00
}

 
```

### **命令行参数**

```go
// 一个 string 的切片
os.Args[]

// flag 包用来解析命令行参数
// main.exe -p 200 -u root

var user string
var port int

flag.StringVar(&user,"u","","用户名默认为空")
flag.IntVar(&port,"p",3306,"端口默认3306")
//important!
flag.Parse()

 
```

### **文件处理**

+ 读文件

```go
os.Open()
file.Close()
bufio.NewReader()
reader.ReadString()

// 使用ioutil一次将整个文件读入到内存中
ioutil.ReadFile

 
```

+ 写文件

```go
func OpenFile(name string,flag int, perm FileMode)(file *file,err error)

ioutil.WriteFile(filePath,data,perm FileMode)

 
```

+ 判断文件是否存在

```go
os.Stat()
os.IsNotExist()

 
```

+ io.Copy(writer, reader)



## 函数

### **func**

+ 一个返回值为另一个函数的函数 称之为工厂函数

+ 不支持嵌套，重载和默认参数

```go
// 一切皆类型
// 匿名函数

// 1在定义匿名函数时直接调用，这种方式匿名函数只用调用一次
res1 := func(a,b int) int {
    return a+b
}(1,2)

// 2将匿名函数赋值给变量，res2的数据类型是函数类型
res2 := func(a,b int) int{
    return a+b
}

// 3全局匿名函数
var (
    Fun1 = func(a,b int) int{
        return a+b
    }
)


// 闭包函数保存并积累其中的变量的值，不管外部函数退出与否，它都能够继续操作外部函数中的局部变量。
func closure(x int) func(int) int {
    return func(y int) int {
        return x + y
    }
}

var g int
go func(i int){
    s :=0
    for j := 0;j < i; j++ {
        s += j
    }
    g = s
}(1000)

println(g) // 0

 
```

### **defer**

+ 在函数体执行结束后按照调用顺序的相反顺序逐个执行，先进后出，后进先出

+ 类似于finally语句块，当函数执行完，可以及时释放某些已分配的资源（最主要的价值）

```go
func test() {
    file = openfile(filename)
    defer file.Close()
}
func test() {
    conn = openDatabase()
    defer conn.Close()
}

 
```

+ 即使发生严重错误也会执行

+ go没有异常机制，但有panic/recover模式来处理错误，panic可以在任何地方引发，但reciver只有在defer调用的函数中有效

```go
func main(){
    A()
    B()
    C()
}
func A(){
    fmt.Println("func A")
}
func B(){
defer func(){
      if err := recover(); err != nil {
          fmt.Println("recover in B")
      }
}
    panic("Panic in B")
}
func C(){
    fmt.Println("func C")
}

输出：
func A
recover in B
func C

 
```



+ 闭包调试

```go
where := func(){
    _,file,line,_ := runtime.Caller(1)
    log.Printf("%s:%d",file,line)
}

where()

# 2
log.SetFlags(log.Llongfile)

var where = log.Print

func func1(){
    where()
}

 
```



## OOP

### **方法**

+ 是指 作用在指定数据类型上的(即:和指定的数据类型绑定)，因此自定义类型都可以有**方法**

  + Method和结构体的结合

```go
type A struct{
    Name string
}
type B struct{}

func main(){
    a := A{}
    a.Print()
}
// Reciver 可以是值类型／指针
func (a A) Print(){
    fmt.Println("A")
}
func (a *A) Print(){
  a.Name = "AA"
    fmt.Println("A")
}

 
```

  + Method Value 和 Method Expression

```go
type TZ int

func (func_a *TZ) Print() {
  fmt.Println("OK")
}

var method_a TZ
method_a.Print() // Method Value
(*TZ).Print(&method_a) // Method Expression

// method value 会复制 receiver。

 
```



### **接口**

+ Structural Typing
+ interface类型默认是一个指针(引用类型)
+ 不能包含任何变量
+ 可以匿名嵌入其他接口和结构体
+ 空接口可以作为任何类型数据的容器
+ 只有当接口存储的类型和对象都为nil时，接口才等于nil
+ 接口里的所有方法都没有方法体
+ 接口不需要显示的实现
+ 一个自定义类型需要将接口中的所有方法都实现
+ 一个自定义类型可以实现多个接口
+ 可以把任何一个变量赋给空接口

```go

type USB interface{
    Name() string
}

type Connecter interface{
    Connect()
}

type PhoneConnecter struct{
    name string
}

func (pc PhoneConnecter) Name() string{
    return pc.name
}

func (pc PhoneConnecter) Connect() {
    fmt.Println("Connected:",pc.name)
}

func Disconnect(usb interface{}){
    switch v:= usb.(type){
        case PhoneConnecter:
          fmt.Println("Disconnected:",v.name)
        default:
          fmt.Println("Unknow decive")
    }

}



func main() {
    interface_a := PhoneConnecter{"HUAWEI"}
    interface_a.Connect()
    Discount(interface_a)

    var n float64 = 8.8
    var a interface{} = n

    // 对象的只读复制品，复制完整对象或指针。
    type User struct {
        id   int
        name string
    }
    u := User{1, "tom"}
    var i interface{} = u

    u.id = 2
    u.name = "jack"

    fmt.Printf("%v", u)        // {2,jack}
    fmt.Printf("%v", i.(User)) // {1,tom}


}

 
```



### **类似封装，继承，多态的特性**

+ 对结构体中的属性进行封装

+ 通过方法，包 实现封装

```go
type Student struct {
    Name  string
    score float64
}

// 一个工厂模式的函数，相当于构造函数
func NewStudent(name string) *Student {
    return &Student{
        Name: name,
    }
}

func (stu *Student) GetScore() float64 {
    return stu.score
}

func (stu *Student) SetScore(score float64) {
    stu.score = score
}

 
```

+ 继承可以解决代码复用

```go
type Human struct {
    Sex int
}
type techer struct {
    Human
    Name string
    Age int
}
type student struct {
    Human
    Name string
    Age int
}

 
```

+ 当结构体和匿名结构体有相同的字段和方法时，编译器采用就近原则。希望访问
    匿名结构体的字段和方法，可以通过匿名结构体名来区分。
+ 结构体嵌入两个(或多个)匿名结构体，如两个匿名结构体有相同的字段和方法(同时结构体本身
    没有同名的字段和方法)，在访问时，就必须明确指定匿名结构体名字

+ 多态特征是通过接口实现的

+ 多态数组

```go
type USB interface{
  Start()
}

type Phone struct{}

type Camera struct{}

func (ph Phone) Start(){
  ...
}

func (c Camera) Start(){
  ...
}

func main(){
    //定义一个 Usb 接口数组，可以存放 Phone 和 Camera 的结构体变量
    var usbArr [3]USB //多态数组
    usbArr[0] = Phone{}
    usbArr[1] = Phone{}
    usbArr[2] = Camera{}
}

 
```


## 协程，管道，Select
### **goroutine**

+ go协程的特点

  + 有独立的栈空间
  + 共享程序的堆空间
  + 调度由用户控制
  + 协程是轻量级的线程，是逻辑态
  + 如果主线程结束了，即使协程还未执行完也会退出

+ goroutine的调度模型

  + MPG模式  M：物理线程，P：协程执行需要的上下文 G：协程

+ runtime.GOMAXPROCS(runtime.NumCPU())

+ goroutine通过channel来共享内存

+ goroutine会有资源争夺的问题

```go
//如 concurrent map writes!

解决方案：
1 全局变量的互斥锁
var (
    lock sync.Mutex
)
lock.Lock()
...
lock.Unlock()

2 使用管道 channel 来解决

 
```


+ sync

```go
func sum(id int) {
    var x int64
    for i := 0; i < math.MaxUint32; i++ {
        x += int64(i)
    }
    println(id, x)
}

func main(){
    wg:=sync.WaitGroup{}
    wg.Add(3)
    for i:=0;i<3;i++{
        go func(id int){
            defer wg.Done()
            sum(id)
        }(i)
    }
    wg.Wait()
}

 
```

```go
//线程安全的方式创建一些东西的最好选择是 sync.Once
var once sync.Once
onceBody := func() {
    fmt.Println("Only once")
}
done := make(chan bool)
for i := 0; i < 10; i++ {
    go func() {
        once.Do(onceBody)
        done <- true
    }()
}
for i := 0; i < 10; i++ {
    <-done
}

 
```



### **管道channel(引用类型)**

  + **必须初始化才能写入数据**

  + 本质是一个数据结构：队列

  + 先进先出

  + 线程安全

  + channel有类型，一个string的channel只能放string类型的数据

  + 定义／声明

```go
var 变量名 chan 数据类型
channel存放满了就不能继续放入，取完以后不能继续取值

var allchan chan interface{}
var mapchan chan map[int]int
var catchan chan Cat
var catchan chan *Cat

chan<- // 写
<-chan // 读


//循环channel
//必须要close
c := make(chan bool)
go func(){
    fmt.Println("GOGOGO")
        c <- true
        close(c)
}()
for v :=range c {
    fmt.Println(v)
}

 
```

+ channel是goroutine沟通的桥梁，默认是同步阻塞，读和取需要同步

+ 异步方式是通过判断缓冲区来决定是否阻塞。如果缓冲区已满，发送被阻塞。缓冲区为空，接受被阻塞

```go
//有缓存和无缓存的差别
chan_c := make(chan int)
go func() {
    fmt.Println("GOGOGO!!!")
    <-chan_c
}()
chan_c <- 1
// 有缓存是异步的
chan_b := make(chan int, 2) //缓冲区可以存储 2 个元素
go func() {
    fmt.Println("GOGOGO!!!")
    chan_b <- 1
}()
<-chan_b

// 除了用range还可以用ok-idiom模式判断channel是否关闭
for{
    if d,ok := <-data; ok{
        fmt.Print(d)
    }else{
        break
    }
}

 
```

+ 可设置单向或者双向通道

```go
var chan chan<- int // 只能写
var chan <-chan int // 只能读

 
```

+ 关闭channel：close(channel)  可以继续从关闭的channel中执行读取操作

+ 一个非空的通道也是可以关闭的， 但是通道中剩下的值仍然可以被接收到。

+ 支持ok写法：这个OK代表有新的数据写入或者是关闭的channel

### **Select**

+ 使用 select 可以解决从管道取数据的阻塞问题


+ select处理一个或多个channel的发送和接收

```go
c1,c2 := make(chan int),make(chan string)
o := make(chan bool)
go func(){
    for{
        select{
        case v,ok := <-c1:
            if !ok {
                o <- true
                break
            }
        fmt.Println("c1",v)
        case v,ok := <-c2:
            if !ok {
                o <- true
                break
            }
        fmt.Println("c2",v)
        }
    }
}()
c1<-1
c2<-"hi"
close(c1)
close(c2)
<-o


// 设置超时 timeout
w := make(chan bool)
c := make(chan int, 2)
go func() {
    select {
    case v := <-c:
        fmt.Println(v)
    case <-time.After(time.Second * 3):
        fmt.Println("timeout.")
    }
    w <- true
}()
// c <- 1 // 注释掉会引发超时
<-w

 
```

+ 同时有多个可用的channel时按随机顺序处理

+ 在goroutine中使用recover，解决协程中出现的panic，导程序奔溃

```go

func test() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Print("panic!!!,recover")
        }
    }()
    panic("...")
}

func main(){
    go test()
    for i := 0; i < 10; i++ {
        fmt.Println("main() ok=", i)
        time.Sleep(time.Second)
    }
}

 
```



### **定时器和打点器**

+ 定时器表示在未来某一时刻的独立事件。你告诉定时器需要等待的时间，然后它将提供一个用于通知的通道。

+ 如果仅仅是单纯的等待，你需要使用 `time.Sleep`。 定时器有用原因之一就是你可以在定时器失效之前，取消这个定时器。

```go
timer2 := time.NewTimer(time.Second)
go func() {
    <-timer2.C
    fmt.Println("Timer 2 expired")
}()
stop2 := timer2.Stop()
if stop2 {
    fmt.Println("Timer 2 stopped")
}

// func (*Timer) Reset
func (t *Timer) Reset(d Duration) bool
//Reset 使 t 重新开始计时，（本方法返回后再）等待时间段 d 过去后到期。如果调用时 t 还在等待中会返回真；如果 t 已经到期或者被停止了会返回假。

wait := sync.WaitGroup{}
fmt.Println("start", time.Now())
wait.Add(1)
timer := time.NewTimer(time.Second * 2)
go func() {
    <-timer.C
    fmt.Println("get timer", time.Now())
    wait.Done()
}()
time.Sleep(time.Second)
fmt.Println("sleep", time.Now())
timer.Reset(time.Second * 3)
wait.Wait()
// 输出:
// start 2018-06-04 13:07:51.780367114 +0800 CST m=+0.000324036
// sleep 2018-06-04 13:07:52.783389811 +0800 CST m=+1.003316644
// get timer 2018-06-04 13:07:55.784534298 +0800 CST m=+4.004371103

 
```

+ 打点器：想要在固定的时间间隔重复执行。打点器可以和定时器一样被停止

```go
type Ticker struct {
    C <-chan Time // 周期性传递时间信息的通道
    // 内含隐藏或非导出字段
}
  // Ticker 保管一个通道，并每隔一段时间向其传递”tick”。

 
```

```go
ticker := time.NewTicker(time.Millisecond * 500)
go func() {
  for t := range ticker.C {
      fmt.Println("Tick at", t)
  }
}()
time.Sleep(time.Millisecond * 1600)
ticker.Stop()
fmt.Println("Ticker stopped")

func Tick(d Duration) <-chan Time
//Tick 是 NewTicker 的封装，只提供对 Ticker 的通道的访问。如果不需要关闭 Ticker，本函数就很方便。

fmt.Println("start", time.Now())
count := 0
wait := sync.WaitGroup{}
wait.Add(1)
ticker := time.Tick(time.Second)
go func() {
  for tick := range ticker {
      count += 1
      fmt.Println("tick at", tick)
      if count >= 5 {
          wait.Done()
          break
      }
  }
}()
wait.Wait()
fmt.Println("stoped", time.Now())

 
```



##  其他


### **反射Reflection**

+ reflect.TypeOf()
+ reflect.ValueOf()
+ reflect.ValueOf().Kind() // 类别，是一个常量
+ Elem()
+ 反射可以 让 变量，interface{},reflect.Value 互相转换
+ 应用：
  + 对结构体字段的反射
  + 函数的适配器



### **Redis**

+ go get [github.com/gomodule/redigo/redis](https://gowalker.org/github.com/gomodule/redigo/redis)

+ String hash list set zset

+ 操作方法

```go
conn,err := redis.Dail("tcp","127.0.0.1:6379")
defer conn.Close()
//string
set/get
setex
//hash
hset/hget/hgetall/hdel
//list
lpush/rpush/lrange/lpop/rpop/del
//set
sadd/smembers/srem

_, err = conn.Do("expire", "name", 10)

_, err = conn.Do("HSet", "user01", "name", "leighj")
// 因为返回 r 是 interface{} 需要转换
r, err = redis.String(conn.Do("HGet", "user01", "name"))
_, err = conn.Do("HSet", "user01", "age", 18)
// 因为返回 r 是 interface{} 需要转换
i, err := redis.Int(conn.Do("HGet", "user01", "age"))
//list
_, err = conn.Do("lpush", "heroList", "no1:宋江", 30, "no2:卢俊义", 28)
// 因为返回 r 是 interface{} 需要转换
r, err = redis.String(conn.Do("rpop", "heroList"))

 
```

+ 连接池

```go
var pool *redis.Pool
func init() {
    pool = &redis.Pool{
        MaxIdle:   8, //最大空闲链接数
        MaxActive: 0, //表示和数据库的最大链接数， 0 表示没有限制
        IdleTimeout: 100, // 最大空闲时间
        Dial: func() (redis.Conn, error) { // 初始化链接的代码， 链接哪个 ip 的 redis
        return redis.Dial("tcp", "127.0.0.1:6379")
        },
    }
}

func main(){
    conn := poll.Get()
    defer conn.Close()
    ...
}

 
```


### **正则**

regex.MustCompile()

```go
re := regexp.MustCompile("(gopher){2}")
fmt.Println(re.MatchString("gopher")) //false
fmt.Println(re.MatchString("gophergopher")) //true
fmt.Println(re.MatchString("gophergophergopher")) //true

 
```


## Test用例

好处：

+ 减少Reviewer
+ 降低修改代码错误
+ 确保代码品质

优势：

+ 不需要引用其他的，自带Testing套件

+ 平行测试：如果一个测试情景超过1s就可以参考用平行测试


|参数|说明|
| ---- | ---- |
| -v | 显示通过的详细测试信息，默认只显示错误信息以及通过的概要 |
| -c | 生成用于运行测试的可执行文件，但不执行它。这个可执行文件会被命名为“pkg.test”，其中的“pkg”即为被测试代码包的导入路径的最后一个元素的名称。 |
| -i | 只是安装测试所依赖的包，不会编译执行 |
| -o | 指定编译生成的可执行文件名 |
| -bench regexp | 执行相应的 benchmarks，例如 -bench=. |
| -cover | 开启测试覆盖率 |
| -run regexp | 只运行 regexp 匹配的函数，例如 -run=Array 那么就执行包含有 Array 开头的函数 |

### **单元测试**

### **性能测试Benchmark**

```go
go test -v -bench=. .
go test -v -bench=. -run=none .
go test -v -bench=. -run=none -benchmem .

go test -v -bench=. int2str_test.go -benchmem
go test -bench=. -cpu=1 benchmark/goroutine_test.go
 
```



## **Special Tips**

```go
func f1() {
    var a, b struct{}
    print(&a, "\n", &b, "\n") // Prints same address
    fmt.Println(&a == &b)     // Comparison returns false
}

func f2() {
    var a, b struct{}
    fmt.Printf("%p\n%p\n", &a, &b) // Again, same address
    fmt.Println(&a == &b)          // ...but the comparison returns true
}

// 保留n位小数
fun Round(f float64, n int) float64 {
    pow10_n := math.Pow10(n)
    return math.Trunc( (f+0.5/pow10_n)*pow10_n ) / pow10_n
}

// for-range
// 在Go的for…range循环中，Go始终使用值拷贝的方式代替被遍历的元素本身，简单来说，就是for…range中那个value，是一个值拷贝，而不是元素本身.
// 当用&取元素的地址时，实际上取到的是临时变量value的地址。
type Foo struct {
    bar string
}
list := []Foo{
    {"A"},
    {"B"},
    {"C"},
}
list2 := make([]*Foo, len(list))
for i, value := range list {
    list2[i] = &value
}
fmt.Println(list[0], list[1], list[2]) // {A} {B} {C}
fmt.Println(list2[0], list2[1], list2[2]) // &{C} &{C} &{C}

// 过滤不分配
b := a[:0]
for _, x := range a {
    if f(x) {
        b = append(b, x)
    }
}

//获取一个字符串的字节数
utf8.RuneCountInString(str)
len([]rune(str))

//拼接字符串
Strings.Join()

var buffer bytes.Buffer
for{
  if s,ok := fun(); ok{
        buffer.WriteString(s)
  }else{
        break
  }
}
buffer.String()

// 不要忘记停止ticker
ticker := time.NewTicker(1 * time.Second)
defer ticker.Stop()
//在协程周期性的执行一些事情（打印状态日志，输出，计算等等）的时候非常有用。
select{
    case u:=<-ch1:
      ...
    case u:=<-ch2:
      ...
    case <-ticker.C:
      logState(status)
}

//限制处理频率
rate_per_sec = 10
var dur Duration = 1e9/rate_pre_sec
chRate := time.Tick(dur)
for req := range requests{
    <-chRate
    go client.Call("Service.Method", req, ...)
}


// dump goreoutine
go func() {
    sigs := make(chan os.Signal, 1)
    signal.Notify(sigs, syscall.SIGQUIT)
    buf := make([]byte, 1<<20)
    for {
        <-sigs
        stacklen := runtime.Stack(buf, true)
        log.Printf("=== received SIGQUIT ===\n*** goroutine dump...\n%s\n*** end\n", buf[:stacklen])
    }
}()


//函数“实现”接口
type Tester interface {
    Do()
}

type FuncDo func()

func (self FuncDo) Do() {
    self()
}

var t Tester = FuncDo(func() {
    println("Hello, World!")
})

t.Do()


// type只能使用在interface
//panic-recover 对重复解锁互斥锁引发的panic却是无用的

 
```


**Tips**

```go
builtin函数 ：
new 和 make的区别：
new
func new(Type) *Type

make
func make(t Type, size ...IntegerType) Type

new 计算类型大小，分配零值内存，返回指针。适用于数组和结构体
make 会被编译器翻译成具体的创建函数，由其分配内存和初始化成员结构，返回对象而非指针。适用于切片、map 和 channel

 
```

## **Go为数不多的陷阱**

```go
// Go 语言为数不多的陷阱之Slice
arr := []int{1, 2, 3, 4, 5}
fmt.Println(len(arr), cap(arr)) // 5 5
slice := arr[1:2]
fmt.Println(slice, len(slice), cap(slice)) // [2] 1 4
slice = append(slice, 6, 7, 8)
fmt.Println(slice)                  // [2,6,7,8]
fmt.Println(len(slice), cap(slice)) // 4,4
fmt.Println(arr)                    // [1,2,6,7,8]
// 信条: 不对函数slice类型的参数append

 
```

+ [参考 Go语言slice的本质-SliceHeader](https://www.flysnow.org/2018/12/21/golang-sliceheader.html)

```go
// Go 语言为数不多的陷阱之闭包
// 闭包只是带有父函数的上下文（context）的函数
// 闭包对父函数的context只是引用而不复制
// Go 的函数返回值是通过堆栈返回的。return语句不是原子操作。
func f6() (r int) {
    t := 5
    defer func() {
        t = t + 5
    }()
    return t
}
// 这里t虽然是闭包引用，但返回值r不是闭包引用
// 可以拆解为
// r = t
// rval = r
// t = t + 5
// return rval
// 所以输出：5

func f7() (r int) {
  defer func(r int) {
    r = r + 5
  }(r)
  return 1
}
// 因为匿名函数的参数也是r，所以相当于是
// 匿名函数的参数r = r + 5，不影响外部
// 所以输出：1
func main() {
    println(f6()) // 5
    println(f7()) // 1

    func test(){
        x,y := 10,20
        defer func(i int){
            println("defer:",i,y) // y是闭包引用
        }(x) // x被复制,复制的时候x是10
        x+= 10
        y+= 100
        println(x,y)
    }
    // 20 120
    // defer: 10 120
    return
}
// 总结: 1谨记defer和return执行的顺序 2注意返回值是否为闭包引用

 
```

