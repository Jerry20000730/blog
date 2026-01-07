---
title: Go Programming Languages - 框架篇 - 日志 - Zap
tags: [Go]
categories: Programming Languages
cover: images/ProgrammingLanguages/Go/cover.svg
excerpt: Go常用日志框架 - zap
---

# Zap

zap是uber开发的一个日志框架，相较于logrus来说，性能要好一些，公司内部也会用zap来进行日志的格式规范。

# 类型
zap内置三种logger，对于`NewProduction`生产环境中运用的logger，会设置日志级别为info（意味着debugger不会被输出），error及error以上会打印调用堆栈，会上报位置。

```go
logger := zap.NewExample() // 测试
logger := zap.NewDevelopment() // 开发环境
logger, _ := zap.NewProduction()
```

zap的性能之所以比较好，很大一部分原因是因为日志里面需要显式地声明数据的类型，比如`zap.Int`，`zap.Namespace`。

```go
logger.Debug("hello")
logger.Info("hello", zap.Int("age", 18))
logger.Error("hello", zap.Namespace("china"), zap.Int("age", 18))
```

一般来说，不需要定制log的，直接使用`logger := zap.NewDevelopment()` 或 `logger, _ := zap.NewProduction()`即可，但如果需要定制部分功能的，需要灵活操作的，则可以使用`logger.New()`进行定制

```go
file, err := os.OpenFile(logFile, os.O_CREATE|os.O_APPEND|os.O_WRONLY, os.ModePerm)
if err != nil {
    panic(err)
}

encoderConfig := zap.NewProductionEncoderConfig()
encoderConfig.EncodeTime = zapcore.TimeEncoderOfLayout("2006-01-02 15:04:05.000") // 指定时间格式，不是时间戳
// {"level":"info","ts":1744551191.6673155,"caller":"io/zap_test.go:13","msg":"hello","age":18}
// ts 换成 time
encoderConfig.TimeKey = "time"

// level的显示样式为大写
encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder

core := zapcore.NewCore(
    // zapcore.NewJSONEncoder(encoderConfig)
    zapcore.NewConsoleEncoder(encoderConfig),
    zapcore.AddSync(file),
    zapcore.InfoLevel,
)

logger := zap.New(
    core,
    zap.AddCaller(),
)

return logger
```

也可以使用第三方日志轮询库，如之前提到的`rotatelogs`或者`lumberjack`([https://github.com/natefinch/lumberjack.git](https://github.com/natefinch/lumberjack.git))

zap与logrus一样，可以添加**钩子**

```go
logger := zap.New(
    core,
    zap.AddCaller(),
    zap.AddStacktrace(zapcore.ErrorLevel),
    zap.Hooks(func(e zapcore.Entry) error {
        if e.Level >= zapcore.ErrorLevel {
            fmt.Println(e.Message)
        }
        return nil
    })
)
```

上述代码在error级别及以上的日志可以在终端中打印错误的message，比如`logger.Error("hello", zap.Namespace("china"), zap.Int("age", 18))`则会打印`hello`。`zap.AddStacktrace(zapcore.ErrorLevel)`允许在error level及其以上打印调用堆栈

```txt
2025-04-13 23:03:20.000	ERROR	io/zap_test.go:22	hello	{"china": {"age": 18}}
github.com/Jerry20000730/go-framework/io.TestZap2
	/home/guofangcheng/go-framework/io/zap_test.go:22
testing.tRunner
	/usr/local/go/src/testing/testing.go:1792
```

如果需要公共的field，可以使用`logger.With`

```go
logger = logger.With(
    zap.Namespace("system")
    zap.String("T-uuid", uuid.New().String())
)
```

打印出来的公共参数：

```txt
2025-04-13 23:09:44.954	INFO	io/zap_test.go:21	hello	{"system": {"T-uuid": "091dfb4e-c6a3-463e-9dd0-67c02521c7ee", "age": 18}}
2025-04-13 23:09:44.954	ERROR	io/zap_test.go:22	hello	{"system": {"T-uuid": "091dfb4e-c6a3-463e-9dd0-67c02521c7ee", "china": {"age": 18}}}
github.com/Jerry20000730/go-framework/io.TestZap2
	/home/guofangcheng/go-framework/io/zap_test.go:22
testing.tRunner
	/usr/local/go/src/testing/testing.go:1792
```