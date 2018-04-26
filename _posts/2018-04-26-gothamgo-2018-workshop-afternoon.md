---
layout: post
title: gothamgo 2018 workshop afternoon
date: '2018-04-26T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-04-26T09:16:43.607-07:00'
---

# gothamgo 2018 workshop afternoon

## Testing in Go
* You can import packages to make assertions nicer, for example the `is` package

## Maps

### Initializing maps
* A map is an unordered set of values indexed by a unique key.
* Randomness in map is enforced in go since 1.6x, this is to discourage use of unsupported accessor methods during Go version upgrades
* `len` and `cap` same as slice

```go
  beatles := map[string]string{
    "John":   "guitar",
    "Paul":   "bass",
    "George": "guitar",
    "Ringo":  "drums",
  }
```

### Map keys
* keys must be comparables

```go
type simple struct {
  ID int
}

type complex struct {
  f func(id int) simple
}

func main() {
  m := map[simple]string{}

  fmt.Println(m)

  // invalid map key type complex
  m1 := map[complex]string{}
}
```

### Iterators
```go
for key, value := range beatles {
  fmt.Printf("%s plays %s\n", key, value)
}

```

### Validating if an accessed value exists
```go
  // Look up the key `Bob` and detect that it wasn't found by printing `not found`
  _, ok := beatles["Bob"]

  if !ok {
    fmt.Println("not found")
  }

  ...
  // or more simply
  if _, ok := beatles["Bob"]; !ok {
    fmt.Println("not found")
  }

```

