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


## Usage {#usage}

Basically, what you have to do is 3 things.

-   Study the ****static page**** (Not dynamically loaded content with javascript) and identify the elements with css selector
-   Construct a golang struct with the goquery tag (e.g. `` `goquery:"div p`" ``)
-   Decode into a struct with `goq.NewDecorder`


## Static site crawling {#static-site-crawling}

Here I will be using a scraping friendly website ([example.com](http://example.com/)), and capture the header `h1` and the following paragraph `div p`.

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


## Demo {#demo}

(To be added)


## Final thoughts {#final-thoughts}

I have used many different packages in Python and Go, this is by far the easiest and most intuitive package for simple static site crawling.

Happy Coding.

<span class="underline">Reference</span>

-   [andrewstuart/goq](https://github.com/andrewstuart/goq)
-   Photo by [Jean Carcallas](<https://unsplash.com/@jean8carcallas?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>) on [Unsplash](<https://unsplash.com/s/photos/capture?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>)
