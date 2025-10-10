# Golang 面试题详解文档
## 一、基础入门
### 1. Golang开发新手常犯的50个错误（精选重点）
**常见错误包括：**

1. **左大括号不能单独一行** - Go要求左大括号必须在同一行
2. **未使用的变量和导入** - Go不允许存在未使用的变量和包
3. **短变量声明只能在函数内部使用**
4. **不能对map字段直接取址** - map的value不可寻址
5. **goroutine泄露** - 没有正确关闭goroutine
6. **defer执行时机理解错误** - defer在return之前执行
7. **range循环变量重用** - 循环变量地址不变
8. **nil接口和nil值的区别** - 接口包含类型和值两部分

---

[Golang开发新手常犯的50个错误_go (type) is not an expression-CSDN博客](https://blog.csdn.net/gezhonglei2007/article/details/52237582)

## 二、数据类型详解
### 2.1 切片相关
#### **nil切片和空切片的区别**
```go
// nil切片
var s1 []int        // s1 == nil, len=0, cap=0
// 空切片
s2 := []int{}       // s2 != nil, len=0, cap=0
s3 := make([]int, 0) // s3 != nil, len=0, cap=0
```

**区别：**

+ **nil切片**：底层数组指针为nil，未分配内存
+ **空切片**：底层数组指针指向一个空数组，已分配内存
+ **JSON序列化**：nil切片序列化为null，空切片序列化为[]
+ **判断空**：都应该用`len(s) == 0`来判断，而不是`s == nil`

#### **零切片、空切片、nil切片**
+ **零切片**：`var s []int` - 等同于nil切片
+ **空切片**：`s := []int{}` 或 `make([]int, 0)` - 长度为0但已初始化
+ **nil切片**：未初始化，值为nil

#### **slice深拷贝和浅拷贝**
```go
// 浅拷贝 - 共享底层数组
s1 := []int{1, 2, 3}
s2 := s1  // s2和s1共享底层数组

// 深拷贝 - 独立的底层数组
s3 := make([]int, len(s1))
copy(s3, s1)  // s3有独立的底层数组
```

#### **slice的len、cap、共享、扩容**
```go
s := make([]int, 5, 10)
// len(s) = 5  当前元素个数
// cap(s) = 10 底层数组容量
```

**扩容机制：**

1. **Go 1.18之前**：
    - 容量<1024：扩容为原来的2倍
    - 容量≥1024：扩容为原来的1.25倍
2. **Go 1.18之后**：
    - 容量<256：扩容为原来的2倍
    - 容量≥256：扩容公式为 `newcap = oldcap + (oldcap+3*256)/4`

**共享问题：**

```go
s1 := []int{1, 2, 3}
s2 := s1[0:2]  // s2和s1共享底层数组
s2[0] = 100    // 会影响s1
```

#### **拷贝大切片一定比小切片代价大吗？**
**答案：不一定**

```go
// 切片结构
type slice struct {
    array unsafe.Pointer  // 指向底层数组的指针
    len   int
    cap   int
}
```

+ **切片赋值**（浅拷贝）：只拷贝slice结构体（24字节），与切片大小无关
+ **copy函数**（深拷贝）：拷贝底层数组，代价与切片长度成正比

#### **自定义类型切片转字节切片**
```go
import "unsafe"

type MyInt int32

func main() {
    // 自定义类型切片转字节切片
    ints := []MyInt{1, 2, 3}
    bytes := *(*[]byte)(unsafe.Pointer(&ints))
    
    // 字节切片转回自定义类型切片（需调整slice header）
    // 注意：需要重新设置len和cap
}
```

**注意：** 使用unsafe包需要特别小心，要考虑内存对齐和大小端问题。

### 2.2 数组相关
#### **array和slice的区别**
| 特性 | Array（数组） | Slice（切片） |
| --- | --- | --- |
| **长度** | 固定，编译时确定 | 动态，运行时可变 |
| **类型** | 长度是类型的一部分 | 长度不是类型的一部分 |
| **传递** | 值传递（拷贝整个数组） | 引用传递（拷贝slice header） |
| **内存** | 在栈上分配（小数组） | 底层数组在堆上 |
| **零值** | 元素的零值 | nil |


```go
var arr [5]int      // 数组，长度固定为5
var slice []int     // 切片，长度可变
```

#### **array作为函数参数是值传递还是引用传递？**
**答案：值传递**

```go
func modify(arr [3]int) {
    arr[0] = 100  // 不会影响原数组
}

func main() {
    arr := [3]int{1, 2, 3}
    modify(arr)
    fmt.Println(arr)  // 输出: [1 2 3]
}
```

如果要修改原数组，应该传递指针：

```go
func modify(arr *[3]int) {
    arr[0] = 100  // 会影响原数组
}
```

#### **怎么判断一个数组是否已经排序**
```go
func isSorted(arr []int) bool {
    for i := 1; i < len(arr); i++ {
        if arr[i] < arr[i-1] {
            return false
        }
    }
    return true
}

// 或使用标准库
import "sort"
sorted := sort.IntsAreSorted(arr)
```

### 2.3 Map相关
#### **map不初始化使用会怎么样**
```go
var m map[string]int
// m["key"] = 1  // panic: assignment to entry in nil map

// 读取nil map不会panic，返回零值
value := m["key"]  // value = 0, 不会panic
```

**结论：** 向nil map写入会panic，但读取不会。

#### **map不初始化长度和初始化长度的区别**
```go
// 不指定长度
m1 := make(map[string]int)

// 指定长度
m2 := make(map[string]int, 100)
```

**区别：**

+ **不指定长度**：初始bucket数量较小，可能需要多次扩容
+ **指定长度**：预分配足够的bucket，减少扩容次数，提高性能
+ **性能影响**：如果知道大致元素数量，指定长度可以提升性能

#### **map承载多大，大了怎么办**
**承载能力：**

+ Go的map没有硬性上限，受限于内存
+ 负载因子（load factor）为6.5，即平均每个bucket存储6.5个键值对

**大map处理方案：**

1. **分片（Sharding）**：将大map拆分成多个小map
2. **使用sync.Map**：适合读多写少场景
3. **外部存储**：Redis、数据库等
4. **定期清理**：删除过期数据

#### **map的iterator是否安全？能不能一边delete一边遍历？**
```go
m := map[string]int{"a": 1, "b": 2, "c": 3}

// 可以一边遍历一边删除
for k := range m {
    delete(m, k)  // 安全，不会panic
}

// 但不能一边遍历一边添加（结果不可预测）
for k := range m {
    m[k+"_new"] = 1  // 新添加的元素可能会被遍历到，也可能不会
}
```

**注意：**

+ map遍历顺序是随机的
+ 可以安全地删除当前遍历到的元素
+ 添加新元素时，新元素是否被遍历到是未定义的

#### **map触发扩容的时机**
**两种扩容条件：**

1. **负载因子超过6.5**
    - 计算公式：`负载因子 = 元素个数 / bucket个数`
    - 当 `count / 2^B > 6.5` 时触发
2. **overflow bucket过多**
    - 当overflow bucket数量 >= 2^15 时
    - 或overflow bucket数量 >= bucket数量时

#### **map扩容策略**
**两种扩容方式：**

1. **翻倍扩容（增量扩容）**
    - 条件：负载因子>6.5
    - 策略：bucket数量翻倍，`2^B` -> `2^(B+1)`
    - 原因：元素太多
2. **等量扩容**
    - 条件：overflow bucket过多但负载因子不高
    - 策略：bucket数量不变，重新排列
    - 原因：删除元素过多导致空间碎片

**渐进式扩容：**

+ 不是一次性完成，而是渐进式搬迁
+ 每次操作(写入/删除)时搬迁1-2个bucket
+ 避免一次性扩容造成的延迟

#### **map如何顺序读取？**
```go
// map本身无序，需要先排序key
m := map[string]int{"c": 3, "a": 1, "b": 2}

// 提取keys并排序
keys := make([]string, 0, len(m))
for k := range m {
    keys = append(keys, k)
}
sort.Strings(keys)

// 按排序后的key访问
for _, k := range keys {
    fmt.Println(k, m[k])
}
```

#### **普通map如何不用锁解决协程安全问题**
**方案一：使用sync.Map**

```go
var m sync.Map
m.Store("key", "value")
value, ok := m.Load("key")
```

**方案二：使用channel**

```go
type MapOp struct {
    key   string
    value int
    op    string  // "read" or "write"
    resp  chan int
}

func mapManager(ops chan MapOp) {
    m := make(map[string]int)
    for op := range ops {
        if op.op == "write" {
            m[op.key] = op.value
        } else {
            op.resp <- m[op.key]
        }
    }
}
```

**方案三：分片加锁**

```go
type ShardedMap struct {
    shards []*MapShard
}

type MapShard struct {
    sync.RWMutex
    items map[string]int
}
```

#### **线程安全的map怎么实现**
**方法1：sync.Map（推荐）**

```go
var m sync.Map
m.Store("key", "value")
value, ok := m.Load("key")
m.Delete("key")
```

**适用场景：**

+ 读多写少
+ 多个goroutine读写不同的key

**方法2：map + sync.RWMutex**

```go
type SafeMap struct {
    sync.RWMutex
    m map[string]int
}

func (sm *SafeMap) Get(key string) int {
    sm.RLock()
    defer sm.RUnlock()
    return sm.m[key]
}

func (sm *SafeMap) Set(key string, value int) {
    sm.Lock()
    defer sm.Unlock()
    sm.m[key] = value
}
```

#### **go中怎么实现set**
```go
// 方法1：使用map[T]struct{}
set := make(map[string]struct{})
set["item"] = struct{}{}  // 添加
delete(set, "item")        // 删除
_, exists := set["item"]   // 检查存在

// 方法2：使用map[T]bool
set := make(map[string]bool)
set["item"] = true
delete(set, "item")
exists := set["item"]
```

**推荐使用**`**struct{}**`**：** 因为空结构体不占用内存空间。

#### **使用值为nil的slice、map会发生什么？**
```go
// nil slice
var s []int
s = append(s, 1)  // 正常工作，会分配内存
len(s)            // 返回0，不会panic
s[0]              // panic: index out of range

// nil map
var m map[string]int
value := m["key"] // 返回零值，不会panic
m["key"] = 1      // panic: assignment to entry in nil map
```

**总结：**

+ nil slice可以直接append，会自动分配内存
+ nil map读取返回零值，写入会panic
+ nil channel读写都会永久阻塞

### 2.4 字符串相关
#### **字符串转成byte数组，会发生内存拷贝吗？**
**答案：会发生内存拷贝**

```go
s := "hello"
b := []byte(s)  // 发生内存拷贝
```

**原因：**

+ 字符串是不可变的（immutable）
+ 字节切片是可变的（mutable）
+ 必须拷贝一份数据，避免通过切片修改字符串

**零拷贝方法（不安全）：**

```go
import "unsafe"

func stringToBytes(s string) []byte {
    return *(*[]byte)(unsafe.Pointer(
        &struct {
            string
            Cap int
        }{s, len(s)},
    ))
}
```

#### **翻转含有中文、数字、英文字母的字符串**
```go
func reverseString(s string) string {
    // 转换为rune切片处理Unicode字符
    runes := []rune(s)
    
    for i, j := 0, len(runes)-1; i < j; i, j = i+1, j-1 {
        runes[i], runes[j] = runes[j], runes[i]
    }
    
    return string(runes)
}

// 测试
s := "Hello世界123"
reversed := reverseString(s)  // "321界世olleH"
```

**关键点：**

+ 必须转换为`[]rune`而不是`[]byte`
+ 中文等Unicode字符占多个字节，rune可以正确处理

#### **字符串不能改，那转成数组能改吗，怎么改**
```go
s := "hello"

// 方法1：转成[]byte
b := []byte(s)
b[0] = 'H'
newS := string(b)  // "Hello"

// 方法2：转成[]rune（处理Unicode）
r := []rune(s)
r[0] = 'H'
newS := string(r)

// 注意：原字符串s不会改变
fmt.Println(s)     // "hello"
fmt.Println(newS)  // "Hello"
```

### 2.5 结构体相关
#### **go struct能不能比较？**
**答案：分情况**

**可比较的情况：**

```go
type Point struct {
    X, Y int
}

p1 := Point{1, 2}
p2 := Point{1, 2}
fmt.Println(p1 == p2)  // true
```

**不可比较的情况：**

```go
type NotComparable struct {
    S []int     // slice不可比较
    M map[string]int  // map不可比较
    F func()    // 函数不可比较
}

// n1 == n2  // 编译错误
```

**规则：**

+ 所有字段都可比较 -> 结构体可比较
+ 包含slice、map、func等不可比较类型 -> 结构体不可比较
+ 可以用`reflect.DeepEqual()`比较任何类型

### 2.6 其他类型问题
#### **make和new的区别**
| 特性 | make | new |
| --- | --- | --- |
| **返回值** | 类型本身 | 指针 |
| **适用类型** | slice、map、chan | 任何类型 |
| **初始化** | 初始化内部数据结构 | 只分配内存，零值初始化 |
| **是否可用** | 返回可直接使用 | 返回指向零值的指针 |


```go
// make
s := make([]int, 5)    // []int，可直接使用
m := make(map[string]int)  // map，可直接使用

// new
p := new(int)          // *int，指向0的指针
ps := new([]int)       // *[]int，指向nil slice的指针
```

#### **slice、map、channel创建时参数的含义**
```go
// slice
make([]T, len)        // 长度为len，容量为len
make([]T, len, cap)   // 长度为len，容量为cap

// map
make(map[K]V)         // 默认容量
make(map[K]V, size)   // 预分配size个元素的空间

// channel
make(chan T)          // 无缓冲channel
make(chan T, size)    // 缓冲channel，缓冲区大小为size
```

#### **Golang有没有this指针？**
**答案：没有**

Go使用**方法接收者（receiver）**代替this：

```go
type Person struct {
    Name string
}

// 值接收者
func (p Person) GetName() string {
    return p.Name
}

// 指针接收者
func (p *Person) SetName(name string) {
    p.Name = name
}
```

**接收者的选择：**

+ **值接收者**：不会修改原对象，拷贝开销小
+ **指针接收者**：可以修改原对象，避免拷贝大结构体

#### **Golang中局部变量和全局变量的缺省值**
**所有变量的零值：**

+ 数值类型：`0`
+ 布尔类型：`false`
+ 字符串：`""`（空字符串）
+ 指针、slice、map、channel、func、interface：`nil`

```go
var i int       // 0
var f float64   // 0.0
var b bool      // false
var s string    // ""
var p *int      // nil
```

#### **Golang中的引用类型包含哪些？**
**Go的引用类型：**

1. **slice（切片）**
2. **map（映射）**
3. **channel（通道）**
4. **interface（接口）**
5. **func（函数）**

**注意：**

+ 这些类型的值实际上是一个描述符（descriptor）
+ 包含指向底层数据结构的指针
+ 函数传递时拷贝的是描述符，而不是底层数据

#### **使用range迭代map是有序的吗？**
**答案：无序的，且每次遍历顺序可能不同**

```go
m := map[string]int{"a": 1, "b": 2, "c": 3}

// 每次运行输出顺序可能不同
for k, v := range m {
    fmt.Println(k, v)
}
```

**原因：**

+ Go故意在每次遍历时随机化起始位置
+ 防止开发者依赖遍历顺序
+ 如需有序遍历，需要先提取key并排序

#### **类型的值可以修改吗？**
**const常量：** 不可修改

```go
const Pi = 3.14
// Pi = 3.14159  // 编译错误
```

**普通变量：** 可以修改

```go
var x int = 10
x = 20  // 正常
```

**字符串：** 不可修改

```go
s := "hello"
// s[0] = 'H'  // 编译错误
```

#### **解析JSON数据时，默认将数值当做哪种类型**
**答案：**`**float64**`

```go
import "encoding/json"

jsonStr := `{"age": 25, "score": 98.5}`
var result map[string]interface{}
json.Unmarshal([]byte(jsonStr), &result)

fmt.Printf("%T\n", result["age"])    // float64
fmt.Printf("%T\n", result["score"])  // float64

// 转换为int需要类型断言
age := int(result["age"].(float64))
```

#### **json包变量不加tag会怎么样？**
```go
type Person struct {
    Name string  // 默认使用字段名"Name"
    Age  int     // 默认使用字段名"Age"
    secret string  // 小写字母开头，不会被导出
}

p := Person{Name: "Alice", Age: 30, secret: "xxx"}
data, _ := json.Marshal(p)
fmt.Println(string(data))  // {"Name":"Alice","Age":30}
```

**规则：**

+ 不加tag默认使用字段名作为JSON key
+ 只有大写字母开头的字段会被导出
+ 小写字母开头的私有字段不会出现在JSON中

#### **reflect如何获取字段tag？为什么json包不能导出私有变量的tag？**
```go
import "reflect"

type User struct {
    Name string `json:"name" db:"user_name"`
    age  int    `json:"age"`  // 私有字段
}

func main() {
    t := reflect.TypeOf(User{})
    
    // 获取公有字段的tag
    field, _ := t.FieldByName("Name")
    fmt.Println(field.Tag.Get("json"))  // "name"
    
    // 获取私有字段的tag
    field, _ = t.FieldByName("age")
    fmt.Println(field.Tag.Get("json"))  // "age"，可以获取
}
```

**为什么json包不能导出私有变量：**

+ 反射可以读取私有字段的tag
+ 但json包**无法设置**私有字段的值
+ Go的反射规则：无法设置未导出字段的值
+ `CanSet()`会对私有字段返回false

#### **Golang中指针运算有哪些？**
**Go中的指针运算非常受限：**

```go
// 支持的操作
p := &x
*p = 10     // 解引用
p == nil    // 比较

// 不支持的操作（与C不同）
// p++         // 错误：指针不能自增
// p = p + 1   // 错误：指针不能算术运算
// p - q       // 错误：指针不能相减
```

**使用unsafe包实现指针运算：**

```go
import "unsafe"

p := &arr[0]
// 移动到下一个元素
p = (*int)(unsafe.Pointer(uintptr(unsafe.Pointer(p)) + unsafe.Sizeof(int(0))))
```

---

## 三、流程控制
### 3.1 循环相关
#### **在for循环里append元素**
```go
// 陷阱：可能导致无限循环
s := []int{1, 2, 3}
for _, v := range s {
    s = append(s, v)  // 危险！
}
```

**问题分析：**

+ `range`在循环开始时会拷贝切片的slice header（ptr, len, cap）
+ 循环次数在开始时就确定了
+ append可能触发扩容，但循环次数不变
+ 不会无限循环，但结果可能不符合预期

**正确做法：**

```go
s := []int{1, 2, 3}
length := len(s)
for i := 0; i < length; i++ {
    s = append(s, s[i])
}
```

#### **for select时，如果通道已经关闭会怎么样？如果只有一个case呢？**
**已关闭的通道：**

```go
ch := make(chan int, 3)
ch <- 1
ch <- 2
close(ch)

for {
    select {
    case v, ok := <-ch:
        if !ok {
            fmt.Println("通道已关闭")
            return
        }
        fmt.Println(v)
    }
}
```

**行为：**

+ 从已关闭的通道接收数据会立即返回零值
+ `ok`为false表示通道已关闭
+ 不会阻塞，会不断执行

**只有一个case：**

```go
for {
    select {
    case v := <-ch:
        fmt.Println(v)
    }
}
```

如果通道关闭，会不断接收零值，形成忙循环（busy loop）。

**最佳实践：**

```go
for {
    select {
    case v, ok := <-ch:
        if !ok {
            return  // 通道关闭时退出
        }
        fmt.Println(v)
    }
}

// 或使用for range
for v := range ch {
    fmt.Println(v)
}
```

### 3.2 defer相关
#### **go defer（for defer）**
```go
// 示例1：defer在函数返回前执行
func example1() {
    defer fmt.Println("defer")
    fmt.Println("normal")
}
// 输出: normal -> defer

// 示例2：多个defer遵循LIFO（后进先出）
func example2() {
    defer fmt.Println("1")
    defer fmt.Println("2")
    defer fmt.Println("3")
}
// 输出: 3 -> 2 -> 1

// 示例3：defer参数在声明时求值
func example3() {
    i := 0
    defer fmt.Println(i)  // 立即求值，i=0
    i++
}
// 输出: 0

// 示例4：defer修改返回值
func example4() (result int) {
    defer func() {
        result++  // 可以修改命名返回值
    }()
    return 0
}
// 返回: 1
```

**for循环中的defer陷阱：**

```go
func deferInLoop() {
    for i := 0; i < 5; i++ {
        defer fmt.Println(i)  // defer会累积
    }
}
// 输出: 4 3 2 1 0
// 注意：defer不会在每次循环迭代时执行，而是累积到函数返回时

// 如果要在每次迭代时执行，应该使用匿名函数
func correctWay() {
    for i := 0; i < 5; i++ {
        func() {
            defer fmt.Println(i)
        }()  // 立即执行
    }
}
```

#### **在循环内执行defer语句会发生什么？**
**问题：** defer会累积，直到函数返回才统一执行

```go
func deferInLoop() {
    for i := 0; i < 10000; i++ {
        defer fmt.Println(i)  // 不好的做法
    }
}
```

**可能导致的问题：**

1. 内存累积：每个defer都会占用内存
2. 资源泄露：文件句柄等资源不能及时释放
3. 性能问题：大量defer影响性能

**解决方案：**

```go
func correctWay() {
    for i := 0; i < 10000; i++ {
        func() {
            defer cleanup()  // 在匿名函数内defer
            // do something
        }()  // 立即执行，defer也立即生效
    }
}

// 或者不使用defer，手动清理
func manualCleanup() {
    for i := 0; i < 10000; i++ {
        resource := acquire()
        // use resource
        release(resource)  // 手动释放
    }
}
```

### 3.3 select相关
#### **select可以用于什么？**
**主要用途：**

1. **多通道操作**

```go
select {
case v := <-ch1:
    fmt.Println("received from ch1:", v)
case v := <-ch2:
    fmt.Println("received from ch2:", v)
case ch3 <- value:
    fmt.Println("sent to ch3")
```

}