### Thread Safety
* One of the awesome things you've heard of is that Go is multi-threaded
* Maps are not thread safe, since Go 1.9x use [sync.Map](https://golang.org/pkg/sync/#Map)


## Pointers
* pointers are simpler, no pointer arithmetics and can't overwrite memory locations


### `pass by value` vs `pass by reference`
* by default, functions in go are pass by value
* you can pass by reference by passing a pointer to the pointer

#### Pass by value
```go
type Beatle struct {
  Name string
}

func main() {
  b := Beatle{Name: "Ringo"}
  changeBeatleName(b)
  fmt.Println(b.Name) // Ringo
}

func changeBeatleName(b Beatle) {
  b.Name = "George"
  fmt.Println(b.Name) // George
}

```

#### To pass by reference

To indicate we are expecting a pointer, we use the * modifier:
```go
func changeBeatleName(b *Beatle) {}
```

To get the address of a value we use the & modifier:
```go
&Beatle{}
```

And we can store pointers too:
```go
b := &Beatle{}
```


```go
func main() {
  x := "George"
  printValue(&x)
  fmt.Println(x)
}

func printValue(s *string) {
  // print the pointer value
  fmt.Println(s)

  // print the value of a pointer to this pointer
  fmt.Println(&s)

  // print the string value
  fmt.Println(*s)

  // change the string value
  *s = "Susan"
}

// Produces
// 0xc42000e1e0
// George
// Susan
```

#### Pointer type checking
go is so strongly typed that you can't do this
```go
type Beatle struct {
    Name string
}

type NonBeatle struct {
    Name string
}

func main() {
    b := &Beatle{Name: "Ringo"}
    n := &NonBeatle{Name: "Susan"}

    changeUserName(n)
}

func changeUserName(b *Beatle) {
    b.Name = "George"
    fmt.Println(b.Name) // George
}
```

will result in
```go
cannot use n (type *NonBeatle) as type *Beatle in argument to changeUserName
```

#### Security vs. Performance
By default go passes by value for security, but it can be more performant to pass a pointer to a large file by pointer (reference)

## Interfaces
* Interfaces allow us to abstract code to make it more reusable and more testable.
* like duck typing, but technically called `structural typing` because it happens on compile type and not like duck typing at run time for interpreted languages
* go does not have class inheritance, it has interfaces and a concept like a mixin
* specifies behavior, there's no field but you can specify a method
* ideally the smaller the interface the better

```go
type Entertainer interface {
  Play()
}

// to invoke a passed concrete struct that implemented the interface
func entertain(e Entertainer) {
  e.Play()
}
```

### Inteface implementation
```go
type scribe struct {
  data []byte
}

// note the pointer receiver `s *scribe` is because you need to mutate the byte field, you have to pass a reference
// if you change it to a value accessor `s scribe`, value accessor will not allow a mutate
// the compiler will allow both but it's more maintainability
func (s *scribe) Write(p []byte) (int, error) {
  s.data = p
  return len(p), nil
}
```

#### Pointer receiver vs. value receivers
* in the example above, `s *scribe` is called a `Pointer Receiver`. Methods defined on pointer receivers can only be accessed when using a pointer. However, methods defined on a value type can be access by both pointers and values.

```go
type User struct {
  First string
  Last  string
}

func (u *User) String() string {
  return fmt.Sprintf("%s %s", u.First, u.Last)
}

func pretty(v fmt.Stringer) {
  fmt.Println(v.String())
}

func main() {
  u := User{First: "Rob", Last: "Pike"}
  pretty(u)
}

/* produces
   cannot use u (type User) as type fmt.Stringer in argument to pretty:
   User does not implement fmt.Stringer (String method has pointer receiver)

 Instead
 A. use
 func (u User) String() string {
  return fmt.Sprintf("%s %s", u.First, u.Last)
 }

 or B. pass the reference
 pretty(&u)
*/
```

### Stylist notes
* It is important to note that interfaces are a collection of methods, not fields.

```go
// good
type Writer interface {
  Write(p []byte) (int, error)
}

// bad
type Emailer interface {
  Email string
}
```

### Empty interface
* not great but since go doesn't have any generics, sometimes interfaces are used to get around that
* you don't get any type safety and runtime panic is very possible, hard to test and can't be confident

```go
// generic empty interface:
interface{}

// a named empty interface:
type foo interface{}
```

* to avoid runtime panic, you should do runtime type checking (type assertions) on empty interfaces
```go
func print(i interface{}) {
  switch t := i.(type) {
  case int:
    t = t + 3
    fmt.Printf("INT: %d\n", t)
  case float64:
    fmt.Printf("FLOAT: %f\n", t)
  case fmt.Stringer:
    fmt.Printf("%T\n", t)
    fmt.Println(t.String())
  default:
    fmt.Println(t)
  }
}
```

## Errors

### No try/catch
```go
f, err := os.Open("/not/a/real/path")
if err != nil { // note you should explicitly check for nil, `if !err` only works for bool
  // alert
  return or break
}

// f is the file handle
```

### creating errors
Defining errors in the standard library
```go
// in the `errors` package
errors.New("a message")

// in the `fmt` package
fmt.Errorf("a %s message", "formatted")
```

Defining custom errors
```go
type error interface {
  Error() string
}
```

### Recovering from panics
* Panics are caused by run time errors, such as cannot find a file path, index out of bound, access nil pointer
* use `defer` with its built-in `recover()` function to recover from panics. Also you can log the error while you're at it.

```go
func main() {
  defer func() {
    if err := recover(); err != nil {
      fmt.Println(err)
    }
  }()
  a := []string{}
  a[42] = "Bring a towel"
}
```

### Sentinenal errors
* Instead of invoking the `recover` function in the `defer`, use a specific value to signify that no further processing is possible.
* Considered bad practice
1. Sentinel errors become part of your public API
1. Sentinel errors create a dependency between two packages

### Alternate error package with stack and wrap
* The standard library errors package lacks support for things like capturing a stack trace or annotating errors.
[github.com/pkg/errors](github.com/pkg/errors)

## Best practices
* use a value receiver for an accessor and a pointer receiver for a mutator
* smaller interfaces
* try not to use else to left align happy paths
* others, check [gometalinter](https://github.com/alecthomas/gometalinter)

## Concurrency
### go routine
* A goroutine is a lightweight "thread" managed by the Go runtime. Goroutines execute concurrently.
```go
func main() {
  go sayHello()
}

func sayHello() {
  fmt.Println("hello")
}
```

This won't print anything, because main exited because the goroutine finished. Instead just wait.

### waitgroup
[waitgroup](https://golang.org/pkg/sync/#WaitGroup)

* Add function
Add adds delta, which may be negative, to the WaitGroup counter. If the counter becomes zero, all goroutines blocked on Wait are released. If the counter goes negative, Add panics.

```go
func main() {
  wg := &sync.WaitGroup{}
  wg.Add(1)
  go sayHello(wg)
  wg.Wait()
}

func sayHello(wg *sync.WaitGroup) {
  defer wg.Done()
  fmt.Println("hello")
}
```

### anonymous functions and closure
Instead of explicitly defining `sayHello`, you can use an anonymous function
```go
for i := 0; i < 5; i++ {
  go func(i int) {
    defer wg.Done()
    fmt.Printf("starting %d...\n", i)
    time.Sleep(1 * time.Second)
    fmt.Printf("ending %d...\n", i)
  }(i)
}
```

Note, you must pass in the `i` variable. If you don't, the scope of `i` would at the outer scope.
Since the goroutines are being added to the execution stack, but not run, by the time they run,
and access that memory space, i has already been changed.

Sending i in as an argument creates a new scope and copies the value to a new memory location to preserve the proper value.

### Mutex
A mutex allows you to synchronize goroutines for safe access to the same shared memory. There are two types of mutexes
1. full lock
1. read/write lock

```go
func main() {
  var mu sync.Mutex
  var counter int

  set := func(i int) {
    mu.Lock()
    defer mu.Unlock()
    counter = i
  }

  get := func() int {
    mu.Lock()
    defer mu.Unlock() // Note, defer will execute at the exit of a function, not at the exit of a code {} block
    return counter
  }

  go func() {
    for {
      time.Sleep(500 * time.Millisecond)
      i := get()
      fmt.Printf("counter: %d\n", i)
    }
  }()

  var wg sync.WaitGroup

  for i := 0; i <= 10; i++ {
    wg.Add(1)
    go func(i int) {
      set(i)
    }(i)
    time.Sleep(750 * time.Millisecond)
  }

  wg.Done()
}
```

### Channel
Channels are a typed conduit through which you can send and receive values.


#### initialize a channel
```go
message := make(chan string)
```

#### Send and receive
```go
ch <- // data is going into the channel
<- ch // data is coming out of the channel
```

#### Buffered channels
By default channels are unbuffered -- sends on a channel will block until there is something ready to receive from it.
You can create a buffered channel - which allows you to control how many items can sit in a channel unread.

```go
  // Adding a second argument to the make function creates a buffered channel
  messages := make(chan string, 2)
```

Channels are not message queues. Only one channel will receive a message sent down a channel. If multiple goroutines are listening to a channel, only one will receive the message.

#### Closing Channels
You can signal that there are no more values in a channel by closing it.
```go
func echo(s string, c chan string) {
  for i := 0; i < cap(c); i++ {
    // write the string down the channel
    c <- s
  }
  close(c)
}

func main() {
  // make a channel with a capacity of 10
  c := make(chan string, 10)

  // launch the goroutine
  go echo("hello!", c)

  for s := range c {
    fmt.Println(s)
  }
}
```


### books and channel
### Books
* The Go Programming Language
* Go Programming Blueprints, 2nd Edition

### Videos
* O'Reilly O'Reilly has a lot of great videos.
* Just For Func is a great series by Francesc Campoy

