# golang-base

### liwenzhou个人博主学习站
一个个人的学习站,博主在每一期都录制了视频  
https://www.liwenzhou.com/archives/  
[视频地址](https://www.bilibili.com/video/BV16E411H7og)  

### 一个字节同学的个人博主学习站  
Go 语言设计与实现的作者  
https://draveness.me/golang/  

## 基础

### slice 和map 声明, 初始化方式, 初始化 空切片和空map

```
// 声明切片类型
var name []T
    var a []string              //声明一个字符串切片
    var b = []int{}             //声明一个整型切片并初始化
// 上面都是基于数组来创建的切片，如果需要动态的创建一个切片，我们就需要使用内置的make()函数
make([]T, size, cap)
    s1 := make([]int, 3) //[0 0 0]

// map格式定义
map[KeyType]ValueType
// map类型的变量默认初始值为nil，需要使用make()函数来分配内存
make(map[KeyType]ValueType, [cap])
    scoreMap := make(map[string]int, 8)
    scoreMap["张三"] = 90

// slice遍历
        for index, value := range s {
                fmt.Println(index, value)
        }        
// map遍历
        for k, v := range scoreMap {
                fmt.Println(k, v)
        }
```

### slice 和 map 的循环遍历(有兴趣可以了解为什么map循环无序)

```
源码：
// decide where to start
r := uintptr(fastrand())
结果：
因此每次重新 for range map，你见到的结果都是不一样的。那是因为它的起始位置根本就不固定！

根本原因：
你想问为什么要这么做？当然是官方有意为之，因为官方在 Go 早期的时候，发现很多工程师都较依赖 map 的遍历迭代顺序。但这将会导致可移植性存在问题。因此，改之。也请不要依赖...
```

-   ### append 实现两个slice 合并方式
-   -   进阶可以了解 append 内存扩容机制,内存对齐情况下的扩容
    -   声明cap的好处

```
Go语言的内建函数append()可以为切片动态添加元素。 可以一次添加一个元素，可以添加多个元素，也可以添加另一个切片中的元素（后面加…）。

var citySlice []string
// 追加一个元素
citySlice = append(citySlice, "北京")
// 追加多个元素
citySlice = append(citySlice, "上海", "广州", "深圳")
// 追加切片
a := []string{"成都", "重庆"}
citySlice = append(citySlice, a...) // //[北京 上海 广州 深圳 成都 重庆]

首先判断，如果新申请容量（cap）大于2倍的旧容量（old.cap），最终容量（newcap）就是新申请的容量（cap）。
否则判断，如果旧切片的长度小于1024，则最终容量(newcap)就是旧容量(old.cap)的两倍，即（newcap=doublecap），
否则判断，如果旧切片长度大于等于1024，则最终容量（newcap）从旧容量（old.cap）开始循环增加原来的1/4，即（newcap=old.cap,for {newcap += newcap/4}）直到最终容量（newcap）大于等于新申请的容量(cap)，即（newcap >= cap）
如果最终容量（cap）计算值溢出，则最终容量（cap）就是新申请容量（cap）。
```

### 结构体和结构体方法使用

```
类型别名规定：TypeAlias只是Type的别名，本质上TypeAlias与Type是同一个类型。

//类型定义
type NewInt int
//类型别名
type MyInt = int

使用type和struct关键字来定义结构体，结构体本身也是一种类型，我们可以像声明内置类型一样使用var关键字声明结构体类型。
type person struct {
        name string
        city string
        age  int8
}
在定义一些临时数据结构等场景下还可以使用匿名结构体
使用new关键字对结构体进行实例化，方法定义格式，接收者的概念就类似于其他语言中的this或者 self，方法与函数的区别是，函数不属于任何类型，方法属于特定的类型。
func (接收者变量 接收者类型) 方法名(参数列表) (返回参数) {
    函数体
}
结构体中字段大写开头表示可公开访问，小写表示私有（仅在定义当前结构体的包中可访问）。
```

### struct 和 json 之间转换(tag使用, omitempty 使用)

```
//JSON序列化：结构体-->JSON格式的字符串
data, err := json.Marshal(c)
if err != nil {
        fmt.Println("json marshal failed")
        return
}

//JSON反序列化：JSON格式的字符串-->结构体
str := `{"Title":"101","Students":[{"ID":0,"Gender":"男","Name":"stu00"},{"ID":1,"Gender":"男","Name":"stu01"},{"ID":2,"Gender":"男","Name":"stu02"}]}`
c1 := &Class{}
err = json.Unmarshal([]byte(str), c1)

Tag是结构体的元信息，可以在运行的时候通过反射的机制读取出来。 Tag在结构体字段的后方定义，由一对反引号包裹起来，具体的格式如下：
`key1:"value1" key2:"value2"`
//Student 学生
type Student struct {
        ID     int    `json:"id"` //通过指定tag实现json序列化该字段时的key
        Gender string //json序列化是默认使用字段名作为key
        name   string //私有不能被json包访问
}
如果想要在序列序列化时忽略这些没有值的字段时，可以在对应字段添加omitempty tag。对象、引用和零值用指针可以破解
type Dog struct {
        Breed    string
        // dog的weight是未知，而不是0,并不是我们想要的结果
        WeightKg int `json:",omitempty"`
}
```

-   ### 了解go的控制结构
-   -   if-else
    -   for
    -   switch
    -   goto
    -   break
    -   continue

```
if 表达式1 {
    分支1
} else if 表达式2 {
    分支2
} else{
    分支3
}
// for循环可以通过break、goto、return、panic语句强制退出循环。
for 初始语句;条件表达式;结束语句{
    循环体语句
}
Go语言中可以使用for range遍历数组、切片、字符串、map 及通道（channel）。 通过for range遍历的返回值有以下规律：
    数组、切片、字符串返回索引和值。
    map返回键和值。
    通道（channel）只返回通道内的值。
// fallthrough语法可以执行满足条件的case的下一个case，是为了兼容C语言中的case设计的。
func switchDemo5() {
        s := "a"
        switch {
        case s == "a":
                fmt.Println("a")
                fallthrough
        case s == "b":
                fmt.Println("b")
        case s == "c":
                fmt.Println("c")
        default:
                fmt.Println("...")
        }
}
// goto语句通过标签进行代码间的无条件跳转。goto语句可以在快速跳出循环、避免重复退出上有一定的帮助。Go语言中使用goto语句能简化一些代码的实现过程。
func gotoDemo2() {
        for i := 0; i < 10; i++ {
                for j := 0; j < 10; j++ {
                        if j == 2 {
                                // 设置退出标签
                                goto breakTag
                        }
                        fmt.Printf("%v-%v\n", i, j)
                }
        }
        return
        // 标签
breakTag:
        fmt.Println("结束for循环")
}

break语句可以结束for、switch和select的代码块。
continue语句可以结束当前循环，开始下一次的循环迭代过程，仅限在for循环内使用。
```

### func 参数传递和实现不定参数的方法

```
func 函数名(参数)(返回值){
    函数体
}

函数可以作为类型、变量、参数、返回值

// 匿名函数
func(参数)(返回值){
    函数体
}
// 闭包
func adder() func(int) int {
        var x int
        return func(y int) int {
                x += y
                return x
        }
}
func main() {
        var f = adder()
        fmt.Println(f(10)) //10
        fmt.Println(f(20)) //30
        fmt.Println(f(30)) //60
}
```

### interface接口定义和使用场景

```
type 接口类型名 interface{
    方法名1( 参数列表1 ) 返回值列表1
    方法名2( 参数列表2 ) 返回值列表2
    …
}
```

### defer 方法使用及场景

```
Go语言中的defer语句会将其后面跟随的语句进行延迟处理。在defer归属的函数即将返回时，将延迟处理的语句按defer定义的逆序进行执行，也就是说，先被defer的语句最后被执行，最后被defer的语句，最先被执行。
由于defer语句延迟调用的特性，所以defer语句能非常方便的处理资源释放问题。比如：资源清理、文件关闭、解锁及记录时间等。
在Go语言的函数中return语句在底层并不是原子操作，它分为给返回值赋值和RET指令两步。而defer语句执行的时机就在返回值赋值操作后，RET指令执行前。

recover()必须搭配defer使用。
defer一定要在可能引发panic的语句之前定义。
```

### _, ok 模式的使用场景

```
我们在使用Golang编程时，经常会使用到Golang比较特殊的一种写法，即：", OK"
使用场景：在一个表达式返回2个参数的时候使用，第一个参数是一个值或者nil，第二个参数是true/false或者一个错误error
在一个需要赋值的if条件语句中，使用这种模式去检测第二个参数值会让代码显得优雅简洁。

在函数返回时检测错误
检测映射中是否存在一个键值
检测一个接口类型变量 varI 是否包含了类型 T：类型断言
检测一个通道 ch 是否关闭
```

-   ### 使用go实现发送如下请求到接口,并把结果解析到对应结构体
-   -   GET 访问接口https://httpbin.org/get
    -   POST form 表单请求到接口 https://httpbin.org/post
    -   POST body json 请求到接口https://httpbin.org/post
    -     思考: 以上请求如何添加超时时间设置
