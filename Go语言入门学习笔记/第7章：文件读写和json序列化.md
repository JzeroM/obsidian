## 读写文件的操作
### 读取单行文件
```go
package main  
  
import (  
"fmt"  
"os"  
)  
  
func main() {  
//调用os下的Open函数打开文件  
file, err := os.Open("读写文件/a.txt")  
if err != nil {  
fmt.Println("文件打开错误")  
return  
}  
//关闭资源，这句实际会最后运行  
defer file.Close()  
//创建切片content，make([]byte, 100, 100)可以简写make([]byte, 100)  
content := make([]byte, 100)  
//调用Read函数，读取文件，返回该文件占多少个字节  
n, err := file.Read(content)  
if err != nil {  
fmt.Println("文件读取错误")  
return  
}  
//打印前n个字节，并强制转化为字符串  
fmt.Println(string(content[0:n]))  
}
```
### 读取多行文件
```go
package main  
  
import (  
"bufio"  
"fmt"  
"io"  
"os"  
)  
  
func main() {  
open, err := os.Open("读写文件/c.txt")  
if err != nil {  
fmt.Println("读取文件错误")  
return  
}  
defer open.Close()  
//缓冲读取  
reader := bufio.NewReader(open)  
for {  
//按换行符停顿读取单行内容，然后用for循环到最后一行  
readString, err := reader.ReadString('\n')  
if err != nil {  
//触发错误，io.EOF表示读取到了最后一行  
if err == io.EOF {  
//打印最后一行内容，并结束循环  
fmt.Print(readString)  
break  
} else {  
fmt.Println("出现错误：", err)  
return  
}  
} else {  
//依次打印除了最后一行，前面每行内容  
fmt.Print(readString)  
}  
}  
}
```
### 写入文件
```go
package main  
  
import (  
"fmt"  
"os"  
)  
  
func main() {  
//调用os下的Create函数创建文件 
file, err := os.Create("读写文件/c.txt")  
if err != nil {  
fmt.Println("创建文件失败")  
return  
}  
defer file.Close()  
content := "你好，美女！"
//调用Write函数，写入文件，返回该文件占多少个字节 
n, err := file.Write([]byte(content))  
if err != nil {  
fmt.Println("写入文件失败")  
return  
} else {  
fmt.Printf("写入了%d个字节", n)  
}  
}
```

## json 序列化
```go
package main  
  
import (  
"encoding/json"  
"fmt"  
)  
  
type Student struct {  
Name string  
Sex string  
Age int  
}  
  
type Class struct {  
Id string  
Students []Student  
}  
  
func main() {  
s := Student{"张三", "男", 18}  
c := Class{"3年2班", []Student{s, s, s}}  
//调用Marshal函数序列化  
m, err := json.Marshal(c)  
if err != nil {  
fmt.Println("json序列化失败", err)  
return  
}  
fmt.Println(string(m))  
println("_______________________________________________")  
//调用Marshal函数反序列化  
var x Class  
err = json.Unmarshal(m, &x)  
if err != nil {  
fmt.Println("json反序列化失败", err)  
return  
}  
fmt.Println(x)  
}
```
使用第三方库来优化：
> 先导包，命令终端输入 go get github.com/bytedance/sonic
```go
package main  
  
import (  
"fmt"  
"github.com/bytedance/sonic"  
)  
  
type Student struct {  
Name string  
Sex string  
Age int  
}  
  
type Class struct {  
Id string  
Students []Student  
}  
  
func main() {  
s := Student{"张三", "男", 18}  
c := Class{"3年2班", []Student{s, s, s}}  
//sonic替代json,调用Marshal函数序列化  
m, err := sonic.Marshal(c)  
if err != nil {  
fmt.Println("json序列化失败", err)  
return  
}  
fmt.Println(string(m))  
println("_______________________________________________")  
//sonic替代json,调用Marshal函数反序列化  
var x Class  
err = sonic.Unmarshal(m, &x)  
if err != nil {  
fmt.Println("json反序列化失败", err)  
return  
}  
fmt.Println(x)  
}
```
