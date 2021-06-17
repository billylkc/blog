+++
title = "Python Cheatsheet"
author = ["Billy Lam"]
date = 2021-06-17
lastmod = 2021-06-17
tags = ["python", "workflow"]
categories = ["python"]
draft = false
weight = 2001
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/9-cheatsheet/thumbnails.jpg"
[menu.main]
  weight = 2001
  identifier = "python-cheatsheet"
+++

Dont ask me about the syntax. I look it up on Google and API documentations. And now `cht.sh` too.

<!--more-->


## Introduction {#introduction}

> “Talk is cheap. Show me the code.” - Linus Torvalds

If you are having trouble to remember the exact syntax no matter how many times you've used it, you are not alone.

There is a community driven programming cheatsheet, so you can lookup the common usage of the function and it gives you a quick example to refresh your memories.

Introducing the ultimate programming cheatsheet - **cheat.sh** ([Official Site](https://cht.sh/)).

```text
      _                _         _    __
  ___| |__   ___  __ _| |_   ___| |__ \ \      The only cheat sheet you need
 / __| '_ \ / _ \/ _` | __| / __| '_ \ \ \     Unified access to the best
| (__| | | |  __/ (_| | |_ _\__ \ | | |/ /     community driven documentation
 \___|_| |_|\___|\__,_|\__(_)___/_| |_/_/      repositories of the world
```


## Why Cheatsheet {#why-cheatsheet}

-   ****Straight to the point.**** Quickly give you some useful code snippets.

-   ****Efficiency.**** Stay in your editor while searching.

-   ****Easy context switching.**** Extremely useful when you need to constantly switching between different programming languages. (e.g. Python, R, Spark, etc..)


## Some Examples {#some-examples}

1.  Python group by lambda

    `curl http://cht.sh/python/group+by+lambda`

    ```python

    #  The apply method itself passes each "group" of the groupby object as
    #  the first argument to the function. So it knows to associate 'Weight'
    #  and "Quantity" to `a` and `b` based on position. (eg they are the 2nd
    #  and 3rd arguments if you count the first "group" argument.

    df = pd.DataFrame(np.random.randint(0,11,(10,3)), columns = ['num1','num2','num3'])
    df['category'] = ['a','a','a','b','b','b','b','c','c','c']
    df = df[['category','num1','num2','num3']]
    df

    category  num1  num2  num3
    0        a     2     5     2
    1        a     5     5     2
    2        a     7     3     4
    3        b    10     9     1
    4        b     4     7     6
    5        b     0     5     2
    6        b     7     7     5
    7        c     2     2     1
    8        c     4     3     2
    9        c     1     4     6

    gb = df.groupby('category')

    #  implicit argument is each "group" or in this case each category

    gb.apply(lambda grp: grp.sum())

    #  The "grp" is the first argument to the lambda function
    #  notice I don't have to specify anything for it as it is already,
    #  automatically taken to be each group of the groupby object

    category  num1  num2  num3
    category
    a             aaa    14    13     8
    b            bbbb    21    28    14
    c             ccc     7     9     9

    #  So apply goes through each of these and performs a sum operation

    print(gb.groups)
    {'a': Int64Index([0, 1, 2], dtype='int64'), 'b': Int64Index([3, 4, 5, 6], dtype='int64'), 'c': Int64Index([7, 8, 9], dtype='int64')}

    print('1st GROUP:\n', df.loc[gb.groups['a']])
    1st GROUP:
    category  num1  num2  num3
    0        a     2     5     2
    1        a     5     5     2
    2        a     7     3     4

    print('SUM of 1st group:\n', df.loc[gb.groups['a']].sum())

    SUM of 1st group:
    category    aaa
    num1         14
    num2         13
    num3          8
    dtype: object

    #  Notice how this is the same as the first row of our previous operation
    #
    #  So apply is _implicitly_ passing each group to the function argument
    #  as the first argument.
    #
    #  From the [docs](https://pandas.pydata.org/pandas-
    #  docs/stable/generated/pandas.core.groupby.GroupBy.apply.html)
    #
    #  > GroupBy.apply(func, *args, **kwargs)
    #  >
    #  > args, kwargs : tuple and dict
    #  >> Optional positional and keyword arguments to pass to func
    #
    #  Additional Args passed in "\*args" get passed _after_ the implict
    #  group argument.
    #
    #  so using your code

    gb.apply(lambda df,a,b: sum(df[a] * df[b]), 'num1', 'num2')

    category
    a     56
    b    167
    c     20
    dtype: int64

    #  here 'num1' and 'num2' are being passed as _additional_ arguments to
    #  each call of the lambda function
    #
    #  So apply goes through each of these and performs your lambda operation

    # copy and paste your lambda function
    fun = lambda df,a,b: sum(df[a] * df[b])

    print(gb.groups)
    {'a': Int64Index([0, 1, 2], dtype='int64'), 'b': Int64Index([3, 4, 5, 6], dtype='int64'), 'c': Int64Index([7, 8, 9], dtype='int64')}

    print('1st GROUP:\n', df.loc[gb.groups['a']])

    1st GROUP:
    category  num1  num2  num3
    0        a     2     5     2
    1        a     5     5     2
    2        a     7     3     4

    print('Output of 1st group for function "fun":\n',
          fun(df.loc[gb.groups['a']], 'num1','num2'))

    Output of 1st group for function "fun":
    56

    #  [RSHAP] [so/q/47551251] [cc by-sa 3.0]
    ```

2.  R ggplot scatter

    `curl http://cht.sh/r/ggplot2+scatter`

    ```r

    # question_id: 7714677
    # One way to deal with this is with alpha blending, which makes each
    # point slightly transparent. So regions appear darker that have more
    # point plotted on them.
    #
    # This is easy to do in `ggplot2`:

    df <- data.frame(x = rnorm(5000),y=rnorm(5000))
    ggplot(df,aes(x=x,y=y)) + geom_point(alpha = 0.3)

    # ![enter image description here][1]
    #
    # Another convenient way to deal with this is (and probably more
    # appropriate for the number of points you have) is hexagonal binning:

    ggplot(df,aes(x=x,y=y)) + stat_binhex()

    # ![enter image description here][2]
    #
    # And there is also regular old rectangular binning (image omitted),
    # which is more like your traditional heatmap:

    ggplot(df,aes(x=x,y=y)) + geom_bin2d()

    # [1]: http://i.stack.imgur.com/PJbMn.png
    # [2]: http://i.stack.imgur.com/XyWw1.png
    #
    # [joran] [so/q/7714677] [cc by-sa 3.0]
    ```

    {{< figure src="http://i.stack.imgur.com/PJbMn.png" width="250px" >}}

    {{< figure src="http://i.stack.imgur.com/XyWw1.png" width="250px" >}}

3.  PySpark dataframe filter

    `curl http://cht.sh/pyspark/filter`

    ```python

    /*
    ​ * Pyspark: Filter dataframe based on multiple conditions
     *
    ​ * <!-- language-all: lang-python -->
     *
    ​ * Your logic condition is wrong. IIUC, what you want is:
     */

    import pyspark.sql.functions as f

    df.filter((f.col('d')<5))\
        .filter(
    	((f.col('col1') != f.col('col3')) |
    	 (f.col('col2') != f.col('col4')) & (f.col('col1') == f.col('col3')))
        )\
        .show()

    /*
    ​ * I broke the filter() step into 2 calls for readability, but you could
    ​ * equivalently do it in one line.
     *
    ​ * Output:
     */

    +----+----+----+----+---+
    |col1|col2|col3|col4|  d|
    +----+----+----+----+---+
    |   A|  xx|   D|  vv|  4|
    |   A|   x|   A|  xx|  3|
    |   E| xxx|   B|  vv|  3|
    |   F|xxxx|   F| vvv|  4|
    |   G| xxx|   G|  xx|  4|
    +----+----+----+----+---+

    /* [pault] [so/q/49301373] [cc by-sa 3.0] */
    ```


## My Workflow {#my-workflow}

-   Have my emacs setup with left pane as ****code**** and right pane as ****command line console****

-   Set up ****alias**** to run go and python program with less keystrokes
    -   alias `pp` as `python main.py`
    -   alias `gg` as `go run main.go`

-   Created an ****utility**** command line program and alias to quickly call cheatsheet with `chp sth` (`curl http://cht.sh/python/sth`) and `chg sth` (`curl http://cht.sh/go/sth`)


## Demo {#demo}

Quick demo to create a dummy python dataframe
![](https://storage.googleapis.com/billylkc-blog-image/images/posts/9-cheatsheet/cheatsheet%5Fquick.gif)


## Final Thoughts {#final-thoughts}

Hopefully you find it useful too. <br />
Happy Coding!

\_

Reference

-   Reference Photo by [cottonbro](<https://www.pexels.com/@cottonbro?utm%5Fcontent=attributionCopyText&utm%5Fmedium=referral&utm%5Fsource=pexels>) on [Pexels](<https://www.pexels.com/photo/white-printer-paper-on-brown-round-table-7128752/?utm%5Fcontent=attributionCopyText&utm%5Fmedium=referral&utm%5Fsource=pexels>)
