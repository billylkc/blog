+++
title = "Functional Options in Go"
author = ["Billy Lam"]
date = 2021-02-22
lastmod = 2021-06-18
tags = ["golang"]
categories = ["golang"]
draft = false
weight = 2002
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/4-functional-options/thumbnails.jpg"
[menu.main]
  weight = 2002
  identifier = "functional-options-in-go"
+++

A nice way to set options in Go

<!--more-->


## Functional Option {#functional-option}

```go
package main

import (
    "encoding/json"
    "fmt"
    "time"
)

type Foo struct {
    Code      int
    Verbosity int
    Timeout   time.Duration // In nanoseconds
}

type option func(*Foo)

func main() {
    fmt.Println("Demo for option chain")
    foo := New(5)

    fmt.Println("Before")
    fmt.Println(PrettyPrint(foo))

    fmt.Println("After")
    foo.Option(Verbosity(-1))     // Set verbosity
    foo.Option(SetTimeout("10s")) // Set timeout to 10 sec

    fmt.Println(PrettyPrint(foo))
}

func New(code int) Foo {
    return Foo{
	Code: code,
    }
}

// Option sets the options specified.
func (f *Foo) Option(opts ...option) {
    for _, opt := range opts {
	opt(f)
    }
}

// Verbosity sets Foo's verbosity level to v.
func Verbosity(v int) option {
    return func(f *Foo) {
	f.Verbosity = v
    }
}

// SetTimeout sets timeout using ParseDuration
// input is like 5s, 1m, etc
func SetTimeout(t string) option {
    timeout, _ := time.ParseDuration(t)
    return func(f *Foo) {
	f.Timeout = timeout
    }
}

// PrettyPrint to print struct in a readable way
func PrettyPrint(i interface{}) string {
    s, _ := json.MarshalIndent(i, "", "\t")
    return string(s)
}
```


## Results {#results}

```go
// Demo for option chain

// Before
// {
//         "Code": 5,
//         "Verbosity": 0,
//         "Timeout": 0
// }
// After
// {
//         "Code": 5,
//         "Verbosity": -1,
//         "Timeout": 10000000000
// }
```

You can find the complete working example in - [Here](https://github.com/billylkc/blogposts/tree/3%5Fgo%5Ffunctional%5Foptions)

Reference: [Self-referential functions and the design of options](https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html)