```plain

2. **超时控制**
```go
select {
case result := <-ch:
    fmt.Println("got result:", result)
case <-time.After(time.Second):
    fmt.Println("timeout")
}
```

3. **非阻塞操作**

```go
select {
case v := <-ch:
    fmt.Println("received:", v)
default:
    fmt.Println("no data available")
}
```

4. **等待多个goroutine**

```go
select {
case <-done1:
    fmt.Println("goroutine 1 done")
case <-done2:
    fmt.Println("goroutine 2 done")
}
```

**特点：**

+ 随机选择就绪的case
+ 如果多个case同时就绪，随机选择一个执行
+ 有default则不阻塞，否则阻塞直到某个case就绪
+ 空select会永久阻塞：`select {}`

#### **select可以用于实现哪些功能？**
**1. 超时处理**

```go
func fetchWithTimeout(url string) (string, error) {
    result := make(chan string)
    
    go func() {
        data := fetch(url)
        result <- data
    }()
    
    select {
    case data := <-result:
        return data, nil
    case <-time.After(2 * time.Second):
        return "", errors.New("timeout")
    }
}
```

**2. 心跳检测**

```go
func heartbeat() {
    ticker := time.NewTicker(30 * time.Second)
    defer ticker.Stop()
    
    for {
        select {
        case <-ticker.C:
            sendHeartbeat()
        case <-quit:
            return
        }
    }
}
```

**3. 任务取消**

```go
func worker(ctx context.Context) {
    for {
        select {
        case <-ctx.Done():
            fmt.Println("cancelled")
            return
        case task := <-taskChan:
            process(task)
        }
    }
}
```

**4. 扇入（Fan-in）模式**

```go
func fanIn(ch1, ch2 <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        defer close(out)
        for {
            select {
            case v, ok := <-ch1:
                if !ok {
                    ch1 = nil
                    continue
                }
                out <- v
            case v, ok := <-ch2:
                if !ok {
                    ch2 = nil
                    continue
                }
                out <- v
            }
            if ch1 == nil && ch2 == nil {
                return
            }
        }
    }()
    return out
}
```

**5. 限流控制**

```go
func rateLimiter() {
    limiter := time.Tick(100 * time.Millisecond)
    
    for request := range requests {
        <-limiter  // 限流
        go handle(request)
    }
}
```

### 3.4 switch相关
#### **switch中如何强制执行下一个case代码块？**
**使用**`fallthrough`**关键字：**

```go
func switchExample(x int) {
    switch x {
    case 1:
        fmt.Println("case 1")
        fallthrough  // 强制执行下一个case
    case 2:
        fmt.Println("case 2")
        fallthrough
    case 3:
        fmt.Println("case 3")
    default:
        fmt.Println("default")
    }
}

