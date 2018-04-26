---
layout: post
title: gothamgo 2018 workshop morning
date: '2018-04-26T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-04-26T09:16:43.607-07:00'
---

# gothamgo 2018 workshop morning

## Welcome

### History
* go started as a language at google and has since then grown
* cloudflare is almost exclusively go
* used in the critical paths in many enterprises. Like Java or C#, go is now considered a serious language

### Decisions made
* go is a lot simpler than Java or C++, it's deliberately simpler
* has fewer features than some of the other languages that you're used to
* but in the end you end up with code that's easier to maintain, it's easier to collobrate with


1. for example: there is no operator overloading, because it can result in weird behaviors. I call it "glancibility", if you can make + operator do some
1. another example: there's not base class with no type

it will feel limited but you will end up with code that is familiar, to the extent that if I look at another programmer's code it looks like I wrote it

## Getting Started With Go

### PATHS
GOPATH
Add the bin to the PATH

### Common Layout
$GOPATH/src/github.com/username/project

### Syntax and Types
#### keywords
fallthough - in a switch it means it will not break, but also go to the next case. It's a bit confusing and I never use it

#### operators and delimiters
`:=`

without initialization
```go
var a int
```

explicit type
```go
var a int = 1
```

implicit type
```go
a:=1
```

#### data types
uint/int - either 32 or 64 bits depending on the system

#### strings
* strings are immutable in Go
* UTF-8 support is built-in

#### zero values, constant types
* if a variable is declared but not assigned a value, a default value will be provided for that type
* constants cannot be changed at run-time

#### iota
* iota is like an enum
* you can do shift and multipliers on an iota

```go
const (
  _  = 1 << (iota * 10) // ignore the first value
  KB                    // decimal:       1024 -> binary 00000000000000000000010000000000
  MB                    // decimal:    1048576 -> binary 00000000000100000000000000000000
  GB                    // decimal: 1073741824 -> binary 01000000000000000000000000000000
)
```

* You can use the _ to ignore a variable

#### Structs

* Go doesn't have classes, instead we have structs
* A struct is a collection of fields, often called members (or attributes).


```go
type User struct {
  Name string
  Email string
}

...

func main() {
  foo := User{}
  User.Name = "foo"
  User.Name = "bar"
}
```

A more compact way to initialize the struct
```go
u := User {
  Name: "foo",
  Email: "bar", //note you must provide this second comma here or the linter will complain
}
```

Caveat: there is no constructor. You can set as many of the field values on a struct at initialization time as you want.
```go
u := User{Email: "marge@example.com"}
u.Name = "Marge Simpson"
````

#### scope
* There are no type hierarchies
* Capitalized field and struct names are exported, otherwise they're internal to the package


### Arrays and Iterations

### Initialization
```go
names := [4]string{}
names[0] = "John"
```

* 0 by default

### Array Types
* The length is actually part of the type that is defined for arrays.
* An array of size 2 cannot be assigned to an array of size 3

### Matrix
```go
type Matrix [3][3]int

m := Matrix{
    {0, 0, 0},
    {1, 1, 1},
    {2, 2, 3},
  }
```

### Loops
```go
  names := [4]string{"John", "Paul", "George", "Ringo"}

  for i := 0; i < len(names); i++ {
    fmt.Println(names[i])
  }
```

Loop forever, continue or break at a condition
```go
for {
  if i == 3 {
    // go to the start of the loop
    continue
  }

  if i == 4 {
    break
  }
}
```

Range, much more useful than for loop
```go
func main() {
  names := [4]string{"John", "Paul", "George", "Ringo"}

  for i, n := range names {
    fmt.Printf("%d - %s\n", i, n)
  }
```


### Slices
* A window into that array
* Pointers, very efficient
* Similar to arrays but dynamically sized. Fixedtyped but flexible, the language still needs arrays but I tend to use slices.

```go
nameSlice :=[]string{'foo','bar'}
```

#### Slice internals
Think of slice as having three members
* length `len(nameSlice)`
* capacity `cap(nameSlice)`
* pointer to the underlying array, nicer pointers

Make
```go
anotherSlice := make([]string, 1) //specify length of 1
yetAnotherSlice := make([]string, 1, 3) //specify length of 1 and capacity of 3
```

If you know how big the slice will grow to be, you can allocate extra capacity to save some memory, but it can be tempting to optimize too early because the bottleneck are often I/O or waiting for user input

Append
```go
append(nameSlice, 'baz')

...
// append entire other slice
append(nameSlice, anotherSlice...)
```

Should the slice not have enough space Go will automatically reallocate the slice to have more capacity, growth factor is always 2, the old one is garbage collected.

#### 2D slices
```go
// a slice of byte slices
type Modules [][]byte //note instead of int/string like a matrix, you have a byte

course := Modules{
  []byte("Chapter One: Syntax"),
  []byte("Chapter Two: Arrays and Slices"),
  []byte("Chapter Three: Maps"),
}
```

#### Mutate vs. Copy
Danger, changing a slice subset will mutate the original slice, because the underlying arrays are mutated. This might lead to be unexpected behavior or bugs.
```go
names := []string{"John", "Paul", "George", "Ringo"}

fmt.Println(names) // [John Paul George Ringo]

guitars := names[:3]

fmt.Println(guitars) // [John Paul George]

for i, g := range guitars {
  guitars[i] = strings.ToUpper(g)
}

fmt.Println(names) // [JOHN PAUL GEORGE Ringo]
````

Use copy
```go
  veggies := []string{"carrot", "potato", "cucumber", "onion"}
  v := veggies

  v2 := make([]string, len(veggies))
  copy(v2, veggies)
```


