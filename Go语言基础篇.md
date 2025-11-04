
# Go语言完全学习指南 - 从基础到精通

## 目录
1. [Go语言代码结构](#1-go语言代码结构)
2. [Go语言命名规范](#2-go语言命名规范)
3. [Go语言变量](#3-go语言变量)
4. [Go语言常量](#4-go语言常量)
5. [Go语言运算符](#5-go语言运算符)
6. [Go语言结构体](#6-go语言结构体)
7. [Go语言数组与切片](#7-go语言数组与切片)
8. [Go语言Map](#8-go语言map)
9. [Go语言条件句](#9-go语言条件句)
10. [Go语言循环](#10-go语言循环)
11. [Go语言指针](#11-go语言指针)
12. [Go语言函数](#12-go语言函数)
13. [Go语言方法](#13-go语言方法)
14. [Go语言接口](#14-go语言接口)
15. [Go语言error](#15-go语言error)
16. [Go语言defer](#16-go语言defer)
17. [Go语言异常捕获](#17-go语言异常捕获)
18. [Go语言依赖管理](#18-go语言依赖管理)
19. [Go编码规范](#19-go编码规范)

---

## 1. Go语言代码结构

### 1.1 基本程序结构

Go程序由包（package）组成，每个Go源文件都属于一个包。一个标准的Go程序结构如下：

```go
package main  // 包声明

import (      // 导入依赖包
    "fmt"
    "time"
)

// 全局变量声明
var globalVar string = "I'm global"

// 常量声明
const PI = 3.14159

// 类型定义
type Person struct {
    Name string
    Age  int
}

// 函数定义
func main() {
    fmt.Println("Hello, World!")
}

// 其他函数
func helper() {
    // 辅助函数
}
```

### 1.2 包的组织

**工作区结构**：
```
myproject/
├── go.mod              # 模块定义文件
├── go.sum              # 依赖版本锁定文件
├── main.go             # 主程序入口
├── cmd/                # 命令行程序目录
│   └── server/
│       └── main.go
├── internal/           # 私有代码目录
│   └── config/
│       └── config.go
├── pkg/                # 公共库目录
│   └── utils/
│       └── helper.go
└── api/                # API定义
    └── handler.go
```

### 1.3 包的导入规则

```go
// 单个导入
import "fmt"

// 多个导入（推荐）
import (
    "fmt"
    "os"
    "strings"
)

// 别名导入
import (
    f "fmt"
    . "math"  // 点导入，可直接使用包内函数
    _ "github.com/lib/pq"  // 匿名导入，仅执行init函数
)
```

### 1.4 init函数和main函数

```go
package main

import "fmt"

// init函数在main之前自动执行
func init() {
    fmt.Println("init function executed")
}

// main函数是程序入口
func main() {
    fmt.Println("main function executed")
}

// 输出顺序：
// init function executed
// main function executed
```

### 1.5 可见性规则

Go通过首字母大小写控制可见性：
- **大写字母开头**：公开（exported），可被其他包访问
- **小写字母开头**：私有（unexported），仅包内可访问

```go
package mypackage

// 公开的变量、函数、类型
var PublicVar = "可以被其他包访问"
func PublicFunction() {}
type PublicStruct struct {
    PublicField  string  // 公开字段
    privateField int     // 私有字段
}

// 私有的变量、函数
var privateVar = "仅包内访问"
func privateFunction() {}
```

---

## 2. Go语言命名规范

### 2.1 通用命名规则

**基本原则**：
- 简洁明了，避免冗余
- 使用驼峰命名法（camelCase或PascalCase）
- 不使用下划线
- 缩写词统一大写或小写（如HTTP、URL、ID）

### 2.2 包名命名

```go
// ✅ 好的包名
package user
package http
package json

// ❌ 不好的包名
package user_service  // 不使用下划线
package UserService   // 不使用大写
package utils         // 太宽泛
```

**包名规范**：
- 小写单词，不使用下划线或混合大小写
- 简短且有意义
- 避免使用常见名称（如util、common、base）
- 包名应与目录名一致

### 2.3 文件命名

```bash
# ✅ 推荐
user.go
user_test.go
http_client.go

# ❌ 不推荐
User.go
userService.go
```

### 2.4 变量命名

```go
// 局部变量：简短
for i := 0; i < 10; i++ {}
var s string
var b bytes.Buffer

// 全局变量：描述性更强
var ServerConfig Config
var DefaultTimeout = 30 * time.Second

// 布尔变量：使用is/has/can等前缀
var isActive bool
var hasPermission bool
var canDelete bool
```

### 2.5 常量命名

```go
// 使用驼峰命名，不使用全大写
const MaxConnections = 100
const defaultTimeout = 30
const apiVersion = "v1"

// 枚举类型常量
type Status int

const (
    StatusPending Status = iota
    StatusActive
    StatusInactive
)
```

### 2.6 函数和方法命名

```go
// 动词或动词短语
func GetUser(id int) (*User, error) {}
func SaveData() error {}
func IsValid() bool {}

// getter方法不使用Get前缀
type Person struct {
    name string
}

// ✅ 推荐
func (p *Person) Name() string {
    return p.name
}

// ❌ 不推荐
func (p *Person) GetName() string {
    return p.name
}
```

### 2.7 接口命名

```go
// 单方法接口：方法名+er后缀
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}

// 多方法接口：描述性名称
type ResponseWriter interface {
    Header() Header
    Write([]byte) (int, error)
    WriteHeader(statusCode int)
}
```

### 2.8 类型命名

```go
// 结构体：名词
type User struct {}
type HTTPClient struct {}

// 类型别名：清晰描述用途
type UserID int64
type Email string

// 函数类型
type HandlerFunc func(http.ResponseWriter, *http.Request)
```

---

## 3. Go语言变量

### 3.1 变量声明方式

```go
package main

import "fmt"

func main() {
    // 方式1：标准声明
    var name string = "张三"
    var age int = 25
    
    // 方式2：类型推断
    var city = "北京"  // 自动推断为string
    var count = 100    // 自动推断为int
    
    // 方式3：短变量声明（最常用，仅函数内）
    email := "user@example.com"
    isActive := true
    
    // 方式4：批量声明
    var (
        username string = "admin"
        password string = "secret"
        port     int    = 8080
    )
    
    fmt.Println(name, age, city, count, email, isActive)
}
```

### 3.2 基本数据类型

```go
// 布尔类型
var flag bool = true

// 整数类型
var i8 int8 = 127           // -128 到 127
var i16 int16 = 32767       // -32768 到 32767
var i32 int32 = 2147483647
var i64 int64 = 9223372036854775807
var ui8 uint8 = 255         // 0 到 255
var ui32 uint32 = 4294967295

// int和uint的大小取决于平台（32位或64位）
var i int = 100
var ui uint = 200

// 浮点类型
var f32 float32 = 3.14
var f64 float64 = 3.141592653589793

// 复数类型
var c64 complex64 = 1 + 2i
var c128 complex128 = 2 + 3i

// 字符串类型
var str string = "Hello, 世界"

// 字节类型（uint8的别名）
var b byte = 'A'

// Unicode字符类型（int32的别名）
var r rune = '中'
```

### 3.3 零值

Go中未初始化的变量会被赋予零值：

```go
var i int       // 0
var f float64   // 0.0
var b bool      // false
var s string    // ""（空字符串）
var p *int      // nil
var sl []int    // nil
var m map[string]int  // nil
```

### 3.4 类型转换

Go不支持隐式类型转换，必须显式转换：

```go
var i int = 42
var f float64 = float64(i)  // 必须显式转换
var u uint = uint(f)

// 字符串转换
var str string = string(65)  // "A"
var num int = int(str[0])    // 获取字符ASCII值

// 数值和字符串互转需要使用strconv包
import "strconv"

numStr := strconv.Itoa(123)        // int转string
num, err := strconv.Atoi("456")    // string转int
floatNum, err := strconv.ParseFloat("3.14", 64)
```

### 3.5 多变量赋值

```go
// 同时声明多个变量
var x, y int = 1, 2
var a, b, c = 1, "hello", true

// 短变量声明
i, j := 0, 1

// 交换变量（无需临时变量）
a, b = b, a

// 函数返回多个值
func getUser() (string, int) {
    return "Alice", 30
}

name, age := getUser()
```

### 3.6 作用域

```go
package main

var globalVar = "全局变量"  // 包级作用域

func main() {
    var localVar = "局部变量"  // 函数作用域
    
    if true {
        var blockVar = "块级作用域"  // 块作用域
        fmt.Println(blockVar)
    }
    // fmt.Println(blockVar)  // 错误：blockVar未定义
    
    // 短变量声明的陷阱
    x := 1
    if true {
        x := 2  // 这是一个新变量，遮蔽了外部的x
        fmt.Println(x)  // 输出：2
    }
    fmt.Println(x)  // 输出：1
}
```

---

## 4. Go语言常量

### 4.1 常量定义

```go
package main

// 单个常量
const Pi = 3.14159
const AppName = "MyApp"

// 批量常量声明
const (
    StatusOK       = 200
    StatusNotFound = 404
    StatusError    = 500
)

// 类型化常量
const TypedConst int = 100

// 无类型常量（更灵活）
const UntypedConst = 100
```

### 4.2 iota枚举器

iota是Go的常量计数器，在const关键字出现时重置为0：

```go
// 基本使用
const (
    Monday = iota     // 0
    Tuesday           // 1
    Wednesday         // 2
    Thursday          // 3
    Friday            // 4
    Saturday          // 5
    Sunday            // 6
)

// 跳过某些值
const (
    A = iota  // 0
    _         // 跳过1
    C         // 2
    D         // 3
)

// 位掩码（权限系统）
const (
    ReadPermission   = 1 << iota  // 1 << 0 = 1
    WritePermission                // 1 << 1 = 2
    ExecutePermission              // 1 << 2 = 4
)

// 文件大小常量
const (
    _  = iota  // 跳过0
    KB = 1 << (10 * iota)  // 1 << 10 = 1024
    MB                      // 1 << 20 = 1048576
    GB                      // 1 << 30 = 1073741824
    TB                      // 1 << 40
)

// 复杂表达式
const (
    Apple = iota * 10  // 0
    Banana             // 10
    Cherry             // 20
)
```

### 4.3 常量的应用场景

```go
package config

// 配置常量
const (
    MaxConnections     = 1000
    DefaultPort        = 8080
    TimeoutSeconds     = 30
    MaxRetries         = 3
)

// 状态码
type OrderStatus int

const (
    OrderPending OrderStatus = iota
    OrderProcessing
    OrderShipped
    OrderDelivered
    OrderCancelled
)

// HTTP方法
const (
    MethodGet    = "GET"
    MethodPost   = "POST"
    MethodPut    = "PUT"
    MethodDelete = "DELETE"
)

// 数据库常量
const (
    DBDriver   = "mysql"
    DBHost     = "localhost"
    DBPort     = 3306
    DBName     = "myapp"
)
```

### 4.4 常量vs变量

```go
// ✅ 使用常量的场景
const MaxUsers = 100           // 不会改变的配置
const CompanyName = "TechCorp" // 固定的业务值
const Pi = 3.14159             // 数学常量

// ✅ 使用变量的场景
var currentUsers = 0           // 运行时会改变
var userName string            // 需要动态赋值
var config Config              // 复杂的配置对象

// ❌ 常量的限制
// const arr = []int{1, 2, 3}  // 错误：常量不能是数组
// const m = map[string]int{}  // 错误：常量不能是map
```

---

## 5. Go语言运算符

### 5.1 算术运算符

```go
a, b := 10, 3

sum := a + b      // 13 加法
diff := a - b     // 7  减法
prod := a * b     // 30 乘法
quot := a / b     // 3  除法（整数除法）
rem := a % b      // 1  取模（余数）

// 浮点数除法
var x float64 = 10.0
var y float64 = 3.0
result := x / y   // 3.333...

// 自增自减（只有后置，没有前置）
i := 1
i++  // ✅ 正确
i--  // ✅ 正确
// ++i  // ❌ 错误：Go不支持前置++
```

### 5.2 关系运算符

```go
a, b := 10, 20

a == b  // false 等于
a != b  // true  不等于
a < b   // true  小于
a <= b  // true  小于等于
a > b   // false 大于
a >= b  // false 大于等于

// 字符串比较
str1, str2 := "abc", "abd"
str1 == str2  // false
str1 < str2   // true（按字典序比较）
```

### 5.3 逻辑运算符

```go
x, y := true, false

x && y   // false 逻辑与（AND）
x || y   // true  逻辑或（OR）
!x       // false 逻辑非（NOT）

// 短路求值
func expensive() bool {
    fmt.Println("expensive called")
    return true
}

// && 短路：如果第一个为false，不执行第二个
if false && expensive() {
    // expensive()不会被调用
}

// || 短路：如果第一个为true，不执行第二个
if true || expensive() {
    // expensive()不会被调用
}
```

### 5.4 位运算符

```go
a, b := 60, 13  // 二进制：a=0011 1100, b=0000 1101

a & b   // 12  (0000 1100) 按位与
a | b   // 61  (0011 1101) 按位或
a ^ b   // 49  (0011 0001) 按位异或
a &^ b  // 48  (0011 0000) 按位清除（AND NOT）

a << 2  // 240 (1111 0000) 左移
a >> 2  // 15  (0000 1111) 右移

// 实际应用：位掩码
const (
    Read    = 1 << 0  // 001
    Write   = 1 << 1  // 010
    Execute = 1 << 2  // 100
)

permissions := Read | Write  // 011
hasRead := permissions & Read != 0      // true
hasExecute := permissions & Execute != 0 // false
```

### 5.5 赋值运算符

```go
x := 10

x += 5   // x = x + 5  (15)
x -= 3   // x = x - 3  (12)
x *= 2   // x = x * 2  (24)
x /= 4   // x = x / 4  (6)
x %= 4   // x = x % 4  (2)

// 位运算赋值
x &= 3   // x = x & 3
x |= 4   // x = x | 4
x ^= 2   // x = x ^ 2
x <<= 1  // x = x << 1
x >>= 1  // x = x >> 1
```

### 5.6 其他运算符

```go
// 取地址运算符 &
var num int = 42
ptr := &num  // ptr是指向num的指针

// 解引用运算符 *
value := *ptr  // 获取指针指向的值

// 类型断言（接口类型转换）
var i interface{} = "hello"
str, ok := i.(string)  // 类型断言
if ok {
    fmt.Println(str)
}

// 通道运算符 <-
ch := make(chan int)
ch <- 42    // 发送数据到通道
val := <-ch // 从通道接收数据
```

### 5.7 运算符优先级

从高到低：

```go
// 1. ()  []  .  -> (最高优先级)
// 2. !  ~  ++  --  + - (一元)  *  &  (type)
// 3. *  /  %
// 4. +  -
// 5. <<  >>
// 6. <  <=  >  >=
// 7. ==  !=
// 8. &
// 9. ^
// 10. |
// 11. &&
// 12. ||
// 13. = += -= 等赋值运算符 (最低优先级)

// 示例
result := 2 + 3 * 4      // 14 (不是20)
result = (2 + 3) * 4     // 20
result = true || false && false  // true
```

---

## 6. Go语言结构体

### 6.1 结构体定义

```go
// 基本结构体
type Person struct {
    Name string
    Age  int
    Email string
}

// 嵌套结构体
type Address struct {
    Street  string
    City    string
    ZipCode string
}

type Employee struct {
    Person           // 匿名字段（嵌入）
    EmployeeID int
    Department string
    Address    Address  // 命名字段
}

// 空结构体（不占用内存）
type Empty struct{}
```

### 6.2 结构体初始化

```go
// 方式1：字面量初始化（推荐）
p1 := Person{
    Name:  "Alice",
    Age:   30,
    Email: "alice@example.com",
}

// 方式2：按顺序初始化（不推荐，不清晰）
p2 := Person{"Bob", 25, "bob@example.com"}

// 方式3：部分初始化（未指定字段为零值）
p3 := Person{Name: "Charlie"}

// 方式4：new函数（返回指针）
p4 := new(Person)
p4.Name = "David"

// 方式5：取地址
p5 := &Person{
    Name: "Eve",
    Age:  28,
}
```

### 6.3 字段访问和修改

```go
person := Person{Name: "Alice", Age: 30}

// 访问字段
fmt.Println(person.Name)  // Alice

// 修改字段
person.Age = 31

// 指针访问（自动解引用）
ptr := &person
ptr.Name = "Alice Smith"  // 等同于 (*ptr).Name
fmt.Println(ptr.Age)       // 31
```

### 6.4 匿名字段和嵌入

```go
type Animal struct {
    Name string
    Age  int
}

func (a Animal) Speak() {
    fmt.Printf("%s is speaking\n", a.Name)
}

// 嵌入Animal（继承字段和方法）
type Dog struct {
    Animal  // 匿名字段
    Breed string
}

dog := Dog{
    Animal: Animal{Name: "Buddy", Age: 3},
    Breed:  "Golden Retriever",
}

// 可以直接访问嵌入类型的字段和方法
fmt.Println(dog.Name)   // Buddy（提升字段）
dog.Speak()             // Buddy is speaking（提升方法）
fmt.Println(dog.Breed)  // Golden Retriever
```

### 6.5 方法定义

```go
type Rectangle struct {
    Width  float64
    Height float64
}

// 值接收者方法
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// 指针接收者方法（可修改结构体）
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

// 使用
rect := Rectangle{Width: 10, Height: 5}
fmt.Println(rect.Area())  // 50

rect.Scale(2)
fmt.Println(rect.Area())  // 200
```

### 6.6 结构体标签

标签常用于JSON、XML序列化和数据库映射：

```go
import (
    "encoding/json"
    "fmt"
)

type User struct {
    ID        int    `json:"id"`
    Name      string `json:"name"`
    Email     string `json:"email,omitempty"`  // 空值时忽略
    Password  string `json:"-"`                 // 不序列化
    CreatedAt int64  `json:"created_at,string"` // 转为字符串
}

user := User{
    ID:    1,
    Name:  "Alice",
    Email: "",
    Password: "secret",
}

// 序列化为JSON
jsonData, _ := json.Marshal(user)
fmt.Println(string(jsonData))
// 输出：{"id":1,"name":"Alice"}

// 反序列化
var newUser User
json.Unmarshal(jsonData, &newUser)
```

### 6.7 结构体比较

```go
type Point struct {
    X, Y int
}

p1 := Point{1, 2}
p2 := Point{1, 2}
p3 := Point{2, 3}

fmt.Println(p1 == p2)  // true
fmt.Println(p1 == p3)  // false

// 注意：包含切片、map、函数的结构体不可比较
type Data struct {
    Items []int
}

// d1 == d2  // 编译错误
```

### 6.8 结构体作为参数

```go
// 值传递（复制整个结构体）
func printPerson(p Person) {
    fmt.Println(p.Name)
}

// 指针传递（高效，可修改原结构体）
func updateAge(p *Person, newAge int) {
    p.Age = newAge
}

person := Person{Name: "Alice", Age: 30}
printPerson(person)      // 值传递
updateAge(&person, 31)   // 指针传递
```

---

## 7. Go语言数组与切片

### 7.1 数组

数组是固定长度的相同类型元素序列：

```go
// 声明和初始化
var arr1 [5]int                    // [0 0 0 0 0]
arr2 := [5]int{1, 2, 3, 4, 5}      // [1 2 3 4 5]
arr3 := [...]int{1, 2, 3}          // 自动推断长度为3
arr4 := [5]int{1: 10, 3: 30}       // [0 10 0 30 0] 指定索引

// 多维数组
matrix := [3][3]int{
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9},
}

// 访问和修改
arr2[0] = 100
fmt.Println(arr2[0])  // 100

// 遍历
for i := 0; i < len(arr2); i++ {
    fmt.Println(arr2[i])
}

// range遍历
for index, value := range arr2 {
    fmt.Printf("index: %d, value: %d\n", index, value)
}

// 数组是值类型
arr5 := arr2  // 复制整个数组
arr5[0] = 999
fmt.Println(arr2[0])  // 100（arr2未改变）
```

### 7.2 切片（Slice）

切片是动态数组，是对底层数组的引用：

```go
// 创建切片
slice1 := []int{1, 2, 3, 4, 5}
slice2 := make([]int, 5)       // 长度为5，容量为5
slice3 := make([]int, 3, 10)   // 长度为3，容量为10

// 从数组创建切片
arr := [5]int{1, 2, 3, 4, 5}
slice4 := arr[1:4]  // [2 3 4]（包含索引1，不包含4）
slice5 := arr[:3]   // [1 2 3]（从开始到索引3）
slice6 := arr[2:]   // [3 4 5]（从索引2到结束）
slice7 := arr[:]    // [1 2 3 4 5]（完整切片）

// nil切片
var slice8 []int  // nil切片，len=0, cap=0
```

### 7.3 切片操作

```go
// 长度和容量
slice := []int{1, 2, 3, 4, 5}
fmt.Println(len(slice))  // 5（长度）
fmt.Println(cap(slice))  // 5（容量）

// append添加元素
slice = append(slice, 6)           // [1 2 3 4 5 6]
slice = append(slice, 7, 8, 9)     // [1 2 3 4 5 6 7 8 9]
slice = append(slice, []int{10, 11}...)  // 追加另一个切片

// copy复制切片
source := []int{1, 2, 3}
dest := make([]int, len(source))
copy(dest, source)  // 复制source到dest

// 删除元素
slice = []int{1, 2, 3, 4, 5}
index := 2
slice = append(slice[:index], slice[index+1:]...)  // 删除索引2：[1 2 4 5]

// 插入元素
slice = []int{1, 2, 4, 5}
index = 2
value := 3
slice = append(slice[:index], append([]int{value}, slice[index:]...)...)
// [1 2 3 4 5]

// 切片截取
sub := slice[1:4]  // [2 3 4]
```

### 7.4 切片内部原理

```go
// 切片结构包含：指针、长度、容量
type slice struct {
    array unsafe.Pointer  // 指向底层数组
    len   int            // 当前长度
    cap   int            // 容量
}

// 切片扩容机制
s := make([]int, 0)
fmt.Printf("len=%d cap=%d\n", len(s), cap(s))  // len=0 cap=0

for i := 0; i < 10; i++ {
    s = append(s, i)
    fmt.Printf("len=%d cap=%d\n", len(s), cap(s))
}
// 容量增长：0 -> 1 -> 2 -> 4 -> 8 -> 16
// 当容量不足时，容量会扩展为原来的2倍（小容量）或1.25倍（大容量）
```

### 7.5 切片的注意事项

```go
// 1. 切片是引用类型
original := []int{1, 2, 3}
copy := original
copy[0] = 999
fmt.Println(original[0])  // 999（原切片也被修改）

// 2. 切片共享底层数组
arr := [5]int{1, 2, 3, 4, 5}
s1 := arr[1:3]  // [2 3]
s2 := arr[2:4]  // [3 4]
s1[1] = 999     // 修改s1影响s2和arr
fmt.Println(s2[0])  // 999

// 3. append可能导致重新分配
slice := make([]int, 3, 3)
newSlice := append(slice, 4)  // 容量不足，分配新数组
newSlice[0] = 999
fmt.Println(slice[0])  // 0（不受影响）
```

### 7.6 数组vs切片选择

```go
// 使用数组的场景：
// - 长度固定且已知
// - 需要值传递
// - 性能敏感且大小小

// 使用切片的场景：
// - 长度可变
// - 需要灵活操作（追加、删除）
// - 函数参数传递（大多数情况）

// 实例
func processArray(arr [1000]int) {
    // 复制1000个元素，开销大
}

func processSlice(s []int) {
    // 只传递切片结构（24字节），高效
}
```

---

## 8. Go语言Map

### 8.1 Map基础

Map是键值对的无序集合：

```go
// 声明和初始化
var m1 map[string]int              // nil map，不能直接赋值
m2 := make(map[string]int)         // 空map
m3 := map[string]int{              // 字面量初始化
    "apple":  1,
    "banana": 2,
    "orange": 3,
}

// 创建带容量的map（性能优化）
m4 := make(map[string]int, 100)
```

### 8.2 Map操作

```go
// 添加/修改元素
scores := make(map[string]int)
scores["Alice"] = 95
scores["Bob"] = 87
scores["Alice"] = 98  // 更新已存在的key

// 获取元素
score := scores["Alice"]  // 98

// 检查key是否存在（推荐方式）
value, ok := scores["Charlie"]
if ok {
    fmt.Println("Charlie's score:", value)
} else {
    fmt.Println("Charlie not found")
}

// 删除元素
delete(scores, "Bob")

// 获取map长度
fmt.Println(len(scores))  // 1
```

### 8.3 Map遍历

```go
scores := map[string]int{
    "Alice":   95,
    "Bob":     87,
    "Charlie": 92,
}

// 遍历key和value
for name, score := range scores {
    fmt.Printf("%s: %d\n", name, score)
}

// 只遍历key
for name := range scores {
    fmt.Println(name)
}

// 只遍历value
for _, score := range scores {
    fmt.Println(score)
}

// 注意：map遍历顺序是随机的！
// 需要有序遍历，先对key排序
keys := make([]string, 0, len(scores))
for k := range scores {
    keys = append(keys, k)
}
sort.Strings(keys)
for _, k := range keys {
    fmt.Printf("%s: %d\n", k, scores[k])
}
```

### 8.4 复杂Map类型

```go
// Map嵌套
students := map[string]map[string]int{
    "Alice": {
        "Math":    95,
        "English": 88,
    },
    "Bob": {
        "Math":    87,
        "English": 92,
    },
}

// 访问嵌套值
aliceMath := students["Alice"]["Math"]

// 结构体作为value
type Student struct {
    Name  string
    Age   int
    Score int
}

studentMap := map[int]Student{
    1: {Name: "Alice", Age: 20, Score: 95},
    2: {Name: "Bob", Age: 21, Score: 87},
}

// 切片作为value
words := map[string][]string{
    "colors": {"red", "blue", "green"},
    "fruits": {"apple", "banana", "orange"},
}
```

### 8.5 Map的并发安全

普通map不是并发安全的，需要使用sync.Map：

```go
import "sync"

// 方式1：使用互斥锁保护
type SafeMap struct {
    mu   sync.RWMutex
    data map[string]int
}

func (sm *SafeMap) Set(key string, value int) {
    sm.mu.Lock()
    defer sm.mu.Unlock()
    sm.data[key] = value
}

func (sm *SafeMap) Get(key string) (int, bool) {
    sm.mu.RLock()
    defer sm.mu.RUnlock()
    value, ok := sm.data[key]
    return value, ok
}

// 方式2：使用sync.Map（适合读多写少场景）
var syncMap sync.Map

syncMap.Store("key", "value")         // 存储
value, ok := syncMap.Load("key")      // 读取
syncMap.Delete("key")                 // 删除
syncMap.Range(func(k, v interface{}) bool {
    fmt.Println(k, v)
    return true  // 返回false停止遍历
})
```

### 8.6 Map实战技巧

```go
// 1. 统计词频
text := "apple banana apple orange banana apple"
wordCount := make(map[string]int)
for _, word := range strings.Fields(text) {
    wordCount[word]++
}
// 结果：map[apple:3 banana:2 orange:1]

// 2. 去重
nums := []int{1, 2, 2, 3, 4, 4, 5}
unique := make(map[int]bool)
for _, num := range nums {
    unique[num] = true
}
result := make([]int, 0, len(unique))
for num := range unique {
    result = append(result, num)
}

// 3. Map作为Set使用
set := make(map[string]struct{})  // struct{}不占用内存
set["item1"] = struct{}{}
set["item2"] = struct{}{}

if _, exists := set["item1"]; exists {
    fmt.Println("item1 exists")
}

// 4. 分组
type Person struct {
    Name string
    Age  int
}

people := []Person{
    {"Alice", 25},
    {"Bob", 30},
    {"Charlie", 25},
}

ageGroups := make(map[int][]Person)
for _, p := range people {
    ageGroups[p.Age] = append(ageGroups[p.Age], p)
}
```

---

## 9. Go语言条件句

### 9.1 if语句

```go
// 基本if语句
age := 18
if age >= 18 {
    fmt.Println("成年人")
}

// if-else
score := 85
if score >= 90 {
    fmt.Println("优秀")
} else {
    fmt.Println("良好")
}

// if-else if-else
if score >= 90 {
    fmt.Println("A")
} else if score >= 80 {
    fmt.Println("B")
} else if score >= 70 {
    fmt.Println("C")
} else {
    fmt.Println("D")
}

// if语句的初始化（变量作用域仅在if块内）
if err := doSomething(); err != nil {
    fmt.Println("错误:", err)
    return
}

// 实际应用
if value, ok := myMap["key"]; ok {
    fmt.Println("找到值:", value)
} else {
    fmt.Println("未找到")
}
```

### 9.2 switch语句

```go
// 基本switch
day := "Monday"
switch day {
case "Monday":
    fmt.Println("星期一")
case "Tuesday":
    fmt.Println("星期二")
case "Wednesday":
    fmt.Println("星期三")
default:
    fmt.Println("其他")
}

// 多条件case
switch day {
case "Saturday", "Sunday":
    fmt.Println("周末")
case "Monday", "Tuesday", "Wednesday", "Thursday", "Friday":
    fmt.Println("工作日")
}

// 无表达式switch（替代if-else链）
score := 85
switch {
case score >= 90:
    fmt.Println("A")
case score >= 80:
    fmt.Println("B")
case score >= 70:
    fmt.Println("C")
default:
    fmt.Println("D")
}

// switch初始化语句
switch num := getNumber(); {
case num > 0:
    fmt.Println("正数")
case num < 0:
    fmt.Println("负数")
default:
    fmt.Println("零")
}

// fallthrough（强制执行下一个case）
switch num := 2; num {
case 1:
    fmt.Println("一")
case 2:
    fmt.Println("二")
    fallthrough  // 继续执行下一个case
case 3:
    fmt.Println("三")
}
// 输出：二 三
```

### 9.3 类型switch

```go
// 类型断言switch
func checkType(i interface{}) {
    switch v := i.(type) {
    case int:
        fmt.Printf("整数: %d\n", v)
    case string:
        fmt.Printf("字符串: %s\n", v)
    case bool:
        fmt.Printf("布尔值: %t\n", v)
    case []int:
        fmt.Printf("整数切片: %v\n", v)
    default:
        fmt.Printf("未知类型: %T\n", v)
    }
}

checkType(42)        // 整数: 42
checkType("hello")   // 字符串: hello
checkType(true)      // 布尔值: true
```

### 9.4 条件语句最佳实践

```go
// 1. 早返回模式（减少嵌套）
// ❌ 不好的写法
func processData(data []int) error {
    if len(data) > 0 {
        if data[0] != 0 {
            // 处理逻辑
            return nil
        } else {
            return errors.New("首元素为0")
        }
    } else {
        return errors.New("数据为空")
    }
}

// ✅ 好的写法
func processDataBetter(data []int) error {
    if len(data) == 0 {
        return errors.New("数据为空")
    }
    if data[0] == 0 {
        return errors.New("首元素为0")
    }
    // 处理逻辑
    return nil
}

// 2. 避免不必要的else
// ❌ 不好
if condition {
    return value1
} else {
    return value2
}

// ✅ 好
if condition {
    return value1
}
return value2

// 3. 使用switch替代复杂if-else
// ❌ 不好
if status == "pending" {
    // ...
} else if status == "processing" {
    // ...
} else if status == "completed" {
    // ...
}

// ✅ 好
switch status {
case "pending":
    // ...
case "processing":
    // ...
case "completed":
    // ...
}
```

---

## 10. Go语言循环

### 10.1 for循环基本形式

Go只有for一种循环关键字，但有多种使用方式：

```go
// 1. 传统三段式for循环
for i := 0; i < 10; i++ {
    fmt.Println(i)
}

// 2. 类似while的for循环
i := 0
for i < 10 {
    fmt.Println(i)
    i++
}

// 3. 无限循环
for {
    // 无限循环，需要用break跳出
    if condition {
        break
    }
}

// 4. 省略初始化和后置语句
i := 0
for ; i < 10; {
    fmt.Println(i)
    i++
}
```

### 10.2 range遍历

```go
// 遍历数组/切片
nums := []int{10, 20, 30, 40, 50}

// 获取索引和值
for index, value := range nums {
    fmt.Printf("index: %d, value: %d\n", index, value)
}

// 只要索引
for index := range nums {
    fmt.Println(index)
}

// 只要值（使用_忽略索引）
for _, value := range nums {
    fmt.Println(value)
}

// 遍历Map
scores := map[string]int{
    "Alice": 95,
    "Bob":   87,
}
for name, score := range scores {
    fmt.Printf("%s: %d\n", name, score)
}

// 遍历字符串（按rune遍历，支持Unicode）
str := "Hello, 世界"
for index, char := range str {
    fmt.Printf("index: %d, char: %c (Unicode: %U)\n", index, char, char)
}

// 遍历通道
ch := make(chan int, 3)
ch <- 1
ch <- 2
ch <- 3
close(ch)

for value := range ch {
    fmt.Println(value)
}
```

### 10.3 循环控制语句

```go
// break：跳出循环
for i := 0; i < 10; i++ {
    if i == 5 {
        break  // 跳出整个循环
    }
    fmt.Println(i)  // 输出 0 1 2 3 4
}

// continue：跳过当前迭代
for i := 0; i < 10; i++ {
    if i%2 == 0 {
        continue  // 跳过偶数
    }
    fmt.Println(i)  // 输出 1 3 5 7 9
}

// 标签break/continue（用于嵌套循环）
OuterLoop:
for i := 0; i < 3; i++ {
    for j := 0; j < 3; j++ {
        if i == 1 && j == 1 {
            break OuterLoop  // 跳出外层循环
        }
        fmt.Printf("i=%d, j=%d\n", i, j)
    }
}

// goto（不推荐使用，但有时有用）
i := 0
Loop:
    if i < 5 {
        fmt.Println(i)
        i++
        goto Loop
    }
```

### 10.4 循环实战模式

```go
// 1. 反向遍历
nums := []int{1, 2, 3, 4, 5}
for i := len(nums) - 1; i >= 0; i-- {
    fmt.Println(nums[i])  // 5 4 3 2 1
}

// 2. 步进遍历
for i := 0; i < 10; i += 2 {
    fmt.Println(i)  // 0 2 4 6 8
}

// 3. 安全删除切片元素
nums = []int{1, 2, 3, 4, 5}
filtered := nums[:0]  // 重用底层数组
for _, num := range nums {
    if num%2 != 0 {  // 保留奇数
        filtered = append(filtered, num)
    }
}
// filtered = [1 3 5]

// 4. 并发处理（配合goroutine）
items := []int{1, 2, 3, 4, 5}
var wg sync.WaitGroup

for _, item := range items {
    wg.Add(1)
    go func(n int) {
        defer wg.Done()
        // 处理item
        fmt.Println(n)
    }(item)  // 注意：必须传递副本
}
wg.Wait()

// 5. 限制循环次数（超时控制）
timeout := time.After(5 * time.Second)
ticker := time.NewTicker(1 * time.Second)
defer ticker.Stop()

for {
    select {
    case <-timeout:
        fmt.Println("超时")
        return
    case t := <-ticker.C:
        fmt.Println("Tick at", t)
    }
}
```

### 10.5 循环性能优化

```go
// 1. 避免在循环中重复计算
// ❌ 不好
for i := 0; i < len(slice); i++ {  // 每次都调用len()
    // ...
}

// ✅ 好
length := len(slice)
for i := 0; i < length; i++ {
    // ...
}

// 2. range的值是副本
type BigStruct struct {
    Data [1000]int
}

bigSlice := make([]BigStruct, 100)

// ❌ 不好：每次迭代复制1000个int
for _, item := range bigSlice {
    // item是副本
    _ = item
}

// ✅ 好：使用索引访问
for i := range bigSlice {
    item := &bigSlice[i]  // 使用指针
    _ = item
}

// 3. 闭包陷阱
funcs := make([]func(), 0)
for i := 0; i < 3; i++ {
    // ❌ 错误：所有函数都引用同一个i
    funcs = append(funcs, func() {
        fmt.Println(i)  // 输出都是3
    })
}

// ✅ 正确：传递副本
for i := 0; i < 3; i++ {
    i := i  // 创建新变量
    funcs = append(funcs, func() {
        fmt.Println(i)  // 输出 0 1 2
    })
}
```

---

## 11. Go语言指针

### 11.1 指针基础

```go
// 声明和初始化
var num int = 42
var ptr *int = &num  // ptr是指向num的指针

// 简写
num2 := 100
ptr2 := &num2

// 获取指针指向的值（解引用）
value := *ptr  // 42

// 修改指针指向的值
*ptr = 99
fmt.Println(num)  // 99

// nil指针
var p *int  // p = nil
// *p = 10  // panic: 运行时错误

// 检查nil
if p != nil {
    *p = 10
}
```

### 11.2 指针作为函数参数

```go
// 值传递：复制整个值
func incrementValue(n int) {
    n++  // 只修改副本
}

// 指针传递：可以修改原值
func incrementPointer(n *int) {
    *n++  // 修改原值
}

num := 10
incrementValue(num)
fmt.Println(num)  // 10（未改变）

incrementPointer(&num)
fmt.Println(num)  // 11（已改变）

// 大结构体使用指针提升性能
type LargeStruct struct {
    Data [10000]int
}

// ❌ 不好：复制整个结构体（40KB）
func processValue(s LargeStruct) {
    // ...
}

// ✅ 好：只传递指针（8字节）
func processPointer(s *LargeStruct) {
    // ...
}
```

### 11.3 指针与结构体

```go
type Person struct {
    Name string
    Age  int
}

// 创建结构体指针
p1 := &Person{Name: "Alice", Age: 30}
p2 := new(Person)  // 返回指针，字段为零值

// 访问字段（自动解引用）
fmt.Println(p1.Name)   // 等同于 (*p1).Name
p1.Age = 31

// 方法接收者
// 值接收者
func (p Person) PrintValue() {
    fmt.Println(p.Name)
    p.Age++  // 修改的是副本
}

// 指针接收者（推荐）
func (p *Person) PrintPointer() {
    fmt.Println(p.Name)
    p.Age++  // 修改原值
}

person := Person{Name: "Bob", Age: 25}
person.PrintValue()
fmt.Println(person.Age)  // 25（未改变）

person.PrintPointer()
fmt.Println(person.Age)  // 26（已改变）
```

### 11.4 指针与切片、Map

```go
// 切片本身是引用类型，包含指向底层数组的指针
slice := []int{1, 2, 3}

// 函数可以直接修改切片内容
func modifySlice(s []int) {
    s[0] = 999  // 修改生效
}

modifySlice(slice)
fmt.Println(slice[0])  // 999

// 但append可能重新分配，需要返回
func appendSlice(s []int) []int {
    return append(s, 4)  // 可能重新分配
}

slice = appendSlice(slice)

// Map也是引用类型
m := make(map[string]int)
func modifyMap(m map[string]int) {
    m["key"] = 100  // 直接修改原map
}

modifyMap(m)
fmt.Println(m["key"])  // 100
```

### 11.5 指针数组和数组指针

```go
// 指针数组：数组的元素是指针
a, b, c := 1, 2, 3
ptrArray := [3]*int{&a, &b, &c}
*ptrArray[0] = 999
fmt.Println(a)  // 999

// 数组指针：指向数组的指针
arr := [3]int{1, 2, 3}
arrPtr := &arr
arrPtr[0] = 999  // 自动解引用
fmt.Println(arr[0])  // 999
```

### 11.6 指针的注意事项

```go
// 1. 避免野指针
var p *int
// *p = 10  // panic!

// 2. 逃逸分析：局部变量可能分配到堆上
func createPointer() *int {
    x := 42
    return &x  // x逃逸到堆上，不会被回收
}

// 3. 指针比较
a := 10
p1 := &a
p2 := &a
fmt.Println(p1 == p2)  // true（指向同一地址）

b := 10
p3 := &b
fmt.Println(p1 == p3)  // false（不同地址）

// 4. 不能对常量取地址
// ptr := &10  // 错误

// 5. 指针运算在Go中不支持（不像C）
arr := [3]int{1, 2, 3}
ptr := &arr[0]
// ptr++  // 错误：Go不支持指针运算
```

---

## 12. Go语言函数

### 12.1 函数定义

```go
// 基本函数
func add(a int, b int) int {
    return a + b
}

// 相同类型参数简写
func multiply(a, b, c int) int {
    return a * b * c
}

// 无参数无返回值
func sayHello() {
    fmt.Println("Hello!")
}

// 多返回值
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("除数不能为0")
    }
    return a / b, nil
}

// 命名返回值
func swap(a, b string) (x, y string) {
    x = b
    y = a
    return  // 自动返回x和y
}
```

### 12.2 可变参数

```go
// 可变参数函数
func sum(nums ...int) int {
    total := 0
    for _, num := range nums {
        total += num
    }
    return total
}

// 调用
result := sum(1, 2, 3, 4, 5)  // 15

// 传递切片
numbers := []int{1, 2, 3}
result = sum(numbers...)  // 展开切片

// 混合参数（可变参数必须在最后）
func printf(format string, args ...interface{}) {
    fmt.Printf(format, args...)
}

printf("Name: %s, Age: %d\n", "Alice", 30)
```

### 12.3 函数作为值

```go
// 函数赋值给变量
add := func(a, b int) int {
    return a + b
}
result := add(3, 4)  // 7

// 函数作为参数
func apply(f func(int, int) int, a, b int) int {
    return f(a, b)
}

result = apply(add, 10, 20)  // 30

// 函数作为返回值
func makeAdder(x int) func(int) int {
    return func(y int) int {
        return x + y
    }
}

add5 := makeAdder(5)
fmt.Println(add5(3))  // 8
fmt.Println(add5(10)) // 15
```

### 12.4 闭包

```go
// 闭包：函数+引用环境
func counter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

c1 := counter()
fmt.Println(c1())  // 1
fmt.Println(c1())  // 2
fmt.Println(c1())  // 3

c2 := counter()
fmt.Println(c2())  // 1（独立的闭包）

// 实际应用：装饰器模式
func logger(f func(string)) func(string) {
    return func(s string) {
        fmt.Println("开始执行")
        f(s)
        fmt.Println("执行完成")
    }
}

original := func(msg string) {
    fmt.Println("处理:", msg)
}

wrapped := logger(original)
wrapped("test")
// 输出：
// 开始执行
// 处理: test
// 执行完成
```

### 12.5 递归函数

```go
// 阶乘
func factorial(n int) int {
    if n <= 1 {
        return 1
    }
    return n * factorial(n-1)
}

fmt.Println(factorial(5))  // 120

// 斐波那契数列
func fibonacci(n int) int {
    if n <= 1 {
        return n
    }
    return fibonacci(n-1) + fibonacci(n-2)
}

// 优化：使用闭包缓存
func fibonacciMemo() func(int) int {
    cache := make(map[int]int)
    var fib func(int) int
    fib = func(n int) int {
        if n <= 1 {
            return n
        }
        if val, ok := cache[n]; ok {
            return val
        }
        cache[n] = fib(n-1) + fib(n-2)
        return cache[n]
    }
    return fib
}

fib := fibonacciMemo()
fmt.Println(fib(50))  // 快速计算
```

### 12.6 延迟调用（defer）

```go
// defer在函数返回前执行
func example() {
    defer fmt.Println("world")
    fmt.Println("hello")
}
// 输出：hello world

// 多个defer按LIFO顺序执行
func multiDefer() {
    defer fmt.Println("1")
    defer fmt.Println("2")
    defer fmt.Println("3")
}
// 输出：3 2 1

// 常用场景：资源清理
func readFile(filename string) error {
    file, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer file.Close()  // 确保文件关闭
    
    // 读取文件...
    return nil
}
```

### 12.7 匿名函数和立即执行

```go
// 匿名函数
func() {
    fmt.Println("匿名函数")
}()

// 带参数的立即执行函数
func(name string) {
    fmt.Println("Hello,", name)
}("Alice")

// 用于隔离作用域
func processData() {
    // 局部作用域
    func() {
        temp := "临时变量"
        fmt.Println(temp)
    }()
    // temp在这里不可见
}
```

### 12.8 函数类型和接口

```go
// 定义函数类型
type Operation func(int, int) int

func add(a, b int) int { return a + b }
func subtract(a, b int) int { return a - b }

// 使用函数类型
func calculate(op Operation, a, b int) int {
    return op(a, b)
}

result := calculate(add, 10, 5)      // 15
result = calculate(subtract, 10, 5)  // 5

// 函数实现接口
type Handler interface {
    ServeHTTP(w http.ResponseWriter, r *http.Request)
}

type HandlerFunc func(http.ResponseWriter, *http.Request)

// HandlerFunc实现Handler接口
func (f HandlerFunc) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    f(w, r)
}
```

---

## 13. Go语言方法

### 13.1 方法定义

方法是绑定到特定类型的函数：

```go
type Rectangle struct {
    Width  float64
    Height float64
}

// 值接收者方法
func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// 指针接收者方法
func (r *Rectangle) Scale(factor float64) {
    r.Width *= factor
    r.Height *= factor
}

// 使用
rect := Rectangle{Width: 10, Height: 5}
fmt.Println(rect.Area())  // 50

rect.Scale(2)
fmt.Println(rect.Area())  // 200
```

### 13.2 值接收者vs指针接收者

```go
type Counter struct {
    count int
}

// 值接收者：不会修改原对象
func (c Counter) IncrementValue() {
    c.count++  // 修改的是副本
}

// 指针接收者：会修改原对象
func (c *Counter) IncrementPointer() {
    c.count++  // 修改原对象
}

counter := Counter{count: 0}
counter.IncrementValue()
fmt.Println(counter.count)  // 0（未改变）

counter.IncrementPointer()
fmt.Println(counter.count)  // 1（已改变）

// Go自动转换
counter.IncrementPointer()  // 等同于 (&counter).IncrementPointer()
pCounter := &counter
pCounter.IncrementValue()   // 等同于 (*pCounter).IncrementValue()
```

### 13.3 选择接收者类型的规则

```go
// 使用指针接收者的情况：
// 1. 需要修改接收者
type Account struct {
    balance float64
}

func (a *Account) Deposit(amount float64) {
    a.balance += amount  // 需要修改
}

// 2. 接收者是大型结构体（避免复制）
type LargeStruct struct {
    data [10000]int
}

func (l *LargeStruct) Process() {
    // 避免复制10000个int
}

// 3. 保持一致性：如果有一个方法用指针接收者，其他方法也应该用
type User struct {
    name string
    age  int
}

func (u *User) SetName(name string) { u.name = name }
func (u *User) SetAge(age int) { u.age = age }
func (u *User) GetName() string { return u.name }  // 也用指针保持一致

// 使用值接收者的情况：
// 1. 不需要修改接收者
// 2. 接收者是小型不可变类型（如time.Time）
// 3. 接收者是基本类型、切片或小型数组
```

### 13.4 方法集

```go
type Number int

func (n Number) Print() {
    fmt.Println(n)
}

func (n *Number) Increment() {
    *n++
}

// 类型T的方法集：只包含值接收者方法
// 类型*T的方法集：包含值接收者和指针接收者方法

var num Number = 10
num.Print()       // ✅ 值调用值接收者
num.Increment()   // ✅ 值可以自动取址调用指针接收者

var pNum *Number = &num
pNum.Print()      // ✅ 指针可以自动解引用调用值接收者
pNum.Increment()  // ✅ 指针调用指针接收者
```

### 13.5 任意类型的方法

```go
// 可以为任何自定义类型添加方法
type MyInt int

func (m MyInt) Double() MyInt {
    return m * 2
}

var num MyInt = 5
fmt.Println(num.Double())  // 10

// 为切片类型添加方法
type IntSlice []int

func (is IntSlice) Sum() int {
    sum := 0
    for _, v := range is {
        sum += v
    }
    return sum
}

nums := IntSlice{1, 2, 3, 4, 5}
fmt.Println(nums.Sum())  // 15

// 不能为内置类型添加方法
// func (i int) Double() int { ... }  // 错误

// 不能为其他包的类型添加方法
// func (t time.Time) MyMethod() { ... }  // 错误
```

### 13.6 方法表达式和方法值

```go
type Person struct {
    Name string
}

func (p Person) Greet() {
    fmt.Println("Hello, I'm", p.Name)
}

// 方法值（绑定接收者）
p := Person{Name: "Alice"}
greet := p.Greet  // 方法值
greet()           // Hello, I'm Alice

// 方法表达式（未绑定接收者）
greetFunc := Person.Greet  // 方法表达式
greetFunc(p)               // 需要显式传递接收者
greetFunc(Person{Name: "Bob"})  // Hello, I'm Bob
```

### 13.7 组合vs继承

Go使用组合而非继承：

```go
// 基础类型
type Engine struct {
    Power int
}

func (e Engine) Start() {
    fmt.Println("Engine started")
}

// 组合Engine
type Car struct {
    Engine  // 嵌入
    Brand string
}

// Car自动获得Engine的方法
car := Car{
    Engine: Engine{Power: 200},
    Brand:  "Tesla",
}

car.Start()  // Engine started（提升的方法）
fmt.Println(car.Power)  // 200（提升的字段）

// 方法重写
func (c Car) Start() {
    fmt.Println("Car starting...")
    c.Engine.Start()  // 调用嵌入类型的方法
}

car.Start()
// 输出：
// Car starting...
// Engine started
```

---

## 14. Go语言接口

### 14.1 接口定义和实现

```go
// 定义接口
type Shape interface {
    Area() float64
    Perimeter() float64
}

// 实现接口（隐式实现，无需声明）
type Rectangle struct {
    Width, Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

func (r Rectangle) Perimeter() float64 {
    return 2 * (r.Width + r.Height)
}

type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return math.Pi * c.Radius * c.Radius
}

func (c Circle) Perimeter() float64 {
    return 2 * math.Pi * c.Radius
}

// 使用接口
func printShapeInfo(s Shape) {
    fmt.Printf("Area: %.2f, Perimeter: %.2f\n", s.Area(), s.Perimeter())
}

rect := Rectangle{Width: 10, Height: 5}
circle := Circle{Radius: 3}

printShapeInfo(rect)    // Area: 50.00, Perimeter: 30.00
printShapeInfo(circle)  // Area: 28.27, Perimeter: 18.85
```

### 14.2 空接口

```go
// interface{}可以持有任何类型的值
var any interface{}

any = 42
any = "hello"
any = []int{1, 2, 3}

// 实际应用：通用容器
func printAny(v interface{}) {
    fmt.Println(v)
}

printAny(42)
printAny("hello")
printAny([]int{1, 2, 3})

// Go 1.18+: any是interface{}的别名
func process(v any) {
    // 等同于 v interface{}
}
```

### 14.3 类型断言

```go
var i interface{} = "hello"

// 类型断言（不安全）
s := i.(string)
fmt.Println(s)  // hello

// n := i.(int)  // panic: 类型不匹配

// 安全类型断言（推荐）
s, ok := i.(string)
if ok {
    fmt.Println("字符串:", s)
} else {
    fmt.Println("不是字符串")
}

n, ok := i.(int)
if !ok {
    fmt.Println("不是整数")
}
```

### 14.4 类型switch

```go
func describe(i interface{}) {
    switch v := i.(type) {
    case int:
        fmt.Printf("整数: %d\n", v)
    case string:
        fmt.Printf("字符串: %s\n", v)
    case bool:
        fmt.Printf("布尔: %t\n", v)
    case []int:
        fmt.Printf("整数切片: %v\n", v)
    case Shape:
        fmt.Printf("Shape, Area: %.2f\n", v.Area())
    case nil:
        fmt.Println("nil")
    default:
        fmt.Printf("未知类型: %T\n", v)
    }
}

describe(42)           // 整数: 42
describe("hello")      // 字符串: hello
describe([]int{1, 2})  // 整数切片: [1 2]
```

### 14.5 接口组合

```go
// 小接口组合成大接口
type Reader interface {
    Read(p []byte) (n int, err error)
}

type Writer interface {
    Write(p []byte) (n int, err error)
}

type Closer interface {
    Close() error
}

// 组合接口
type ReadWriter interface {
    Reader
    Writer
}

type ReadWriteCloser interface {
    Reader
    Writer
    Closer
}

// io.ReadWriteCloser就是这样定义的
```

### 14.6 接口的最佳实践

```go
// 1. 接受接口，返回具体类型
// ✅ 好的设计
func ProcessData(r io.Reader) (*Data, error) {
    // 接受接口，灵活
    return &Data{}, nil
}

// ❌ 不好的设计
func ProcessData2(r *os.File) (*Data, error) {
    // 接受具体类型，不灵活
    return &Data{}, nil
}

// 2. 保持接口小而专注
// ✅ 好
type Stringer interface {
    String() string
}

// ❌ 不好：接口太大
type Animal interface {
    Eat()
    Sleep()
    Walk()
    Fly()
    Swim()
}

// 3. 在使用方定义接口，而非实现方
// consumer.go
type DataStore interface {  // 使用方定义需要的接口
    Save(data Data) error
}

func Process(store DataStore) {
    // 使用接口
}

// provider.go
type MySQLStore struct {}

func (m MySQLStore) Save(data Data) error {
    // 实现接口（无需显式声明）
    return nil
}
```

### 14.7 常用标准库接口

```go
// 1. fmt.Stringer - 自定义字符串表示
type Person struct {
    Name string
    Age  int
}

func (p Person) String() string {
    return fmt.Sprintf("%s (%d岁)", p.Name, p.Age)
}

p := Person{Name: "Alice", Age: 30}
fmt.Println(p)  // Alice (30岁)

// 2. error接口
type error interface {
    Error() string
}

type MyError struct {
    Msg string
}

func (e MyError) Error() string {
    return e.Msg
}

// 3. sort.Interface - 自定义排序
type Person struct {
    Name string
    Age  int
}

type ByAge []Person

func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age }

people := []Person{
    {"Bob", 30},
    {"Alice", 25},
    {"Charlie", 35},
}
sort.Sort(ByAge(people))
```

---

## 15. Go语言error

### 15.1 错误处理基础

```go
// error是内置接口
type error interface {
    Error() string
}

// 创建错误
import "errors"

err1 := errors.New("出错了")
err2 := fmt.Errorf("文件 %s 不存在", filename)

// 返回错误
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("除数不能为0")
    }
    return a / b, nil
}

// 处理错误
result, err := divide(10, 0)
if err != nil {
    fmt.Println("错误:", err)
    return
}
fmt.Println("结果:", result)
```

### 15.2 自定义错误类型

```go
// 简单自定义错误
type ValidationError struct {
    Field string
    Value interface{}
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("验证失败: 字段 %s, 值 %v", e.Field, e.Value)
}

func validateAge(age int) error {
    if age < 0 || age > 150 {
        return &ValidationError{
            Field: "age",
            Value: age,
        }
    }
    return nil
}

// 使用
if err := validateAge(-5); err != nil {
    // 类型断言获取详细信息
    if ve, ok := err.(*ValidationError); ok {
        fmt.Printf("字段: %s, 值: %v\n", ve.Field, ve.Value)
    }
}

// 包含原始错误的错误类型
type QueryError struct {
    Query string
    Err   error
}

func (e *QueryError) Error() string {
    return fmt.Sprintf("查询失败 [%s]: %v", e.Query, e.Err)
}

func (e *QueryError) Unwrap() error {
    return e.Err  // 支持errors.Unwrap
}
```

### 15.3 错误包装（Go 1.13+）

```go
import (
    "errors"
    "fmt"
)

// 包装错误
func readConfig(filename string) error {
    err := os.Open(filename)
    if err != nil {
        return fmt.Errorf("无法读取配置: %w", err)  // %w包装错误
    }
    return nil
}

// 检查错误链
err := readConfig("config.yaml")
if err != nil {
    // errors.Is检查错误链中是否包含特定错误
    if errors.Is(err, os.ErrNotExist) {
        fmt.Println("文件不存在")
    }
    
    // errors.As提取特定类型的错误
    var pathErr *os.PathError
    if errors.As(err, &pathErr) {
        fmt.Println("路径:", pathErr.Path)
    }
}

// Unwrap解包错误
wrappedErr := fmt.Errorf("外层错误: %w", errors.New("内层错误"))
innerErr := errors.Unwrap(wrappedErr)
fmt.Println(innerErr)  // 内层错误
```

### 15.4 哨兵错误

```go
// 定义包级错误变量
var (
    ErrNotFound    = errors.New("未找到")
    ErrUnauthorized = errors.New("未授权")
    ErrInvalidInput = errors.New("输入无效")
)

func getUser(id int) (*User, error) {
    if id == 0 {
        return nil, ErrInvalidInput
    }
    // 查找用户...
    return nil, ErrNotFound
}

// 使用哨兵错误
user, err := getUser(0)
if err != nil {
    switch err {
    case ErrNotFound:
        fmt.Println("用户不存在")
    case ErrInvalidInput:
        fmt.Println("ID无效")
    default:
        fmt.Println("其他错误:", err)
    }
}

// 使用errors.Is（更安全）
if errors.Is(err, ErrNotFound) {
    fmt.Println("未找到")
}
```

### 15.5 错误处理模式

```go
// 1. 提前返回
func processData(data []byte) error {
    if len(data) == 0 {
        return errors.New("数据为空")
    }
    
    if err := validate(data); err != nil {
        return fmt.Errorf("验证失败: %w", err)
    }
    
    if err := save(data); err != nil {
        return fmt.Errorf("保存失败: %w", err)
    }
    
    return nil
}

// 2. 错误传播
func operation() error {
    if err := step1(); err != nil {
        return fmt.Errorf("步骤1失败: %w", err)
    }
    
    if err := step2(); err != nil {
        return fmt.Errorf("步骤2失败: %w", err)
    }
    
    return nil
}

// 3. 重试机制
func retryOperation(maxRetries int) error {
    var err error
    for i := 0; i < maxRetries; i++ {
        err = doOperation()
        if err == nil {
            return nil
        }
        time.Sleep(time.Second * time.Duration(i+1))
    }
    return fmt.Errorf("重试%d次后失败: %w", maxRetries, err)
}

// 4. 错误聚合
type MultiError []error

func (m MultiError) Error() string {
    var msgs []string
    for _, err := range m {
        msgs = append(msgs, err.Error())
    }
    return strings.Join(msgs, "; ")
}

func processItems(items []Item) error {
    var errs MultiError
    for _, item := range items {
        if err := process(item); err != nil {
            errs = append(errs, err)
        }
    }
    if len(errs) > 0 {
        return errs
    }
    return nil
}
```

### 15.6 panic vs error

```go
// 使用error的场景（大多数情况）
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("除数不能为0")
    }
    return a / b, nil
}

// 使用panic的场景（不可恢复的错误）
func mustLoadConfig(filename string) Config {
    config, err := loadConfig(filename)
    if err != nil {
        panic(fmt.Sprintf("无法加载配置: %v", err))
    }
    return config
}

// panic恢复
func safeExecute(f func()) (err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("panic恢复: %v", r)
        }
    }()
    
    f()
    return nil
}

// ❌ 不要滥用panic
func badFunction(value int) {
    if value < 0 {
        panic("值不能为负")  // 应该返回error
    }
}

// ✅ 正确使用error
func goodFunction(value int) error {
    if value < 0 {
        return errors.New("值不能为负")
    }
    return nil
}
```

---

## 16. Go语言defer

### 16.1 defer基础

```go
// defer延迟函数调用直到外层函数返回
func example() {
    defer fmt.Println("延迟执行")
    fmt.Println("正常执行")
}
// 输出：
// 正常执行
// 延迟执行

// 多个defer按LIFO（后进先出）顺序执行
func multipleDefers() {
    defer fmt.Println("第一个defer")
    defer fmt.Println("第二个defer")
    defer fmt.Println("第三个defer")
}
// 输出：
// 第三个defer
// 第二个defer
// 第一个defer
```

### 16.2 defer的常见用途

```go
// 1. 资源清理
func readFile(filename string) error {
    file, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer file.Close()  // 确保文件关闭
    
    // 读取文件...
    return nil
}

// 2. 解锁
func safeUpdate(m *sync.Mutex, data *Data) {
    m.Lock()
    defer m.Unlock()  // 确保解锁
    
    // 修改data...
}

// 3. 释放连接
func queryDatabase(db *sql.DB) error {
    conn, err := db.Conn(context.Background())
    if err != nil {
        return err
    }
    defer conn.Close()
    
    // 执行查询...
    return nil
}

// 4. 恢复panic
func safeParse(data string) (result int, err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("解析失败: %v", r)
        }
    }()
    
    result = riskyParse(data)
    return
}
```

### 16.3 defer的执行时机

```go
// defer在return之后执行，但在函数真正返回前
func deferReturn() int {
    i := 0
    defer func() {
        i++  // return后执行
    }()
    return i  // 返回0（此时i还是0）
}

// 使用命名返回值可以修改返回值
func deferModifyReturn() (i int) {
    defer func() {
        i++  // 修改返回值
    }()
    return 0  // 实际返回1
}

fmt.Println(deferReturn())        // 0
fmt.Println(deferModifyReturn())  // 1
```

### 16.4 defer的参数求值

```go
// defer语句的参数立即求值
func deferArgs() {
    i := 1
    defer fmt.Println(i)  // 参数i立即求值为1
    
    i++
    fmt.Println(i)  // 2
}
// 输出：
// 2
// 1

// 使用闭包延迟求值
func deferClosure() {
    i := 1
    defer func() {
        fmt.Println(i)  // 闭包捕获i的引用
    }()
    
    i++
    fmt.Println(i)  // 2
}
// 输出：
// 2
// 2
```

### 16.5 defer的性能考虑

```go
// defer有轻微性能开销
// 在性能敏感的循环中避免使用defer

// ❌ 不好：循环中使用defer
func processItems(items []Item) {
    for _, item := range items {
        mu.Lock()
        defer mu.Unlock()  // 在循环结束才执行，导致死锁！
        
        process(item)
    }
}

// ✅ 好：提取到函数
func processItems(items []Item) {
    for _, item := range items {
        processItem(item)
    }
}

func processItem(item Item) {
    mu.Lock()
    defer mu.Unlock()
    
    process(item)
}
```

### 16.6 defer实战模式

```go
// 1. 计时函数执行时间
func timeTrack(start time.Time, name string) {
    elapsed := time.Since(start)
    fmt.Printf("%s 耗时: %s\n", name, elapsed)
}

func operation() {
    defer timeTrack(time.Now(), "operation")
    
    // 执行操作...
    time.Sleep(time.Second)
}

// 2. 错误处理和清理
func processWithCleanup() (err error) {
    resource, err := acquireResource()
    if err != nil {
        return err
    }
    
    defer func() {
        if cleanupErr := resource.Close(); cleanupErr != nil {
            if err == nil {
                err = cleanupErr
            } else {
                err = fmt.Errorf("%v; cleanup error: %v", err, cleanupErr)
            }
        }
    }()
    
    return doWork(resource)
}

// 3. 日志记录
func logOperation(name string) func() {
    fmt.Printf("开始: %s\n", name)
    start := time.Now()
    
    return func() {
        fmt.Printf("完成: %s (耗时: %s)\n", name, time.Since(start))
    }
}

func complexOperation() {
    defer logOperation("complexOperation")()
    
    // 执行操作...
}
```

---

## 17. Go语言异常捕获

### 17.1 panic和recover

```go
// panic引发运行时恐慌
func causePanic() {
    panic("出现严重错误")
}

// recover捕获panic
func handlePanic() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("捕获到panic:", r)
        }
    }()
    
    causePanic()
    fmt.Println("这行不会执行")
}

handlePanic()
fmt.Println("程序继续运行")
// 输出：
// 捕获到panic: 出现严重错误
// 程序继续运行
```

### 17.2 panic的传播

```go
// panic会沿调用栈向上传播
func level3() {
    panic("level3 panic")
}

func level2() {
    defer fmt.Println("level2 defer")
    level3()
}

func level1() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("level1 捕获:", r)
        }
    }()
    defer fmt.Println("level1 defer")
    
    level2()
}

level1()
// 输出：
// level2 defer
// level1 defer
// level1 捕获: level3 panic
```

### 17.3 何时使用panic

```go
// ✅ 适合使用panic的场景：

// 1. 初始化阶段的不可恢复错误
func init() {
    if !checkSystemRequirements() {
        panic("系统不满足运行要求")
    }
}

// 2. 程序员错误（bug）
func getElement(slice []int, index int) int {
    if index < 0 || index >= len(slice) {
        panic("索引越界：程序员错误")
    }
    return slice[index]
}

// 3. 不应该发生的情况
func processStatus(status string) {
    switch status {
    case "active":
        // ...
    case "inactive":
        // ...
    default:
        panic(fmt.Sprintf("未知状态: %s", status))
    }
}

// ❌ 不应使用panic的场景：

// 1. 正常的错误处理（应该返回error）
func readFile(filename string) ([]byte, error) {  // ✅ 正确
    data, err := ioutil.ReadFile(filename)
    if err != nil {
        return nil, err  // 返回error，不要panic
    }
    return data, nil
}

// 2. 可预期的失败
func parseInt(s string) (int, error) {  // ✅ 正确
    return strconv.Atoi(s)  // 可能失败，返回error
}

// 3. 库代码（库应该返回error，让调用者决定如何处理）
func libraryFunction() error {  // ✅ 正确
    // 库函数不应该panic
    return errors.New("错误")
}
```

### 17.4 优雅的panic处理

```go
// 包装panic为error
func safeCall(fn func()) (err error) {
    defer func() {
        if r := recover(); r != nil {
            // 将panic转换为error
            switch x := r.(type) {
            case string:
                err = errors.New(x)
            case error:
                err = x
            default:
                err = fmt.Errorf("未知panic: %v", r)
            }
        }
    }()
    
    fn()
    return nil
}

// 使用
err := safeCall(func() {
    panic("出错了")
})
if err != nil {
    fmt.Println("错误:", err)  // 错误: 出错了
}
```

### 17.5 panic的栈追踪

```go
import "runtime/debug"

func safePanic() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Panic:", r)
            fmt.Println("Stack trace:")
            fmt.Println(string(debug.Stack()))
        }
    }()
    
    panic("测试panic")
}

safePanic()
// 输出完整的栈追踪信息
```

### 17.6 实战：HTTP服务器的panic恢复

```go
// HTTP中间件：捕获handler中的panic
func recoverMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        defer func() {
            if err := recover(); err != nil {
                // 记录错误
                log.Printf("Panic: %v\n%s", err, debug.Stack())
                
                // 返回500错误
                w.WriteHeader(http.StatusInternalServerError)
                json.NewEncoder(w).Encode(map[string]string{
                    "error": "Internal Server Error",
                })
            }
        }()
        
        next.ServeHTTP(w, r)
    })
}

// 使用
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("/", handler)
    
    // 包装中间件
    http.ListenAndServe(":8080", recoverMiddleware(mux))
}
```

### 17.7 自定义panic值

```go
// 使用结构体作为panic值
type PanicError struct {
    Message string
    Code    int
}

func (e PanicError) Error() string {
    return fmt.Sprintf("[%d] %s", e.Code, e.Message)
}

func operation() {
    panic(PanicError{
        Message: "操作失败",
        Code:    500,
    })
}

func handler() {
    defer func() {
        if r := recover(); r != nil {
            if err, ok := r.(PanicError); ok {
                fmt.Printf("错误码: %d, 消息: %s\n", err.Code, err.Message)
            }
        }
    }()
    
    operation()
}
```

---

## 18. Go语言依赖管理

### 18.1 Go Modules基础

Go 1.11+引入了Go Modules作为官方依赖管理工具：

```bash
# 初始化模块
go mod init github.com/username/projectname

# 这会创建go.mod文件
```

**go.mod文件示例**：
```go
module github.com/username/myproject

go 1.21

require (
    github.com/gin-gonic/gin v1.9.1
    github.com/go-sql-driver/mysql v1.7.1
)

require (
    // 间接依赖
    github.com/bytedance/sonic v1.9.1 // indirect
)

replace github.com/old/package => github.com/new/package v1.0.0
```

### 18.2 常用go mod命令

```bash
# 下载依赖
go mod download

# 添加缺失的模块，删除未使用的模块
go mod tidy

# 将依赖复制到vendor目录
go mod vendor

# 验证依赖是否被修改
go mod verify

# 查看依赖图
go mod graph

# 解释为什么需要某个模块
go mod why github.com/some/package

# 编辑go.mod文件
go mod edit -require=github.com/new/package@v1.0.0
```

### 18.3 导入和使用依赖

```go
// 1. 安装依赖包
// go get github.com/gin-gonic/gin@latest

// 2. 在代码中导入
package main

import (
    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "Hello World",
        })
    })
    r.Run()
}

// 3. 运行go mod tidy自动管理依赖
```

### 18.4 版本管理

```bash
# 获取最新版本
go get github.com/some/package@latest

# 获取特定版本
go get github.com/some/package@v1.2.3

# 获取特定commit
go get github.com/some/package@abc123

# 升级所有依赖的次要版本
go get -u ./...

# 升级所有依赖的补丁版本
go get -u=patch ./...

# 降级到特定版本
go get github.com/some/package@v1.0.0
```

### 18.5 replace指令

```go
// go.mod中使用replace替换依赖

// 1. 替换为本地路径（开发时）
replace github.com/original/package => ../local/package

// 2. 替换为fork版本
replace github.com/original/package => github.com/myfork/package v1.0.0

// 3. 替换为特定版本
replace github.com/old/package => github.com/new/package v2.0.0
```

### 18.6 私有模块

```bash
# 配置私有仓库
export GOPRIVATE="github.com/mycompany/*"

# 或配置多个
export GOPRIVATE="github.com/company1/*,gitlab.com/company2/*"

# 配置Git凭证
git config --global url."git@github.com:".insteadOf "https://github.com/"

# .netrc文件配置（Linux/Mac）
machine github.com
login username
password token_or_password
```

### 18.7 工作区（Workspaces，Go 1.18+）

```bash
# 初始化工作区
go work init ./module1 ./module2

# 添加模块到工作区
go work use ./module3

# 同步工作区
go work sync
```

**go.work文件示例**：
```go
go 1.21

use (
    ./module1
    ./module2
    ./module3
)

replace github.com/external/package => ../local/package
```

### 18.8 依赖管理最佳实践

```go
// 1. 始终使用go mod tidy
// 在提交代码前运行，清理未使用的依赖

// 2. 提交go.sum文件
// go.sum记录依赖的校验和，确保一致性

// 3. 使用语义化版本
// v1.2.3: 主版本.次版本.补丁版本
// v0.x.x: 开发版本，API可能不稳定
// v2.0.0+: 需要在import路径中加版本号

// 4. 最小化直接依赖
// 只导入需要的包，避免导入整个库

// 5. 定期更新依赖
// 定期运行 go get -u 更新依赖

// 6. 审查依赖的许可证和安全性
// 使用工具如 go list -m -json all | nancy
```

### 18.9 项目结构示例

```
myproject/
├── go.mod                 # 模块定义
├── go.sum                 # 依赖校验和
├── main.go                # 主入口
├── cmd/                   # 可执行程序
│   ├── server/
│   │   └── main.go
│   └── client/
│       └── main.go
├── internal/              # 私有代码
│   ├── config/
│   │   └── config.go
│   ├── database/
│   │   └── db.go
│   └── models/
│       └── user.go
├── pkg/                   # 可导出的库
│   └── utils/
│       └── helper.go
├── api/                   # API定义
│   └── handler.go
├── web/                   # Web资源
│   ├── templates/
│   └── static/
├── configs/               # 配置文件
│   └── config.yaml
├── scripts/               # 脚本
│   └── build.sh
├── docs/                  # 文档
│   └── README.md
└── tests/                 # 测试
    └── integration_test.go
```

### 18.10 go.mod技巧

```go
// 排除特定版本
exclude github.com/bad/package v1.2.3

// 强制使用特定版本
replace github.com/some/package => github.com/some/package v1.2.3

// 多行replace
replace (
    github.com/pkg1 => ../local/pkg1
    github.com/pkg2 => github.com/fork/pkg2 v1.0.0
)

// 使用retract废弃已发布的版本
retract (
    v1.0.0 // 包含严重bug
    [v1.1.0, v1.2.0] // 废弃版本范围
)
```

---

## 19. Go编码规范

### 19.1 命名规范总结

```go
// 包名：小写，简短，有意义
package user
package http

// 变量：驼峰命名
var userName string
var httpClient *http.Client

// 常量：驼峰命名（不是全大写）
const MaxRetries = 3
const defaultTimeout = 30

// 函数：驼峰命名，动词开头
func GetUser() {}
func SaveData() {}
func IsValid() bool {}

// 接口：-er后缀（单方法）或描述性名称
type Reader interface {}
type ResponseWriter interface {}

// 结构体：驼峰命名，名词
type User struct {}
type HTTPRequest struct {}

// 缩写词保持一致大小写
var userID int      // ✅
var userId int      // ❌
var HTTPServer      // ✅
var HttpServer      // ❌
```

### 19.2 代码组织

```go
// 导入顺序：标准库 -> 第三方 -> 自己的包
import (
    // 标准库
    "fmt"
    "os"
    "time"
    
    // 空行分隔
    
    // 第三方库
    "github.com/gin-gonic/gin"
    "github.com/go-redis/redis"
    
    // 空行分隔
    
    // 项目内部包
    "myproject/internal/config"
    "myproject/pkg/utils"
)

// 包内代码组织顺序
package mypackage

// 1. 常量
const (
    MaxSize = 100
)

// 2. 变量
var (
    defaultConfig Config
)

// 3. 类型定义
type User struct {}

// 4. 构造函数
func NewUser() *User {}

// 5. 公开方法
func (u *User) PublicMethod() {}

// 6. 私有方法
func (u *User) privateMethod() {}

// 7. 辅助函数
func helperFunction() {}
```

### 19.3 注释规范

```go
// 包注释：在package语句之前，描述包的用途
// Package user 提供用户管理相关功能。
//
// 本包实现了用户的CRUD操作，包括：
//   - 用户创建和验证
//   - 用户信息更新
//   - 用户权限管理
package user

// 导出的类型、函数、方法必须有注释
// User 表示系统用户。
type User struct {
    ID   int
    Name string
}

// NewUser 创建一个新用户实例。
//
// 参数:
//   name: 用户名，不能为空
//
// 返回:
//   *User: 新创建的用户指针
//   error: 如果name为空则返回错误
func NewUser(name string) (*User, error) {
    if name == "" {
        return nil, errors.New("name cannot be empty")
    }
    return &User{Name: name}, nil
}

// GetName 返回用户名。
// 此方法是并发安全的。
func (u *User) GetName() string {
    return u.Name
}

// 注释风格
// ✅ 好的注释
// calculateTotal 计算订单总金额
// 包含商品价格、税费和运费

// ❌ 不好的注释
// This function calculates total  （不要用英文，除非是英文项目）
// Get user name  （重复代码含义）
```

### 19.4 错误处理规范

```go
// ✅ 好的错误处理
func processData(data []byte) error {
    if len(data) == 0 {
        return errors.New("data is empty")
    }
    
    if err := validate(data); err != nil {
        return fmt.Errorf("validation failed: %w", err)
    }
    
    if err := save(data); err != nil {
        return fmt.Errorf("save failed: %w", err)
    }
    
    return nil
}

// ❌ 不好的错误处理
func processDataBad(data []byte) error {
    if len(data) == 0 {
        return errors.New("data is empty")
    } else {  // 不必要的else
        if err := validate(data); err != nil {
            return err  // 丢失了上下文
        } else {
            if err := save(data); err != nil {
                panic(err)  // 不要panic正常错误
            }
        }
    }
    return nil
}

// 错误信息规范
// ✅ 小写开头，不要句号
errors.New("file not found")
fmt.Errorf("failed to read file: %w", err)

// ❌ 大写开头或句号结尾
errors.New("File not found.")
```

### 19.5 函数设计规范

```go
// 1. 保持函数简短（一般不超过50行）
func shortFunction() {
    // 做一件事，做好它
}

// 2. 参数不要太多（一般不超过3-4个）
// ❌ 不好
func createUser(name, email, phone, address, city, zip, country string) {}

// ✅ 好：使用结构体
type UserInfo struct {
    Name    string
    Email   string
    Phone   string
    Address Address
}

func createUser(info UserInfo) {}

// 3. 返回error而非bool
// ❌ 不好
func save(data []byte) bool {
    // 失败时无法知道原因
    return false
}

// ✅ 好
func save(data []byte) error {
    if err := writeFile(data); err != nil {
        return fmt.Errorf("write failed: %w", err)
    }
    return nil
}

// 4. 接收接口，返回结构
// ✅ 好的设计
func ProcessData(r io.Reader) (*Result, error) {
    // r是接口，调用者可以传入任何实现
    return &Result{}, nil
}
```

### 19.6 并发规范

```go
// 1. 使用channel或sync包进行同步
// ✅ 好
func worker(jobs <-chan int, results chan<- int) {
    for job := range jobs {
        results <- job * 2
    }
}

// 2. 避免共享内存，使用channel通信
// ✅ 好："通过通信共享内存，而不是通过共享内存通信"
ch := make(chan Data)
go func() {
    ch <- Data{}
}()
data := <-ch

// 3. 保护共享状态
type Counter struct {
    mu    sync.Mutex
    count int
}

func (c *Counter) Inc() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.count++
}

// 4. context传递和取消
func operation(ctx context.Context) error {
    select {
    case <-ctx.Done():
        return ctx.Err()
    case <-time.After(time.Second):
        // 执行操作
        return nil
    }
}
```

### 19.7 性能优化建议

```go
// 1. 使用字符串构建器
// ❌ 不好
s := ""
for i := 0; i < 1000; i++ {
    s += strconv.Itoa(i)  // 每次都分配新字符串
}

// ✅ 好
var builder strings.Builder
for i := 0; i < 1000; i++ {
    builder.WriteString(strconv.Itoa(i))
}
s := builder.String()

// 2. 预分配切片容量
// ❌ 不好
var slice []int
for i := 0; i < 1000; i++ {
    slice = append(slice, i)  // 多次扩容
}

// ✅ 好
slice := make([]int, 0, 1000)
for i := 0; i < 1000; i++ {
    slice = append(slice, i)
}

// 3. 使用指针避免大结构体复制
type LargeStruct struct {
    data [10000]int
}

// ✅ 使用指针
func process(s *LargeStruct) {
    // 只传递指针
}

// 4. 避免不必要的goroutine
// ❌ 不好
go func() {
    fmt.Println("hello")  // 简单操作不需要goroutine
}()

// ✅ 好：直接执行
fmt.Println("hello")
```

### 19.8 测试规范

```go
// 测试文件命名：xxx_test.go
// 测试函数命名：TestXxx

package user

import "testing"

// 基本测试
func TestGetUser(t *testing.T) {
    user, err := GetUser(1)
    if err != nil {
        t.Fatalf("GetUser failed: %v", err)
    }
    
    if user.Name != "Alice" {
        t.Errorf("expected Alice, got %s", user.Name)
    }
}

// 表驱动测试
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 1, 2, 3},
        {"negative", -1, -2, -3},
        {"mixed", 1, -1, 0},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d, want %d",
                    tt.a, tt.b, result, tt.expected)
            }
        })
    }
}

// 基准测试
func BenchmarkAdd(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Add(1, 2)
    }
}
```

### 19.9 代码审查清单

```
✅ 命名是否清晰、符合规范？
✅ 是否有适当的注释？
✅ 错误处理是否完善？
✅ 是否有数据竞争？
✅ 是否正确使用defer？
✅ 是否有资源泄漏（goroutine、文件、连接）？
✅ 接口设计是否合理？
✅ 是否有不必要的复杂度？
✅ 测试覆盖率是否足够？
✅ 性能是否可以接受？
```

---

## 总结

本指南涵盖了Go语言的所有基础知识点：

1. **基础语法**：代码结构、命名、变量、常量、运算符
2. **数据结构**：结构体、数组、切片、Map
3. **控制流程**：条件、循环、指针
4. **函数式编程**：函数、方法、接口
5. **错误处理**：error、defer、panic/recover
6. **工程化**：依赖管理、编码规范

掌握这些知识后，你将能够：
- 编写高质量的Go代码
- 理解Go的设计哲学
- 构建可维护的Go项目
- 遵循Go社区最佳实践

**学习建议**：
1. 多写代码，实践每个知识点
2. 阅读标准库源码
3. 参与开源项目
4. 关注Go官方博客和提案

**推荐资源**：
- 官方文档：https://go.dev/doc/
- Go Tour：https://go.dev/tour/
- Effective Go：https://go.dev/doc/effective_go
- Go Blog：https://go.dev/blog/