// switchExample(1) 输出:
// case 1
// case 2
// case 3
```

**注意事项：**

1. `fallthrough`必须是case块的最后一条语句
2. `fallthrough`无条件执行下一个case，不判断条件
3. 不能用在最后一个case或default中
4. Go的switch默认会break，不需要显式写

**类型switch不支持fallthrough：**

```go
func typeSwitch(x interface{}) {
    switch x.(type) {
    case int:
        fmt.Println("int")
        // fallthrough  // 编译错误！
    case string:
        fmt.Println("string")
    }
}
```

---

## 四、Context包
### **context包的用途？**
**Context的主要用途：**

**1. 取消信号传递**

```go
func worker(ctx context.Context) {
    for {
        select {
        case <-ctx.Done():
            fmt.Println("收到取消信号")
            return
        default:
            // 正常工作
        }
    }
}

func main() {
    ctx, cancel := context.WithCancel(context.Background())
    go worker(ctx)
    
    time.Sleep(time.Second)
    cancel()  // 取消
}
```

**2. 超时控制**

```go
func queryDatabase(ctx context.Context) error {
    ctx, cancel := context.WithTimeout(ctx, 2*time.Second)
    defer cancel()
    
    result := make(chan error)
    go func() {
        result <- db.Query()
    }()
    
    select {
    case err := <-result:
        return err
    case <-ctx.Done():
        return ctx.Err()  // context deadline exceeded
    }
}
```

**3. 截止时间**

```go
deadline := time.Now().Add(5 * time.Second)
ctx, cancel := context.WithDeadline(context.Background(), deadline)
defer cancel()
```

**4. 传递请求范围的值**

```go
// 不推荐传递过多数据
ctx := context.WithValue(context.Background(), "requestID", "12345")

