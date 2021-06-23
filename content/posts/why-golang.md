+++
title = "Why Golang?"
author = ["Billy Lam"]
date = 2021-02-03
lastmod = 2021-06-22
tags = ["golang"]
categories = ["golang"]
draft = false
weight = 60
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go.png"
[menu.main]
  weight = 2004
  identifier = "why-golang"
+++

You can find a lot of reasons why people are using Golang. And here is my list.

<!--more-->


## Creators {#creators}

> Imagine you are having not one, but a couple of yodas in computer science to work on the same language.

-   ****Robert Griesemer**** - V8 JavaScript engine, Java HotSpot virtual machine
-   ****Rob Commander Pike**** - Co-creator of UTF-8, Plan 9 from Bell Labs
-   ****Ken Thompson**** - Designed original Unix system, Invented direct predecessor to C language, Co-creator of UTF-8

I am not saying nothing could go wrong with this team, but this is good enough for me to check the language out.


## Purpose of the Project {#purpose-of-the-project}

> Go was designed to address the problems faced in large scale software development, with concurrency
> and maintainability built in its core.

When Go launched, some claimed it was missing particular features or methodologies that were regarded as de rigueur for a modern language. <br />

How could Go be worthwhile in the absence of these facilities? Our answer to that is that the properties Go does have address the issues that make large-scale software development difficult. These issues include: <br />

-   slow builds
-   uncontrolled dependencies
-   each programmer using a different subset of the language
-   poor program understanding (code hard to read, poorly documented, and so on)
-   duplicate of effort
-   cost of updates
-   version skew
-   difficulty of writing automatic tools
-   cross-language builds

Individual features of a language don't address these issues. A larger view of software engineering is required, and in the design of Go we tried to focus on solutions to these problems.

Reference: [Go Frequently Asked Questions](https://golang.org/doc/faq#What%5Fis%5Fthe%5Fpurpose%5Fof%5Fthe%5Fproject)


## Gopher {#gopher}

> Gopher is the best representation of the language imo.
> It's simple, it's fun. It helps me solve complex problems with simple solutions.

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-gopher.jpg" width="250px" >}}


## Developer Productivity {#developer-productivity}

> Having the expressiveness of dynamic languages, with the performance of compiled languages.

Golang is fast. And it is not just about the performance and compilation time. It also provides all kinds of features and tools for the developers to be more productive.

Here are some reasons that I think Golang stands out in terms of developers' productivity.


### a) Public vs Private function {#a-public-vs-private-function}

Does it bothers you when you first started learning Java with public static void without really knowing what it means?

Instead of using a `public/private` keyword, Go uses initial letter of the identifier to determine the visibility.

-   ****upper case initial letter****: Name is visible to clients of package
-   ****otherwise****: name (or \_Name) is not visible to clients of package

<!--listend-->

```go
package hello

import "fmt"

// hello as some private function
// Not visible outside of the package
func hello() {
    fmt.Println("I am a private function")
}

// SayHello is a public function
// As the func name is starting with Capital letter
func SayHello() {
    fmt.Println("I am a public function")
}
```


### b) Documentation {#b-documentation}

You can generate a package documentation in the command line with `go doc --all`

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-go-doc.png" >}}

or html format with `godoc -http=localhost:7000`

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-go-doc-2.png" >}}


### c) go fmt {#c-go-fmt}

> Gofmt's style is no one's favorite, yet gofmt is everyone's favorite.
>
> -Rob Pike

With Go, it takes an unusual approach and let the machine take care of the most controversial formatting issues.

Less time can be spent on the discussion on whether 2 spaces or 4 spaces or tab or vertical comments alignment should be used. It also makes you feel 'consistent' to read code written by others.

<span class="underline">Auto formatting on save with go fmt</span>
![](https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-go-fmt.gif)


### d) Compiled Language {#d-compiled-language}

Because Go compiles so fast, it gives you the benefit of a compiled language (type checking, efficiency, etc..) and makes you feel like writing in an interpreted language as well.

Here is an example of how you can be benefit from a compiler (Easier to detect error and restructure code, etc..).

Function Signature checking
![](https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-compile.gif)


## Companies using Golang {#companies-using-golang}

> Why Golang? It is battle tested.

Many big companies are using Go programming language in various projects and services due to its scalability, superior error check, and concurrency. Here are some big companies which are using Golang:

-   Google
-   Uber
-   Netflix
-   Alibaba
-   Twitch
-   Dropbox
-   Medium
-   SoundCloud

Reference: [Companies using Go](https://github.com/golang/go/wiki/GoUsers)


## Developer Survey {#developer-survey}

In February 2020 StackOverflow conducted a survey with nearly 65,000 developers on how they learn and level up, which tools they’re using, and what they want. Here are some take aways on Golang.

-   In terms of Median Salary versus Experience. Golang programers are doing AOkay. (Possible bias of the big pay-roll in larger companies though)

    ![](https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-survey-1.png)
    {Remarks: The survey was taken in February 2020, before COVID-19 was declared a pandemic by the World Health Organization and before the virus impacted every country in the world.}

-   For the most loved language, we see a big gain in Go, moving up to 5th (2020) from 10th (2019).
    ![](https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-survey-2.png)

<!--listend-->

-   And for the most dreaded language, Go is amongst the last few, which is always a good sign.
    ![](https://storage.googleapis.com/billylkc-blog-image/images/posts/why-go-survey-3.png)

Reference: [2020 Stack Overflow Developer Survey](https://insights.stackoverflow.com/survey/2020)


## What about Data science {#what-about-data-science}

> Nothing. Who says everything is about DS.

Just kidding 🙂. The truth is data science will not be the main focus of Go in near future. It is being more widely used in areas like, DevOps, Cloud architecture, web development, API/RPC services, etc..

I do find Go very easy to use for data scraping and hosting API.


## Final Thoughts {#final-thoughts}

Some final thoughts
