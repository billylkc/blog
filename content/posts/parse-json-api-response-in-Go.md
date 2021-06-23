+++
title = "Parse JSON API response in Go"
author = ["Billy Lam"]
date = 2021-02-08
lastmod = 2021-06-22
tags = ["golang", "api"]
categories = ["golang"]
draft = false
weight = 80
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/parse-json.jpg"
[menu.main]
  weight = 2003
  identifier = "parse-json-api-response-in-go"
+++

Parse HTTP API response easily

<!--more-->


## Introduction {#introduction}

When you are hosting your model result as a micro-services, or scrapping data from websites, you will often find yourself working on some Restful API JSON object. In this post, I will show you how easily we can work with JSON data in Go.


## Overview {#overview}

1.  Get JSON response from sample API hosting site - reqres
2.  Generate go struct from the response - json-to-go
3.  Unmarshal JSON response to go struct
4.  Loop through the struct and print data from the result


## Get Requests {#get-requests}

```go
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
)

func main() {
    resp, err := http.Get("https://reqres.in/api/users?page=2")
    if err != nil {
	    fmt.Println("No response from request")
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body) // response body is []byte
    fmt.Println(string(body))              // convert to string before print
}
```


## Result JSON {#result-json}

```json
{"page":2,"per_page":6,"total":12,"total_pages":2,"data":[{"id":7,"email":"michael.lawson@reqres.in","first_name":"Michael","last_name":"Lawson","avatar":"https://reqres.in/img/faces/7-image.jpg"},{"id":8,"email":"lindsay.ferguson@reqres.in","first_name":"Lindsay","last_name":"Ferguson","avatar":"https://reqres.in/img/faces/8-image.jpg"},{"id":9,"email":"tobias.funke@reqres.in","first_name":"Tobias","last_name":"Funke","avatar":"https://reqres.in/img/faces/9-image.jpg"},{"id":10,"email":"byron.fields@reqres.in","first_name":"Byron","last_name":"Fields","avatar":"https://reqres.in/img/faces/10-image.jpg"},{"id":11,"email":"george.edwards@reqres.in","first_name":"George","last_name":"Edwards","avatar":"https://reqres.in/img/faces/11-image.jpg"},{"id":12,"email":"rachel.howell@reqres.in","first_name":"Rachel","last_name":"Howell","avatar":"https://reqres.in/img/faces/12-image.jpg"}],"support":{"url":"https://reqres.in/#support-heading","text":"To keep ReqRes free, contributions towards server costs are appreciated!"}}
```


## Convert JSON response {#convert-json-response}

You can go to this site - [JSON to go](https://mholt.github.io/json-to-go/) to convert JSON response to Go struct very easily.

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/2-parse-json-json-to-go.png" >}}


## Unmarshal JSON to Go struct {#unmarshal-json-to-go-struct}

You can then unmarshal the `[]byte` from the GET response to the `Response` struct that we just auto-generated

```go
// Generated go struct
type Response struct {
    Page       int `json:"page"`
    PerPage    int `json:"per_page"`
    Total      int `json:"total"`
    TotalPages int `json:"total_pages"`
    Data       []struct {
	    ID        int    `json:"id"`
	    Email     string `json:"email"`
	    FirstName string `json:"first_name"`
	    LastName  string `json:"last_name"`
	    Avatar    string `json:"avatar"`
    } `json:"data"`
    Support struct {
	    URL  string `json:"url"`
	    Text string `json:"text"`
    } `json:"support"`
}

// snippet only
var result Response
if err := json.Unmarshal(body, &result); err != nil {   // Parse []byte to go struct pointer
	fmt.Println("Can not unmarshal JSON")
}
fmt.Println(PrettyPrint(result))
```

Response struct preview (partial)

```go
{
    "page": 2,
    "per_page": 6,
    "total": 12,
    "total_pages": 2,
    "data": [
	{
	    "id": 7,
		"email": "michael.lawson@reqres.in",
		"first_name": "Michael",
		"last_name": "Lawson",
		"avatar": "https://reqres.in/img/faces/7-image.jpg"
	},
	    {
	    "id": 8,
		"email": "lindsay.ferguson@reqres.in",
		"first_name": "Lindsay",
		"last_name": "Ferguson",
		"avatar": "https://reqres.in/img/faces/8-image.jpg"
	}
    ]
}
```

Finally loop through the data node and print out the FirstName of the result

```go
// Loop through the data node for the FirstName
for _, rec := range result.Data {
    fmt.Println(rec.FirstName)
}
```


## Complete code {#complete-code}

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

type Response struct {
    Page       int `json:"page"`
    PerPage    int `json:"per_page"`
    Total      int `json:"total"`
    TotalPages int `json:"total_pages"`
    Data       []struct {
	    ID        int    `json:"id"`
	    Email     string `json:"email"`
	    FirstName string `json:"first_name"`
	    LastName  string `json:"last_name"`
	    Avatar    string `json:"avatar"`
    } `json:"data"`
    Support struct {
	   URL  string `json:"url"`
	   Text string `json:"text"`
    } `json:"support"`
}

func main() {

    // Get request
    resp, err := http.Get("https://reqres.in/api/users?page=2")
    if err != nil {
	    fmt.Println("No response from request")
    }
    defer resp.Body.Close()
    body, err := ioutil.ReadAll(resp.Body) // response body is []byte

    var result Response
    if err := json.Unmarshal(body, &result); err != nil {  // Parse []byte to the go struct pointer
	    fmt.Println("Can not unmarshal JSON")
    }

    // fmt.Println(PrettyPrint(result))

    // Loop through the data node for the FirstName
    for _, rec := range result.Data {
	    fmt.Println(rec.FirstName)
    }
}

// PrettyPrint to print struct in a readable way
func PrettyPrint(i interface{}) string {
    s, _ := json.MarshalIndent(i, "", "\t")
    return string(s)
}
```

You can find the complete working example in - [Here](https://github.com/billylkc/blogposts/blob/1%5Fjson%5Fapi/main.go)


## Demo {#demo}

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/2-parse-json-demo.gif" >}}

Reference Photo by [Goran Ivos](<https://unsplash.com/@goran%5Fivos?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>) on [Unsplash](<https://unsplash.com/s/photos/programming?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>)