func handler(ctx context.Context) {
    requestID := ctx.Value("requestID").(string)
    fmt.Println(requestID)
}
```

**Context树结构：**

```go
// 根Context
rootCtx := context.Background()  // 或 context.TODO()

// 派生Context
ctx1, cancel1 := context.WithCancel(rootCtx)
ctx2, cancel2 := context.WithTimeout(ctx1, time.Second)
ctx3 := context.WithValue(ctx2, "key", "value")

// 取消传播：cancel1() 会取消 ctx1, ctx2, ctx3
```

**最佳实践：**

1. Context作为函数第一个参数，命名为`ctx`
2. 不要把Context存储在结构体中
3. 不要传递nil context，使用`context.TODO()`
4. Context的Value仅用于请求范围的数据，不要传递可选参数
5. Context是并发安全的，可以在多个goroutine中使用

**四种创建Context的方法：**

```go
// 1. 根Context
context.Background()  // 常用
context.TODO()        // 不确定用什么时

// 2. 可取消的Context
ctx, cancel := context.WithCancel(parentCtx)

// 3. 超时Context
ctx, cancel := context.WithTimeout(parentCtx, duration)

// 4. 截止时间Context
ctx, cancel := context.WithDeadline(parentCtx, time.Time)

// 5. 带值的Context（不返回cancel）
ctx := context.WithValue(parentCtx, key, value)
```

---

## 五、错误处理与恢复
### **如何从panic中恢复？**
**使用recover()捕获panic：**

```go
func safeFunction() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("恢复panic:", r)
            // 打印堆栈信息
            debug.PrintStack()
        }
    }()
    
    // 可能panic的代码
    panic("something went wrong")
}
```

**recover()的规则：**

1. **只能在defer函数中调用**

```go
func wrong() {
    recover()  // 无效，不在defer中
    panic("test")
}

