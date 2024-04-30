# Golang Practical Tips 

- [Golang Practical Tips](#golang-practical-tips)
  - [Context](#context)
    - [Avoid Using `context.Background()`, Make Your Goroutines More Reliable](#avoid-using-contextbackground-make-your-goroutines-more-reliable)
    - [Unfortunately, `context.Value` Is Not Our Friend](#unfortunately-contextvalue-is-not-our-friend)
    - [Keep Contexts Alive with `context.WithoutCancel()`](#keep-contexts-alive-with-contextwithoutcancel)
    - [Schedule Functions After Context Cancellation Using `context.AfterFunc`](#schedule-functions-after-context-cancellation-using-contextafterfunc)
    - [Use an Unexported Empty Struct as a Context Key](#use-an-unexported-empty-struct-as-a-context-key)
    - [Handle Errors of Deferred Calls to Prevent Silent Failures](#handle-errors-of-deferred-calls-to-prevent-silent-failures)
    - [Always Keep Track of Your Goroutine's Lifetime](#always-keep-track-of-your-goroutines-lifetime)
    - [Avoid `time.Sleep()`, It's Not Context-Aware and Can't Be Interrupted](#avoid-timesleep-its-not-context-aware-and-cant-be-interrupted)
    - [Implement a Context-Aware `Sleep` Function](#implement-a-context-aware-sleep-function)
  - [Concurrency \& Synchronization](#concurrency--synchronization)
    - [Prefer `chan struct{}` Over `chan bool` for Signaling Between Goroutines](#prefer-chan-struct-over-chan-bool-for-signaling-between-goroutines)
    - [Buffered Channels as Semaphores to Limit Goroutine Execution](#buffered-channels-as-semaphores-to-limit-goroutine-execution)
    - [Optimize Multiple Calls with `singleflight`](#optimize-multiple-calls-with-singleflight)
    - [`sync.Once` Is the Best Way to Do Things Once](#synconce-is-the-best-way-to-do-things-once)
    - [Manage Multiple Goroutines with `errgroup`](#manage-multiple-goroutines-with-errgroup)
    - [Adjusting `GOMAXPROCS` for Containerized Environments (Kubernetes, Docker, etc.)](#adjusting-gomaxprocs-for-containerized-environments-kubernetes-docker-etc)
      - [Running Go in Containers (Docker \& Kubernetes)](#running-go-in-containers-docker--kubernetes)
      - [The Solution](#the-solution)
      - [How Does it Work?](#how-does-it-work)
    - [`sync.Pool` but Make It Typed-Safe with Generics](#syncpool-but-make-it-typed-safe-with-generics)
      - [Typed-safe Pool](#typed-safe-pool)
    - [Making a Type with Built-in Locking (`sync.Mutex` Embedding)](#making-a-type-with-built-in-locking-syncmutex-embedding)
    - [How to Send on a Channel Without Getting Stuck](#how-to-send-on-a-channel-without-getting-stuck)
  - [Error Handling](#error-handling)
    - [Only Define Errors (`var ErrXXX = errors.New`) When It's Necessary for Your Client](#only-define-errors-var-errxxx--errorsnew-when-its-necessary-for-your-client)
    - [Make Your Errors Clear with `fmt.Errorf`, Don't Just Leave Them Bare](#make-your-errors-clear-with-fmterrorf-dont-just-leave-them-bare)
      - [Using fmt.Errorf with %w](#using-fmterrorf-with-w)
    - [Enhancing Error Handling: Wrapping vs. Joining Errors in Go](#enhancing-error-handling-wrapping-vs-joining-errors-in-go)
    - [Error Messages Should Not Be Capitalized or End With Punctuation](#error-messages-should-not-be-capitalized-or-end-with-punctuation)
    - [Avoid Returning -1 or nil to Indicate Error](#avoid-returning--1-or-nil-to-indicate-error)
      - [Go's solution: Multiple return values](#gos-solution-multiple-return-values)
    - [Single Touch Error Handling: Less Noise](#single-touch-error-handling-less-noise)
    - [Simplify Your Error Messages in `fmt.Errorf`](#simplify-your-error-messages-in-fmterrorf)
  - [Performance](#performance)
    - [Pre-allocate Slices for Performance](#pre-allocate-slices-for-performance)
    - [Prefer `strconv` Over `fmt` for Converting to/from String](#prefer-strconv-over-fmt-for-converting-tofrom-string)
    - [Sort Fields in Struct from Largest to Smallest](#sort-fields-in-struct-from-largest-to-smallest)
    - [Avoid Defer in Loops, or Your Memory Might Blow Up](#avoid-defer-in-loops-or-your-memory-might-blow-up)
    - [Case-insensitive String Comparison with `strings.EqualFold`](#case-insensitive-string-comparison-with-stringsequalfold)
    - [Filter Without Any Allocation](#filter-without-any-allocation)
  - [Readability \& Maintainability](#readability--maintainability)
    - [Make `time.Duration` Clear and Readable](#make-timeduration-clear-and-readable)
    - [Making Sense of Large Numbers with Numeric Separators](#making-sense-of-large-numbers-with-numeric-separators)
    - [Avoid Naked Parameters with Comment or Constant](#avoid-naked-parameters-with-comment-or-constant)
    - [Empty Slice or, Even Better, `nil` Slice](#empty-slice-or-even-better-nil-slice)
    - [Understand "Return Fast, Return Early" to Avoid Nested Code](#understand-return-fast-return-early-to-avoid-nested-code)
    - [Define Interfaces in the Consumer Package, Not the Producer Once](#define-interfaces-in-the-consumer-package-not-the-producer-once)
    - [Avoid Using `break` in Switch Cases, Except When Paired with Labels](#avoid-using-break-in-switch-cases-except-when-paired-with-labels)
    - [Keep Your Names Simple and Clear by Avoiding Repetition](#keep-your-names-simple-and-clear-by-avoiding-repetition)
    - [Skip the 'Get' Prefix for Getters](#skip-the-get-prefix-for-getters)
    - [Naming Unexported Global Variables with an Underscore `_` Prefix](#naming-unexported-global-variables-with-an-underscore-_-prefix)
      - [With an Underscore Prefix](#with-an-underscore-prefix)
    - [Converting Multiple If-Else Statements into Switch Cases](#converting-multiple-if-else-statements-into-switch-cases)
    - [Gracefully Shut Down Your Application](#gracefully-shut-down-your-application)
      - [Kubernetes Consideration](#kubernetes-consideration)
    - [Keep the Mutex Close to the Data It's Protecting](#keep-the-mutex-close-to-the-data-its-protecting)
    - [Avoid Global Variables, Especially Mutable Ones](#avoid-global-variables-especially-mutable-ones)
    - [Implement `String()` for Enum with the `stringer` Tool](#implement-string-for-enum-with-the-stringer-tool)
    - [Use the `deadcode` Tool to Find and Remove Unused Functions](#use-the-deadcode-tool-to-find-and-remove-unused-functions)
    - [Explicitly Ignore Values with Blank Identifier (`_`) Instead of Silently Ignoring Them](#explicitly-ignore-values-with-blank-identifier-_-instead-of-silently-ignoring-them)
    - [If a Parameter Isn't Needed, Either Drop It or Ignore It on Purpose](#if-a-parameter-isnt-needed-either-drop-it-or-ignore-it-on-purpose)
    - [Loop Labels for Cleaner Breaks and Continues](#loop-labels-for-cleaner-breaks-and-continues)
      - [Loop labels](#loop-labels)
    - [The Downsides of Relying on init() Functions in Your Go Code](#the-downsides-of-relying-on-init-functions-in-your-go-code)
    - [Simplify Interfaces and Only Ask for What You Really Need](#simplify-interfaces-and-only-ask-for-what-you-really-need)
  - [Function Designs](#function-designs)
    - [Avoid Named Results unless Necessary for Documentation](#avoid-named-results-unless-necessary-for-documentation)
      - [2. Avoid Naked Returns in Long Functions](#2-avoid-naked-returns-in-long-functions)
      - [3. Necessary for Deferred Closures](#3-necessary-for-deferred-closures)
      - [4. Some Cases Don’t Need Naming, Even With More Than Two Results](#4-some-cases-dont-need-naming-even-with-more-than-two-results)
    - [Lead with Context, End with Options, and Always Close with an Error](#lead-with-context-end-with-options-and-always-close-with-an-error)
    - [Intentionally Stop with Must Functions](#intentionally-stop-with-must-functions)
    - [Pass Values, Not Pointers](#pass-values-not-pointers)
      - [0. Common Thoughts about Pointers:](#0-common-thoughts-about-pointers)
      - [1. Fixed-sized Types](#1-fixed-sized-types)
      - [2. Immutability and Clarity](#2-immutability-and-clarity)
      - [3. Small or Unlikely to Grow Types](#3-small-or-unlikely-to-grow-types)
      - [4. Passing Values is Fast and Rarely Slower Than Pointers](#4-passing-values-is-fast-and-rarely-slower-than-pointers)
      - [5. Make Passing Values Your Default](#5-make-passing-values-your-default)
    - [Prefer Using a Pointer Receiver When Defining Methods](#prefer-using-a-pointer-receiver-when-defining-methods)
    - [Simplify Function Signatures with Structs or Variadic Options](#simplify-function-signatures-with-structs-or-variadic-options)
      - [1. Option Structs](#1-option-structs)
      - [2. Variadic Options](#2-variadic-options)
    - [Give the Caller the Right to Make Decisions](#give-the-caller-the-right-to-make-decisions)
      - [1. Handling Errors](#1-handling-errors)
      - [2. Goroutines](#2-goroutines)
      - [3. Other Operations](#3-other-operations)
    - [Tips for a Cleaner, More Testable main() Function in Go](#tips-for-a-cleaner-more-testable-main-function-in-go)
    - [Just... Don’t Panic()](#just-dont-panic)
  - [Tricks](#tricks)
    - [Measure the Execution Time of a Function Using defer](#measure-the-execution-time-of-a-function-using-defer)
    - [Multistage Defer: Handling the Start and End of a Function Efficiently](#multistage-defer-handling-the-start-and-end-of-a-function-efficiently)
    - [Make Structs Un-Comparable](#make-structs-un-comparable)
    - [Result Forwarding in Function Calls](#result-forwarding-in-function-calls)
    - [Returning Pointers Made Easy with Generics](#returning-pointers-made-easy-with-generics)
    - [Compile-Time Interface Verification](#compile-time-interface-verification)
    - [Prevent Struct Unkeyed Literals by Using an Empty Field](#prevent-struct-unkeyed-literals-by-using-an-empty-field)
  - [Miscellaneous](#miscellaneous)
    - [Parse a Slice into an Array](#parse-a-slice-into-an-array)
    - [Avoid Using `math/rand`, Use `crypto/rand` for Keys Instead](#avoid-using-mathrand-use-cryptorand-for-keys-instead)
    - [Table-driven Tests, Subtests, and Parallel Tests](#table-driven-tests-subtests-and-parallel-tests)
      - [1. Table-driven Tests](#1-table-driven-tests)
      - [2. Subtests \& Parallel](#2-subtests--parallel)
    - [Enums Start from 1 for Categorization and 0 for Default Cases](#enums-start-from-1-for-categorization-and-0-for-default-cases)
    - [Using Flag Enums to Handle Multiple Properties in a Single Variable](#using-flag-enums-to-handle-multiple-properties-in-a-single-variable)
      - [Flag Enums](#flag-enums)

## Context

### Avoid Using `context.Background()`, Make Your Goroutines More Reliable

When we manage multiple tasks at once, we use goroutines, right? Each dedicated to a specific function like making HTTP requests or querying databases. The issue arises when these tasks need to pause, this is where it gets tricky because we don't want our goroutines to just halt indefinitely or block without a way to exit.

> "Why should we avoid using context.Background() directly?"

The main reason we avoid using `context.Background()` directly is because it offers no way to stop or cancel operations if something goes wrong. It’s the simplest form of context we can use, with no values, no deadlines, no cancellation signals. This can be a problem when operations get stuck or need to be ended smoothly.

To handle this, we usually rely on two strategies: cancel and timeout.

```go
context.WithTimeout(ctx, duration)
context.WithTimeoutCause(ctx, duration, errors.New("custom message"))

context.WithCancel(ctx)
context.WithCancelCause(ctx)

context.WithDeadline(ctx)
context.WithDeadlineCause(ctx, deadline, errors.New("custom message"))
```

With these tools, every goroutine we start comes with a clear expectation: "I will finish my task or I'll explain why I couldn’t in time, and you have the power to cancel my task if necessary."

A few points to keep in mind:

- Underneath it all, `WithTimeout` is really just `WithDeadline` with a different name.
- The XXXCause functions, which were added in the recent Go updates (versions 1.20 and 1.21), offer better error reporting.
- If a timeout happens with `XXXCause`, it provides a more detailed error message: _"context deadline exceeded: custom message."_

> "What about channels? I don't want to wait forever on a channel."

When dealing with channels and we want to ensure we're not waiting indefinitely, a better way to manage this is by using the `select` statement, which allows us to set up a timeout option:

```go
select {
case result := <-ch
    fmt.Println("Received result:", result)
case <-time.After(5 * time.Second):
    fmt.Println("Timed out")
}
```

However, it's important to be aware that using `time.After` might lead to short-term memory leaks. In certain cases, it might be more effective to use `time.Timer` or `time.Ticker` which give us better control over timing.

We'll dive into these alternatives and their values in our upcoming tips.

### Unfortunately, `context.Value` Is Not Our Friend

Let's talk about using `context.Value`, it seems like a handy tool since we can just drop some data into the context and pick it up wherever we need it. 

This keeps our function signatures clean and simple, right? Here's how it typically goes down:

```go
func A(ctx context.Context, transactionID string) {
    payment := db.GetPayment(ctx, transactionID)
    ctx = context.WithValue(ctx, "payment", payment)

    B(ctx)
}

func B(ctx context.Context) {
    ...

    C(ctx)
}

func C(ctx context.Context) {
    payment, ok := ctx.Value("payment").(Payment)
    ...
}
```

In this setup, function `A` grabs a payment record and adds it to the context, function `C`, called within `B`, retrieves this payment. This strategy avoids passing the payment directly through function `B`, which doesn't need to know about the payment.

This approach might look good because:

- It allows us to omit passing specific data through functions like B that don't use it.
- It enables us to keep all necessary data in one place, within the context.
- It avoids extra parameters in function signatures.

Why not just call function C directly from function A? Often, C is deeply integrated into B's logic and might depend on some of its computations, arguments.

So what's the problem? Here's where we hit some issues:

- We're giving up the type-checking safety that Go provides during compilation.
- We're putting data into a black box and hoping to find it again later, which after a week could be like searching blindly.
- The payment data seems optional because of implicit passing, yet it's actually important.

From personal perspective, The main issue with using `ctx.Value` is how it hides data. It's like putting something in a safe without a clear label. Sure, it's stored away, but retrieving it becomes a guessing game. 

Being explicit about what we're passing around usually leads to fewer headaches down the road.

> "So, when is it appropriate to use `context.Value()`?"

It's best to limit its use, but the Go documentation suggests it's suitable for passing request-scoped values across API boundaries and between processes. Here are some good uses:

You might consider using it for tracking certain request-related data like:

- Tracking the start time of a request
- Logging the caller's IP address
- Managing trace and span IDs
- Identifying the HTTP route being accessed
- ...

> "Isn't my 'payment' data relevant to the request?"

If payment information is important across multiple functions, it's clearer and safer to pass it **explicitly** through function parameters, this helps anyone reading the code immediately understand that the function directly interacts with the payment data.

Generally, it's better to avoid embedding critical business data within the context, this strategy keeps our code **clear** and maintainable.

### Keep Contexts Alive with `context.WithoutCancel()`

So, when you're working with contexts in Go, one thing that's pretty straightforward is the way cancelling works. If you cancel a parent context, all the child contexts get cancelled too. 

Here's an easy example to see how that plays out:

```go
parentCtx, cancel := context.WithCancel(context.Background())
childCtx, _ := context.WithCancel(parentCtx)

go func(ctx context.Context) {
    <-ctx.Done()
    fmt.Println("Child context done")
}(childCtx)

cancel()
```

In this snippet, once we cancel the `parentCtx`, the `childCtx` is also cancelled and this is normally what we want, but sometimes you might find yourself needing a child context to keep running, even if its parent was cancelled.

There's a common scenario we often run into when working with HTTP requests in Go. Launching a goroutine to handle tasks after the main request has been processed can sometimes lead to errors if not handled carefully:

```go
func handleRequest(req *http.Request) {
    ctx := req.Context()

    go hookAfterRequest(ctx)
}
```

Or we can think about handling an HTTP request where, even if the client disconnects, we still need to log details and gather metrics, but without the cancellation behavior.

> "Okay, I'll just create a new context for those tasks"

That's one way to do it, but the new context won't carry over any information or values from the original context, which you often need for tasks like logging and metrics.

Here's how you can keep those values:

```go
parentCtx := context.WithValue(context.Background(), "requestID", "12345")
childCtx, _ := context.WithCancel(parentCtx)

fmt.Println("Propagated value": childCtx.Value("requestID")) // 12345
```

In scenarios like our HTTP request example, to make sure some operations continue even if the parent context is cancelled, we could try this:

```go
func handleRequest(req * http.Request) {
    ctx := req.Context()

    // Create a child context that doesn't cancel when the parent does
    uncancellableCtx := context.WithoutCancel(ctx)

    go func(ctx context.Context) {
        // This logging operation won't be interrupted if
        // the parent context (ctx) is cancelled
        getMetrics(uncancelableCtx, req)
    }(ctxWithoutCancel)
}
```

Basically, the `context.WithoutCancel()` function, which was introduced in Go 1.21, allows certain operations to continue without being affected by the cancellation of their parent context.

### Schedule Functions After Context Cancellation Using `context.AfterFunc`

So we have discussed how to keep a context active even after its parent has been stopped. Now, we're going to look at another handy feature introduced in Go 1.21, which is `context.AfterFunc`. This function lets you schedule a callback function `f` to run in a separate goroutine once a `ctx` ends, whether due to cancellation or timeout.

Here's how you can use it:

```go
ctx, cancel := context.WithTimeout(parentCtx, 5*time.Second)
defer cancel()

stop := context.AfterFunc(ctx, func() {
    fmt.Println("Cleanup operations after context is done")
})
```

This snippet of code sets up a cleanup task to run once the context is complete and it's really useful for tasks like cleanup, logging, or other operations that need to happen after a cancellation.

> “When does the callback run?”

Well, it kicks off in a new goroutine as soon as the `ctx.done` channel of the parent context sends a signal.

> “What if the context is already cancelled?”

Then the callback runs immediately, of course, in a new goroutine.

Here’s the rundown:

- `AfterFunc` can be used multiple times with the same context, and each task you set up runs independently.
- If the context is already done when you call `AfterFunc`, it triggers the function right away in a new goroutine.
- It provides a `stop` function that lets you cancel the planned function.
- Using the `stop` function is non-blocking, it doesn't wait for the function to finish, it stops it immediately. If you need the function and your main work to be synchronized, you'll have to manage that yourself.

Let's delve a bit into the stop(), the function returned by `AfterFunc`:

```go
stop := context.AfterFunc(ctx, func() {
    ...
})

if stopped := stop(); stopped {
    fmt.Println("Remove the callback before context is done")
}
```

If you call `stop()` before the context finishes and the callback hasn't run yet (meaning the goroutine hasn't been triggered), stopped will return `true`, indicating that you successfully stopped the callback from running.

But, if `stop()` returns `false`, it could mean either the function `f` has already started running in a new goroutine, or it has been stopped already.

### Use an Unexported Empty Struct as a Context Key

Context (context.Context) is great for passing request-scoped values along with cancellation signals and deadlines, you might sometimes need to add and retrieve values from the context. Let’s look at a basic example:

```go
func main() {
    ctx := context.WithValue(
        context.Background(),
        "data", "request-scoped data",
    )

    handleRequest(ctx)
}

func handleRequest(ctx context.Context) {
    fmt.Println("data:", ctx.Value("data"))
}

// Output: "data: request-scoped data"
```

Here, the challenge is making sure the key used to store values in the context is unique. 

Using a string like "data" as a key could lead to conflicts if someone else in another part of your program uses the same string as a key. To avoid this, you can use an empty, unexported struct as a key, since each struct is unique in package scope:

```go
type empty struct{}
type anotherEmpty struct{}

func main() {
    ctx := context.WithValue(
        context.Background(),
        empty{}, "request-scoped data",
    )

    handleRequest(ctx)
}

func handleRequest(ctx context.Context) {
    fmt.Println("data:", ctx.Value(empty{}))
    fmt.Println("data:", ctx.Value(anotherEmpty{}))
}

// Output:
// data: request-scoped data
// data: <nil>
```

Basically, using an unexported (private) empty struct helps you avoid any potential conflicts with other packages.

> "Can I use other types, but the underlying type is still a string or int?"

Yes, we can use another type, and it should avoid conflict. For instance, a number(0) which has an underlying type of int and int(0) are different:

```go
type number int

func main() {
    ctx := context.WithValue(
        context.Background(),
        number(0), "value from number type",
    )

    ctx = context.WithValue(
        ctx,
        0, "value from int type",
    )

    handleRequest(ctx)
}

func handleRequest(ctx context.Context) {
    fmt.Println("data:", ctx.Value(number(0)))
    fmt.Println("data:", ctx.Value(0))
}

// Output:
// "data: value from number type"
// "data: value from int type"
```

This works because in Go, two `interface{}` values are only equal if both their type and value match. So, they're different types and not equal.

- The first value: { type: number, value: 0 }
- The second value: { type: int, value: 0 }

They are different types, so they are not equal.

> "But why use an empty struct{}?"

An empty struct doesn't allocate any memory since it has no fields and therefore no data, but its type can still uniquely identify context values, this makes it a lightweight and conflict-free option for keys.

Of course, there are still cases where you might use type definitions that have an underlying primitive type. 

_Using context values can be tricky, especially when writing business logic. It's not compile-time safe and can be difficult for tracking and debugging because it's an implicit way of passing data._

### Handle Errors of Deferred Calls to Prevent Silent Failures

It's pretty easy to overlook error handling in deferred calls, and that can trap a lot of us:

```go
func doSomething() error {
    file, err := os.Open("file.txt")
    if err != nil {
        return err
    }
    defer file.Close()

    ...
}
```

In this snippet, if closing the file fails, maybe because something wasn't written properly or there's a glitch in the file system, and we don't check for that error, then we're missing a chance to catch and handle a potentially critical issue.

Now, if we stick with using defer, we've basically got three ways to deal with it:

- We can handle the error as part of the function's return.
- We could let the program panic, this might be a bit drastic unless it's a truly severe error that justifies crashing the program.
- Or we might just log the error and move on, it's simpler but means you're not actively handling the error—just noting that it happened.

But how about handling this as a function error? That's a bit more nuanced. We can use named return values to manage it neatly:

```go
func DoSomething(path string) (err error) {
    file, err := os.Open(path)
    if err != nil {
        return err
    }

    defer func() {
        if cerr := file.Close(); cerr != nil {
            err = errors.Join(err, cerr)
        }
    }()

    ...
}
```

In this version, we're using a named return variable `err` for the function's error return. 

Inside the deferred function, we check if `file.Close()` returns an error (captured as `cerr`). If it does, we use `errors.Join` to combine (not wrap) it with any existing error err might already hold. This way, the function can return an error that reflects issues from both the file opening and the file closing operations.

Alternatively, you could simplify it slightly:

```go
defer func() {
    err = errors.Join(err, file.Close())
}()
```

This shorter version does essentially the same thing but compresses it into a single line inside the defer. Now, even this shorter method adds a bit of complexity because of the anonymous function, which increases the nesting and can make the code a bit harder to follow.

There's actually another way to handle errors in deferred calls using a neat helper function:

```go
defer closeWithError(&err, file)

func closeWithError(err *error, f func() error) {
    *err = errors.Join(*err, f())
}
```

> "Hold on, doesn't this risk a panic because of dereferencing err when err could potentially be nil?"

That's a valid concern, but here's the twist: it actually works just fine.

Here’s why: In Go, error is an `interface{}`, and a nil error doesn't behave the same way as a nil pointer does for other types, like say, a *int. 

A nil error is represented internally as` {type = nil, value = nil}`, but it's still a valid, usable value (the zero value for interfaces), to be precise.

So, when we use `&err` in the defer closeWithError(&err, file) call, we're not dealing with a nil pointer scenario. What we're actually getting is a pointer to an interface variable that itself holds {type = nil, value = nil}.

This means that in the `closeWithError` function, when we dereference the error pointer with `*err` to assign a new value, we're not dereferencing a nil pointer (which would indeed cause a panic). Instead, what we're doing is modifying the value of an interface variable through its pointer. This is a safe operation and avoids the panic you might expect.

The solution is inspired by [David Nix](https://twitter.com/davidnix_).

### Always Keep Track of Your Goroutine's Lifetime

Goroutines are stackful, meaning they take more memory than similar constructs in other languages, at least 2KB each, this is small but not negligible.

Each time we launch a goroutine with `go doSomething()`, we're immediately reserving 2KB of memory (this was 4KB in Go 1.2 and increased to 8KB by Go 1.4).

So, the disadvantage is, when your Go program handles a lot of things at once, its memory usage can shoot up quicker compared to languages without such stack allocations.

This initial size is a starting point, and the Go runtime automatically adjusts the stack size of goroutines during their execution to accommodate the memory requirements of each goroutine's workload.

The process works like this: when a goroutine's stack reaches its current limit, the Go runtime detects this condition and allocates a larger stack space. It then copies the existing stack's contents to the new, larger stack, and continues the goroutine's execution with this expanded stack space.

I've personally run into situations where I used a bunch of goroutines with for-loops and `time.Sleep`, like this:

```go
func Job(d time.Duration) {
    for ;; time.Sleep(d) {
        ...
    }
}
```
```go
func Job(d time.Duration){
    for {
        ...
        time.Sleep(d)
    }
}
```

Writing it this way can seem convenient, but it definitely has its drawbacks.

When we talk about gracefully shutting down your application, as outlined in our section on this topic [Gracefully Shut Down Your Application](#gracefully-shut-down-your-application), we hit upon a tricky issue with certain functions, like `time.Sleep`, which inherently don't support graceful shutdowns very well.

- Sleep -> SIGTERM -> Running -> Interrupted.

_[Avoid `time.Sleep()`, It's Not Context-Aware and Can't Be Interrupted](#avoid-timesleep-its-not-context-aware-and-cant-be-interrupted)_

So, for tasks that don't naturally come to an end, like serving network connections or monitoring a configuration file, it’s better to use cancellation signals or conditions to clearly define when these tasks should stop.

```go
func Job(ctx context.Context, d time.Duration) {
    for {
        select {
        case <-ctx.Done():
            return
        default:
            ...
            time.Sleep(d)
        }
    }
}
```

In this setup, the context should be derived from a base context, which gets canceled when a SIGTERM is received. This way, at least we know the task won’t be interrupted unexpectedly, even if it's right at program termination.

_But this doesn't completely solve the problem, what if we need to stop the goroutine almost immediately whenever we want? This is discussed in [Avoid `time.Sleep()`, It's Not Context-Aware and Can't Be Interrupted](#avoid-timesleep-its-not-context-aware-and-cant-be-interrupted))_

Now, consider another scenario where a goroutine could be stuck forever:

```go
func worker(jobs <-chan int) {
    for i := range jobs {
        ...
    }
}

jobs := make(chan int)
go worker(jobs)
```

You might think it's simple to determine when the routine finishes, just close the jobs channel. 

But when exactly is the jobs channel closed? If there’s a mistake and we don’t `close` the channel and return from the function, the goroutine hangs indefinitely, leading to a memory leak.

So, it’s important to make it obvious when goroutines start and stop, and to pass context to processes that run for a long time. 

### Avoid `time.Sleep()`, It's Not Context-Aware and Can't Be Interrupted

Using `time.Sleep()` might seem like a simple solution when you want to pause execution in your Go app, but it comes with a significant drawback: it's not context-aware and can't be interrupted.

Let's say you've got an application that's in the process of shutting down. 

If there's a function that's currently sleeping due to `time.Sleep()`, we can't just wake it up and tell it to stop what it's doing. It will wake up on its own, start executing the next lines of code, and only then might it realize, "Oh, I should stop," because the rest of the application is shutting down. 

Here’s a personal "anecdote", I've actually made this mistake myself and it turned into a pretty good learning experience:

```go
func doJob(d time.Duration) {
    for ;; time.Sleep(d) {
        ...
    }
}

func doJob() {
    for {
        ...

        time.Sleep(d)
    }
}
```

In these loops, the function does some work, pauses for, let’s say, 5 seconds with `time.Sleep()`, and then continues. The problem is, these loops can’t be stopped by a context cancellation. 

A better way is to make your functions respect the context:

```go
func doWork(ctx context.Context, d time.Duration) {
    for {
        select {
        case <-ctx.Done():
            return
        default:
            time.Sleep(d)
        }

        ... 
    }
}
```

This version is a bit more verbose, but now our job respects the context. For example, if a shutdown signal is sent, the function checks the context and can stop immediately if it's done:

1. -> doWork -> sleep -> shutdown -> ctx.Done() -> out.
2. -> doWork -> shutdown -> sleep -> ctx.Done() -> out

There's still a catch. 

We have to wait out the sleep duration, which could be up to 5 seconds or possibly even longer, depending on how the delays are set up in the job. This isn't ideal if you need the function to be able to respond instantly to a shutdown command. 

So while this approach is better, it's not perfect.

**Maybe we can do better?**

We've been using the time package to manage our pauses, but there are a couple of tweaks we can consider to improve how this all works.

```go
func doWork(ctx context.Context, d time.Duration) {
    for {
        select {
        case <-ctx.Done():
            return
        case <-time.After(d):
        }

        ...
    }
}
```

This strategy is simple and gets the job done, but it's not without its flaws. 

- It creates a new timer channel each time it runs through the loop. This can lead to unnecessary allocations. 
- Also, there’s a potential issue with short-term memory leaks, something the Go community often points out. If the function exits because of **ctx.Done()** before the timer runs out, that time.After timer is still ticking in the background until it completes and that's not ideal.

Now, we might consider a slightly more complex solution that handles timers more efficiently:

```go
func doWork(ctx context.Context, d time.Duration) {
    delay := time.NewTimer(d)

    for {
        select {
        case <-ctx.Done():
            if !delay.Stop() {
                <-delay.C
            }
            return
        case <-delay.C:
            _ = delay.Reset(d)
        }

        ...
    }
}
```

Here, we're using a single timer and resetting it each cycle, which is more efficient. When the context signals done, it’s important to stop the timer to prevent any leaks.

If the timer has already been stopped, we ensure that the channel is cleared by receiving from `delay.C`. 

> "Why not just Ticker?"

Tickers are indeed a cleaner solution for certain scenarios.

While timers are typically used for one-off events, tickers are designed to handle repeated events. However, one of the nuances of using a ticker is that it doesn’t pause to check if the previous task has finished and it will keep ticking according to the set interval.

For instance, if we set a `Ticker` for 1 minute but our task takes 2 minutes, the `Ticker` will still send to the channel after one minute, regardless. This means as soon as our longer task finishes, the Ticker **immediately** triggers the task to start again without delay.

> "Can we reset the Ticker after each task completes to avoid overlapping tasks?"

The answer is... maybe not. 

Let’s walk through a scenario to illustrate this:

```go
func doWork(ctx context.Context, d time.Duration) {
	now := time.Now()
	delay := time.NewTicker(d)

	for {
		workIn2Minute()
		delay.Reset(d)

		select {
		case <-ctx.Done():
			delay.Stop()
		case <-delay.C:
		}
	}
}
```

In this setup, even though we reset the ticker after `workIn2Minute()` completes, the ticker’s tick might already be sent to the channel while the task was running. This can cause the next task to start immediately, which isn’t what we want.

Now, consider changing the placement of the job:

```go
func doWork(ctx context.Context, d time.Duration) {
    now := time.Now()
    delay := time.NewTicker(d)

    for {
        select {
        case <-ctx.Done():
            delay.Stop()
            return
        case <-delay.C:
        }

        workIn2Minute()
        delay.Reset(d)
    }
}
```

Now, the `workIn2Minute` function is executed after the delay, but again, this isn't working because the tick has already been sent to the channel while the `workIn2Minute` is being executed. So, we need to `delay.Stop()` the ticker before executing the task and then reset it after the task is completed. However, this brings us back to our Timer solution.

Note that the timer also encounters this issue when the task is placed before the delay.

### Implement a Context-Aware `Sleep` Function

The regular `time.Sleep()` doesn't care about context as we pointed out in the previous tip.

We've discussed a solution before, but it can be quite a bit to write every time you need it. So, how about we make a more user-friendly version that behaves like a `Sleep` function but also respects when the context is canceled?

We can create a kind of "fake" sleep function that halts if the context signals to do so:

```go
func Sleep(ctx context.Context, d time.Duration) error {
    timer := time.NewTimer(d)
    defer timer.Stop()

    select {
    case <-ctx.Done():
        return ctx.Err()
    case <-timer.C:
        return nil
    }
}
```

This setup allows us to pause our Go code, but if something tells the context to stop, the sleep will end early, like this:

```go
func Job(ctx context.Context) {
    for {
        select {
        case <-ctx.Done():
            return
        default:
            ...
            _ = Sleep(ctx, 10 * time.Second);
        }
    }
}
```

> "Wait, Why don't you handle the error of sleep()?"

Well, we could, but it's not necessary.

most of the time, when the context is canceled, it’s not really about the sleep function messing up. It’s usually due to a larger issue in the program, which includes the sleep part but isn’t limited to it.

If there are other steps after the sleep() in your code, and they're designed to listen to the context, they'll halt as well if the context is canceled.

So, let’s simplify our `sleep` function to make it shorter and more direct:

```diff
- func Sleep(ctx context.Context, d time.Duration) error {
+ func Sleep(ctx context.Context, d time.Duration) {
    timer := time.NewTimer(d) 
    defer timer.Stop()

    select {
    case <-ctx.Done():
-       return ctx.Err()
+       return  
    case <-timer.C:
-       return nil
+       return
    }
}
```

This way, the function is easier to use and integrate into various parts of your code where you might need a context-aware pause.

## Concurrency & Synchronization

### Prefer `chan struct{}` Over `chan bool` for Signaling Between Goroutines

When we're working with goroutines and need to signal between them, we might wonder whether to use `chan bool` or `chan struct{}`. 

> "Why prefer 'chan struct{}'?"

So, `chan bool` can signal events too, right? It sends a boolean value, `true` or `false`, which might carry some specific meaning depending on how you set it up. But here's where it gets tricky:

```go
type JobDispatcher struct {
    start chan bool
}


func NewJobDispatcher() *JobDispatcher {
    return &JobDispatcher{
        start: make(chan bool),
    }
}

// Unclear: What does sending true or false mean?
```

When you use this, it could be confusing. 

Like, do you send true to start? Or does true mean stop? It's not always clear, and that can lead to some pausing moments when someone else tries to figure out your code or even when you come back to it after a while.

Now, let’s talk about chan `struct{}`, this type is used purely for signaling because the `struct{}` type doesn’t occupy any memory at all, just like saying: "Hey, something happened." without sending any actual data.

```go
type JobDispatcher struct {
    start chan struct{}
}

func NewJobDispatcher() *JobDispatcher {
    return &JobDispatcher{
        start: make(chan struct{}),
    }
}

func (j *JobDispatcher) Start() {
    j.start <- struct{}{}
}

// Clear: Sending anything means "start the job"
```

So what are the main advantages here? 

- First off, since `struct{}` is zero size, sending a value over a chan `struct{}` doesn’t actually move any data across the channel, it’s just the signal. This is a subtle but nice memory optimization. 
- When a dev sees chan `struct{}` in code, it’s immediately clear that this channel is used for signaling, which cuts down on confusion.

The downside? 

It's that somewhat awkward `struct{}{}` syntax, but this small inconvenience is worth it because it prevents the channel from being misused for transmitting data when all you want is a simple signal.

And for a one-time signal, you might not even need to send a value. You could just close the channel:

```go
func (j *JobDispatcher) Start() {
    close(j.start)
}
```

Closing the channel is a clear and effective way to broadcast a signal to multiple receivers that the job should start, all without sending any data

### Buffered Channels as Semaphores to Limit Goroutine Execution

When we're looking to control **how many goroutines can access a particular resource at the same time**, using a semaphore is a good choice. We can simply create a semaphore using a buffered channel in Go. 

The size of the channel dictates the number of goroutines that can run concurrently:

```go
semaphore := make(chan struct{}, numTokens)
```

Here's the basic flow:

- A goroutine attempts to send a value into the channel, taking up one of the available slots.
- Once the goroutine completes its task, it removes the value from the channel, freeing up that slot for another goroutine to use..

```go
var wg sync.WaitGroup
wg.Add(10)

for i := 0; i < 10; i++ {
    go func(id int) {
        defer wg.Done()
        semaphore <- struct{}{} // Acquire a token.
        ...
        <-semaphore // Release the token.
    }(i)
}

wg.Wait()
```

In this snippet:

- `wg.Add(10)`: sets us up for 10 goroutines.
- `make(chan struct{}, 3)`: initializes a semaphore that permits only 3 goroutines to operate simultaneously.

If you're looking for a more structured way, we could define a `Semaphore` type to encapsulate all semaphore-related actions:

```go
type Semaphore chan struct{}

func NewSemaphore(maxCount int) Semaphore {
	return make(chan struct{}, maxCount)
}

func (s Semaphore) Acquire() {
	s <- struct{}{}
}

func (s Semaphore) Release() {
	<-s
}
```

Using this custom Semaphore type simplifies how we manage resource access:

```go
func doSomething(semaphore *Semaphore) {
    semaphore.Acquire()
    defer semaphore.Release()

    ...
}
```

Additionally, for more complex scenarios, you might want to look into the http://golang.org/x/sync/semaphore package, which offers a weighted semaphore implementation. 

This is particularly useful when some tasks might require more resources than others, such as managing a pool of database connections where certain operations need multiple connections simultaneously. 

Weighted semaphores allow a single goroutine to **consume more than one slot at a time**.

### Optimize Multiple Calls with `singleflight`

So, let’s say we got this function that pulls data from somewhere, and it’s not exactly quick, it takes about 3 seconds each time you call it:

```go
func FetchExpensiveData() (int64, error) {
    time.Sleep(3 * time.Second)

    return time.Now().Unix() / 10, nil
}
```

The simluation function emits a different number after 10 seconds.

Now, if we call this function 3 times back-to-back, we’re looking at waiting around 9 seconds.

We might think using 3 goroutines could cut that down to about 3 seconds, but we’d still be running the function 3 times, and all for the same result. 

This is where the `singleflight` package can really change the game and it’s designed to ensure that no **matter how many times we call the function in those 3 seconds, it only actually runs once and returns the same result** to everyone who called it.

We can find it at http://golang.org/x/sync/singleflight. 

Now, here’s how we can use it:

```go
var group singleflight.Group

func UsingSingleFlight(key string) {
    v, _, _ = group.Do(key, func() (any, error) {
        return FetchExpensiveData()
    })

    fmt.Println(v)
}
```

What happens here is pretty straightforward.

We create a `singleflight.Group`, then you wrap your expensive function call inside the `group.Do()` method, which is clever. 

It checks if the same key has already been requested. If it has, it waits for the result of the original call and returns it to all the callers, not just the first one.

And that's basically it.

> "What's the purpose of the 'key' argument"

It's what tells `singleflight` that multiple requests are actually asking for the same thing. So, it uses this key to check if it should run the function again or just wait and return the result of the ongoing operation.

If you want to see how this plays out in real code, you can check it out here: https://go.dev/play/p/30kdFPsy2HR

Basically, if the same function is called multiple times simultaneously, only one real call is made and the result from this single call is then shared among all the callers.

> "Why not using cache?"

Singleflight is not working as a cache, it's a way to get the result or call a function that highly be simultaneous run. 

The function may not return any result, the example may leads to misunderstand that this is for expensive data but we just want to make sure that we fire that function once.

Like sending a `ping()` to a server and you wouldn’t cache the ping result, but you also don’t want to overload the server with multiple pings at the same time.

### `sync.Once` Is the Best Way to Do Things Once

So, we know how sometimes we need to make sure something only happens once in our app, even if we've got a ton of things running at the same time? 

Let's talk about that using `sync.Once`, which is extremely common for things like setting up a singleton.

For example, we've got a config object, right? And we need to set it up just once and then use that same setup everywhere else in our app:

```go
var instance *Config

func GetConfig() *Config {
    if instance == nil {
        instance = loadConfig()
    }

    return instance
}
```

But here's the catch, if a bunch of goroutines try to get this config all at the same time, and it hasn't been set up yet, you could end up running `loadConfig()` multiple times, which isn't what we want.

This is where `sync.Once` comes in:

```go
var (
    once     sync.Once
    instance *Config
)

func GetConfig() *Config {
    once.Do(func() {
        instance = loadConfig()
    })

    return instance
}
```

We wrap the setup code in a function and pass it to `once.Do()` and this way, no matter how many goroutines are calling `GetConfig()` simultaneously, the `loadConfig()` function is guaranteed to only run once. 

This ensures that everyone gets the same instance of `Config` without setting it up multiple times.

Now, it's important to understand that `sync.Once` doesn't mean a function will run only once in the general sense, but rather that it will only execute once within the context of that specific `sync.Once` instance, this is a big difference.

Here's the thing, if you try to use `sync.Once` again with a different function, it won't work, even if that new function hasn't been run with `sync.Once` before:

```go
o.Do(f1)
o.Do(f2)
```

The second function, `f2`, will just be ignored if `f1` has already run. That's because `sync.Once` isn't about the function, it's about the act of doing something just once, regardless of what that something is.

From Go version 1.21, the `sync` package provides expanded functionalities, which are quite useful. We now have `sync.OnceFunc`, `sync.OnceValue`, and `sync.OnceValues`. These new functions transform a standard function into a sync.Once-like function, ensuring that the function is executed just once, regardless of how many times it is called. 

Here's how they are defined:

```go
func OnceFunc(f func()) func()
func OnceValue[T any](f func() T) func() T
func OnceValues[T1, T2 any](f func() (T1, T2)) func() (T1, T2)
```

This addition is particularly useful when you need to return a value from a function that you intend to execute only once, and this can even include handling errors:

```go
var GetConfig = sync.OnceValues(func() (Config, error) {
    return Config{..}, nil
})
```

`GetConfig` will only be executed once to retrieve configurations, potentially including an error, and after its first call, any further calls to `GetConfig` will return the same results that were obtained the first time and the function itself won't actually run again.

Let's dive a bit into how `sync.Once` works internally, a `sync.Once` keeps track of two things:

- An atomic counter, which can be either 0 or 1.
- A mutex, which is used to protect the slower operations.

Now, let me break down the structure of sync.Once for you:

```go
type Once struct {
    done atomic.Uint32
    m    Mutex
}
```

**The fast path**

When you call `once.Do(f)`, the first thing it does is check the atomic counter and if the counter is at 0, it indicates that the function hasn't been run yet. 

This check allows any subsequent calls to bypass the function execution if it’s already been done, which speeds things up considerably.

**The slow path**

Now, if the counter is 0, `sync.Once` switches to what's called the slow path, which involves executing the function:

```go
func(o *Once) doSlow(f func()) {
    o.m.Lock()
    defer o.m.Unlock()

    if o.done.Load() == 0 {
        defer o.done.Store(1)
        f()
    }
}
```

- `o.m.Lock()`: This locks the mutex to ensure that only one goroutine can execute the following steps at any given time.
- `o.done.Load() == 0`: Once it has the lock, it checks the counter again just to be sure that no other goroutine has run the function in the meantime.
- `o.done.Store(1)`: After the function has been executed, it updates the counter to 1, indicating that the function has been run and shouldn’t be run again.

> "Why slow path and fast path?"

The reason we have both a fast path and a slow path is to balance speed and safety. 

The fast path allows the system to bypass the mutex lock and the function execution if it’s already been done, which is much faster. 

The slow path, however, ensures that the function is executed safely the first time without interference from other goroutines. This initial setup might be a bit slower, but once it’s done, every call to `once.Do()` is quick, which is good in the long run.

### Manage Multiple Goroutines with `errgroup`

When we're dealing with multiple goroutines, managing them and their errors can get a bit tricky.

We might already be familiar with `sync.WaitGroup` for handling multiple goroutines. But, there's another tool called `errgroup` that offers some neat features to simplify this process even more.

First, you'll need to grab the package:

```go
$ go get -u golang.org/x/sync
```
Imagine you want to fetch data from multiple URLs concurrently:

```go
func main() {
    urls := []string {
        "https://blog.devtrovert.com",
        "https://example.com",
    }

    var g errgroup.Group

    for _, url := range urls {
        url := url // safe before Go 1.22
        g.Go(func() error {
            return fetch(url)
        })
    }

    if err := g.Wait() ; err != nil {
        log.Fatal(err)
    }
}
```

In this snippet, we're fetching 2 pages simultaneously. The `g.Wait()` method is the method we want to focus on, it waits for all the goroutines to finish. If any of the goroutines encounter an error, `g.Wait()` will return the first error it encounters.

Errgroup simplifies the process of managing multiple goroutines and handling errors that might occur during their execution.

Let's break down the key points:

- Start each task in its own goroutine using `g.Go()` and pass it a function that does the work.
- Use `g.Wait()` to wait for all goroutines to complete, it returns the first error encountered among the goroutines, not all errors.
- Errgroup works seamlessly with context. By using errgroup.WithContext(), if any goroutine fails and returns an error, the context is automatically canceled.

**What's going on inside**

The `errgroup.Group` struct is built using a few components from the Go standard library:

```go
type Group struct {
    cancel func()
    
    wg sync.WaitGroup
    sema chan struct{}
    errOnce sync.Once

    err error
}
```

- `wg sync.WaitGroup`: This is used to wait for all goroutines to finish their tasks.
- `errOnce sync.Once`: Make sure the first error captured is done in a thread-safe manner, meaning no race conditions when setting the error.
- `sema chan struct{}`: A semaphore controls the number of goroutines that can run at the same time. We can even set a limit on the number of concurrent goroutines with errg.SetLimit().

Whenever you use g.Go(), you're essentially adding a new task to a group. This task is a function that doesn't take any arguments but returns an error. The number of concurrent goroutines that can run at the same time is managed by a [semaphore]((#buffered-channels-as-semaphores-to-limit-goroutine-execution)), which helps to control the execution and prevent too many tasks from running simultaneously.

Here's what the implementation looks like:

```go
func (g *Group) Go(f func() error) {
	if g.sem != nil {
		g.sem <- token{}
	}

	g.wg.Add(1)
	go func() {
		defer g.done()

		if err := f(); err != nil {
			g.errOnce.Do(func() {
				g.err = err
				if g.cancel != nil {
					g.cancel(g.err)
				}
			})
		}
	}()
}
```

In this setup, error handling is centralized through the `errOnce` mechanism, which makes sure that only the first error is recorded. If an error occurs, and a context is provided, it triggers a cancellation signal of that context. 

This setup means that as soon as one goroutine fails, it can prevent unnecessary work by other goroutines.

Now, let's look at what happens when a goroutine completes:

```go
func (g *Group) done() {
    if g.sem != nil {
        <-g.sem
    }
    g.wg.done()
}

func (g *Group) Wait() error {
	g.wg.Wait()
	if g.cancel != nil {
		g.cancel(g.err)
	}
	return g.err
}

```

The `done()` function plays an important role, it not only signals the `WaitGroup` that a goroutine has completed, but it also manages the semaphore to make sure that the limits on running goroutines are respected.

It's also good to remember that using goroutines might not always be the best solution, especially for tasks that are quick to complete. Sometimes, running tasks sequentially, one after the other, might turn out to be faster and easier to manage.

### Adjusting `GOMAXPROCS` for Containerized Environments (Kubernetes, Docker, etc.)

Changing `GOMAXPROCS` for containerized environments like Kubernetes or Docker is something you might need to consider.

**But, what is GOMAXPROCS?**

By default, Go can execute up to 10,000 threads concurrently, but the actual number running in parallel depends on a critical setting: GOMAXPROCS. It's not just about being concurrent; it's about actual parallel execution.

By default, `GOMAXPROCS` is set to the number of logical CPU cores available to your OS, as reported by `runtime.NumCPU()`:

```go
fmt.Println("GOMAXPROCS:", runtime.GOMAXPROCS(0))
fmt.Println(runtime.NumCPU())

// Output:
// 8
// 8
```

For example, on my `MacOS`, that's 8 cores. This means that by default, Go is configured to run up to 8 threads simultaneously.

#### Running Go in Containers (Docker & Kubernetes)

In a containerized setup like in Kubernetes, you can defintely limit the CPU usage for each container. 

When you set these limits, you're essentially telling the container, _"This is your portion of the CPU resources."_ For example, a limit of 250m means 1/4 of a core, and a limit of 1 means 1 full core.

Sadly, Go doesn't automatically recognize this, it still sees and uses the total number of CPU cores available on the host machine (or node), not just the slice allocated to the container. 

This can lead to situations where a Go program tries to use more CPU cores than it's supposed to, based on the container's settings.

> ""But why? I thought it was better when using more CPUs."

Well, it might seem like using more CPUs would always be better, but there are a few reasons why this isn't necessarily the case:

1. Context switching: When there are more threads than CPU cores, the OS has to switch between these threads more often and this switching can slow things down as it takes time and resources to handle.

2. Inefficient scheduling: The Go scheduler might create more runnable goroutines than can actually be executed given the CPU constraints, this leads to contention for CPU time, where threads are essentially fighting over processing power.

3. Inefficient use of CPU Bound: Go application are typically CPU bound, which means they perform best when each thread can run on its own CPU core without having to wait for others. 

If you have more `GOMAXPROCS` than CPU cores available, it forces the Go runtime to plan for more threads than there are cores, leading to inefficient execution of these CPU-bound tasks.

So, what can we do about it?

#### The Solution

For those who prefer a more hands-off strategy, the `uber-go/automaxprocs` package might be just what you need. 

This library automatically adjusts `GOMAXPROCS` to match your container's CPU limit:

```go
import _ "go.uber.org/automaxprocs"

func main() {
    ...
}
```

Otherwise, if you're familiar with your deployment or pod specifications, you might just set the `GOMAXPROCS` environment variable to match your CPU limit manually.

However, from a DevOps perspective, it's generally better to [avoid setting CPU limits](https://home.robusta.dev/blog/stop-using-cpu-limits) while always specifying CPU requests.

#### How Does it Work?

Turns out, when we set `resources.limits.cpu` for our pod, a component named Cgroups, or Control Groups, manages this for us. Cgroups are part of the Linux kernel and they help manage and prioritize resources like CPU, memory, and I/O bandwidth for groups of processes.

Here’s a bit of code to give you an idea:

```go
type CGroup struct {
    path string
}

// NewCGroup returns a new *CGroup from a given path
func NewCGroup(path string) *CGroup {
    return &CGroup{path: path}
}

// CGroups is a map that associated each CGroup with its subsystem name
type CGroups map[string]*CGroup
```

We ran some tests in our staging cluster to see how this works in practice, and the results were pretty much what we expected:

```bash
# no limit
maxprocs: Leaving GOMAXPROCS=4: CPU quote undefined

# 200m = 1/5 core
maxprocs: Updating GOMAXPROCS=1: using minimum allowed GOMAXPROCS

# 1.5 cores
maxprocs: Updating GOMAXPROCS=1: determined from CPU quota

# 2 cores
maxprocs: Updating GOMAXPROCS=2: determined from CPU quota
```

As you can see, without a set limit, it uses up to 4 cores of our worker node. This ties back to why we usually don't set a CPU limit, which we discussed earlier.

So, what's the deal with uber-go/automaxprocs and cgroups?

We might have guessed by now that the `automaxprocs` package relies on Cgroups to do its job and here's the process:

1. Checking GOMAXPROCS in the Environment

It first checks if the GOMAXPROCS environment variable is set. If it is, the package respects this value and doesn't make any changes:

```go
if max, exists := os.LookupEnv(_maxProcsKey); exists {
    ... // leaving the GOMAXPROCS as is
}
```

2. CPU quota retrieval

If GOMAXPROCS isn’t set, the package then tries to find out the CPU quota for the current process, it does this by looking up the Linux cgroups to see if there’s a CPU quota set for the container.

> "But what happens internally?"

It checks whether the system uses Cgroups v1 or v2 by looking at the filesystem and reading specific files. While you might not need to worry about the details, it looks at `/proc/self/mountinfo` for mount points and `/proc/self/cgroup` for cgroup membership.

- For cgroups v1, it reads parameters from files in the cgroup filesystem, usually mounted under /sys/fs/cgroup. It looks for `cpu.cfs_quota_us` and `cpu.cfs_period_us` to calculate the CPU quota.
- For cgroups v2, it checks the `cpu.max` file in the cgroup directory, which lists both the quota and period on a single line.

If the calculated value falls below a minimum threshold (defaulting to 1 but customizable), it defaults to the minimum.

3. No CPU limit specified

If no quota is defined, it leaves GOMAXPROCS at its default setting.

### `sync.Pool` but Make It Typed-Safe with Generics

So, let's move to `sync.Pool` and for those who might not be familiar, `sync.Pool` is a feature from the Go standard library that's all about reusing objects. 

This is pretty handy because it helps cut down on the number of memory allocations, which is great for boosting performance.

Imagine you've got a super fast printer that needs to churn out 100 pages per minute. It wouldn't make much sense for the printer to run to the storeroom to grab new ink and paper for every single page, right? 

Instead, think of it like this: the printer has a tray loaded with about 100 sheets of paper that it can use **repeatedly**. You've got a set batch of items on hand, ready to be used over and over, saving you both time and resources. 

Here’s a little snippet to show what that might look like in code:

```go
var paperPool = sync.Pool{
    New: func() interface{} {
        return new(Paper) // create a new sheet of paper
    }
}

func printPage() {
    page := paperPool.Get().(*Paper)
    page.Reset() // make sure to reset the page before using it

    defer paperPool.Put(page)

    page.Print()
}
```

But there are a few things to notice:

- `sync.Pool` doesn’t have a fixed size, which means you can keep adding and retrieving items without any hard limits.
- Once you put an object back in the pool then forget about it because it might get removed or collected by the garbage collector.
- Since objects can have states, it's crucial to clear or reset their state before putting them back in the pool or right after taking them out.

#### Typed-safe Pool

We already know that `sync.Pool` uses the empty `interface{}` for storing and retrieving items. This is pretty flexible but doesn't provide type safety, we can wrap this process to make it type-safe:

```go
type Pool[T any] struct {
    internal sync.Pool
}

func NewPool[T any](newF func() T) *Pool[T] {
    return &Pool[T]{
        internal: &sync.Pool{
            New: func() interface{} {
                return newF()
            }
        }
    }
}
```

With this setup, we've created a pool that's tied to a specific type, T, although it still uses `interface{}` under the hood. The magic happens in how we handle getting and putting data in a type-safe manner:

```go
func (p *Pool[T]) Get() T {
    return p.internal.Get().(T)
}

func (p *Pool[T]) Put(x T) {
    p.internal.Put(x)
}
```

When we retrieve data from the pool, we simply convert the interface to type T. You might wonder why we're not checking for errors during this conversion.

> "Why don't we check for errors in conversion?"

Here's the thing: the generic layer we've added ensures the type for us, so there's no need to worry about the conversion failing.

The `sync.Pool` will only ever contain instances of type T, so the assertion `p.internal.Get().(T)` is safe and won't cause a panic under normal scenarios. This solution keeps our code clean and efficient.

### Making a Type with Built-in Locking (`sync.Mutex` Embedding)

In scenarios where different parts of your application need to access shared resources concurrently, it's common to use a `sync.Mutex` to manage access and ensure data integrity.

Typically, you might see something like this:

```go
type MyStruct struct {
    mu sync.Mutex

    ...
}

func (s *MyStruct) DoSomething() {
    s.mu.Lock()
    defer s.mu.Unlock()

    ...
}
```

This pattern, while effective, can lead to code that's littered with `mu.Lock()` and `mu.Unlock()` calls, which can make it harder to read and maintain.

A neater way to handle this is by embedding `sync.Mutex` directly into your struct, this allows you to call `Lock` and `Unlock` directly on instances of the struct:

```go
type MyStruct struct {
    sync.Mutex

    ...
}

func (s *MyStruct) DoSomething() {
    s.Lock()
    defer s.Unlock()

    ...
}
```

This solution simplifies the syntax and makes your locking mechanism less intrusive in your method implementations. However, there's a caveat, if `MyStruct` is exported (i.e., it starts with an uppercase letter), embedding `sync.Mutex` directly also makes the `Lock` and `Unlock` methods publicly accessible.

Therefore, this technique is generally more suited for types that are internal to a package, where exposing locking mechanisms isn't a concern. For public types, you might still prefer to keep the mutex as a private field to control the exposure of your synchronization primitives.

**The Generic Trick**

While checking some coding concepts and resources, I came across a really interesting method that caught my attention. 

It involves using generics in Go to create a type-safe, lockable structure, here’s how you can implement it:

```go
type Lockable[T any] struct {
    sync.Mutex
    Value T
}

func (l *Lockable[T]) SetValue(v T) {
    l.Lock()
    defer l.Unlock()

    l.Value = v
}

func (l *Lockable[T]) GetValue() T {
    l.Lock()
    defer l.Unlock()

    return l.Value
}
```

This structure allows us to wrap any type with a mutex, making sure that the data is safe from concurrent access issues. We can use this generic type in a couple of ways, either directly or by defining a new type based on it:

```go
// directly use
func main() {
    var safeUser Lockable[User]

    safeUser.Set(..)
    safeUser.Lock()
}

// or make a new type
type IntLockable Lockable[int]
```

> "why we don’t just embed the type T directly instead of using Value T"

The reason is that Go currently does not support embedding a type parameter directly within a struct. 

### How to Send on a Channel Without Getting Stuck

Normally, when we send something over a channel, the code sits there and waits for a receiver to be ready to take the value, like this:

```go
ch <- value // blocking operation
```

But what if we’re in a situation where we don’t want to wait around? 

Maybe we’ve got a system where we’re managing resources with a semaphore, and we’ve got a function like `TryAcquire()` that should immediately tell we if it can’t acquire a resource because they’re all in use.

_[Buffered Channels as Semaphores to Limit Goroutine Execution](#buffered-channels-as-semaphores-to-limit-goroutine-execution)_

Let’s take a look at how something like this is handled in practice, using the `errgroup` package as an example. This package uses a simple semaphore mechanism internally to limit the number of goroutines that can run at once.

Here’s what happens if the semaphore is full and you try to start a new goroutine:

```go
func (g *Group) TryGo(f func() error) bool {
    if g.sem != nil {
        select {
        case g.sem <- token{}:
        default:
            return false
        }
    }

    ...
}
```

The magic happens in the `select{}` statement. Usually, `select{}` is used to wait on multiple channel operations, but here it’s used in a clever way to attempt a non-blocking send:

- `case g.sem <- token{}`: This line tries to send a token to the semaphore channel g.sem. If there’s space in the channel (meaning it’s not at its capacity), the token is sent successfully, and the function proceeds.
- `default`: This part kicks in if the g.sem channel is already full. Instead of blocking and waiting for space to free up, it just moves straight to the default case.

The default case in a `select` statement is a tricky path since it executes immediately if no other case is ready. In this scenario, it lets us know right away by returning false, signaling that we couldn’t start a new goroutine because we’ve hit the limit we set for active ones.

## Error Handling

### Only Define Errors (`var ErrXXX = errors.New`) When It's Necessary for Your Client

It's pretty common to see a lot of error definitions in codebases, each one with a detailed name, verbose description. But is it always necessary? 

Let's think about that:

```go
var (
    ErrPriceTooHigh = errors.New("sale: input price is too high")
    ErrPriceTooLow  = errors.New("sale: input price is too low")
    ErrAlreadySold  = errors.New("sale: already sold")
)
```

In these cases, developers are trying to keep every possible error under tight control, they want to account for every scenario that could go wrong in their business logic. 

But honestly, this approach can be overkill for a few reasons:

- It becomes a burden for anyone maintaining the code, just imagine having to remember or constantly look up what each error means.
- It might seem like a good idea to you now, but give it a few weeks or months, and even you might forget why you created some of these errors.
- Sometimes, our client doesn't even need to know about these errors. For example, if your frontend already limits the input price range, why bother defining errors like `ErrPriceTooHigh` or `ErrPriceTooLow`? A general error message would be enough.

Only someone bypassing the frontend and hitting the API directly might run into these, and typically, that's not a behavior we want to support.

This principle of not over-defining errors isn't just for client-server interactions. 

It applies to internal code as well, let's say we're unable to publish a message to our message queue. Before we rush to create an `ErrPublishMessage`, consider whether it's really necessary. Will anyone need to catch this specific error?

**So, what's the recommended approach in these cases?**

If we're not expecting out clients (whether they're other parts of our code or external users of our library) to take specific actions based on different types of errors, it's usually best to keep things simple. 

Let me walk you through a couple of strategies that might work better in these scenarios. One simple way is to return a basic error when something goes wrong, like this:

```go
func Sale(price int) error {
    ...
    if isPriceHigh(price) {
        return errors.New("sale: input price is too high")
    }
}
```

This method is quite direct. But if you need to include more context in your error message, `fmt.Errorf` might be a better choice. 

It lets you format the error message with dynamic data, which can be really helpful for understanding the issue at a glance:

```go
func Sale(price int) error {
    ...
    if isPriceHigh(price) {
        return fmt.Errorf("sale: input price (%d) is too high, cap (%d)", price, cap)
    }
}
```

Now, in cases where you might need a custom error that can be reused instead of defining multiple error variables:

```go
type Error struct {
    Input int
    Min   int 
    Max   int
}
```

But then, when should you actually go ahead and define specific error variables? 

Well, there are certain situations where this makes perfect sense. For instance, when your application logic requires different reactions based on the type of error:

- You might want to retry an operation if a particular error occurs.
- Specific errors could trigger different logging mechanisms, warnings for some, and errors for others.
- Perhaps you need to show a popup, like running out of funds, please deposit more money.
- ...

In these cases, having predefined error variables can help you manage these different scenarios better.

### Make Your Errors Clear with `fmt.Errorf`, Don't Just Leave Them Bare

In Go, unlike some other languages where you might throw exceptions, errors are treated just as values. This means we usually return errors rather than throwing them:

```go
func doOperation() error {
    err := doSomething() 
    if err != nil {
        return err
    }

    return nil
}
```

Just returning errors like this, without any context, can sometimes frustrate the person trying to debug the code and try to figure out where exactly things went wrong. 

#### Using fmt.Errorf with %w

Now, with Go 1.13, there's a better way to add more information to errors while keeping the original error intact. This is done using `fmt.Errorf` with the `%w` verb, which wraps the error. 

This allows you to maintain the underlying error while providing more context:

```go
func doSomething() error {
    err := doAnotherThing()
    if err != nil {
        return fmt.Errorf("do another thing: %w", err)
    }

    return nil
}
```

> "I still don’t see the benefit, it’s just an error either way."

Well, let's look at a practical example to see why it's beneficial:

```go
func getResourceHandler(http.ResponseWriter, *http.Request)
|
| -> func authorizeUser(userID, resourceID string) error
|    |
|    | -> fetchUserFromDB(userID string) (User, error)
|
| -> func getResourceFromDB(resourceID string) (Resource, error)
```

Which of these error messages gives you more information?

- "retrieve resource: authorization check: fetch user from db: mongo: no documents in result"
- "retrieve resource: mongo: no documents in result"

The first one tells you right away that the issue is with fetching the user from the database. The second one is vague—it's unclear whether the problem is with the user or the resou

By wrapping errors and adding these layers of information, you don't just get a clearer picture of what went wrong, you also get to use tools like `errors.Is()` to check for specific types of errors:

```go
func readConfig(path string) error {
    return fmt.Errorf("read config: %w", ErrNotFound)
}

func main() {
    err := readConfig("config.json")
    if errors.Is(err, ErrNotFound) {
        fmt.Println("config file not found")
    }
}
```

Always make your errors as clear as possible, it's the rope the helps us when things go wrong.

### Enhancing Error Handling: Wrapping vs. Joining Errors in Go

In Go, wrapping errors is a standard approach, especially useful when you want to add more context or deal with multiple errors at once.

Let's start with how we traditionally add context to an error, we often use `%w` with `fmt.Errorf` like this:

```go
err := errors.New("this is an error")

return fmt.Errorf("call Func2: %w", err)
```

Now, when it comes to handling several errors **simultaneously**, previously we'd still lean on `fmt.Errorf`. With the release of Go 1.20, `fmt.Errorf` now allows wrapping multiple errors together:

```go
fmt.Errorf("%w: %w", err1, err2)
```

However, also with Go 1.20, we also got a newer, simpler tool for this job called `errors.Join()`:

```go
func Func2() error {
    err := Func1()
    if err != nil {
        return errors.Join(err, errors.New("error from Func2"))
    }
    return nil
}
```

But this doesn't mean we should discard `fmt.Errorf` altogether. Both methods are compatible with `errors.Is()`, which lets you check for specific errors. 

However, they serve slightly different purposes.

`fmt.Errorf` is excellent for adding more detail to a single error, enhancing clarity about what went wrong and where. On the other hand, `errors.Join` is ideal for grouping multiple errors, which is very helpful when you encounter several issues at the same time and want to keep track of all of them without missing any detail.

> "I still don't see the difference, why not just use fmt.Errorf all the time?"

Let's think about a situation where we have many functions that could each return an error, and we're running them at the same time in different goroutines. If several of these functions fail, we'll end up with multiple errors.

If we use `fmt.Errorf` to stack these errors, like `fmt.Errorf("%w: %w")`, it can create a confusing message. 

This method suggests a chain of dependencies among errors, which isn't accurate since these errors are happening concurrently, not sequentially.

Instead, using `errors.Join` in such scenarios makes more sense.

### Error Messages Should Not Be Capitalized or End With Punctuation

You might wonder why error messages in Go shouldn't start with a capital letter or end with punctuation. It can seem a bit strange at first, but there's a practical reason for it.

When you're working with error messages in Go, they often get wrapped or combined with other messages. Imagine an error message that starts with a capital letter showing up in the middle of another sentence, it can look really out of place. 

Here’s an example to show you what I mean:

```go
func openDatabase() error {
    return fmt.Errorf("Could not open the database.")
}

func initModule() error {
    return fmt.Errorf("Initialize module: %w.", openDatabase())
}

func startApp() error {
    return fmt.Errorf("Application startup failed: %w.", initModule())
}

func main() {
    fmt.Println(startApp().Error())
}

// Output: Application startup failed: Initialize module: Could not open the database...
```

Notice how each part starts with a capital letter? It kind of breaks the flow and makes the whole error message look a bit awkward. Now, if we start with lowercase letters, it fits together much better:

```go
func openDatabase() error {
    return fmt.Errorf("could not open the database")
}

func initModule() error {
    return fmt.Errorf("initialize module: %w", openDatabase())
}

func startApp() error {
    return fmt.Errorf("application startup failed: %w", initModule())
}

func main() {
    fmt.Println(startApp().Error())
}

// Output: application startup failed: initialize module: could not open the database
```

This way, it reads more like a continuous sentence rather than separate, awkward chunks.

> "Why no punctual?"

Take a look at the first example again, we'll see it ends with an ellipsis (...), which looks a bit odd. This happens because fmt.Errorf automatically formats it that way when you include punctuation at the end after `%w`.

### Avoid Returning -1 or nil to Indicate Error

In other programming languages, it's pretty common to see functions indicate errors or missing results by returning special values like `-1`, `null`, or even an empty string `""`. This method is known as "in-band error signaling."

```go
func OpenFile(fileName) *string {
    if (cannotOpenFile) {
        return nil
    }

    return fileContent
}
```

The big problem with in-band error signaling is that it forces whoever calls the function to always remember to check for these special values. And let's be honest, this is error-prone.

So, what’s Go's solution? 

#### Go's solution: Multiple return values

In Go, a function can return the usual result along with an error value that clearly indicates whether the operation was successful or not.

```go
func OpenFile(filename string) (string, error) {
	if cannotOpenFile {
		return "", fmt.Errorf("open file %q", filename)
	}
	return fileContent, nil 
}
```

If you try to use the result without checking the error, Go won't let you off the hook. It throws a compile-time error if you don't handle the error value, forcing you to deal with the possibility of failure right away:

```go
content, err := OpenFile("example.txt")

processFileContent(content)

// compile error: err is unused
```

So, what do you get with this?

- Clear separation of concerns: It's clear which part of the return is the actual result and which part tells you if something went wrong.
- Forced error handling: Go makes sure you don’t just ignore potential problems. This drastically cuts down on those sneaky bugs that happen when errors are overlooked.
- Better readability and maintainability: Your code practically documents itself, showing exactly where things can go wrong.

Of course, there are exceptions to every rule.

Even though Go generally prefers using multiple return values for clearer error handling, there are times when returning `nil` or `-1` makes sense and is practical. 

For instance, some functions in the Go standard library, like those in the “strings” package, use these special values to indicate specific outcomes and this can make string manipulation a bit less verbose.

But it also means you need to have domain knowledge to understand when these special values are used and what they mean, such as `io.EOF` in the `io` package.

### Single Touch Error Handling: Less Noise

Let's dive into something called single touch error handling, which is all about cutting down on unnecessary noise. We've got a couple of functions, A and B, and they're set up like this:

```go
func B() error {
    if err := doSomething(); err != nil {
        log.Printf("failed do something: %v", err)
        return err
    }

    return nil
}

func A() error {
    if err := B(); err != nil {
        log.Printf("unable to call B: %v", err)
        return err
    }

    return nil
}
```

In this setup, when B runs into trouble, it logs the error and then passes it up to A. Then A, upon catching this error, logs it again before possibly sending it further up the chain.

> "Why is this a problem?"

Now, you might think this is solid because it tracks the error at multiple points, but it's actually just creating a lot of noise.

Here’s why this can be problematic:

- Duplicate logs: This creates noise in your log files, making it harder to diagnose issues because the same error is recorded multiple times.
- Complex error handling: It makes the error handling logic more complicated than it needs to be.
- Potential for other errors: More code to handle errors means more room for bugs to sneak in.

The idea behind single touch error handling is pretty simple: each error should be handled once and only once at a well-defined point in your code.

Better Solution

So, a better way to handle this whole error logging and propagation issue is to choose clearly whether you're going to handle the error right there, or just pass it up the chain without adding any extra noise. 

If you're passing it up, it's often a good idea to add more context to help whoever ends up handling it understand what went wrong along the way.

```go
func B() error {
    if err := doSomething(); err != nil {
        return fmt.Errorf("do something: %w", err)
    }

    return nil
}

func A() error {
    if err := B(); err != nil {
        return fmt.Errorf("call B: %w", err)
    }

    return nil
}

// Centralized error logging when calling A
if err := A(); err != nil {
    log.Printf("failed to do A: %v", err)
}
```

In this setup, neither A nor B logs the error immediately. Instead, they wrap the error with additional context and pass it up.

The actual logging happens once, at the highest level where the function `A` is called. This is where you decide how to handle the error, maybe log it, maybe panic, or perhaps take some corrective action.

### Simplify Your Error Messages in `fmt.Errorf`

When you're working with errors in Go, it's extremely important to get the details right so you know exactly what went wrong. 

_[Make Your Errors Clear with `fmt.Errorf`, Don't Just Leave Them Bare](#only-define-errors-var-errxxx--errorsnew-when-its-necessary-for-your-client)_

We all know about using `fmt.Errorf` and how `%w` lets us wrap errors to maintain that chain of what went wrong:

```go
if err != nil {
    return fmt.Errorf("failed to open file %s: %w", filename, err)
}
```

But let's be real, sometimes our error messages turn into something like a mini saga: "**error while** crawling: **can't** retrieve log: **failed to** open file server-logs.txt: file not exist." It's informative, sure, but it's also a bit of an overkill.

We keep repeating phrases like "error while" and "failed to," which, to be honest, we already know because, well, it’s an error.

Here’s a slicker way to handle it:

```go
if err != nil {
    return fmt.Errorf("open file %s: %w", filename, err)
}
```

Look at the difference here, instead of that lengthy message, you get: _"crawling: retrieve log: open file server-logs.txt: file not exist."_ It’s straightforward, easier to read, and cuts right to the chase.

So, when you're crafting those error messages in Go, remember to keep them concise and focus on what action didn’t pan out. 

This way, we're not just making our code cleaner, shorter, but we're also making life easier for anyone who has to read through our logs later on.

Personally, I prefer to keep the "failed" or negative word in the error log message, but not the error itself, it's up to you to decide what works best for your team.

## Performance

### Pre-allocate Slices for Performance

Back in the day, I used to pre-allocate slices using something like `make(a, 10)`, but over time, I realized that I was making a common mistake by habitually using `append()`, which often led to slices filled with unwanted leading zeroes. 

For those who might not have seen this in action, let me paint you a picture with an example here:

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
a := make([]int, 5)
a = append(a, 1) // [0 0 0 0 0 1]
```

</td><td>

```go
b := make([]int, 0, 5)
b = append(b, 1) // [1]
```

</td></tr>
</tbody></table>

In the first example, I ended up with a slice full of zeroes before the actual data, this is pretty inefficient, right? To avoid this, it’s better to use `make(a, 0, 10)`. 

What happens when you append an item to a slice and it exceeds the slice's capacity? This is where Go has to do some heavy lifting behind the scenes to 're-scale' the slice:

1. First, it figures out the old length of the slice and how many new elements you want to add.

2. Then, it decides on the new capacity. If your slice is small, it might just double the current capacity. For larger slices, the increase is generally by a smaller factor to avoid using too much memory.

3. After deciding the new capacity, it allocates memory for this adjusted slice.

4. If your slice elements include pointers, Go takes extra steps to handle the memory correctly to maintain data integrity.

5. Next, the existing elements are copied over to the new memory location, ensuring everything stays intact.

6. Finally, the function provides you with a new slice that has the adjusted length and capacity ready to use.

So, what’s the moral of the story here? Whenever you can predict how much space you’ll need, go ahead and pre-allocate your slices. 

### Prefer `strconv` Over `fmt` for Converting to/from String

When it comes to converting numbers into strings in Go, choosing the right tool for the job can really make a difference in terms of performance. Now, you might think about using `fmt.Sprint`, but let's talk about why `strconv` might be a better choice for this specific task.

The strconv package is tailor-made for string conversion, which means it's optimized for tasks like converting numbers to strings. Every little bit of performance boost and memory saving really counts, especially when you're dealing with large-scale applications.

To give you a clearer picture, let's look at a simple benchmark comparison between fmt and strconv:

```go
func BenchmarkFmt(b *testing.B) {
    for i := 0; i < b.N; i++ {
        _ = fmt.Sprint(i)
    }
}

func BenchmarkStrconv(b *testing.B) {
    for i := 0; i < b.N; i++ {
        _ = strconv.Itoa(i)
    }
}
```

When we run these benchmarks, check out the performance difference:

```shell
BenchmarkFmt-8      23821753    50.17 ns/op    16 B/op  2 allocs/op
BenchmarkStrconv-8  100000000   11.47 ns/op     3 B/op  1 allocs/op
```

_(not sure about any optimization made by the compiler, but the context of both is the same)_

As you can see, `strconv.Itoa` is significantly faster and more efficient in terms of memory allocation compared to `fmt.Sprint`, but why is this the case?

- The `strconv.Itoa(..)` is specifically designed for converting integers to strings, this specialization allows it to execute more quickly than the more generalized `fmt` functions.
- On the other hand, `fmt.Sprint` and its variants need to perform a bit of extra work, they use reflection to understand what type of data they're dealing with and to determine the best way to format it as a string.

```go
func (p *pp) doPrint(a []any) {
	prevString := false
	for argNum, arg := range a {
+		isString := arg != nil && reflect.TypeOf(arg).Kind() == reflect.String
		...
+		p.printArg(arg, 'v') 
		...
	}
}
```

This process of reflection isn't free, it adds both time and memory overhead, which can be quite significant when you're processing large amounts of data or require high performance.

### Sort Fields in Struct from Largest to Smallest

The way fields are ordered in a Go struct can seem like a minor detail, but it actually has a significant impact on the memory usage of your programs, especially in what we call a hot path, where performance is critical.

Take a look at this example where we compare two struct definitions, `A` and `OptimizedA`.

```go
// 32 bytes
type A struct {
    A byte 
    B int32
    C byte
    D int64
    E byte
}

// 16 bytes
type OptimizedA struct {
    D int64
    B int32
    A byte
    C byte
    E byte
}
```

In the case of `A`, the struct consumes a total of 32 bytes, but `OptimizedA` reduces that to just 16 bytes, this difference arises from how the fields are aligned and padded within the struct.

Let’s break down these concepts a bit further:

- **Alignment**: This refers to how data types have specific alignment requirements based on their size. For instance, an `int32` might need to be aligned on a 4-byte boundary, meaning its starting memory address should be divisible by 4.
- **Padding**: To meet these alignment requirements, compilers often insert unused space (known as padding) between struct fields.

For a more visual understanding, let's look at how A is laid out internally, which effectively becomes 8x4 bytes due to how the fields are aligned and padded:

<div align="center">

|0|1|2|3|4|5|6|7|
|-|-|-|-|-|-|-|-|
|A| | | |B|B|B|B|
|C| | | | | | | |
|D|D|D|D|D|D|D|D|
|E| | | | | | | |

</div>

- A (byte): Occupies 1 byte, but since the next field, B, requires 4-byte alignment, there are 3 bytes of padding after A to align B properly.
- B (int32): Occupies 4 bytes directly after the padding, fitting perfectly on a 4-byte boundary.
- C (byte): Takes 1 byte, but then to align D, which needs an 8-byte alignment, 7 bytes of padding are added after C.
- D (int64): This field needs 8 bytes, and it fits snugly in its slot without any additional padding needed right after it.
- E (byte): This is the final byte, which follows D. Depending on the struct's overall alignment needs, there might be additional padding at the end to align the entire struct’s size to a boundary.

The way that the fields in OptimizedA are structured is pretty clever in terms of memory efficiency

<div align="center">

|0|1|2|3|4|5|6|7|
|-|-|-|-|-|-|-|-|
|D|D|D|D|D|D|D|D|
|B|B|B|B|A|C|E| |

</div>

In this configuration:

- D (int64): Positioned right at the beginning. This solution liminated any need for preceding padding which often just fills up space without any real benefit.
- B (int32): which follows, sits on a 4-byte boundary, this happens quite naturally after D.
- A, C, E (bytes): The single-byte types are grouped together next. Since these are bytes, they fit together without necessitating extra padding between them.

What we're seeing here is a strategic ordering from the largest to the smallest fields, this is not just about neatness but it's a calculated move to minimize padding. 

Less padding means a reduction in the total size of the struct, which in turn means it uses less memory.

A tool like [betteralign](https://github.com/dkorunic/betteralign), which I mentioned just now, is quite adept at finding these kinds of inefficiencies in field alignment. It can even offer automatic suggestions for reordering to enhance memory efficiency.

However, it’s important to remember that reordering fields for efficiency **isn’t always the right move**. 

Sometimes, maintaining an order that reflects how the fields are used or their importance in the broader context of our application makes more sense. This can make the code more intuitive and maintainable, even if it's not the most compact format memory-wise.

### Avoid Defer in Loops, or Your Memory Might Blow Up

The `defer` allows us to schedule a function to be executed at a later time, specifically just before the current function is about to return:

```go
func main() {
    defer fmt.Println("World")
    fmt.Println("Hello")
}

// Output:
// Hello
// World
```

However, the use of defer within a loop may not be advisable:

```go
for _, file := range files {
    f, err := os.Open(file)
    if err != nil {
        ...
    }
    defer f.Close()
    
    ...
    return nil
}
```

For the purposes of this explanation, let's not dive into handling errors that might occur when calling `f.Close()`, we have discussed that in _[Handle Errors of Deferred Calls to Prevent Silent Failures](#handle-errors-of-deferred-calls-to-prevent-silent-failures)_

Here are a couple of reasons why using defer in loops can be problematic:

1. Execution timing

All these deferred calls are queued up to execute only when the entire function is on the returning, not after each iteration of the loop. 

If our loop resides within a lengthy function, this setup means that none of our deferred tasks will be executed until much later

So, instead of freeing up these resources as soon as they are no longer needed, we potentially end up holding onto them until the very end of the function's execution.

2. Potential for memory blow-up

Each defer statement adds to the memory stack. 

In scenarios where we have a loop that cycles through hundreds or thousands of iterations, we might accumulate a significant number of deferred calls. 

Each of these calls, with its associated details such as the function pointer and arguments, needs to be stored somewhere until they can be executed. 

This storage happens within the function's stack frame or perhaps on the heap, depending on how the compiler manages memory allocation and optimization, but either way, it can lead to a memory blow-up.

**The solution**

There are a few strategies we can consider to mitigate the impact.

If you're searching for a simpler, or shall we say "lazy" solution, you might want to look into employing an anonymous function to encapsulate the `defer` statement. 

Here’s how you could structure it:

```go
for _, file := range files {
    func (fileName string) error {
        f, err := os.Open(file)
        if err != nil {
            ...
        }

        defer f.Close()
        ...
    }(file)
}
```

Or if you're aiming for a more structured method, you could extract the logic into a separate function or an in-function function. 

```go
doSomething := func(filename string) error {
    f, err := os.Open(filename)
    if err != nil {
        ...
    }

    defer f.Close()
    ...
}

for _, file := range files {
    if err := doSomething(file); err != nil {
        ...
    }
}
```

We could separate this functionality into a named function, or perhaps, **cautiously** choose not to use defer (Remember defer is still invoked even if a panic occurs, which can be a safety net in some scenarios).

### Case-insensitive String Comparison with `strings.EqualFold`

If our goal is to compare strings without considering case sensitivity, we might initially think about converting both strings entirely to lower case using `strings.ToLower()` or to upper case using `strings.ToUpper()`, and then performing a comparison:

```go
if strings.ToLower(a) == strings.ToLower(b) {
    ...
}
```

But Go provides a simpler and more effective method for case-insensitive string comparison: `strings.EqualFold`. 

This function is designed for such comparisons and offers a more optimal solution:

```go
if strings.EqualFold(a, b) {
    ...
}
```

The preference for `strings.EqualFold` isn't just due to its brevity. It's specifically optimized for case-insensitive comparisons, which not only simplifies the code but significantly enhances performance.

> "Is strings.EqualFold faster than using ToLower and then comparing?"

Yes, it definitely is. 

When we compare the performance numbers, it's clear that `strings.EqualFold` is not just about simplicity but also about efficiency:

```bash
BenchmarkToLower-8      29140400    40.67 ns/op     8 B/op  1 allocs/op
BenchmarkEqualFold-8    208766617   5.718 ns/op     0 B/op  0 allocs/op
```

The function doesn't merely convert characters from upper to lower case.

It takes into consideration the complexities of Unicode, ensuring that the comparison is accurate across all languages:

```go
strings.EqualFold("Σ", "σ")           // true
strings.EqualFold("RESUMÉ", "resumé") // true
```

Here’s how it works:

- Fast path: It checks ASCII characters quickly, one by one.
- Slow path: If it finds a Unicode character, it shifts to a detailed comparison to handle these characters accurately.

_We can still use `strings.ToLower` or `strings.ToUpper` for example above, they both handle Unicode characters well._

Not yet, there are situations where `strings.EqualFold` might not cover all bases, for example:

```go
s1 := "Resumé" // Normal 'é'
s2 := "resume\u0301" // 'e' followed by a combining acute accent
```

Although the characters appear similar, they are encoded differently. 

In such cases, `strings.EqualFold` alone might not suffice and to address this, you'd need to integrate more specific handling, potentially using [normalization techniques](https://golang.org/x/text/unicode/norm):

```go
import "golang.org/x/text/unicode/norm"

s1 := "Resumé"       // Normal 'é'
s2 := "resume\u0301" // 'e' followed by a combining acute accent

s1Normalized := norm.NFC.String(s1)
s2Normalized := norm.NFC.String(s2)

fmt.Println(s1Normalized == s2Normalized)                  // Output: false
fmt.Println(strings.EqualFold(s1Normalized, s2Normalized)) // Output: true
```

### Filter Without Any Allocation

Filtering slices in Go typically involves creating a new slice for the filtered elements. This method, however, leads to additional memory allocation:

```go
var filtered []int

for _, v := range numbers {
    if isOdd(v) {
        filtered = append(filtered, v)
    }
}
```

But there's a smarter way to handle this by filtering the slice "in place," using the original slice's underlying array to avoid extra allocations.

Instead of starting fresh with a new slice, you set up filtered as a zero-length slice that still shares the same underlying array with numbers:

```go
filtered := numbers[:0]
```

This setup means `filtered` begins with no elements but uses the same array as `numbers`.

```go
for _, v := range numbers {
    if isOdd(v) {
        filtered = append(filtered, v)
    }
}
```

Here's the tricky part: we're not actually allocating new memory, what we're doing is populating the existing array that `numbers` originally referenced. So, both `filtered` and `numbers` will reflect the changes, but only up to the last element we add to `filtered`:

```go
numbers := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}

// 
filtered: [1 3 5 7 9]
numbers: [1 3 5 7 9 6 7 8 9]
```

This method is really handy if:

- You're okay with changing the original numbers array since you won't need it in its original form after filtering.
- You're dealing with large datasets and performance is a key concern, mainly because this method minimizes memory usage by not creating additional slices.

## Readability & Maintainability

### Make `time.Duration` Clear and Readable

Handling time intervals in Go often involves working with the `time.Duration` type, which can sometimes be a bit tricky to interpret quickly.

A common issue that can make things confusing is when you write:

```go
const refreshDuration = 168 * time.Hour
```

The meaning of `168 * time.Hour` might not be immediately obvious. This can require a mental calculation, which isn't ideal when you're trying to understand or review code quickly.

A more intuitive way to express the same duration could be:

```go
const refreshDuration = 7 * 24 * time.Hour
```

By breaking it down into `7 * 24 * time.Hour`, it becomes instantly clear to anyone reading the code that refreshDuration equates to **one week**. This way, there's no need to pause and calculate the total number of hours in a week.

### Making Sense of Large Numbers with Numeric Separators

Dealing with long numbers in code often leads to a readability challenge. 

It's easy to lose track of **how many zeros** are in a billion or where the decimal places should go in more precise figures like pi.

```go
// NOT BAD
const oneBillion = 1000000000
```

It works, but it's a bit hard to quickly confirm the number of zeros at a glance. Now, compare it with this:

```go
// BETTER
const oneBillion = 1_000_000_000
```

With the addition of underscores, the number is broken up into more digestible chunks, making it instantly clear that it's indeed one billion.

This trick doesn't just apply to integers:

```go
// float
const pi = 3.141_592_653_589_793 // 3.141592653589793
```

Numeric separators make your numbers easier to read and your code less prone to mistakes, all while keeping everything neat and tidy.

### Avoid Naked Parameters with Comment or Constant

Here's a neat little technique to improve the readability of your functions, especially when our IDE doesn't support inlay hints that show parameter names alongside their values.

Sometimes, when we use functions with multiple parameters, it's hard to tell what each parameter stands for just by looking at the call, especially if they are of the same type or are literal values like booleans or numbers:

```go
// BAD
printInfo("foo", true, true)
```

What do "true" values represent? A clearer method is to use in-line comments that directly associate each argument with its purpose:

```go
// BETTER
printInfo("foo", true /* isLocal */, true /* done */)

// STILL
printInfo("foo",
    true /* isLocal */,
    true /* done */
)
```

This makes it immediately obvious what each true stands for without having to go back to the function definition.

Another way to solve unclear parameters is to use constants, even if they're only used once and only in that specific function:

```go
const isLocal, done = true, true
printInfo("foo", isLocal, done)

---
const isLocal, notDone = true, false
printInfo("foo", isLocal, notDone)
```

The constant name is important, by defining `isLocal` and `done` (or `notDone`) as constants with descriptive names, anyone looking at the `printInfo` call can immediately understand what true values are being passed for.

### Empty Slice or, Even Better, `nil` Slice

When we're setting up to work with slices, we might wonder whether to start with what's technically an empty slice or, perhaps even more efficiently, a `nil` slice. 

First, there's the method using the var keywor:

```go
var t []int
```

Here, `t` is a slice of type `int`, but it hasn't been initialized yet, so it's considered nil. 

This means it doesn't point to any underlying array, and both its length (len) and capacity (cap) are zero. 

On the other hand, you could initialize an empty slice using a slice literal:

```go
t := []int{}
```

This slice isn’t nil. It points to an underlying array, but it's an array without any elements. 

While this method does allocate a small amount of memory to maintain that underlying, yet empty, array, the difference in memory usage compared to a `nil` slice is usually quite small.

So, which one is considered idiomatic?

In Go, the nil slice is often favored, it aligns well with Go's philosophy of simplicity and using zero values effectively. 

1. A nil slice doesn’t allocate any memory, it’s just a pointer that leads nowhere, which can be particularly advantageous in high-performance scenarios where every bit of efficiency counts.

2. The Go community prefers using `nil` slices because it aligns well with Go's philosophy around simplicity and working with zero values.

3. There are exceptions to every rule, for instance, when dealing with JSON serialization, a `nil` slice translates to `null` in JSON, whereas an empty slice turns into an empty array ([]).

4. It's also good practice to write your code in a way that it treats `nil` slices, empty slices, and non-empty slices similarly. Things like `for range`, `len()`, and `append()` handle `nil` slices gracefully, so using them can simplify your error handling.

<table>
<thead><tr><th>Not bad</th><th>Better</th></tr></thead>
<tbody>
<tr><td>

```go
emptySlice := []int{}

for _, value := range emptySlice {}
```

</td><td>

```go
var nilSlice []int

for _, value := range nilSlice {}
```

</td></tr>
</tbody></table>


### Understand "Return Fast, Return Early" to Avoid Nested Code

Clarity is key, we want anyone who looks at our code (including future you) to quickly understand what's happening. 

An effective strategy to enhance this clarity is to structure your code so the main flow, often called the "happy path," is on the left side of the screen, while error handling and edge cases are on the right.

Look at a common scenario where this could be applied:

```go
if fileExists(fileName) {
    content, err := readFile(filename)
    if err != nil {
        // handle error
    } else {
        // process the content
    }
} else {
    // handle file not existing
}
```

The above snippet creates multiple nested conditions which can make the code harder to follow.

The tip of "return fast, return early" can really simplify how this looks and operates.

It's about addressing errors or special conditions upfront and then getting them out of the way as soon as possible:

- You handle it immediately.
- Stop the execution of the current operation using: return, break, continue,...
- Or manage the error in a way that allows the normal execution to proceed safely, if appropriate.

Let's refactor the example:

```go
if !fileExists(fileName) {
    // handle file not existing
    return
}

content, err := readFile(fileName)
if err != nil {
    // handle error
}
```

This version quickly exits if the file doesn't exist or if there's an error reading the file, which reduces the nesting and makes the "happy path" more apparent.

> "But what if my function returns two values, like when fetching a user, and I only use one of them in a specific scope?"

For instance, even though `user` is only needed in the else scope, it's cleaner to separate the error checking from the initialization:

<table>
<thead><tr><th>Bad</th><th>Better</th></tr></thead>
<tbody>
<tr><td>

```go
if user, err := db.FetchUser(userID); err != nil {
    return err
} else {
    // process the user
}

// continuing without using 'user'

return nil
```

</td><td>

```go
user, err := db.FetchUser(userID);
if err != nil {
    return err
}

// process the user

// continuing without using 'user'

return nil
```

</td></tr>
</tbody></table>

> "But what if I only want to use 'user' within that else scope?"

If `user` is strictly used within that scope and doesn't impact the logic outside, you might consider encapsulating that specific logic into its own function:

```go
func processUser(userID string) error {
    user, err := db.FetchUser(userID)
    if err != nil {
        return err
    }

     // process the user
}

if err := processUser(userID); err != nil {
    return err
}
```

Now we could apply the "return fast, return early" tip to the `processUser` function.

### Define Interfaces in the Consumer Package, Not the Producer Once

There's a pretty nifty guideline that often gets overlooked using interfaces in Go. 

It revolves around where to define these interfaces, should it be in the package where they are implemented (producer) or the package where they are used (consumer)?

1. Define interfaces in the consumer package, not the producer.

This might seem a bit counterintuitive, but it’s all about who needs what, and who knows what. 

When the consumer defines the interface, it ensures that only the necessary methods required by the consumer are included, nothing more. 

This way, we can avoid the drawback of an overly prescriptive interface that dictates implementation details unnecessarily.

2. In the producer package, it’s generally better to return concrete types rather than interfaces.

This ties back to not defining interfaces in the producer package, by returning concrete types, we maintain flexibility. We can freely add new methods to these types down the line without worrying about breaking existing contracts defined by an interface

3. Avoid defining interfaces prematurely. 

It’s tempting to lay down a bunch of interfaces early in the development process, but it’s often unnecessary. 

```go
type UserRepository interface {
    FindByID(id string) (*User, error)
    FindByEmail(email string) (*User, error)
    FindByPhone(phone string) (*User, error)
    BanUser(id string) error
    ...
}
```

Only define an interface when you have a clear, immediate need for it. This ensures that your interfaces are designed with purpose and are designed to actual use cases, rather than hypothetical ones.

----------
Now, let's look at a common scenario where these principles can be applied or are often violated:

```go
package logger

type Logger interface {
    Log(message string)
}

type consoleLogger struct{}

func (l consoleLogger) Log(message string) {
    fmt.Println(message)
}

func NewConsoleLogger() Logger {
    return consoleLogger{}
}
```

The `Logger` interface and its implementation (`consoleLogger`) are defined in the same package, the `logger` package, this setup is quite typical but goes against our first principle.

And then, when this Logger is used in another package:

```go
package service

import "logger"

func NewService(l logger.Logger) Service {
    ...
}
```

Here, the service package is consuming the `Logger` interface defined in the logger package. According to our guideline, the `service` package should be the one defining the `Logger` interface based on its needs, not the `logger` package.

> "Is this bad?"

Well, it could be seen that way, depending on the angle you're looking from.

Let's revise the snipept to align with our outlined principles, here’s an updated version of our producer, the `logger` package:

```go
package logger

type ConsoleLogger struct {}

func (l ConsoleLogger) Log(message string) {
    fmt.Println(message)
}
```

Rememeber 3 principles we discussed earlier? 

1. The `Logger` interface is no longer part of the producer package, then, `ConsoleLogger` is just a regular struct with no ties to any interface.
2. When we instantiate a `Logger`, we’re directly working with or returning a concrete type.
3. We're holding off on defining interfaces until there's a definite demand for them, avoiding the need to predict what functions might be necessary for the consumer.

Now, let's see how the consumer integrates this:

```go
package service

type Logger interface {
    Log(message string)
}

func NewService(l Logger) Service {
    ...
}

type fakeLogger struct{}

func (f fakeLogger) Log(message string) {}
```

By defining interfaces in the locations where they're used, in this case, within the service package, we're ensuring that these high-level components rely on abstractions rather than concrete implementations from lower-level modules.

> "But what if I have multiple consumers for the same interface?"

There are some strategies to handle this:

- You could place the interface in a shared package accessible to all consumers, but this approach has its drawbacks. If one consumer requires a new method, it impacts all other consumers who share this interface.
- Duplicate the interface in each consumer package, it might seem like violating the DRY (Don't Repeat Yourself) principle, but it's actually a good practice in this context.

If you change the producer, all the consumers will be affected, but this is better because you need to be aware of the changes. Also, if 1 consumer needs a new method, it won't affect other consumers.

A little more work, but it's a small price to pay for the flexibility and maintainability it brings.

### Avoid Using `break` in Switch Cases, Except When Paired with Labels

Programming languages like C, C#, and JavaScript typically require a `break` statement at the end of each case to prevent the code from accidentally running into the next case:

```go
switch (1) {
case 1:
    printf("Play selected\n")
    break
case 2:
    printf("Settings selected\n")
    break
default:
    printf("Invalid selection\n")
}

// Output:
// Play selected
```

This is necessary because, without the `break`, the program would continue executing the following cases even if a match had already been found.

However, Go simplifies this process. 

In Go, each case in a `switch` statement automatically includes an implicit `break` and this means that once a case matches, Go will stop executing any further cases without requiring an explicit break statement:

```go
switch (1) {
case 1:
    fmt.Println("Play selected")
case 2:
    fmt.Println("Settings selected")
default:
    fmt.Println("Invalid selection")
}

// Output:
// Play selected
```

This design decision by Go not only makes our code cleaner but also reduces the chance of bugs related to the unintentional "fall-through" behavior seen in other languages.

Most often, that's exactly what we need: to stop once we've found a match, it's rare to want to continue executing subsequent cases without interruption. Go's design caters to this common scenario rather than the exceptions.

> “What if I want to continue?”

For those situations where we do want the execution to proceed from one case to the next, Go has you covered with the `fallthrough` keyword. This allows the execution to pass intentionally from the matched case to the following case:

```go
switch 1 {
    fmt.Println("Play selected")
    fallthrough
switch 2:
    fmt.Println("Settings selected")
default:
    fmt.Println("Invalid selection")
}

// Output:
// Play selected
// Settings selected
```

However, there's a particular scenario where you might still need to use `break` in a `switch` statement, specifically when you want to exit a loop that contains the `switch`. 

Normally, a break inside a `switch` statement doesn't affect the enclosing loop, right? So, if we want to exit a loop based on a condition evaluated within a `switch`, we'll need to use labels:

```go
loop:
for {
    switch 1 {
    case 1:
        break loop
    }
}
```

Using a label in this way lets us clearly define where we want to exit, ensuring that the `break` statement affects the loop rather than just terminating the switch case.

### Keep Your Names Simple and Clear by Avoiding Repetition

1. Package vs. exported symbol name

When you're naming something that's exported (that is, visible outside its package), try not to repeat the package name in what you're naming.

Here's why: repeating the package name makes everything longer and more cluttered, and since the package name shows up whenever you use these exported names outside the package, it's just unnecessary:

<table>
<thead><tr><th>Bad</th><th>Better</th></tr></thead>
<tbody>
<tr><td>

```go
chocolate.NewChocolateBar()
userrepository.NewUserRepository()
```

</td><td>

```go
chocolate.NewBar()
userrepository.New()
```

</td></tr>
</tbody></table>

With the improved version, the repetition is gone. 

When we use something like `chocolate.NewBar()`, it's straightforward and clear, we’re making a new chocolate bar.

2. Variable name vs. type

It's often unnecessary to repeat the type of a variable in its name, especially when the context or usage makes it clear.

Here's a simple comparison:

<table>
<thead><tr><th>Bad</th><th>Better</th></tr></thead>
<tbody>
<tr><td>

```go
var secondaryHero *Hero
var employeeList []Employee
```

</td><td>

```go
var secondary *Hero
var employees []Employee
```

</td></tr>
</tbody></table>

These examples show how you can skip the type in the name without losing meaning. The improved names are just as clear but without the clutter.

Of course, there are times when including the type in the name can help avoid confusion. This is especially true if you're dealing with multiple structures of the same type. For example, if you have an array of cars and a map for cars used for quick lookups, distinguishing between them becomes important.

> "Should I name them carList and carMap?"

`carList` and `carMap` are certainly good, but we could also describe the purpose or the form of the data to make it even clearer. For instance, `[]Car cars` and `map[string]Car carLookup` are descriptive enough to convey both the type and the intended use.

Both versions below are good:

```go
dateString := "2024-12-31"
date, err := time.Parse("2006-01-02", dateString)
```
```go
dateInput := "2024-12-31"
date, err := time.Parse("2006-01-02", dateInput)
```

In the better example, adding 'Input' to the variable name clarifies that it's a string being used as input data.

3. Avoiding repetition boils down to 'context'

So, what we're talking about boils down to context, this includes:

- Package names
- Method names
- Type names
- Filename

These should help you pick names that are simple but tell you enough, without repeating stuff that's already clear.

Let's dive into more examples that show how context matters:

- Method with type name:

<table>
<thead><tr><th>Bad</th><th>Better</th></tr></thead>
<tbody>
<tr><td>

```go
func (u *User) GetUsername() string
```

</td><td>

```go
func (u *User) Username() string
```

</td></tr>
<tr><td>

```go
func (l *Logger) LogMessageToConsole(msg string)
```

</td><td>

```go
func (l *Logger) ToConsole(msg string)
```

</td></tr>
</tbody></table>

- Function and its arguments:

<table>
<thead><tr><th>Bad</th><th>Better</th></tr></thead>
<tbody>
<tr><td>

```go
func SendEmail(
    recipientEmailAddress string,
    emailSubject string,
    emailBody string,
)
```

</td><td>

```go
func SendEmail(recipient, subject, body string)
```

</td></tr>
</tbody></table>

- Inside functions, especially with parameters or data closely tied to what the function does::

<table>
<thead><tr><th>Bad</th><th>Better</th></tr></thead>
<tbody>
<tr><td>

```go
func (o *Order) CalculateOrderTotalCost() float64 {
    var orderItemsTotal float64

    for _, orderItem := range o.Items {
        orderItemsTotal += orderItem.Price * orderItem.Amount
    }

    return orderItemsTotal
}
```

</td><td>

```go
func (o *Order) TotalCost() float64 {
    var total float64
    for _, item := range o.Items {
        total += item.Price * item.Amount
    }

    return total
}
```

</td></tr>
</tbody></table>

In each of these cases, the better names make your code more straightforward and easy to get at a glance.

### Skip the 'Get' Prefix for Getters

So, when we need to name a function, we usually start with a verb, right? Like get, set, fetch, update, calculate, and so on. But, you know, getters are kind of an exception in Go.

> "Wait, Why do I even need getters and setters?"

Well, in Go, we handle encapsulation through how we control the visibility of methods and by following naming conventions. This provides a nice, subtle way to support encapsulation without having to strictly enforce getter and setter methods.

However, if you need to include some additional logic, or if you're dealing with a computed field, it's totally fine to define getters and setters manually.

> "So, what's the typical way to name a getter?

The usual practice is to just use the field name and capitalize the first letter to make it public, or "export" it:

```go
type Person struct {
	LastName  string
	FirstName string
}

// getter
func (b *Person) Name() string {
	return b.FirstName + " " + b.LastName
}
```

Here's another example where methods provide computed properties or configurations that aren't directly stored within a struct:

```go
// package net/http

func (r *Request) UserAgent() string {
    return r.Header.Get("User-Agent")
}

func (r *Request) Cookies() []*Cookie {
    return readCookies(r.Header, "")
}
```

These examples show how Go lets us create getters that feel natural and intuitive, aligning well with the language's philosophy of simplicity and clarity.

### Naming Unexported Global Variables with an Underscore `_` Prefix

_(Note: This convention isn't widely accepted as the standard in the Go community, but it's something the folks at Uber have put in their style guide)_

So, top-level variables and constants can be accessed all over the package they're declared in.

> "What’s the issue with the usual way?"

Without a specific way to name things, it’s really easy to accidentally use the same name for something local that’s meant for use across the package, this can lead to what we call "shadowing."

Here’s what I mean:

```go
const dataSize = 100

func ProcessData() {
    dataSize := 50
    fmt.Println(dataSize) // 50
}
```

In this snippet, the local `dataSize` inside `ProcessData` overshadows the global `dataSize`.

> "What’s the big deal if they have different names?"

Let's break it down with an example:

```go
const maxUser = 100

func addUser() {
    limit := maxUsers + 10

    if currentUsers < limit {
        ...
    }
}
```

This might look simple, but it still raises a question: Where does maxUsers come from?

- Is it just another local variable like 'limit'?
- Maybe it’s an argument passed to the function?
- Or is it something from the global scope?

In complex code, figuring this out might mean a lot of flipping back and forth or using navigation shortcuts (e.g. Cmd + Click) in an IDE, which can really disrupt our flow.

#### With an Underscore Prefix

By starting these global names with an underscore (_), it actually acts like a flag, telling us that these identifiers are global:

```diff
+ const _maxUser = 100

func addUser() {
+   limit := _maxUsers + 10
-   limit := maxUsers + 10

    if currentUsers < limit {
        ...
    }
}
```

With this small change, it becomes crystal clear that `_maxUsers` is a global variable or constant. 

### Converting Multiple If-Else Statements into Switch Cases

We've probably run into situations where you're dealing with complex conditions using multiple if-else statements:

```go
if canProcess && userActive {
    ...
} else if !canProcess && quotaReached {
    ...
} else if quotaExceeded {
    ...
} else {
    ...
}
```

While there's nothing inherently wrong with this solution, there is a cleaner and more readable alternative we can use by transforming these if-else chains into switch statements.

To get a handle on this, let’s first inspect how the switch-case structure is typically set up:

```go
switch initializations; condition {
    case condition1:
        ...
    case condition2:
        ...
    default:
        ...
}
```

In cases where we're dealing with straightforward boolean conditions, we can actually skip over the 'initializations' and the 'condition' parts. 

What we end up with is something like `switch true {}`, but since the true is implied, we can leave it out altogether.

Now, let's take the concepts we've discussed and use them to refine our original switch statement example:

```go
switch {
case canProcess && userActive:
    ...
case !canProcess && !quotaExceeded:
    ...
case quotaExceeded:
    ...
default:
    ...
}
```

It’s a subtle shift, but it can make a significant difference, This technique is not just theoretical but also widely used in Go codebases including the standard library.

Take, for example, this snippet from the `net/http` package:

```go
func (b *body) Close() error {
    ...
    switch {
    case b.sawEOF:
        return nil
    case b.hdr == nil && b.closing:
        return http.ErrBodyReadAfterClose
    case b.doEarlyClose:
        return errReadOnClosedResBody
    default:
        return nil
    }
    ...
}
```

### Gracefully Shut Down Your Application

When we discuss the idea of gracefully shutting down an application, what we're really talking about is making sure a few important things happen smoothly:

- No new requests: First off, the server stops accepting any new incoming requests, this prevents additional load or new processes from starting up while we're trying to close down.
- Completion of ongoing tasks: This means giving ongoing operations enough time to complete properly rather than cutting them off abruptly, which could lead to data loss or corruption.
- Resource cleanup: We need to clean up and release any resources that the application was using

So, how do we implement this smoothly? 

There are several ways to approach this, but let's go through a simple yet effective method:

```go
ctx, cancel := signal.NotifyContext(context.Background(), os.Interrupt, syscall.SIGTERM)
defer cancel()

server := http.Server{Addr: ":8080"}

g, gCtx := errgroup.WithContext(ctx)

g.Go(func() error { return server.ListenAndServe() })
g.Go(func() error {
    <-gCtx.Done()

    return server.Shutdown(context.Background())
})

if err := g.Wait(); err != nil && !errors.Is(err, http.ErrServerClosed) {
    log.Printf("failed to shutdown gracefully: %v", err)
}
```

Here’s what we’re doing:

- We start by setting up a context that listens for interrupt signals like Ctrl+C or a `SIGTERM`. This context helps us handle these signals so we can start the shutdown process when they're received.
- We use an `errgroup` with this context to manage two goroutines:
    - We launch our server, remember that `ListenAndServe` will always return a non-nil error when the server stops, whether it's due to an error or a regular shutdown.
    - The second goroutine is where the graceful shutdown logic lives. This part waits for a signal that the context is done (meaning an interrupt signal was received), and then proceeds to shut down the server.

This shutdown process is where our application ensures that all the tasks are either completed or stopped at a suitable point, and all resources are properly released.

#### Kubernetes Consideration

When our service is running in a Kubernetes environment, it's important to fine-tune how our application handles shutdown processes, especially in relation to SIGTERM signals.

A common misconception is that once a SIGTERM is received, our application should immediately start shutting down, but in a Kubernetes setup, this solution can be problematic.

Instead of stopping all new requests right away when we receive a SIGTERM, it's better to keep our application running a little longer. Why? 

Because we want to make sure it finishes dealing with all the active requests first. Additionally, it's better to keep accepting incoming connections for a short period after the shutdown begins. This strategy helps because it sometimes takes a little time for Kubernetes to notify all the kube-proxies, load balancers about your pod shutting down.

### Keep the Mutex Close to the Data It's Protecting

It's generally a good idea to keep the mutex close to the data it's meant to protect. This tip helps ensure that whenever we lock and unlock the mutex, we're fully aware of what exactly it's safeguarding.

Let's go over a couple of examples to see how this works in practice:

```go
type UserSession struct {
	mu          sync.Mutex
	ID          string
	Preferences map[string]any
	LastLogin   time.Time
	Profile     *UserProfile
	Cart        []string
	isLoggedIn  bool
}
```

Now, looking at this structure, which fields do you think the mutex is protecting?

It might be guarding the 'Preferences', perhaps the 'Cart', 'Profile', or other fields, or it could even be all of them. The tip here is to organize our struct in such a way that it becomes apparent immediately, without the need to dive into the logic.

The trick is to arrange your struct so we can tell at a glance, without needing to dig through our code.

> ""But didn't you say to order fields in a struct from biggest to smallest?"

_[Sort Fields in Struct from Largest to Smallest](#sort-fields-in-struct-from-largest-to-smallest)_

True, we did mention organizing fields by size for optimization purposes. But the latter part of that advice emphasizes prioritizing readability and ease of understanding over strict size optimization.

What I often do is group related fields together within the struct, separated by a blank line, instead of adhering strictly to size order. It makes the struct much easier to read.

**The solution**

If we revisit our example, the clue to understanding it lies in the tip's title itself: "Keep the mutex near the data it's protecting."

We can clarify which fields the mutex protects by simply placing it directly above those fields, separated from others with a blank line:

```go
type UserSession struct {
	mu          sync.Mutex
	Preferences map[string]any
	Cart        []string

	ID          string
	LastLogin   time.Time
	Profile     *UserProfile
	isLoggedIn  bool
}
```

By placing the mutex right above the 'Preferences' and 'Cart', we show our team (and our future selves) that these fields, in particular, need to be accessed safely when multiple goroutines are involved.

This principle isn't limited to just structs, though.

They can also be crucial when dealing with global variables or functions that should be executed one at a time:

```go
var (
    mu sync.Mutex // Protects the global count
    count int 
)

func IncrementCount() {
    mu.Lock()
    defer mu.Unlock()

    count++
}
```

Now, we know there's a bit of a debate around using global variables due to issues like making testing harder, making the code less clear, etc. But for this example, let's focus on understanding how the mutex works in this context, rather than diving into the pros and cons of global variables.

### Avoid Global Variables, Especially Mutable Ones

Global variables are those we place outside functions or methods, making them accessible to any part of our code. 

So, they can be modified by any piece of the code that accesses them, they're convenient, they're a common source of bugs:

```go
var featureConfig = loadFeatureConfig() 

func processCheckout(userID string, cart Cart) {
    if featureConfig.isFeatureEnabled() {
        ...
    }

    ...
}
```

Now, it's not that all global variables are necessarily bad, but they often bring more trouble than they're worth, let’s delve into why that is, using the example we have:

- Tracking changes is tough: When `featureConfig` can be changed by any part of our app, finding where changes were made becomes a challenge. 
- Testing gets tricky: Suppose we want to test different checkout processes, if both tests change the same global `featureConfig`, they can't run independently without potentially interfering with each other. 
- Concurrency problems: When multiple requests attempt to read or modify `featureConfig` simultaneously, it can lead to inconsistencies and race conditions.

> "So, what is the solution?"

The answer lies in Dependency Injection.

This is a technique where we pass the necessary objects to a component from the outside rather than having the component create them internally or fetch them from globally accessible variables:

```go
func NewCheckoutService(featureConfig FeatureConfig) CheckoutService {
    return CheckoutService{featureConfig}
}

func (s *CheckoutService) Process(userID string, cart Cart) {
    if s.featureConfig.NewCheckoutProcessEnabled() {
        ...
    }
}
```

Admittedly, this solution adds a bit of complexity initially, but it significantly eases maintaining the code, testing it, and debugging. 

With dependency injection, setting up tests for different scenarios, like having a feature enabled or disabled becomes simpler. You can run as many concurrent tests as you want without worrying about them interfering with each other:

```go
func TestProcessWithFeatureEnabled(t *testing.T) {
    featureConfig := FeatureConfig{
        NewCheckoutProcessEnabled: true,
    }
    checkoutService := NewCheckoutService(featureConfig)

    ...
}

func TestProcessWithFeatureDisabled(t *testing.T) {
    featureConfig := FeatureConfig{
        NewCheckoutProcessEnabled: false,
    }
    checkoutService := NewCheckoutService(featureConfig)

    ...
}
```

But, if our global variables are static, don’t require testing, and need to stay consistent throughout the application's lifecycle, using global variables might still be the right solution in those specific cases. 

And if you must use global variables that change during runtime, make sure you're implementing proper synchronization techniques, like mutexes, to manage access safely.

### Implement `String()` for Enum with the `stringer` Tool

Have you noticed when you print a duration, like `fmt.Println(time.Second)`, it shows up as "1s" instead of "1000000000" even though `time.Duration` is technically an int64? 

This handy representation comes from the `String()` method defined on the `time.Duration` type, which is designed to make the output more user-friendly.

Also, this is a part of the `fmt.Stringer` interface:

```go
package fmt

type Stringer interface{
    String() string
}

---
fmt.Println(time.Second) // 1s
```

For our custom types to be just as clear, we can implement a `String()` method too.

Now, consider enums, we often use numbers to represent different constants, but it's much more meaningful to print out something readable. 

For instance, we might have an enum for different hero types in a game, which we would typically represent with a switch statement like this:

```go
func (h HeroType) String() string {
    switch h {
    case HeroTypeTank:
        return "Tank"
    case HeroTypeAssasin:
        return "Assasin"
    case HeroTypeMage:
        return "Mage"
    }

    return ""
}
```

However, this approach can be "burdensome". 

If we add a new type to the enum and forget to update the `String()` method, it won't print correctly, which can lead to confusion and errors.

This is where the stringer tool becomes invaluable, since it generates the `String()` method for you, ensuring that your enum values always have corresponding string representations, which keeps the code up-to-date and reduces manual maintenance:

```go
$ go install golang.org/x/tools/cmd/stringer@latest
$ stringer -type HeroType
```

> "Do we need to run this for every single type in different packages?"

There is a way to automate this process using `go generate`.

By adding a special comment in your code, go generate will automatically invoke the stringer tool and create the `String()` method for you:

```go
//go:generate stringer -type=HeroType
type HeroType int 

const (
    HeroTypeTank HeroType = iota + 1
    HeroTypeAssasin
    HeroTypeMage
)
```

We can place the comment anywhere in the same package, but it's generally a good practice to place this comment directly above the enum type to keep the related code organized.

There are options to change how String() works:

- trimprefix: Remove a specified prefix from the enum names. 

For instance, if our enum value is `HeroTypeTank`, it would normally be represented as the string "HeroTypeTank". 

But, by setting the trimprefix to "HeroType", the output changes to just "Tank":

```go
//go:generate stringer -type=HeroType -trimprefix=HeroType
```

-linecomment: Choose a completely different name by using a comment right after the enum value.

We simply put the desired string right in a comment next to the enum value, and the stringer tool will use that instead. 

```go
//go:generate stringer -type=HeroType -linecomment
const (
    HeroTypeTank HeroType = iota + 1 // Knat
    HeroTypeAssasin                  // Nisassa
    HeroTypeMage                     // Egam
)
```

### Use the `deadcode` Tool to Find and Remove Unused Functions

Sometimes in our codebase, we end up with functions that aren't used anywhere. This is what we call "dead code." It complicates maintenance because it's often unclear whether these functions are safe to delete or modify.

Fortunately, there's a tool called `deadcode` that helps us identify these unused functions, we can install it with the following command:

```bash
$ go install golang.org/x/tools/cmd/deadcode@latest
```

Once installed, running it is simple with this command:

```bash
$ deadcode . 
```

This will scan our project and list out the functions that aren't being used, along with where they're located in our code.

```bash
internal/params.go:34:6: unreachable func: WithUserRanking
internal/wallets.go:50:35 unreachable func: Transaction.CollectionName
internal/utilx/randomx/string.go:23:6: unreachable func: RandomAlphabet
```

This output shows us exactly where the dead code locates, it's easy for us to go in and clean it up.

If you're curious about why a particular function isn't being used or how it's disconnected from the rest of our codebase, we can use the `-whylive` flag. This flag tells the tool to provide explanations about the function's relationship to the rest of our code, which can be especially helpful if you're trying to understand large and complex codebases.

How it works (simplified)

1. First off, it begins by loading up all the code you've written and performs type-checking

2. Once the initial setup is done, `deadcode` identifies the main entry points of your application, typically the `main()` function and any `init()` functions

3. From these points, the tool starts tracing the execution path, looks at which functions are called directly from these starting points

4. The tool goes deeper and looks at indirect calls as well, and these are typically the ones made through interfaces.

5. The tool also tracks any types that are converted into interfaces, since once a type is associated with an interface, any methods defined by that interface could potentially be called.

6. Finally, any function that doesn't connect back to the main execution path—meaning it isn't called either directly or indirectly, is labeled as "dead."

---- 
You can read more about finding and removing "dead-function" here: https://go.dev/blog/deadcode

### Explicitly Ignore Values with Blank Identifier (`_`) Instead of Silently Ignoring Them

Sometimes, you run into functions that return values you might not need to use and in these cases, we have two ways to handle these unwanted return values.

The first way involves implicitly ignoring the return value. 

Here, you simply call the function without assigning its return value to any variable, this method is simple and keeps your code short:

```go
func PerformOperation() string {
    ... 
    return "anything"
}

func main() {
    PerformOperation()
}
```

The second method is more explicit. 

You **explicitly ignore** the return value by assigning it to the blank identifier _. This makes your intent clear but adds a bit more verbosity to your code:

```go
func main() {
    _ = PerformOperation()
}
```

> "why the explicit approach is preferred when it seems less concise?"

The answer lies in the value of **clarity over brevity** in coding practices. 

Using `_ =` to ignore a return value signals to other developers, or even to your future self, that the omission of the return value was intentional rather than an oversight.

> "What about an error?"

When it comes to handling errors, if a function returns an error, it's important to address it directly.

We should either handle the error appropriately or, at the very least, log it. 

It might also be helpful to add a comment explaining why the error is being ignored, to provide clarity for anyone reviewing the code in the future. 

### If a Parameter Isn't Needed, Either Drop It or Ignore It on Purpose

Before we dive into the tip, let's look at a problem in the following example:

```go
func (d Downloader) FetchFile(url string, checknum string) error {
    err := sd.file.Download(url)
    if err != nil {
        return fmt.Errorf("download file: %w", err)
    }

    return nil
}
```

Here, the `FetchFile` function is given a `url` and a `checksum` for downloading a file. But, it only uses the `url` and completely ignores the checksum, and the thing is, the Go compiler won't point out if you forgot to use an argument in a function, which is not the case with unused local variables.

Because of this, it's hard to tell if leaving out the checksum was a mistake or done on purpose.

Now, to handle this better, we have a couple of options:

- We could just remove the unused parameter from the function altogether.
- Or, We can clearly show that we're ignoring a parameter by using an underscore _. This way, anyone looking at our code can easily see that we meant to leave it out.

Let's clean up that function using what we discussed:

```go
func (d Downloader) FetchFile(url string, _ string) error {
    ...
}
```

By using the underscore in place of a parameter name, we're letting everyone know that this part of the function is intentionally not used. 

> "Why not just take it out?"

Yeah, removing the unused parameter can definitely clean things up. 

However, there are times when you might need to keep it there, especially if you are following a specific pattern or an interface:

```go
type FileDownloader interface {
    FetchFile(url string, checksum string) error
}
```

In cases like this, even if you don't use the checksum in your implementation, you still need to include it to match the interface requirements.

But, if you find yourself using a lot of underscores to ignore parameters, it might be a hint that there’s something off with how the interface or function setup is designed. It could mean that it's time to rethink whether all those parameters are necessary or if the interface needs changing to better fit the actual usage.

### Loop Labels for Cleaner Breaks and Continues

Labels and goto can be tricky and are often avoided because they can make code less readable:

```go
if userAuthenticated {
    goto SkipAuth
}

err := authenticatingUser()
if err != nil {
    return err
}

skipAuth:
fmt.Println("User authenticated")
```

In this small snippet, everything might seem straightforward because it's pretty simple. But when your code starts getting more complex, following these `goto` statements can turn into a real headache.

- Imagine having to trace a label that's way far off from where the `goto` statement is, like, you might end up looking 'hundreds of miles' up and down your code trying to figure out where exactly it jumps. 
- Your eyes moving back and forth through the code to track the flow can quickly turn into a confusing task.

#### Loop labels

Now, when it comes to handling nested loops, using loop labels can be a really good practice in certain situations. 

Let's say we're searching for a specific number within 2D arrays:

```go
found := false
for i, row := range matrix {
    for j, cell := range row {
        if cell == target {
            fmt.Println("Found")
            found = true
            break
        }
    }

    if found {
        break
    }
}
```

This definitely works, but there's a cleaner way to manage it. 

By using a **loop label**, we can name a loop and then control not just the current loop but any surrounding ones as well with a break or continue followed by the label. Here’s how we can do it:

```go
OuterLoop:
for i, row := range matrix {
    for j, cell := range row {
        if cell == target {
            fmt.Println("Found")
            break OuterLoop
        }
    }
}
```

This strategy doesn't just shorten the code, it also makes it easier to understand by clearly defining which loop is being exited.

Loop labels can be useful with `select{}` statements within loops too. Normally, if you use a `break` in a `select` statement without specifying a label, you'll only exit the `select`, not the loop containing it:

```go
for {
    select {
    case <-done:
        ...
        break // only exits the select
    }
}
```

A better solution would be to use a loop label:

```go
Loop:
for {
    select {
    case <-done:
        ...
        break Loop // exits the loop
    }
}
```

While this tip is particularly handy for loops, it can also be applied in other contexts, like switch cases:

```go
SwitchChoice:
switch choice {
case 1:
    ...
case 2:
    ...
    for range 10 {
        break SwitchChoice // exits the switch
    }
default:
    ...
}
```

### The Downsides of Relying on init() Functions in Your Go Code

The `init()` function in Go is quite unique as it runs before the `main` function and after global variables have been initialized. You might come across code where global variables are initialized in `init()` to keep track and centralize the initialization process.

```go
var preComputedValue float64

func init() {
    preComputedValue = math.Sqrt(2) * math.Pi
}

func main() {
    println("preComputedValue:", preComputedValue)
}
```

_[Avoid Global Variables, Especially Mutable Ones](#avoid-global-variables-especially-mutable-ones)_

While this method seems convenient for keeping initialization in one place, it's generally better to keep global variables to a minimum. 

Moreover, we can initialize `precomputedValue` directly where it's declared:

```go
var precomputedValue = math.Sqrt(2) * math.Pi
```

> "But I prefer using `init()` because it provides a centralized place to set up values, rather than having to scan through each file to find and change them"

It's usually more transparent to initialize the value of a global variable as close to its declaration as possible. This way, we can understand the variable's value right away without needing to search for an `init()` function and trace back to where the variable was defined.

> "Why should we try to avoid using `init()`?"

1. Side effects

The `init()` function can change global state or have other unexpected effects that are not part of the main flow. This means simply adding a package to your program could change its behavior in unintended ways, complicating your understanding of the application's operation.

2. Testing challenges

Tests may fail or pass for reasons unrelated to what you're actually aiming to test, just because an `init()` function in an imported package is doing something unexpected.

3. Working in teams

Whether it's revisiting your own code after some time or trying to understand someone else's, figuring out the effects of an `init()` function can be a challenge.

4. Global variables

Using `init()` often involves setting up global variables, but it's important to handle these with care. Global variables can be accessed and modified from anywhere in your code, which can lead to unpredictable results and difficult-to-track bugs. 

Whenever possible, think about allowing the user of your package to handle the setup. 

This style not only makes your code more modular and easier to manage but also enhances transparency, helping clients to see and control exactly how and when components are initialized.

5. If you want to use it, go ahead

We're here not entirely against the use of the `init()` function, there are valid reasons why you might still choose to use init() in your projects:

- You prefer not to require users to explicitly call `yourpackage.Init()` every time they use your package.
- You need to register certain hooks that are critical for your package's functionality.
- Your package needs to configure itself based on environment variables, which is a common requirement in many applications.
- You have global variables whose values depend on different build tags.
- ...

If you decide that init() is necessary, here are some guidelines to follow to mitigate potential issues:

- Try to avoid making external calls, including I/O operations, within `init()`.
- Refrain from starting goroutines in init(), as this can lead to complex and unpredictable concurrency issues.
- Do not rely on the order in which `init()` functions are called. 
- Make sure that you do not modify the global variables of other packages.
- It's helpful to place your `init()` function close to the global variable it modifies to make the relationship clear.
- Document which `init()` function affects each global variable, ideally with a comment above the global variable declaration.

Most importantly, aim for determinism: your `init()` function should produce the same results no matter how many times or under what conditions it is run.

### Simplify Interfaces and Only Ask for What You Really Need

When we're working with interfaces in Go, here are a few practical tips:

1. We should never define an interface until we actually need one, this prevents unnecessary abstraction.
2. It's a good practice to accept interfaces as function arguments but return concrete types.
3. Place interfaces where they are used (consumer), not where they are implemented (provider).

The third point builds directly on the first one.

Let’s dive into an example to see why these points matter, especially the one about "accepting interfaces and returning concrete types":

```go
type UserProfileManager interface {
    CreateUser(profile string) error
    UpdateUser(profile string) error
    GetUser(profile string) (UserProfile, error)
}

func LogUserDetails(manager UserProfileManager, userID string) error {
    user, err := manager.GetUser(userID)
    if err != nil {
        ...
    }

    log.Printf("User details: %v", user)
    return nil
}
```

Here, the `LogUserDetails(..)` function only needs to use the `GetUser()` method from the `UserProfileManager` interface. 

It doesn't need `CreateUser` or `UpdateUser`. This is a bit of a problem because it means the function is tied to a bigger interface than it actually needs, which can make testing tougher, mess with flexibility, and make the code harder to read.

Now, interfaces facilitate abstraction, but the larger the interface, the less abstract it becomes.

> "Why does this make testing a pain?"

When we're setting up tests, it's easiest if you can clearly see which methods of an interface are actually being used by the function. If the interface is too big, you end up having to mock extra methods that aren’t even relevant, which just makes more work for no good reason.

What’s a better way to handle it?

You probably guessed it, our function should just ask for an interface that includes exactly what it needs:

```go
type UserSetter interface {
    CreateUser(profile string) error
    UpdateUser(profile string) error
}

type UserGetter interface {
    GetUser(profile string) (UserProfile, error)
}

func LogUserDetails(getter UserGetter, userID string) error {
   ...
}
```

Any concrete type that fits the `UserManager` should work just fine with the `UserGetter` as well. This small change makes the code easier to test and clearer to understand.

## Function Designs

### Avoid Named Results unless Necessary for Documentation

Named results can really help make your code easier to read, both in the source code and in the documentation generated by tools like `godoc` or `pkg.go.dev`. But it's important to know when and how to use them properly, here’s a straightforward rundown:

1. Clarification when necessary

Do:
- Use named results when a function returns multiple values of the same type.
- Name them for clarity if it's not immediately obvious what each value represents.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func location() (float64, float64, error)
```

</td><td>

```go
func location() (lat, long float64, err error)
```

</td></tr>
</tbody></table>

In the "Good" example, naming the returned `float64` values as `lat` and `long` immediately lets you know that these represent latitude and longitude, which is a lot clearer than just seeing two `float64` types.

Don't:
- Use named results just to avoid declaring a variable inside the function.
- Use them as a shortcut to avoid writing out `return nil, err` in favor of just `return`.

#### 2. Avoid Naked Returns in Long Functions

People are often aggressive about avoiding naked returns because they can seriously ruin readability and confuse the clarity of the code, especially in more complex functions.

However, in shorter, simpler functions, naked returns can be perfectly acceptable:

```go
func calculateStats(a, b int) (sum int, product int) {
	sum = a + b
	product = a * b
	return
}
```

In this snippet, the function is concise enough that it's straightforward to follow what `sum` and `product` represent without needing to track them through a lot of code, their purpose is clear right from the start.

But for longer functions, the consensus is strong: avoid naked returns. When in doubt, favor explicitness to maintain clarity and ease of understanding.

#### 3. Necessary for Deferred Closures

It’s important to name result parameters when you need to modify a return value in a deferred function call.

```go
func doSomething() (result int, err error) {
    defer func() {
        if r := recover(); r != nil {
            err = fmt.Errorf("caught panic: %v", r)
        }
    }()

    result = doPanic()
    return
}
```

In the example, the result parameters `result` and `err` are specifically named. 

This naming allows these variables to be accessible and modifiable within the deferred closure, which is particularly useful for handling outcomes or responding to a `panic` situation. This setup makes sure that any necessary changes to the return values are handled gracefully.

#### 4. Some Cases Don’t Need Naming, Even With More Than Two Results

When functions return objects of the same type, particularly in methods of a type, naming each returned object can be repetitive and make our documentation unnecessarily dense.

Or the type itself may already be self-explanatory.

<table>
<thead><tr><th>Bad</th><th>Good</th></tr></thead>
<tbody>
<tr><td>

```go
func (n *Node) Parent() (node *Node) {}
```

</td><td>

```go
func (n *Node) Parent() (*Node) {}
```

</td></tr>

<tr><td>

```go
func (n *Node) Parent() (node *Node, err error) {}
```

</td><td>

```go
func (n *Node) Parent() (*Node, err) {}
```

</td></tr>

<tr><td>

```go
func fetchDetails(id int) (user *User, detail *Detail, err error) {
    ...
    return
}
```

</td><td>

```go
func fetchDetails(id int) (*User, *Detail, error) {
    ...
    return user, detail, nil
}
```

</td></tr>
</tbody></table>

In these comparisons, the more effective examples demonstrate how omitting the naming of returned objects can lead to cleaner, more concise code

### Lead with Context, End with Options, and Always Close with an Error

Writing Go in a way that aligns with its idioms often requires following specific patterns and best practices that ensure your code is predictable and easy to follow.

Here are three essential guidelines for designing function signatures:

1. context.Context goes first

The context.Context parameter should always be the first in your function signatures.

> "Why?"

A context is usually connected to the lifecycle of a request or operation. When you look over the code, seeing a `context.Context` as the first parameter immediately indicates that the function is designed to handle:

- Cancellation
- Deadlines
- Other context-related mechanisms. 

This placement enhances readability and simplifies navigation through the codebase.

```go
func FetchData(ctx context.Context, url string) ([]byte, error) {
    ...
}
```

Additionally, you should avoid embedding the `context.Context` in a struct (not always).

The nature of context is to be transient and flow through the application rather than being part of an object's state, this ensures that the context remains relevant only for the duration it's needed and isn't tied down unnecessarily.

_(there are exceptions, such as in the case of an HTTP Handler where it's typical to pull the context from the request since the context is already tied to the lifecycle of that request)_

2. Options struct trails behind

The "options struct" pattern is a flexible and powerful approach for managing functions that might need to evolve over time without breaking compatibility. 

_[Simplify Function Signatures with Structs or Variadic Options](#simplify-function-signatures-with-structs-or-variadic-options)_

```go
type FetchOption struct {
    Cache     bool
    Priority  int
}

func FetchData(context.Context, string, FetchOptions) ([]byte, error)
```

The order of arguments shows their importance. 

Putting this struct at the end of the function does two things:

- It keeps things consistent (like with the variadic options pattern).
- It shows that these are extra settings, not essential to the function's main task.

3. End with an error (or sometimes a bool)

In Go, the standard way to show whether an operation worked or not is by using the last return value, which is usually an error.

In situations where a simple yes or no answer is more suitable, like checking if something exists, that too is positioned at the end.

If both an error and a boolean are needed, the order should go (x, bool, error).

```go
func FetchData(context.Context, url string) ([]byte, error)

func TryFetchData(context.Context, url string) ([]byte, bool, error)
```

In a function like `TryFetchData`, a boolean is used to indicate whether something exists, particularly when non-existence isn't necessarily an error.

### Intentionally Stop with Must Functions

Here's something that might seem a bit unusual at first: using 'Must functions' to deliberately allow your application to stop.

Usually, we try to avoid situations where our program could panic, but there are times when using these functions can actually make your code simpler:

```go
var validID = regexp.MustCompile(`^[a-zA-Z0-9]+$`)

var tmpl = template.Must(template.New("name").Parse("{{.}}"))
```

If you've been using Go for a while, you might have seen these functions in the standard library.

These functions are easily spotted by their names, they start with 'Must' (or 'must'). This naming is a clear sign that they can cause a panic if something unexpected happens.

```go
func MustCompile(text string) *Regexp {
    regexp, err := Compile(str)
    if err != nil {
        panic(...)
    }

    return regexp
}
```

Must functions are mainly useful for initialization tasks that are expected not to fail under normal circumstances, such as setting up package-level variables at the beginning of an application, preparing regex patterns, or establishing database connections.

```go
func MustLoadConfig(path config) *Config {
    config, err := LoadConfig(path)
    if err != nil {
        panic(...)
    }

    return config
}
```

They are also really helpful in testing scenarios, where they allow tests to fail immediately using `t.Fatal`:

```go
func mustDecodeJSON(t *testing.T, jsonStr string, v any) {
    t.Helper()
    if err := json.Unmarshal([]byte(jsonStr), v); err != nil {
        t.Fatalf(...)
    }
}
```

Must functions serve as tools for initialization and testing, situations where failures are truly unexpected.

They help make error handling simpler in specific situations but should be used carefully to avoid causing unintended panics in your program.

### Pass Values, Not Pointers

This is a topic that has tripped up many folks, myself included, when we first started working with Go.

Often, there's a strong temptation to pass pointers in our functions for a couple of reasons:

- One might be looking to avoid the overhead associated with copying a struct.
- Or perhaps, you already have a pointer, and it feels like an unnecessary step to dereference it just to pass the value (*T).

#### 0. Common Thoughts about Pointers:

It's quite usual to think of pointers as a clever way to conserve memory.

Why would one go through the problem of copying all that data to pass to a function when you could simply send over a tiny address that points to where the data is stored, right?

However, the advice generally leans towards preferring to pass values directly to functions instead of passing pointers.

Why? Here are 5 key points on when to pass values.

#### 1. Fixed-sized Types

We're talking about integers, floats, small structs, and small arrays here.

These types have a fixed memory footprint that's often comparable to or even smaller than the size of a pointer on many systems.

```go
type Rectangle struct{
    Width, Height int
}

func CalculateArea(rect Rectangle) int {
    return rect.Width * rect.Height
}
```

Here, passing the `Rectangle` struct directly is efficient because the size of the struct is on par with a pointer in terms of memory usage, this means you don't incur any significant overhead.

#### 2. Immutability and Clarity

Passing by value means that the function receives its own copy of the data, unattached from the original (not completely).

This way ensures that you don't have to worry about unintended side effects because any changes made within the function remain localized to that function. Of course, this doesn't apply to slices, maps, and channels, which are "reference" types.

Moreover, passing a value rather than a pointer sends a clear message to your team: "Hey, I'm not messing with your data, I just need to work with it."

This solution is both clear and safe.

```go
func AdjustWidthByValue(rect Rectangle, newWidth int) Rectangle {
    rect.Width = newWidth
    return rect
}

func AdjustWidthByPointer(rect *Rectangle, newWidth int) {
    rect.Width = newWidth
}
```

In the examples provided, both methods are considered good practice. If you need to modify the value inside the function you're calling, using a pointer makes sense, of course.

> "Didn't you advise using pointer receivers as the default approach, and now you're suggesting passing by value?"

When it comes to passing values to functions, the guidelines differ somewhat.

For methods, using pointer receivers is generally recommended because in Go, we can call methods on both values and pointers to a type, which already makes the distinction between the two a bit blurry, there are also several rules in that tip.

However, when you're dealing with "standalone" functions, passing values is frequently the preferred method because it avoids potential side effects.

#### 3. Small or Unlikely to Grow Types

For data types that are inherently small or unlikely to expand significantly in size, passing them directly avoids the additional step of dereferencing pointers. This simplifies the function's interaction with the data and can improve performance by reducing indirection.

#### 4. Passing Values is Fast and Rarely Slower Than Pointers

It might indeed seem counterintuitive because it involves copying, but here's why passing values can often be faster than passing pointers:

- Copying a small amount of data is highly efficient and can sometimes be quicker than dealing with the indirection that comes with using pointers.
- It reduces the workload on the garbage collector, when values are passed directly, the garbage collector has fewer pointer references to keep track of.
- Data that is passed by value tends to be stored more contiguously in memory, which allows the CPU to access this data more efficiently.

It's a rare occurrence to have a struct that is so large it actually benefits from being passed by pointer due to its size.

#### 5. Make Passing Values Your Default

We should default to passing values unless you have benchmarks that demonstrate a clear advantage to using pointers.

A slight performance improvement is often not worth the loss of clarity that can come with using pointers.

Indeed, pointers can enhance performance for very large or dynamically expanding structs. You really need to justify their use with solid evidence that the gains are substantial and meaningful.

### Prefer Using a Pointer Receiver When Defining Methods

The guidance around when to use a pointer receiver isn't quite as straightforward as this: "Use pointer receivers to modify, and value receivers otherwise." There are nuances and specific scenarios where one might be more appropriate than the other.

Here is a quick insight: https://twitter.com/func25/status/1757759982354026636

So, when should you consider opting for a pointer receiver?

- To modify the receiver's state directly within the method.
- For structs that are considered "large" — though what qualifies as large can be a bit subjective and might vary depending on the context.
- When a struct includes synchronizing fields such as `sync.Mutex`, using a pointer in these cases helps prevent copying the lock mechanism, which is crucial for maintaining thread safety.
- If you're unsure, relying on the side of a pointer receiver might be a safer bet as it covers more scenarios without adverse effects.

```go
type SafeCounter struct {
    mu    sync.Mutex
    count int
}

func (s *SafeCounter) Increment() {
    s.mu.Lock()
    defer s.mu.Unlock()

    ...
}
```

Now, when is a value receiver more suitable?

- For types that are inherently small and are not expected to change — essentially, immutable types.
- If our type is a map, func, channel, or involves slices that are not changed in terms of size or capacity (even though the elements within may be modified).

> "Why do slices that aren't changed in size or capacity matter in this context?"

Een though we can modify the elements of the slice or the content of the underlying array through a value receiver, which indeed affects the original slice, any operation that resizes the slice, like using the `append` function that **increases its capacity**, won't impact the original slice outside the method. 

To illustrate this, consider the following example:

```go
func main() {
    s1 := []int{1, 2, 3}
    s2 := s1

    s2[0] = 5
    s2 = append(s2, 4)

    s1 // [5 2 3]
    s2 // [5 2 3 4]
}
```

In this example, both `s1` and `s2` initially share the same underlying array. However, once the `append` operation on `s2` exceeds the original capacity, a new array is allocated for `s2`, and as a result, `s1` remains unaffected by further modifications to `s2`.

Lastly, consistency is important.

It's advisable to avoid mixing receiver types for a given struct to maintain consistency. If any method within the struct requires a pointer receiver due to the need for mutations, it's generally better to apply pointer receivers across all methods of that struct, even those that don't involve mutations.

There are a couple of main reasons for this:

- Using both pointer and value receivers can lead to confusion and inconsistency in how instances of that struct are manipulated, in Go, we can call methods on both values and pointers to a type.
- Consistency in receiver types helps maintain straightforward and predictable interactions with interfaces. (for more details, refer back to my earlier [tweet in part 4/4](https://twitter.com/func25/status/1757760004768354346)).

### Simplify Function Signatures with Structs or Variadic Options

When designing functions in Go, you might find yourself needing to pass a large number of parameters. 

```go
func ConnectToService(
    host string,
    port int,
    username string,
    password string,
    ssl bool
) { 
    ...
}
```

This approach can obscure the function's purpose and make maintaining the code more cumbersome, particularly when multiple parameters of the same type are involved.

To keep things tidy, consider two strategies:

- Option structs.
- Variadic options.

#### 1. Option Structs

One effective method is to bundle your parameters into a struct, this not only enhances readability but also simplifies the process of passing arguments.

When should we use it?

- When our function has a long list of parameters, of course.
- If we're aiming for self-documenting code, as struct fields inherently describe their purpose by their names, their comments.
- If we want to easily set default values or modify options in a flexible way.

```go
type ServiceOptions struct {
    Host     string
    Port     int
    Username string 
    Password string 
    SSL      bool
}

func ConnectToService(options ServiceOptions) {
    ...
}
```

When employing this pattern, it's important to keep the `context.Context` as a separate parameter and not include it within the options struct.

```go
func ConnectToService(ctx context.Context, options ServiceOptions) {
    ...
}
```

This separation is important because the context plays a unique role in controlling request-scoped values, deadlines, and cancellation signals.

A few tips to keep in mind when using this:

- Ensure the struct remains backward-compatible. This means that when you add a new field, you don't disrupt anything that was previously working.
- Always validate the struct before processing it.
- Consider making your option struct unexported and exposing a `NewXXX()` function to set your default values.

#### 2. Variadic Options

This method takes advantage of Go's functional capabilities, allowing you to pass a flexible number of options in a cleaner manner.

It proves ideal when:

- The function requires a high degree of configurability.
- Most of the options are optional or seldom used.
- We are aiming for a concise call site.

Take a detailed look at how you can implement this:

```go
type ServiceConfig struct {
    ssl bool
}

type ServiceOption func(*ServiceConfig) 

func WithSSL(enable bool) ServiceOption {
    return func(cfg *ServiceConfig) {
        cfg.ssl = enable
    }
}

func ConnectToService(options ...ServiceOption) {
    cfg := ServiceConfig{}
    for _, option := range options {
        option(&cfg)
    }

    ... 
}
```

And using it is straightforward, whether you provide options or not:

```go
ConnectToService()

ConnectToService(WithSSL(true))
```

Setting a default value is easier than with an optional struct; there is no need to hide it. Instead, you can just place the default value directly in the ConnectToService function.

We may hate the idea of variadic options, because they're more complex to implement than a struct, but personally, I would prefer to handle the complexity of implementing variadic options on the provider/ library side, allowing the caller to have an easier experience using my service.

### Give the Caller the Right to Make Decisions

Here's the concept: When you're crafting a function, you'll need to make some choices, such as:

- How to handle errors—should you log them or let the program panic?
- Is it appropriate to spawn goroutines within the function?
- Should you set a context timeout for 10 seconds?

The fundamental idea is to let the user of your code (the caller) have the control and responsibility to make these important decisions, instead of deciding for them in your code.

#### 1. Handling Errors

Here's a scenario where we log the error and then pass it back to the caller:

```go
func DoSomething() error {
    if err := doSomething(); err != nil {
        log.Println("do something failed:", err)
        return err
    }

    return nil
}
```

Rather than choosing to panic or logging the error directly within your function, think about simply returning the error to the caller.

```go
func DoSomething() error {
    if err := doSomething(); err != nil {
        return fmt.Errorf("do something: %w", err)
    }
}
```

This method allows the caller to decide what to do next, whether that's logging the error, attempting the action again, or even panicking if the situation truly calls for it.

> "Why not log the error in both our function and the caller's, to make it easier to trace back?"

_This thought links back to [Single Touch Error Handling: Less Noise](#single-touch-error-handling-less-noise)_

By doing so, we allow the callers the authority to decide how severe an error is in their specific context and what the appropriate action should be, logging, retrying, panicking, etc.

#### 2. Goroutines

I've observed some functions that are designed to execute background tasks like this:

```go
func fire() {
    go func() {
        ...
    }()
}
```

When our function requires performing concurrent operations, it can be tempting to launch a goroutine within it. However, it’s often more suitable to let the caller decide when and how to handle concurrency, such as by invoking 'go fire()' from their side.

The example above also shows a lack of control for the caller, perhaps we should consider giving the caller the ability to manage the goroutine's lifetime as well.

#### 3. Other Operations

This tip extends beyond just goroutines and error handling, which we've focused on because they're integral to our programming language.

It includes a wider range of code design decisions, allowing the caller to determine:

- How long operations should wait before timing out.
- What level of logging to implement.
- Whether to initiate a database transaction.
- Among other considerations...

However, as with many strategies in software development, it depends. You don’t want to burden the caller with too many choices, rather, provide them with just what they need.

By giving critical decision-making to the caller, our code becomes more adaptable, reusable, and sensitive to varying contexts.

### Tips for a Cleaner, More Testable main() Function in Go

Often, we stuff a lot of different tasks into the main() function, such as:

- Setting up the environment and JSON configurations.
- Connecting to a database, a Redis cache, or other services.
- Establishing connections to a message queue or linking up with other services.
- ...

When there's a problem, it's common practice to halt the program using log.Fatal:

```go
func main() {
    conf, err := config.Fetch()
    if err != nil {
        log.Fatal("fetch config failed:", err)
    }

    if err := db.Connect(conf.DB); err != nil {
        log.Fatal("unable to connect db:", err)
    }

    ... // additional setup might also call log.Fatal
}
```

If this solution sounds familiar, it's not necessarily bad, particularly if you don't require very detailed control. 

What if we aimed to improve it? Let's list out some goals:

- Using log.Fatal abruptly stops the program, which means any functions that are deferred to run after the code completes won't execute.
- We'd like the flexibility to tweak arguments (such as `os.Args`) or the environment settings to facilitate different testing scenarios.
- I'd prefer not to stick to using `log.Fatal` for every error that crops up.
- I want to have control over how the application terminates, whether in error (with an exit code of 1) or smoothly (with an exit code of 0).

_[Gracefully Shut Down Your Application](#gracefully-shut-down-your-application)_

To solve these issues, let's move all the setup work out of the main function:

```go
func main() {
    if err := run(os.Args[1:]); err != nil {
        log.Println("error:", err)
        os.Exit(1)
    }
}

func run(args []string) error {
    conf, err := config.Fetch()
    if err != nil {
        return fmt.Errorf("fetch config failed: %w", err)
    }

    dbConn, err := db.Connect(conf.DB);
    if  err != nil {
        return fmt.Errorf("unable to connect db: %w", err)
    }
    defer dbConn.Close() // This now will be called when run() ends even any seting up error

    ... 
    return nil
}
```

By restructuring it this way, we've achieved all our goals, and now the `main()` function primarily handles getting things ready to run.

This setup allows us to test the 'run' part independently with different sets of arguments.

If we want to do more than just return an error, like returning a specific exit code, we could modify the run function to return an integer. This way, we can end the program with os.Exit and the appropriate exit code:

```go
func run(args []string) int {
    ...
}
```

Also, by delegating error handling to the main function, we avoid the need to write a log for each error, simplify our error management process.

### Just... Don’t Panic()

The advice "Don't panic" might seem a bit aggressive (or blunt?), but it's actually a solid guideline when it comes to running software in production environments.

> ""Why? Even though I can catch a panic with recover()?"

Well, it turns out that you might not always be able to recover from a panic(), even with the help of recover(), let me explain this a bit further:

```go
func panicFunc() {
    panic("I'm panicking")
}

func main() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered from panic", r)
        }
    }()

    go panicFunc()

    time.Sleep(time.Second) // wait for goroutine to finish
}

// panic: I'm panicking
```

In the snippet above, the panic is triggered in a separate goroutine (`go panicFunc()`).

The mechanism for panic recovery with `recover()` only works properly if the panic happens within the same goroutine as where the `recover()` call is made. This means that the defer function in the main cannot catch or recover from the panic, leading to a crash of the program despite the attempted recovery.

But there's more to it than just technical limitations. Here are a couple more reasons why preventing panics is crucial, especially in production:

1. In production, your code needs to be stable. 

An unexpected crash is a serious issue and it can lead to downtime, affecting not only user experience but potentially harming your business's reputation as well.

2. A panic in one part of your system can set off a chain reaction, causing more failures across the system.

This is particularly risky in environments like microservices or other distributed systems where components heavily depend on each other, potentially leading to what is known as cascading failures.

Let's discuss a common scenario where you're initializing your application in the `main` function. If something goes wrong during this initialization, you might be tempted to use panic to halt the process:

```go
func initFile(filePath string) {
    file, err := os.Open(filePath)
    if err != nil {
        panic(err)
    }
    defer f.Close()
}

func main() {
    initFile("config.json")
}
```

While this solution might seem passable, it actually encourages the use of panic, and in doing so, and we lost the ability to handle the error in a more graceful way.

```go
func initFile(filePath string) error {
    file, err := os.Open(filePath)
    if err != nil {
        return fmt.Errorf("open file %s: %w", filePath, err)
    }
    defer f.Close()
    ...
    return nil
}

func main() {
    if err := initFile("config.json"); err != nil {
        fmt.Fprintf(os.Stderr, "error: %v\n", err)
        os.Exit(1)
    }
    ...
}
```

If an error occurs, it's returned to the `main` function, **where decisions can be made** about how to proceed. This could involve retrying the operation, using a default value, or logging a detailed error message for debugging purposes. 

_[Give the Caller the Right to Make Decisions](#give-the-caller-the-right-to-make-decisions)_

Regarding the use of panics, they should generally be reserved as a last resort:

- Only use panics for truly unrecoverable errors, this means situations where continuing to run the program could lead to more severe issues, e.g. data corruption, undefined behavior.
- During program initialization, using a panic might be considered "acceptable" if a critical component fails to start, this indicates that the program cannot operate as intended and should be stopped immediately.

## Tricks

### Measure the Execution Time of a Function Using defer

Here's a simple helper to measure how long it takes for a function to run using just one line, by using defer. 

This method is really useful for quick debugging and when developing, but remember, it might need some adjustments before you use it in a live setting.

```go
func main() {
    defer TrackTime(time.Now()) // <--- THIS

    time.Sleep(500 * time.Millisecond)
}

func TrackTime(pre time.Time) time.Duration {
    elapsed := time.Since(pre)
    fmt.Println("elapsed:", elapsed)

    return elapsed
}

// elapsed: 501.11125ms
```

This approach works well because of how `defer` handles its arguments. 

When we use a defer statement, it figures out the values of its arguments right away, but it doesn't actually call the function until the very end of the function it's in, after everything else in that function has already happened. 

In this case, it waits until the `main` function is about to finish. This way, the elapsed time recorded includes all the actions that happened from the start till the end of the function, giving you a complete picture of how long the function took to execute.

### Multistage Defer: Handling the Start and End of a Function Efficiently

When we talk about multistage defer, we're looking at a method to manage the start and end of a function efficiently, without requiring the person using your code to handle these aspects manually.

We can use defer in Go to ensure certain actions are performed at the beginning and end of a function.

```go
func main() {
    defer MultistageDefer()() // <-------

    fmt.Println("Main function called")
}

func MultistageDefer() func() {
    fmt.Println("Run initialization")

    return func() {
        fmt.Println("Run cleanup")
    }
}

// Output:
// Run initialization
// Main function called
// Run cleanup
```

This technique is particularly useful for managing resources, like a database connection, by setting it up at the start and ensuring it closes properly at the end. This setup frees the user from having to remember to close it themselves.

It can also be used to track how long a function runs with a simple setup:

```go
func TrackTime() func() {
    pre := time.Now()

    return func() {
        elapsed := time.Since(pre)
        fmt.Println("elapsed:", elapsed)
    }
}

func main() {
    defer TrackTime()()

    time.Sleep(500 * time.Millisecond)
}

// Output:
// elapsed: 501.11125ms
```

This trick provides a neat way to learn how defer works. However, it’s important to note that the syntax `()()` can be tricky to use correctly and isn’t typically recommended for larger, team-based projects.

It's more suited to smaller or personal projects where you have full control over the environment.

This trick is inspired by [@teivah](https://twitter.com/teivah)

### Make Structs Un-Comparable

If every field within a struct is comparable, then the struct itself is comparable, this means we can use `==` and `!=` operators to compare two structs directly:

```go
type Point struct {
    X, Y float64
}

p1 := Point{1.0, 2.0}
p2 := Point{1.0, 2.0}

fmt.Println(p1 == p2) // true
```

However, directly comparing structs that contain floats can lead to issues since floating-point values are best compared with an approximation due to their inherent precision limitations.

We might prefer our team to use a custom `.Equals()` method for comparison:

```go
func (p Point) Equals(other Point) bool {
    return math.Abs(p.X - other.X) < 1e-9 && math.Abs(p.Y - other.Y) < 1e-9
}
```

But let's consider this: using p1 == p2 is straightforward, fast, and all too tempting for anyone who might not be fully aware of the nuances or might not read through the entire methods of a type.

To make sure that every developer consistently uses your defined comparison methods, here's a zero-cost strategy to make your structs non-comparable:

```go
type Point struct {
    _     [0]func()
    X, Y  float64
}
```

This [0] func() field has three key properties:

- It's unexported, keeping it hidden from users of your struct, preventing external manipulation.
- It's zero-width or no-cost, meaning this array takes up no space in memory because its length is zero.
- It's non-comparable, `func()` is a function type, and functions are non-comparable in Go, which in turn makes the struct non-comparable.

Trying to compare two instances directly using the `==` or `!=` operators when there's a [0]func() involved will indeed lead to a compile-time error.

Now, it's particularly interesting to note the placement of the [0]func() within the struct, even though it doesn't actually take up any space, its position can affect the overall size of the struct.

For instance, if you check out this code:

```go
// 16 bytes
type Point struct {
    _     [0]func()
    X, Y  float64
}

// 24 bytes
type Point struct {
    X, Y  float64
    _     [0]func()
}
```

You can see how the placement of the `[0]func()` changes the size of the struct, this discussion is quite relevant and can be explored further in the GitHub issue [runtime: pointer to struct field can point beyond struct allocation #9401.](https://github.com/golang/go/issues/9401)

To make it clearer and more understandable, especially when notifying our team, we might define a custom type that encapsulates this non-comparable trait, something like this:

```go
type nonComparable [0]func()

type Point struct {
    nonComparable
    X, Y  float64
}
```

This idea was inspired by [@nalesnikowydzem](https://twitter.com/nalesnikowydzem)

### Result Forwarding in Function Calls

When first started using Go, we may find one concept a bit tricky: result forwarding in function calls.

In Go, it's pretty common to receive multiple values from a function, right? Often a result paired with an error. Let's take a look at a typical example where we handle the output from a function like `processResult(result)` at the end of the function:

```go
func doSomething() (int, error) {
    ...

    return result, nil
}

func main() {
    result, err := doSomething() 
    if err != nil {
        log.Fatal(err)
    }

    processResult(result)
}
```

What's interesting here is that if the result directly matches the expected type for the `processResult` function, we can shorten the code by passing the result straight through:

```diff
func main() {
-   result, err := doSomething() 
-   if err != nil {
-       log.Fatal(err)
-   }
-   processResult(result)
+   processResult(doSomething())
}

func processResult(result int, err error) {
    ...
}
```

Now, let’s consider a scenario where we receive a result and an error, and based on that, we need to send a response back to the client with an appropriate status code.

In many API layers, we'll find numerous controllers or functions handling this pattern. They process the (result, error) tuple, returning a 400 status code if there’s an error, or a 200 OK if everything checks out fine:

```go
func GetResult(api *API) {
    result, err := Response()
    if err != nil {
        api.JSON(http.StatusBadRequest, err)
        return
    }

    api.JSON(http.StatusOK, result)
}
```

Instead of repeatedly applying the same pattern across various functions, we could greatly cut down our code by implementing a standardized method to handle these responses based on the function calls:

```go
func GetResult(api *API) {
    api.JSONWithStatus(Response())
}

func (api *API) JSONWithStatus(result any, err error) {
    if err != nil {
        api.JSON(http.StatusBadRequest, err)
        return
    }

    api.JSON(http.StatusOK, result)
}
```

This tip is particularly effective when used in conjunction with a 'must' function: _[Intentionally Stop with Must Functions](#intentionally-stop-with-must-functions)_

Additionally, I have a little generic helper function that takes care of returning the result if there’s no error, or halts the process if there is:

```go
func Must[T any](result T, err error) T { ... }
```

Of course, do not overuse this pattern. 

While it's highly useful when you have a clear, repetitive pattern in your codebase, like the `Must` or the `JSONWithStatus` function we've talked about, it can be a poor choice, because hiding the error handling in utility functions can sometimes obscure what the code is actually doing.

### Returning Pointers Made Easy with Generics

Here's a handy tip for those coding in Go who frequently find themselves needing a pointer from a function return value.

In the past, we might have handled this in a couple of ways, perhaps we did something like this:

```go
result := getData() 
ptr := &result
```

Or maybe you tried to streamline the process into a single line using a "clever" workaround:

```go
ptr := func(t Data) *Data { return &t }(getData())
```

While this method gets the job done, it can be somewhat verbose, especially if you're dealing with multiple data types and find yourself repeating the process.

Now, let's discuss a more optimised, "trendy" solution using generics:

```go
func Ptr[T any](v T) *T {
    return &v
}
```

This simple function allows us to generate a pointer for any type of value without the repetitive coding. All we need to do is pass our value to the `Ptr` function, and it returns the necessary pointer. 

Here's how you can use it:

```go
timePtr := Ptr(time.Now())

intPtr := Ptr(42)

strPtr := Ptr("hello")
```

### Compile-Time Interface Verification

Let's discuss a topic that resonates with those of us working with interfaces in Go, especially when it's important to ensure your structs properly implement an interface at compile time. Also, we have a nice central place to check it instead of looking around the codebase.

Consider we have a `Buffer` interface that requires a `Write()` function, and we've created a `StringBuffer` struct intended to implement this interface.

```go
type Buffer interface {
    Write(p []byte) (n int, err error)
}

type StringBuffer struct{}
```

However, let’s say we accidentally introduce a typo in our method name, such as `Writeee()` instead of `Write()`, as shown here:

```go
func (s *StringBuffer) Writeee(p []byte) (n int, err error) {
}
```

To help catch such errors more promptly, at compile time, you can use a simple yet effective method:

```go
// syntax: var _ <interface> = (*type)(nil)
var _ Buffer = (*StringBuffer)(nil)
```

This line of code will not change the runtime behavior but will compel the Go compiler to check whether `*StringBuffer` genuinely satisfies the `Buffer` interface. 

If `StringBuffer` fails to properly implement all the methods defined in Buffer, the compiler will immediately flag an error and tell us what's missing.

```go
// cannot use (*StringBuffer)(nil) (value of type
// *StringBuffer)
// as Buffer value in variable declaration: *StringBuffer
// does not implement Buffer (missing method Write)
```

### Prevent Struct Unkeyed Literals by Using an Empty Field

The main goal here is to make sure that when someone uses our package and decides to create an instance of our struct, they are required to use named fields in their struct literals.

Here is a scenario where we have a `Point` struct defined with two fields: `X` and `Y`.

```go
// package math
type Point struct {
    X float64
    Y float64
}

a := math.Point{1, 4}
```

Using this method to instantiate the struct isn't typically problematic for something straightforward like a `Point` struct, which just includes `X` and `Y`.

Let's say we decide to expand the `Point` struct by adding additional fields, such as a string 'label' field. 

If this change is made, any existing code written by users of your library that does not update to include the new field will fail to compile. The error they would encounter would be something like "too few values in struct literal of type config.Point," and this results in issues with backward compatibility.

To encourage users to explicitly define fields like `X` and `Y`, here’s a strategy: add a special variable to the struct that is both non-exported and zero-size.

Common choices for zero-size types include empty structs (struct{}) and zero-length arrays:

```go
type Point struct {
    _ struct{}
    X float64
    Y float64
}

a := math.Point{X: 1, Y: 4}
b := Point{1, 4} // compile-time error
```

This setup essentially signals: "This Point isn't just X and Y, there's more to come."

> "Why a non-exported, zero-size field?"

Using a non-exported, zero-size field is a clever tactic. 

- The non-exported aspect makes it inaccessible outside the package, maintaining encapsulation. 
- Since it's zero-size, it doesn’t add any memory overhead to the struct.

If the `_ struct{}` syntax feels too obscure and doesn't clearly communicate the intention to prevent unkeyed literals, here’s another approach:

```go
type noUnkeyed struct {}

type Point struct {
    noUnkeyed
    X float64
    Y float64
}
```

While a linter could catch issues with unkeyed literals, not all clients might use one, and this solution ensures compliance without reliance on external tools.

Of course, it's worth mentioning that there are scenarios where using unkeyed literals might be preferable. 

For instance, when dealing with a map element that only contains only key and value, e.g. `Pair { Key; Value }`, like `bson.E` in MongoDB, if these fields are unlikely to change, forcing keyed literals might actually reduce the readability of the code.

## Miscellaneous

### Parse a Slice into an Array

You might think of using the `copy()` function when you need to convert a slice into an array, right?

```go
slice := []int{1, 2, 3, 4, 5}
var array [5]int

copy(array, slice)
```

While this method works perfectly fine, it can seem a bit verbose.

If we've updated our project to Go 1.20, we can handle this conversion much more straightforwardly, similar to how you might convert between other types, like from int to int32.

```go
// Go 1.20

a := []int{0, 1, 2, 3, 4, 5}
b := [3]int(a[0:3])

fmt.Println(b) // [0 1 2]
```

But if you're coming from an older version, Go 1.17 still offers a one-liner solution for you.

```go
// Go 1.17

a := []int{0, 1, 2, 3, 4, 5}
b := *(*[3]int)(a[0:3])

fmt.Println(b) // [0 1 2]
```

This technique uses a type assertion to convert a segment of a slice directly into an array.

### Avoid Using `math/rand`, Use `crypto/rand` for Keys Instead

When you're working on projects that involve generating keys, whether it's for encryption or for creating unique identifiers, the quality and security of those keys are important.

**Why not math/rand?**

The `math/rand` package is great for generating pseudo-random numbers. But this method has a major drawback: if someone figures out how the numbers are generated (the seed), they can predict future numbers. 

```go
import "math/rand"

func Key() string {
    // rand.Seed(time.Now().UnixNano()) // DEPRECATED
    r := rand.New(rand.NewSource(time.Now().UnixNano()))

    buf := make([]byte, 16)

    for i := range buf {
        buf[i] = byte(r.Intn(256))
    }

    return fmt.Sprintf("%x", buf)
}
```

Even if you seed the generator with something like the current time, the amount of unpredictability, or entropy, is fairly low. This is because there isn’t much variation in the time from one execution to the next.

**Why crypto/rand?**

On the other hand, `crypto/rand` offers a better solution for generating numbers that are cryptographically secure. This library is designed to be completely unpredictable, using sources of randomness provided by your OS, which are typically much more difficult to forecast.

```go
import "crypto/rand"

func Key() string {
    buf := make([]byte, 16)

    _, err := rand.Read(buf)
    if err != nil {
        panic(err)
    }

    return fmt.Sprintf("%x", buf)
}
```

Using `crypto/rand` is particularly needed for operations like encryption, authentication, or any other context where security is a critical concern.

Again, if you're generating keys for non-security-related purposes, `math/rand` is perfectly fine. 

### Table-driven Tests, Subtests, and Parallel Tests

It would indeed be a significant oversight to overlook the testing section in our series of tips, considering how important it is for ensuring we can sleep peacefully after deploying our applications.

#### 1. Table-driven Tests

Table-driven testing is a strategy where tests are structured in a table format, clearly outlining the inputs and the expected outcomes.

Take a quick example, we have a function add() that sums two numbers. Below is the way we could set up a testcase table:

```go
testCases := []struct {
    a, b, expected int
}{
    {1, 2, 3},
    {4, 5, 9},
    {7, 8, 15},
}
```

Once we have these test cases defined, we can execute all of them using a single `TestXXX` function:

```go
func TestAdd(t *testing.T) {
    ... // test cases which are defined above

    for _, tc := range testCases {
        got := add(tc.a, tc.b)
        if got != tc.expected {
            t.Errorf("add(%d, %d) = %d; want %d", tc.a, tc.b, got, tc.expected)
        }
    }
}
```

This setup allows us to add as many test cases as we need, and if any of them fail, the results will be neatly printed out on your console or any other output.

For instance, here's how some failed tests might look after mistakenly using a*b in the add() function:

```go
--- FAIL: TestAdd (0.00s)
   add(1, 2) = 2; want 3
   add(5, 0) = 0; want 5
   add(-1, -2) = 2; want -3
   add(-5, 10) = -50; want 5
```

Okay, that's good enough, but there's still one piece missing here: the name of each test case.

Naming each test case becomes incredibly important if a test fails, it helps us quickly identify which specific test didn't pass without needing to scrutinize the inputs and expected outcomes too closely.

#### 2. Subtests & Parallel

Subtests provide a way to structure tests logically under a larger test "umbrella" and allow for them to be run as part of a broader test function.

To start, let’s assign a name to each test case, we could stick with an array format as before, or we could opt for using a `map[string]testcase` to make it clearer:

```go
func TestAdd(t *testing.T) {
    testCases := []struct {
        name string
        a, b, expected int
    }{
        {"2 positives", 1, 2, 3},
        {"positive & zero", 5, 0, 5}
        {"2 negatives", -1, -2, -3},
        {"negative & positive", -5, 10, 5},
    }
}
```

Next, we’ll modify our test to include a subtest for each case and enable parallel execution, pay attention to these two updates:

```go
for _, tc := range testCases {
    tc := tc // before Go 1.22

    t.Run(tc.name, func(t *testing.T) {
        t.Parallel() // enable parallel execution
        got := add(tc.a, tc.b)
        if got != tc.expected {
            t.Errorf("add(%d, %d) = %d; want %d", tc.a, tc.b, got, tc.expected)
        }
    })
}
```

The results now displayed on the console are much more structured. They clearly indicate which test and which subtest failed, presenting it in a hierarchical format:

```go
--- FAIL: TestAdd (0.00s)
       --- FAIL: TestAdd/2 positives (0.00s)
                         add(1, 2) = 2; want 3
```

This strategy of structuring test output makes it significantly easier and quicker to detect and resolve issues.

### Enums Start from 1 for Categorization and 0 for Default Cases

Just to point out that Go doesn't actually support enums natively, but we've all come to know a common workaround that sort of emulates this feature.

Let’s look at an example to clarify this point:

```go
type UserRole int 

const (
    Admin UserRole = iota  // 0
    User                   // 1
)
```

In this setup, if a `UserRole` variable is declared without being explicitly initialized, it defaults to 0, which in this case, could inadvertently assign someone the `Admin` role. 

Clearly, this isn't ideal, as it could lead to significant security flaws.

Here’s a useful guideline to take:

- Starting enums at 1 is a strategy used to ensure that the zero value, which is the default for numeric type variables in Go, does not accidentally take on a meaningful state.
- Starting enums at 0 is recommended when you want the default state to represent a meaningful and intentional choice.

A better way to solve this might be:

```go
type UserRole int

const (
    Admin  UserRole = iota + 1  // 1
    User                        // 2
    Viewer                      // 3
)
```

Or, you could think about using a different strategy where you introduce a role named 'Unknown' that kicks in as the default when things don't go as planned.

```go
const (
    Unknown UserRole = iota  // 0
    ...
)
```

> "I believe defaulting to Viewer is a better choice."

It actually depends, that might not be the best idea all the time, e.g. an editor might be incorrectly assigned the `Viewer` role due to the default value, a potential logic error. The `Viewer` role could let someone see more than the `Editor` and this might not suit an editor who only needs to work on certain content, not everything a `Viewer` can see.

That's why we use 0 to detect issues in our setup and avoid any potential problems.

Here are a few more examples to paint a clearer picture:

- AppMode (development, testing, production)
- Status (success, error, pending)
- Action (login, logout, purchase). 

In these cases, each option is just as important as the others, so there's no default or preferred choice that stands out.

How ever, when there's a definite default that makes sense, it's totally fine to set the zero value to reflect that:

```go
type ConnectionState int

const (
    Disconnected ConnectionState = iota  // 0
    Connecting                           // 1
    Connected                            // 2
)
```

Deciding whether to start enums at 0 or 1 really boils down to what you need for your project and what makes the most sense from a security standpoint.

### Using Flag Enums to Handle Multiple Properties in a Single Variable

In Go, enums aren't quite set up like they are in some other programming languages (as we pointed out in previous tips)

What Go offers instead is a neat trick where you use a block of constants with the iota identifier, and this setup acts a lot like an enum:

```go
type BasicInfo int

const (
	IsBoolean  BasicInfo = iota + 1 // 1
	IsInteger                       // 2
	IsFloat                         // 3
	IsComplex                       // 4
	IsString                        // 5
)
```

Here, we begin our numbering at 1 by using iota + 1, this approach can be quite handy if we want to keep zero as a special case or if a zero value doesn't really fit into our scenario, something we've again, discussed.

_[Enums Start from 1 for Categorization and 0 for Default Cases](#enums-start-from-1-for-categorization-and-0-for-default-cases)_

> "In the above example, what happens when a type can be both an integer and an unsigned?"

Well, one option could be to introduce another type like `UnsignedInteger` or to create a separate enum to indicate whether something is `Signed` or `Unsigned`. 

However, this solution doesn't scale very well as we start adding more combinations and it can get pretty cumbersome to manage all those possible states, especially if your application needs to handle multiple such types with many overlaps.

#### Flag Enums

In practice, we might want to use something called a bit mask or flag enums.

This strategy lets us handle enums in a way that allows **multiple states** or properties to be combined into just one variable:

```go
type BasicInfo int

const (
	IsBoolean  BasicInfo = iota + 1 // 1
	IsInteger                       // 2
	IsUnsigned                      // 4
	IsFloat                         // 8
	IsComplex                       // 16
	IsString                        // 32
	IsUntyped                       // 64
)
```

With flag enums, we can easily combine properties like `IsInteger` and `IsUnsigned` without needing to create a brand new type. 

Instead, we use the bitwise OR operator `|` to indicate that an unsigned integer possesses both of these characteristics:

```go
var unsignedInteger = IsInteger | IsUnsigned
```

For better clarity and to make the code more reusable, you might choose to define the enum like this:

```go
type BasicInfo int

const (
	IsBoolean  BasicInfo = iota + 1 // 1
	IsInteger                       // 2
	...

	IsOrdered   = IsInteger | IsFloat | IsString
	IsNumeric   = IsInteger | IsFloat | IsComplex
	IsConstType = IsBoolean | IsNumeric | IsString
)
```

This particular way of setting it up is actually taken directly from Go's own source code.