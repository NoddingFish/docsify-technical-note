# GoLang

##### 示例：多个协程跑同一个任务

```go
package main
import "fmt"
//import "time"


func loop_in(num int, ch chan int) {
    fmt.Printf("I'm the %d goroutine!!!\n", num)
    for i:=1; i<=100; i++ {
        ch <- i
        fmt.Println(i)
    }
    close(ch)
}


func loop_out(num int, ch chan int, ch_out chan bool, store chan int) {
    fmt.Printf("I'm the %d goroutine!!!\n", num)
    for  {
        temp, ok := <- ch
        if !ok {
            break
        }
        store <- temp
        fmt.Printf("I'm the %d goroutine! and print %d!\n", num, temp)
    }
    ch_out <- true
}


func main() {
	ch := make(chan int, 100)
	store := make(chan int, 100)
	ch_out := make(chan bool, 3)
	go loop_in(1, ch)

    /* tips: because it's only one core for goroutine, so one goroutine will be running. you can change the parameter for core.
    runtime.GOMAXPROCS(4)
    fmt.Println(runtime.NumCPU())
*/
	for i := 2; i <= 4; i++ {
	    go loop_out(i, ch, ch_out, store)
	}
	
	
	//temp := <- ch
	//fmt.Println(temp)
	//close(ch)
	//<- ch_out
	for i := 1; i <= 3; i++ {
	    v, ok := <- ch_out
	    fmt.Println("it's over. ", v, ok)
	}
	close(store)
	
}
```

