+++
title = "Getting HKEX data with Quandl in Python"
author = ["Billy Lam"]
date = 2021-06-21
lastmod = 2021-06-21
tags = ["python", "api"]
categories = ["python"]
draft = true
weight = 2001
nolastmod = true
[menu.main]
  weight = 2001
  identifier = "getting-hkex-data-with-quandl-in-python"
+++

Free alternatives to get HKEX daily stock data after Yahoo Finance

<!--more-->


## Introduction {#introduction}

Free Quandl Stock API for HK stock data.

Getting a stable source of daily stock data is all I wanted. But since Yahoo Finance shutdown their API, I have been using Quandl as an free alternatives. In this post, I will quickly show you how we can get some daily stock price data from HK Stock Exchange Market.


## Overview {#overview}

In this article, we will be covering the following topics

-   Introduction to Quandl Python API

-   Basic setup and quick exploration

-   Getting 10 days of records for all stocks

-   Set up a cron job to extract the data daily


## Quandl Python API {#quandl-python-api}

****Quandl**** is a marketplace for financial, economic and alternative data delivered in
modern formats for today's financial analysts It provides free API (Python, Excel, Matlab, R, etc..) for registered users.

You can register and get a free API key here - [Here]. The setup should be quite straight forward.

Here is a usage table of the free tier for your reference. We will be using the free tier as an example here.

| Tier | Requests per Day | Concurrent Request |
|------|------------------|--------------------|
| Free |                  | 1                  |


## Basic setup and quick exploration {#basic-setup-and-quick-exploration}


### a) Install package {#a-install-package}

```text
pip install quandl
```


### b) Set up environment variable {#b-set-up-environment-variable}

Given that the free API is not a 'secret' secret. Putting the token in an environment variable would serve the purpose of this short example.

-   Open your `~/.bashrc` and add the following line (Persistent)

    ```bash
    # Inside .bashrc
    export QUANDL_TOKEN="YOUR_API_TOKEN"
    ```

    ```bash
    # In command line
    source ~/.bashrc
    ```

<!--listend-->

-   Or simply run in the command line (One off)

    ```bash
    export QUANDL_TOKEN="YOUR_API_TOKEN"
    ```


### c) Quick demo {#c-quick-demo}

It is quite straight forward to get the data, just call it with the ****stock code**** `HKEX/00005` and `quandl.get`.

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
data['code'] = code

print(data)
```


## Getting data {#getting-data}

Let's extend our example and try to get ****all**** the stocks in the past 10 days.


### a) Get a list of all codes {#a-get-a-list-of-all-codes}

As the list of all listed companies are likely to change, let's get the latest list from the HKEX page.


### b) Rewrite get\_code function {#b-rewrite-get-code-function}

```python

```


### c) Accept date parameters {#c-accept-date-parameters}


### d) Complete example {#d-complete-example}

The complete code example can be founded - [[][Here]]


## Setting a cron job {#setting-a-cron-job}

\_
Reference

-   Photo by [Chris Liverani](<https://unsplash.com/@chrisliverani?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>) on [Unsplash](<https://unsplash.com/s/photos/stock?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent>=


## Final Thoughts {#final-thoughts}

With the example here, you should be able to get a daily udpate of HKEX stock data for analysis. It would be quite easy to save the data into a DBMS like mysql or postgresql too.

Happy Codding.

\_

Reference:

-   Reference Photo by []() on []()
