---
layout: post
title: gothamgo 2018 workshop afternoon
date: '2018-04-26T09:15:00.001-07:00'
author: Feng Ting Zhang
tags:
modified_time: '2018-04-26T09:16:43.607-07:00'
---

# gothamgo 2018 workshop afternoon

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

```

### Thread Safety
* One of the awesome things you've heard of is that Go is multi-threaded
* Maps are not thread safe, since Go 1.9x use [sync.Map](https://golang.org/pkg/sync/#Map)


