## defer+recover 机制处理错误
<sup>go 中追求代码优雅，引入机制：defer+recover 机制捕获处理错误</sup>
```go
package main  
  
import "fmt"  
  
func main() {  
test()  
fmt.Println("上面的除法操作执行成功")  
fmt.Println("正常执行下面逻辑")  
}  
func test() {  
defer func() {  
//调用recover内置函数，可以捕获错误：  
err := recover()  
//如果没有捕获错误,返回值为零值：nil  
if err != nil {  
fmt.Println("错误已经捕获")  
fmt.Println("错误是：", err)  
}  
}()  
num1 := 10  
num2 := 0  
result := num1 / num2  
fmt.Println(result)  
}
```
运行结果：
```
错误已经捕获
错误是： runtime error: integer divide by zero
上面的除法操作执行成功                        
正常执行下面逻辑   
```
## 自定义错误错误
*自定义错误需要调用 errors 包下的 New 函数：返回 error 类型*
*如果程序出现错误，想中断退出程序：借助 builtin 包下的 panic 函数*
```go
package main  
  
import (  
"errors"  
"fmt"  
)  
  
func main() {  
e := test2()  
panic(e) //如果出现错误会中断退出程序  
fmt.Println("上面的除法操作执行结束")  
fmt.Println("正常执行下面逻辑")  
}  
func test2() (err error) {  
num1 := 10  
num2 := 0  
if num2 == 0 { //如果num2为0，返回错误  
return errors.New("程序出现错误，除数不能为0") //这里的New函数返回的是一个error类型  
} else { //如果num2不为0，正常执行  
result := num1 / num2  
fmt.Println(result)  
return nil  
}  
}
```
运行结果： 
```
panic: 程序出现错误，除数不能为0                 
                                                 
goroutine 1 [running]:                           
main.main()                                      
        E:/Golang/demo/错误处理/test2.go:10 +0x27
```
