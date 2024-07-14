# Golang实用技巧

[toc]

## Context

### 为了gorouines更可靠，避免实用context.Bachgroud()

在我们同时管理多个任务时，我们会实用goroutine，对吧？每个goroutine专用于特定的功能，比如发HTTP请求或者查询数据库。当这些任务需要暂停时，问题就出现了，这也是棘手的地方。因为我们不希望goroutines永久停止或阻塞，没有办法退出。

> “为什么我们应该避免直接使用 context.Background() ？”

我们之所以避免直接使用 context.Background()，主要是因为它无法在出现问题时停止或取消执行。它是我们能用context的最简单形式，没有值（values） 、没有截止日期（截止日期）、没有取消信号（cancellation signals）。当执行卡住或要顺利结束时，这可能会成为一个问题。

为了解决这个问题，通常我们依靠两个策略：取消（cancel）和超时（timeout）:

```go
context.WithTimeout(ctx, duration)
context.WithTimeoutCause(ctx, duration, errors.New("custom message"))

context.WithCancel(ctx)
context.WithCancelCause(ctx)

context.WithDeadline(ctx)
context.WithDeadlineCause(ctx, deadline, errors.New("custom message"))
```

有了

