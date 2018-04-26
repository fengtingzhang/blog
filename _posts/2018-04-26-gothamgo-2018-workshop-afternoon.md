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
// if you change it to a value accessor `s scribe`, value accessor will not allow a mutate but can be access by both pointers and values
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
