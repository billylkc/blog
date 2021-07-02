+++
title = "Getting HKEX data with Quandl in Python"
author = ["Billy Lam"]
date = 2021-06-21
lastmod = 2021-06-29
tags = ["python", "api"]
categories = ["python"]
draft = false
weight = 30
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/11-quandl/thumbnails.jpg"
[menu.main]
  weight = 2003
  identifier = "getting-hkex-data-with-quandl-in-python"
+++

Free alternatives to get HKEX daily stock data after Yahoo Finance

<!--more-->


## Introduction {#introduction}

Free Quandl Stock API for HK stock data.

Getting a stable source of daily stock data is all I needed. And since Yahoo Finance shutdown their API, I have been looking for a free alternative data source. In this post, I will quickly show you how we can get some daily stock price data from HK Stock Exchange Market with the [Quandl](https://www.quandl.com/) Python API.


## Overview {#overview}

In this article, we will be covering the following topics

-   Introduction to Quandl Python API

-   Basic setup and quick exploration

-   Getting 10 days of records for all stocks


## Quandl Python API {#quandl-python-api}

****Quandl**** is a marketplace for financial, economic and alternative data delivered in modern formats for today's financial analysts. It provides free API (Python, Excel, Matlab, R, etc..) for registered users. You can register and get a ****free API key**** - [Here](https://www.quandl.com/sign-up). The setup should be quite straight forward.

Here is a usage table of the free tier for your reference. We will be using the free tier as an example

<div class="table-caption">
  <span class="table-number">Table 1</span>:
  Rate Limits
</div>

| Tier    | Requests per Day  | Concurrent Request |
|---------|-------------------|--------------------|
| Free    | 50,000 calls/day  | 1                  |
| Premium | 720,000 calls/day | -                  |

Reference: [Quandl Pricing](https://help.quandl.com/article/132-how-much-does-quandl-data-cost) and [Usage rate](https://docs.quandl.com/docs)


## Basic setup and quick exploration {#basic-setup-and-quick-exploration}


### a) Install package {#a-install-package}

```text
pip install quandl
```


### b) Set up environment variable {#b-set-up-environment-variable}

Given that the free API key is not a 'secret' secret. Putting the token in an environment variable would serve the purpose for this demo.

-   Open your `~/.bashrc` and add the following line ****(Persistent)**** <br />

In `~/.bashrc`

```bash
export QUANDL_TOKEN="YOUR_API_TOKEN"
```

In command line

```bash
source ~/.bashrc
```

-   Or simply run in the command line ****(One off)****

<!--listend-->

```bash
export QUANDL_TOKEN="YOUR_API_TOKEN"
```


### c) Quick demo {#c-quick-demo}

It is quite straight forward to get the data, just call it with the ****stock code**** `HKEX/00005` and `quandl.get` function.

```python
import os
import requests
import pandas as pd
import quandl
import numpy as np

pd.set_option('display.max_columns', None)
quandl.ApiConfig.api_key = os.environ['QUANDL_TOKEN']

num = 5     # HSBC
code = str(num).zfill(5)
code_str = "HKEX/{}".format(code)
data = quandl.get(code_str, rows = 10)
data['Code'] = code

print(data)
```


## Getting data {#getting-data}

Let's extend our example and try to get ****all the stocks**** in the past 10 days.


### a) Get a list of all codes {#a-get-a-list-of-all-codes}

As the list of all listed companies are likely to change, let's get the latest one from the HKEX page - [Here](https://www.hkexnews.hk/sdw/search/stocklist%5Fc.aspx?sortby=stockcode&shareholdingdate=20210621).

We are going to the page, get the text from the cells of the table, then use regular expression to capture the stock code with 5 digits value (e.g. 00005). I will leave the introduction to BeautifulSoup for another post 😁.

```python
from bs4 import BeautifulSoup
from datetime import datetime
from typing import List
import requests
import re
import pandas as pd
import numpy as np
import quandl


def get_codes() -> List[int]:

    """
    Get all the codes from the listed companies in HK main board from HKEX page

    Args:
	    None

    Returns:
	    codes ([]int): List of codes in HKEX main board

    Example:
	    codes = get_codes()

    Data preview:
	    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ..]
    """

    regex = re.compile(r"\s*(\d{5})(.*)")  # Get 5 digit codes only
    url = "https://www.hkexnews.hk/sdw/search/stocklist_c.aspx?sortby=stockcode&shareholdingdate={}".format(
	    datetime.today().strftime("%Y%m%d")
    ) # derive url, e.g. https://www.hkexnews.hk/sdw/search/stocklist_c.aspx?sortby=stockcode&shareholdingdate=20210621

    res = requests.get(url)
    soup = BeautifulSoup(res.text, "html.parser")

    codes = []
    for s in soup.select("table.table > tbody > tr"):
	    text = s.get_text().replace(" ", "").strip()  # Replace extra spaces
	    matchResult = regex.search(text)

	    if matchResult:
		    code = int(matchResult.group(1).lstrip("0"))  # Convert to int, e.g. 00005 to 5

		    if code <= 10000:  # main board only
			    codes.append(code)

    return codes
```


### b) Get individual stock (First try) {#b-get-individual-stock--first-try}

Here we are having a function with input as stock code (int) and return a dataframe of the historical records.

****Remarks****: This implementation is ****rate limited**** with the package function `quandl.get` as free account.

```python
import pandas as pd
import numpy as np
import quandl

pd.set_option("display.max_columns", None)
quandl.ApiConfig.api_key = os.environ["QUANDL_TOKEN"]

def get_stock(num: int, nrow: int = 10) -> pd.DataFrame:

    """
    Call Quandl API to get the historical data for the stock number (Rate limited)
    """

    code = str(num).zfill(5)
    code_str = "HKEX/{}".format(code)  # e.g. HKEX/00005

    try:
	    data = quandl.get(code_str, rows = nrow)
	    data['Code'] = code

	    col_name = data.columns.tolist()
	    clean_col_name = [re.sub(r'\W+', '', x) for x in col_name]  # Replace special character in column name
	    col_dict = dict(zip(col_name, clean_col_name))

	    data.rename(columns=col_dict, inplace=True)
	    print("Finished getting code - {}".format(code))

	    return(data)

    except Exception as e:
	    print("No records - {}".format(code))
	    print(e)
```


### c) Get individual stock (Second Try) {#c-get-individual-stock--second-try}

Turns out there is no rate limits for the GET requests. Let's do it again with `requests.get` and `io.StringIO`.

```python
from datetime import datetime
import pandas as pd
import numpy as np
import requests
import io
import os

quandl.ApiConfig.api_key = os.environ["QUANDL_TOKEN"]

def get_stock(num: int, nrow: int = 10) -> pd.DataFrame:

    """
    Call Quandl API to get the historical data for the stock number using GET requests

    Args:
	    num (int): Stock num, e.g. 5
	    nrow (int): No of rows specified in the API calls. Default 10

    Returns:
	    data (Dataframe): Dataframe returned from Quandl API

    Example:
	    data = get_stock(num=1, nrow=10)

    TODO:
	    Add date parameter to specify the latest date of the call

    Data preview:
				  NominalPrice NetChange Change    Bid    Ask   PEx   High    Low  PreviousClose  ShareVolume000  Turnover000 LotSize   code
      Date
      2019-03-19         80.45      None   None  80.40  80.45  None  81.15  80.20          80.95          7374.0     593781.0    None  00001
      2019-03-20         82.50      None   None  82.50  82.55  None  83.30  80.30          80.45         12420.0    1018144.0    None  00001
      2019-03-21         81.60      None   None  81.60  81.75  None  83.50  81.60          82.50         12224.0    1009254.0    None  00001
      2019-03-22         83.80      None   None  83.75  83.80  None  84.65  82.85          81.60         13478.0    1124179.0    None  00001
    """
    today = datetime.today().strftime("%Y-%m-%d")  # e.g. 2021-06-23
    code = str(num).zfill(5)
    code_str = "HKEX/{}".format(code)  # e.g. HKEX/00005

    # Get from csv
    endpoint = "https://www.quandl.com/api/v3/datasets/{}/data.csv?limit={}&end_date={}&order={}&api_key={}".format(
	    code_str,
	    nrow,
	    today,
	    "desc",
	    quandl.ApiConfig.api_key,
    )
    r = requests.get(endpoint).content
    data = pd.read_csv(io.StringIO(r.decode("utf-8")))

    data["Code"] = code

    # Check if there is any error message
    col_name = data.columns.tolist()
    if "message" in col_name:
	    raise Exception("Incorrect stock code - {}".format(code))

    clean_col_name = [re.sub(r"\W+", "", x) for x in col_name]  # Replace special character in column name
    col_dict = dict(zip(col_name, clean_col_name))

    data.rename(columns=col_dict, inplace=True)
    print("Finished getting code - {}".format(code))

    return data
```


### d) Get all stocks {#d-get-all-stocks}

We finally loop through all the codes and concat the results to a single dataframe.

```python
def get_all_stock(nrow: int = 10) -> pd.DataFrame:
    """ Loop through the list of codes, and concat the results to a single dataframe. """
    codes = get_codes()
    codes = codes[0:10] # Hardcorded 20 stocks for demostration.

    # Initialize result dataframe
    result = pd.DataFrame()
    for code in codes:
	    try:
		    data = get_stock(code, nrow)
		    result = pd.concat([result, data], sort=True)

	    except Exception as e:
		    print("No records")
		    print(e)

    return result
```


### e) Complete example {#e-complete-example}

```python
from bs4 import BeautifulSoup
from datetime import datetime
from typing import List
import requests
import re
import os
import io
import pandas as pd
import numpy as np
import quandl

pd.set_option("display.max_columns", None)
quandl.ApiConfig.api_key = os.environ["QUANDL_TOKEN"]


def get_codes() -> List[int]:

    """
    Get all the codes from the listed companies in HK main board from HKEX page

    Args:
	    None

    Returns:
	    codes ([]int): List of codes in HKEX main board

    Example:
	    codes = get_codes()

    Data preview:
	    [1, 2, 3, 4, 5, 6, 11,..]
    """

    regex = re.compile(r"\s*(\d{5})(.*)")  # Get 5 digit codes only
    url = "https://www.hkexnews.hk/sdw/search/stocklist_c.aspx?sortby=stockcode&shareholdingdate={}".format(
	    datetime.today().strftime("%Y%m%d")
    )  # derive url, e.g. https://www.hkexnews.hk/sdw/search/stocklist_c.aspx?sortby=stockcode&shareholdingdate=20210621

    res = requests.get(url)
    soup = BeautifulSoup(res.text, "html.parser")

    codes = []
    for s in soup.select("table.table > tbody > tr"):
	    text = s.get_text().replace(" ", "").strip()  # Replace extra spaces
	    matchResult = regex.search(text)

	    if matchResult:
		    code = int(matchResult.group(1).lstrip("0"))  # Convert to int, e.g. 00005 to 5

		    if code <= 10000:  # main board only
				codes.append(code)

    return codes


def get_stock(num: int, nrow: int = 10) -> pd.DataFrame:

    """
    Call Quandl API to get the historical data for the stock number using GET requests

    Args:
       num (int): Stock num, e.g. 5
       nrow (int): No of rows specified in the API calls. Default 10

    Returns:
       data (Dataframe): Dataframe returned from Quandl API

    Example:
       data = get_stock(num=1, nrow=10)

    TODO:
       Add date parameter to specify the latest date of the call

    Data preview:
				  NominalPrice NetChange Change    Bid    Ask   PEx   High    Low  PreviousClose  ShareVolume000  Turnover000 LotSize   code
      Date
      2019-03-19         80.45      None   None  80.40  80.45  None  81.15  80.20          80.95          7374.0     593781.0    None  00001
      2019-03-20         82.50      None   None  82.50  82.55  None  83.30  80.30          80.45         12420.0    1018144.0    None  00001
      2019-03-21         81.60      None   None  81.60  81.75  None  83.50  81.60          82.50         12224.0    1009254.0    None  00001
      2019-03-22         83.80      None   None  83.75  83.80  None  84.65  82.85          81.60         13478.0    1124179.0    None  00001
    """

    today = datetime.today().strftime("%Y-%m-%d")  # e.g. 2021-06-23
    code = str(num).zfill(5)
    code_str = "HKEX/{}".format(code)  # e.g. HKEX/00005

    # Get from csv
    endpoint = "https://www.quandl.com/api/v3/datasets/{}/data.csv?limit={}&end_date={}&order={}&api_key={}".format(
	    code_str,
	    nrow,
	    today,
	    "desc",
	    quandl.ApiConfig.api_key,
    )
    r = requests.get(endpoint).content
    data = pd.read_csv(io.StringIO(r.decode("utf-8")))

    data["Code"] = code

    # Check if there is any error message
    col_name = data.columns.tolist()
    if "message" in col_name:
	    raise Exception("Incorrect stock code - {}".format(code))

    clean_col_name = [re.sub(r"\W+", "", x) for x in col_name]  # Replace special character in column name
    col_dict = dict(zip(col_name, clean_col_name))

    data.rename(columns=col_dict, inplace=True)
    print("Finished getting code - {}".format(code))

    return data


def get_all_stock(nrow: int = 10) -> pd.DataFrame:

    """ Loop through the list of codes, and concat the results to a single dataframe. """

    codes = get_codes()
    codes = codes[0:20]  # Hardcorded 20 stocks for demostration.

    # Initialize result dataframe
    result = pd.DataFrame()

    for code in codes:
	    try:

		    data = get_stock(code, nrow)
		    result = pd.concat([result, data], sort=True)
		    print("=========================")
		    print(code)
		    print(data.head())

	    except Exception as e:
		    print("No records")
		    print(e)

    return result


def main():
    df = get_all_stock()
    print(df)


if __name__ == "__main__":
    main()
```

The complete code example can be found - [Here](https://github.com/billylkc/blogposts/blob/6%5Fquandl%5Fpy/main.py)

**Demo** <br />

{{< figure src="https://storage.googleapis.com/billylkc-blog-image/images/posts/11-quandl/demo-q.gif" >}}


## Final Thoughts {#final-thoughts}

With the example here, you should be able to get a daily update of HKEX stock data for analysis. It would be quite easy to save the data into a DBMS like mysql or postgresql too.

Happy Coding!

\_

Reference

-   Photo by [Jamie Street](<https://unsplash.com/@jamie452?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>) on [Unsplash](<https://unsplash.com/s/photos/stock?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>)
