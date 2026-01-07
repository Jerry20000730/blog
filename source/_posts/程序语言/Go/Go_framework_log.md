---
title: Go Programming Languages - 框架篇 - 日志 - Logrus
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: Go常用日志框架 - logrus
---

# 日志作用
1. 定位问题 - 这个就不用多说了，平常排查问题用的最多的方法就是看日志
2. 监控 - 这个没咋用过，留个空
3. 业务分析 - 统计数据吧，比如有多少请求，多少次I/O请求啥的

# 日志级别(程度由低到高，大体上是这个顺序)
- DEBUG
- INFO
- WARN
- ERROR
- FATAL

# Logrus介绍

[https://github.com/sirupsen/logrus](https://github.com/sirupsen/logrus)

根据github介绍，logrus的定义是

> Logrus is a structured logger for Go (golang), completely API compatible with the standard library logger.

logrus完全兼容标准的log库，还支持文本、JSON 两种日志输出格式

```shell
go get github.com/sirupsen/logrus
```

## 指定输出格式
**文本**输出格式

```go
logger.SetFormatter(&logrus.TextFormatter{
    DisableColors: true, 							// 强制不显示颜色
    TimestampFormat: "2006-01-02 15:04:05.000",		// 显示ms
})
```

**JSON**输出格式
```go
logger.SetFormatter(&logrus.JSONFormatter{
    TimestampFormat: "2006-01-02 15:04:05.000",
})
```

这个包的问题：不会进行日志轮询，即定期进行清理，避免log文件不断大下去

有一个包叫rotatelogs，但已经不维护了

## rotatelogs介绍

[https://github.com/lestrrat-go/file-rotatelogs](https://github.com/lestrrat-go/file-rotatelogs)

根据定义，rotatelogs的作用是

> Provide an io.Writer that periodically rotates log files from within the application

也就是说在应用程序内定期轮换日志文件

**安装命令**

```shell
go get github.com/lestrrat-go/file-rotatelogs
```

rotatelogs可以指定：

1. 日志名格式
2. 软链接
3. 每隔多少时间生成一份日志
4. 最大保存期限


```go
fout, err := rotatelogs.New(
    logFilePath+".%Y%m%d%H",                  // 指定日志文件的路径和名称，路径不存在的时候会创建
    rotatelogs.WithLinkName(logFilePath),     // 为最新的一份日志创建软链接
    rotatelogs.WithRotationTime(1*time.Hour), // 每隔1小时生成一份新的日志文件
    rotatelogs.WithMaxAge(7*24*time.Hour),    // 只留最近7天的日志，或使用WithRotationCount，表示只保留最近的几份日志
)
```

**设置日志输出方式**

```go
// 设置为日志文件输出
logger.SetOutput(fout)
// 设置为终端输出
logger.SetOutput(os.Stdout)
// 设置为不输出
log.SetOutput(ioutil.Discard)
```

**设置日志调用关系**

```go
logger.SetReportCaller(true)
```
这样输出的日志就会带上method，比如：
```json
{"animal":"penguin","level":"fatal","method":"github.com/sirupsen/arcticcreatures.migrate","msg":"a penguin swims by",
"time":"2014-03-10 19:57:38.562543129 -0400 EDT"}
```

**钩子（Hooks）**

简单来说就是打印日志的时候顺带会执行的操作，比如发短信啦，接入一些平台bot发消息啦

logrus支持hooks请参考官网：[https://github.com/sirupsen/logrus/wiki/Hooks](https://github.com/sirupsen/logrus/wiki/Hooks) 

**自定义钩子**
实现hook的两个方法，一个是`Levels`，表示适用于哪些等级，另一个是`Fire`，表示具体做法

```go
// 实现logurs.Hook接口
type AppHook struct {
	AppName string
}

// 适用于哪些Level
func (h *AppHook) Levels() []logrus.Level {
	return []logrus.Level{logrus.ErrorLevel, logrus.FatalLevel, logrus.PanicLevel}
}

// 在Fire函数时可读取或修改logrus.Entry
func (h *AppHook) Fire(entry *logrus.Entry) error {
	entry.Data["app"] = h.AppName
    // 这里你可以将Error Fatal 和 Panic级别的错误日志发送到kafka
	fmt.Println(entry.Message)
	return nil
}
```

其中Entry的结构是

```go
type Entry struct {
	Logger *Logger

	// Contains all the fields set by the user.
	Data Fields

	// Time at which the log entry was created
	Time time.Time

	// Level the log entry was logged at: Trace, Debug, Info, Warn, Error, Fatal or Panic
	// This field will be set on entry firing and the value will be equal to the one in Logger struct field.
	Level Level

	// Calling method, with package name
	Caller *runtime.Frame

	// Message passed to Trace, Debug, Info, Warn, Error, Fatal or Panic
	Message string

	// When formatter is called in entry.log(), a Buffer may be set to entry
	Buffer *bytes.Buffer

	// Contains the context set by the user. Useful for hook processing etc.
	Context context.Context

	// err may contain a field formatting error
	err string
}

type Fields map[string]interface{}
```

对应生成的日志就是中的话，`Message`就是`msg`，`Level`就是`level`，实际存储在的是`Data`变量中

```json
{"age":18,"file":"/home/guofangcheng/go-framework/io/logrus_test.go:14","func":"github.com/Jerry20000730/go-framework/io.TestLogrus","level":"info","msg":"this is info log","name":"test","time":"2025-03-24 01:03:48.080"}
```

示例代码就是做了个简单的处理，实际可以做更为复杂的逻辑，比如加入kafka，对接到提醒bot等。并且因为修改了`Data`，所以本质上可以修改他的日志输出内容。
