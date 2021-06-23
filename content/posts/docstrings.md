+++
title = "What is docstrings?"
author = ["Billy Lam"]
date = 2021-06-19
lastmod = 2021-06-22
tags = ["python"]
categories = ["python"]
draft = false
weight = 40
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/10-docstrings/thumbnails.jpg"
[menu.main]
  weight = 2004
  identifier = "what-is-docstrings"
+++

Code is more often read than written.

<!--more-->


## Introduction {#introduction}

Learn to write a good function documentation.

Even if you don’t intend anybody else to read your code, there’s still a very good chance that somebody will have to stare at your code and figure out what it does. That person is probably going to be you too, twelve months from now. So be kind to yourself, and start writing some good code and documentation.

Docstrings are the string literals that appear right after the definition of a function, method, class, or module. To me, it is like ****a summary of what a function does****. Sometimes I just dont care what the actual implementation is (At first at least 🙂), just tell me what the input, output, and a short descriptin of what it does, before even asking me to have a look at your code.

Let's have a look at the examples in different languages.


## Python Docstrings {#python-docstrings}


### a) One-liner docstring {#a-one-liner-docstring}

Use short one-liner description for functions that you think is intuitive.
But always always write a docstings for your functions.

```python
def square(n):
    """ Takes an integer and return a square of it """
    return n**2
```


### b) Detailed docstring {#b-detailed-docstring}

Make sure to make it clear that what is the ****input**** and ****output**** of your function, and more importantly is to include the ****type**** as well.
I usually include the followings.

-   Description
-   Arguments with types
-   Return value
-   (Optional) Detailed steps
-   (Optional) Example and output

<!--listend-->

```python
from datetime import datetime
from dateutil.rrule import rrule, MONTHLY
from typing import List

def get_months_between_dates(start: str, end:str) -> List:

    """
    Return a list of months between two dates in YYYYMM format.
    Use to convert from some start end date to a list of months

    Args:
	     start (str): Start date in YYYYMM format
	     end (str): End date in YYYYMM format

    Returns:
	     month_list ([]str): A list of month between the two months input (Inclusive)

    Example:
	     month_list = get_months_between_dates('201802', '201902')

    Example output:
	     ['201802', '201803', '201804', '201805', '201806', '201807', '201808', '201809', '201810', '201811', '201812', '201901', '201902']
    """

    start_dt = datetime.strptime(start, "%Y%m")
    end_dt   = datetime.strptime(end, "%Y%m")
    month_list = [dt.strftime("%Y%m") for dt in rrule(MONTHLY, dtstart=start_dt, until=end_dt)]
    return month_list
```


## R Docstrings {#r-docstrings}

For R, I find that the standard documentation format is kinda hard to read (personal preference), so I follow the python docstring format as well.

One of the draw back would be you cant read the documentation from the `help` function natively supported by R. But it gives you a more consistent feel between Python and R projects.

```r
library(data.table)
round_dataframe <- function(df, digits = 2) {

  ## Round the numeric columns of the provided dataframe
  ##
  ## Args:
  ##  df (Dataframe): Dataframe to be rounded
  ##  digits (num): No of digits to be rounded for numeric columns
  ##
  ## Returns:
  ##  df (Dataframe): Dataframe with rounded numbers
  ##
  ## Example:
  ##  dt = as.data.table(iris)
  ##  dt = round_dataframe(df = dt, digits = 0)

  # Find numeric cols, round the columns with no of digits provided
  numeric.cols = colnames(Filter(is.numeric, df))
  df[, (numeric.cols) := round(.SD, digits), .SDcols = numeric.cols]

  return(df)
}
```

Reference: [R tidyverse style guide](https://style.tidyverse.org/%20)


## Go Docstrings {#go-docstrings}

For Go, you can easily tell from the function signature about the input, output and description of the function.
It also comes with some built-in support for documentation. You can easily generate the doc with `go doc --all` or `godoc -http=localhost:7000`.

```go

// Add simply adds the two integers together
func Add(x int, y int) int{
	return x + y
}
```


## Demo {#demo}

Writing docstrings might sound a lot of trouble at first. But it may not be as much as you think. Many modern IDE supports some code snippets for you to define a template for code generation.

Here is a quick demo on how I usually generate the docstrings with ****yasnippet****. I group all my snippets starting with the letter `s` for snippets.

-   `sifm` prints the `if __name__ == '__main__'`
-   `sfn` stands for snippet function, which generate a docstring snippet whenever I define a function.

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/10-docstrings/docstrings-o.gif" >}}


## Final Thoughts {#final-thoughts}

Hopefully, you start to see the benefits of writing a good function signature after this post. <br />
Happy Coding!

\_

Reference Photo by [Gustavo Fring](<https://www.pexels.com/@gustavo-fring?utm%5Fcontent=attributionCopyText&utm%5Fmedium=referral&utm%5Fsource=pexels>) on [Pexels](<https://www.pexels.com/photo/clever-little-student-writing-in-notebook-while-studying-at-home-3874375/?utm%5Fcontent=attributionCopyText&utm%5Fmedium=referral&utm%5Fsource=pexels>)
