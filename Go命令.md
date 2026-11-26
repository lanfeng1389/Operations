# Go命令

1.  **基础命令—命令行**
    
    **go env -w GO111MODULE=off       ##能手动编写修改变量值**
    
2.  **脚本中代码命令解析**
    

1.  _**变量用法**_
    

```javascript
package main   //闷包，一个程序有且只有一个
import "fmt"   //导入一个系统包fmt用来输出的
/*
func代表函数，main主函数是go语言的启动入口，{}代表代码段所有的代码写在里面
*/
func main() {
    // var代表变量 string代表字符串
	var name string = "朱连奥"
  
    //int代表数字
	var age int = 23 
  
    // 这句代码的意思是打印一句话，执行完毕后进行换行
	fmt.Println(name,age) 
}
```
```javascript
package main
import "fmt"
func main() {
/*
变量也可不赋予值，这样打印出来string默认是"空"，int默认是"0"
*/
  var (
    name string
    age int
    addr string
  )
fmt.Println(name,age,addr)
}
/*
整数和浮点小数默认值是0和0.0
字符串变量默认值是 空
布尔值的默认值是false
切片、函数、指针的默认值是nil
*/
```
```javascript
package main

import "fmt"

func main() {
	// := 自动推导（短变量声明并初始化）
	name := "zhulianao"
	age := 18
	fmt.Println(name, age)
	fmt.Printf("%T,%T", name, age) //%T代表类型type
}

//%d 代表数字，%p 代表内存地址 &表示取地址符
```
```javascript
package main

import "fmt"

func main() {
	a := 100
	b := 200
	b, a = a, b
    fmt.Printf("%d,%d", a, b)
//或者,输出结果是一样的
    var a = 100
    var b = 200
    b,a=a,b
    fmt.Println(a,b)
}
```
```javascript
package main

import "fmt"

func test() (int, int) {
	return 100, 200
}

func main() {
	_, b := test()
	fmt.Println(b)
}
//没有名称的变量，都称为匿名变量
```

2.  **常量用法**
    

```javascript
/*
常量中的量就是不能变的量，且数据类型只能是布尔值、数字、字符
*/
package main

import "fmt"

func main() {
	const URL string = "www.baidu.com" //显示定义
	const URL2 = "zhulianao"		   //隐式定义
	const a, b, c = 3.14, "zhangsan", false //同时定义多个常量
	fmt.Println(URL, URL2, a, b, c)
}
```