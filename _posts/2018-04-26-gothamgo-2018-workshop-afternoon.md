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

