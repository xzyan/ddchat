# Fuseutil
this is basic utils for secure coding

![](https://img.shields.io/badge/安全编码基本工具集-FUSEUTIL-blue)

<br>

## console
这是一个简单的日志记录工具，通过异步的日志消费引擎实现。

```go
// 使用前初始化和配置
defer console.New(&console.Options{
	Info: true, Debug: true, Warning: true, Error: true,
	Print:    true,
	Filename: "console.log",
}).Wait()

// 演示
message := "open config.lua: no such file or directory"
console.INFO(message)
console.DEBUG(message)
console.WARN(message)
console.ERROR(errors.New(message))
```

<br>

## siggroup
这是一个基于系统信号的任务组管理工具，解决进程中多个任务同时作业的优雅方法。也使得安全关机变得非常容易。

```go
// 添加一个任务，此方法可添加多个任务。注意！只适用于常驻任务
siggroup.Add(func() {
	fmt.Println("任务持续3秒...")
	time.Sleep(time.Second * 3)
})

// 等待任务结束，注意！只要有一个任务被结束，程序便会退出
siggroup.Wait(func() {
	// 所有任务结束后被执行
	fmt.Println("程序结束")
})
```

<br>

## hystrix
这是一个用于熔断降级的小工具，名字借鉴了 Java 体系。

```go
// 开启一个具有熔断降级和超时功能的代码域
err := hystrix.Try(time.Second, func() error {

	// 正常处理域，你期望的程序逻辑
	time.Sleep(time.Second)
	_, resp, err := feign.Request(nil, "GET", "https://www.baidu.com/sugrec", nil)
	if err != nil {
		return err
	}
	fmt.Printf("response: %s\n", resp)
	return nil

}, func(err error) error {

	// 降级处理域，当正常处理域发生意外时
	fmt.Printf("ERROR %s\n", err)
	if err == hystrix.ErrorTimeout {
		return nil
	}

	return err
})

// 捕获降级处理域返回的错误
if err != nil {
	panic(err)
}
```
