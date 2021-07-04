+++
title = "Static site crawling with goq"
author = ["Billy Lam"]
date = 2021-02-28
lastmod = 2021-07-04
tags = ["golang", "crawler"]
categories = ["golang"]
draft = false
weight = 40
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/5-goq/thumbnails.jpg"
[menu.main]
  weight = 2002
  identifier = "static-site-crawling-with-goq"
+++

Easy static site crawling with goq

<!--more-->


## Introduction {#introduction}

> We get it. You want the data, not the headache.

A robust web scrapers helps you to extract data from websites, saving you time on some tedious data collection tasks. It also allows you to make smarter decisions with the insights discovered from the captured data.

Here are some of the common use cases with web crawlers.

-   Sentiment analysis on social media
-   Price tracking
-   Content Aggregation
-   Product data enrichment
-   Brand Monitoring
-   etc..

In this article we will introduce a package in Go (`goq`) to crawl some static websites.


## Usage {#usage}

Basically, what you have to do is 3 things.

-   Study the ****static page**** (Not dynamic content loaded with Javascript) and identify the elements with css selector
-   Construct a golang `struct` with the goquery tag (e.g. `` `goquery:"div p`" ``)
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


## Other Demo {#other-demo}

The following demos are for illustration purposes. Do check the `robots.txt` file of each site and crawl responsibly.


### a) Wikipedia {#a-wikipedia}

Let say we want to create a program that retrieves the first 3 paragraphs of the keyword (e.g. Linear regression) that we provide. Here are some key steps.

1.  Study the ****css selector**** of the info that we need

    Right Click on the paragraph -> Inspect -> Properties -> css selector
    ![](https://storage.googleapis.com/billylkc-blog-image/images/posts/5-goq/css-selector.png)

2.  Create a ****struct**** with the ****goquery tag****

    We can easily check that the header is `h1` and detailed descriptions `div.mw-parser-output p` and it should return a list of strings

    ```go
    type Wiki struct {
        Title   string   `goquery:"h1"`
        Content []string `goquery:"div.mw-parser-output p"`
    }
    ```

3.  ****Decode**** into Go struct with the GET response

    ```go
    // wiki gets the wikipedia link
    func wiki(keyword string) Wiki {

        // construct wiki links
        link := fmt.Sprintf("https://en.wikipedia.org/wiki/%s", keyword)
        res, err := http.Get(link)
        if err != nil {
    	    fmt.Println(err)
        }
        defer res.Body.Close()

        var wiki Wiki
        err = goq.NewDecoder(res.Body).Decode(&wiki)
        if err != nil {
    	    fmt.Println(err)
        }

        return wiki
    }
    ```

4.  Print the output

    ```go
     package main

     import (
         "encoding/json"
         "fmt"
         "log"
         "net/http"

         "astuart.co/goq"
     )

     func main() {
         kw := "Logistic Regression"
         w := wiki(kw)

         if len(w.Content) >= 3 {
    	     w.Content = w.Content[0:3]
         }
         fmt.Println(w)
    }
    ```

5.  Demo

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/5-goq/goq-wiki-o.gif" >}}

Full Example

You can find out the full code example - [Here](https://github.com/billylkc/blogposts/blob/4%5Fgoq%5Fwiki/main.go)


### b) OpenRice {#b-openrice}

And for OpenRice. You can easily check that the css selector for title would be `div.cms-detail-title.or-font-family` and content as `div.cms-detail-body-text div`.

Full code

```go
package main

import (
    "encoding/json"
    "fmt"
    "net/http"

    "astuart.co/goq"
)
// Article as a struct to parse Openrice Article
type Article struct {
    Title   string   `goquery:"div.cms-detail-title.or-font-family"`
    Content []string `goquery:"div.cms-detail-body-text div"`
}


func main() {
	// Random promotion link from the front page. May be going to be expired
	link := "https://www.openrice.com/zh/hongkong/promo/%E3%80%90%E7%B5%82%E6%96%BC%E7%B4%84friend%E9%A3%9F%E9%A3%AF%E3%80%91%E5%B0%96%E6%B2%99%E5%92%80%E4%B8%BB%E6%89%93%E9%A3%B2%E9%85%92%E6%B5%B7%E9%AE%AE-outdoor%E4%BD%8D%E6%9C%89%E6%B0%A3%E6%B0%9B-a5816"
	result := getArticle(link)

	fmt.Println(PrettyPrint(result))
}

// PrettyPrint to print struct in a readable way
func PrettyPrint(i interface{}) string {
	s, _ := json.MarshalIndent(i, "", "\t")
	return string(s)
}

// getArticle parses the input link and extract the content of the article
// We are passing the link as argument this time
func getArticle(link string) Article {
	res, err := http.Get(link)
	if err != nil {
		fmt.Println(err)
	}
	defer res.Body.Close()

	var rice Article
	err = goq.NewDecoder(res.Body).Decode(&rice)
	if err != nil {
		fmt.Println(err)
	}

	return rice
}
```

Result (Partial)

```json
{
	"Title": "【終於約Friend食飯】尖沙咀主打飲酒海鮮　Outdoor位有氣氛",
	"Content": [
		"好耐無同朋友夜晚食飯傾計喇！久違咁耐嘅晚餐，梗係想搵個舒服地方飲吓酒、食好嘢啦，尖沙咀呢間樓上餐廳Poseidon 就有個戶外大平台，可以Chill 住睇夜景食飯；室內亦有舒服卡位、高凳位、煙霧特效座位。",
		"",
		"餐廳有駐場調酒師，多款啤酒、紅白酒、雞尾酒可以飲成晚。最正係大廚會定期引入Seasonal 靚食材推新菜式，記住飲酒都要留肚食好嘢呀。",
		"",
		"同場有卡位、高凳同戶外位揀。",
		"",
		"",
		"煙霧特效座位好有氣氛。",
		"",
		"人氣招牌菜　海鮮拼盤",
		"呢度一向主打靚食材，食靚嘢點少得海鮮，所以呢個「海鮮拼盤」一直好有人氣！今次新Menu 繼續有新鮮即開生蠔、雞尾凍蝦、翡翠螺、青口、蝦，新引入超鮮甜花甲，任沾泰式辣醬、紅酒醋、雞尾酒醬3款醬汁，有2人份或4人份揀。",
		"",
		"想食得盡興啲，都可以追加特級鱘魚魚子醬、波士頓龍蝦，會同其他海鮮一齊放喺貝殼大碟上枱，影相一流。",
		"",
		"貝殼大碟上枱超靚。"
      ]
}
```

Here is the source code - [Here](https://github.com/billylkc/blogposts/blob/5%5Fgoq%5Fopenrice/main.go)


## Final thoughts {#final-thoughts}

I have used many packages in Python and Go, this is by far the easiest package for static site crawling. All you have to do is to define a goquery tag in the struct, then Voila, you get your content.

Happy Coding!

\_

Reference

-   [andrewstuart/goq](https://github.com/andrewstuart/goq)
-   Photo by [Jean Carcallas](<https://unsplash.com/@jean8carcallas?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>) on [Unsplash](<https://unsplash.com/s/photos/capture?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>)