func correct() {
    defer func() {
        recover()  // 有效
    }()
    panic("test")
}
```

2. **必须在defer函数中直接调用**

```go
// 无效的recover
defer recover()  // 直接defer，无效

// 无效的recover
defer func() {
    helper()  // helper中的recover无效
}()

// 有效的recover
defer func() {
    if r := recover(); r != nil {
        // 处理panic
    }
}()
```

3. **只能捕获当前goroutine的panic**

```go
func main() {
    defer func() {
        recover()  // 无法捕获子goroutine的panic
    }()
    
    go func() {
        panic("in goroutine")  // 这个panic无法被捕获
    }()
    
    time.Sleep(time.Second)
}

// 正确做法：在每个goroutine中recover
func main() {
    go func() {
        defer func() {
            if r := recover(); r != nil {
                fmt.Println("recovered:", r)
            }
        }()
        panic("in goroutine")
    }()
    
    time.Sleep(time.Second)
}
```

**完整的panic处理示例：**

```go
import (
    "fmt"
    "runtime/debug"
)

func safeDivide(a, b int) (result int, err error) {
    defer func() {
        if r := recover(); r != nil {
            // 记录panic信息
            fmt.Printf("Panic: %v\n", r)
            
            // 打印堆栈
            fmt.Printf("Stack trace:\n%s\n", debug.Stack())
            
            // 转换为error返回
            err = fmt.Errorf("panic recovered: %v", r)
        }
    }()
    
    result = a / b  // 当b=0时会panic
    return result, nil
}

func main() {
    result, err := safeDivide(10, 0)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Println("Result:", result)
    }
}
```

**HTTP服务器中的panic恢复中间件：**

```go
func RecoveryMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        defer func() {
            if r := recover(); r != nil {
                // 记录日志
                log.Printf("Panic: %v\n%s", r, debug.Stack())
                
                // 返回500错误
                http.Error(w, "Internal Server Error", 
                    http.StatusInternalServerError)
            }
        }()
        
        next.ServeHTTP(w, r)
    })
}
```

**注意事项：**

1. recover只能捕获panic，不能捕获其他错误
2. 恢复panic后，程序继续执行defer链和函数返回
3. 不要滥用panic/recover，应该优先使用error
4. panic/recover适用于不可恢复的错误（如编程错误）
5. 库代码不应该panic，应该返回error

**什么时候应该panic：**

+ 初始化失败（如配置文件缺失）
+ 编程错误（如数组越界、空指针）
+ 不可能发生的情况

**什么时候应该用error：**

+ 预期的错误（如文件不存在、网络超时）
+ 业务逻辑错误
+ 可以恢复的错误

---



