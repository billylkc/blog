+++
title = "Static site crawling with goq"
author = ["Billy Lam"]
date = 2021-02-28
lastmod = 2021-06-15
tags = ["golang", "crawler"]
categories = ["golang"]
draft = false
weight = 2001
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/5-goq/thumbnails.jpg"
[menu.main]
  weight = 2001
  identifier = "static-site-crawling-with-goq"
+++

Easy static site crawling with goq

<!--more-->


## Static site crawling {#static-site-crawling}

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"

    "astuart.co/goq"
)

// Example as a struct to capture data from example.com
type Example struct {
    Title     string `goquery:"h1"`
    Paragraph string `goquery:"div p"`
}

func main() {
    res, err := http.Get("https://example.com/")
    if err != nil {
	log.Fatal(err)
    }
    defer res.Body.Close()

    var example Example

    err = goq.NewDecoder(res.Body).Decode(&example)
    if err != nil {
	log.Fatal(err)
    }

    fmt.Println(PrettyPrint(example))
}

// PrettyPrint to print struct in a readable way
func PrettyPrint(i interface{}) string {
    s, _ := json.MarshalIndent(i, "", "\t")
    return string(s)
}
```


## Result {#result}

```go
{
    "Title": "Example Domain",
    "Paragraph": "This domain is for use in illustrative examples in documents. You may use this domain in literature without prior coordination or asking for permission.More information..."
}
```


## Final thoughts {#final-thoughts}

I have used many different packages in Python and Go, this is by far the easiest and most intuitive package for simple static site crawling.
