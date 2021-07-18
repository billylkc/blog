+++
title = "Definitive guide to python debugging with pdb and Visual Studio Code"
author = ["Billy Lam"]
date = 2021-07-12
lastmod = 2021-07-13
tags = ["python", "workflow"]
categories = ["python"]
draft = true
weight = 20
nolastmod = true
cover = "https://storage.googleapis.com/billylkc-blog-image/images/posts/14-debug/thumbnails.jpg"
[menu.main]
  weight = 2002
  identifier = "definitive-guide-to-python-debugging-with-pdb-and-visual-studio-code"
+++

I don't know where you are, I don't know how you work. But I will find you, And.. <br />

I will fix you - TheRaxTon

<!--more-->


## Introduction {#introduction}

> The only way to be a zero bug programmer is to not write any code. No Cap.

Tooling plays an important roles in debugging. It is like you can do all your calculation with a pen, it is just easier with a calculator.

This post is a gentle introduction of the debugging tools `pdb` and `debugging mode` in Visual Studio Code. Hopefully you will have a better understanding on what functionalities a good debugger offers.


## Overview {#overview}

In this post, we will be covering the following topics

-   Create a group by function for debugging
-   Introduction to pdb
-   pdb demo
-   Debugging in Visual Studio Code
-   Debug mode Demo


## Create a group by function for debugging {#create-a-group-by-function-for-debugging}

Turns out it is not easy for me to find a good function for debugging demo. I am just going to use one of my favorite `group by apply` function for demostration. It seems a lot of code, but it simply generates some dummy grouped data, then run a regression model for each group.

<br />

****Functions****

1.  **generate\_trend** - Generates dummy trend data with slope and intercept with a gaussian noise

2.  **generate\_noise** - A wrapper function to return a guassian noise

3.  **build\_super\_model** - Just a simple regression model with a cool name

4.  **main** - Calls the group by apply function and display the result

<br />


### a) Generate trend {#a-generate-trend}

A simple function to generate dummy data with a slope and intercept

```python
def generate_trend(slope, intercept, group, size=1000):
    """ Generate dummy trend data with slope and intercept with a gaussian noise """

    idx = np.arange(start=1, stop=size + 1, step=1)
    y = [slope * x + intercept for x in idx]
    noise = generate_noise(0, 3, size)

    dict = {
	    "x": idx,
	    "y": np.add(y, noise),
	    "group": group,
    }
    df = pd.DataFrame(dict)

    return df


def generate_noise(mu, sigma, size):
    """ Gaussian noise """
    return np.random.normal(mu, sigma, size)
```

Result

```python
df = generate_trend(slope=2, intercept=3, group="A", size=5)
print(df)

    x   y        group
0   1   6.490142     A
1   2   6.585207     A
2   3  10.943066     A
3   4  15.569090     A
4   5  12.297540     A
```


### b) Generate noise {#b-generate-noise}

Wrapper function to generate a guassian noise

```python
def generate_noise(mu, sigma, size):
      """ Gaussian noise """
      return np.random.normal(mu, sigma, size)
```

Result

```python
noise = generate_noise(mu=0, sigma=1, size=10)
print(noise)

[ 0.49671415 -0.1382643   0.64768854  1.52302986 -0.23415337 -0.23413696
  1.57921282  0.76743473 -0.46947439  0.54256004]
```


### c ) Build super model {#c--build-super-model}

```python
def build_super_model(dd):
    """
    Super Model

    Args:
      dd (Dataframe): Grouped Dataframe

    Return:
      result (Dataframe): Linear Model coefficient with Test set evlauation metrics (e.g. rmse, mape, etc..)
    """
    # TODO: add cross validation
    # Split training and testing set
    X = np.array(dd.x).reshape((-1, 1))
    y = dd.y
    X_train, X_test, y_train, y_test = train_test_split(
	    X, y, test_size=0.2, random_state=42
    )

    # Fit model with train set
    lm = LinearRegression()
    lm.fit(X_train, y_train)

    # Predict on test set
    y_pred = lm.predict(X_test)
    rmse = mean_squared_error(y_test, y_pred, squared=False)
    mape = mean_absolute_percentage_error(y_test, y_pred)

    result = pd.DataFrame.from_dict(
	    {
		    "coef": lm.coef_,
		    "intercept": lm.intercept_,
		    "rmse": rmse,
		    "mape": mape,
	    }
    )

    return result
```

Result

```python
df_A = generate_trend(slope=2.3, intercept=3.1, group="A", size=1_000)
df_B = generate_trend(slope=1, intercept=2, group="B", size=1_000)
df = pd.concat(objs=[df_A, df_B], axis=0)
result = df.groupby("group").apply(lambda x: build_super_model(x))
result = result.droplevel(1)
result.reset_index(drop=False, inplace=True)

print(result)

    group     coef  intercept      rmse      mape
  0     A  2.30039   2.982111  2.924085  0.004046
  1     B  0.99966   2.425999  3.011673  0.010840
```


### d ) Main function {#d--main-function}

```python
def main():

    slopes = [1, 0.4, 0.8]
    intercepts = [3, 4, 5]
    groups = ["a", "b", "c"]

    dfs = map(
	    generate_trend,
	    slopes,
	    intercepts,
	    groups,
    )
    df = pd.concat(dfs, 0)

    result = df.groupby("group").apply(lambda x: build_super_model(x))
    result = result.droplevel(1)
    result.reset_index(drop=False, inplace=True)

    # Print result
    print("------------------")
    print("Raw Data \n")
    print(df.head())
    print(df.shape)

    print("------------------")
    print("Predicted \n")
    print(result)

    print("------------------")
    print("Actual \n")
    dict = {
	    "group": groups,
	    "coef": slopes,
	    "intercepte": intercepts,
    }
    print(pd.DataFrame.from_dict(dict))
    print("------------------")


if __name__ == "__main__":
      main()
```

Result

```python
------------------
Raw Data

   x          y group
0  1   5.490142     a
1  2   4.585207     a
2  3   7.943066     a
3  4  11.569090     a
4  5   7.297540     a
(3000, 3)
------------------
Predicted

  group      coef  intercept      rmse      mape
0     a  1.000390   2.882111  2.924085  0.009209
1     b  0.399660   4.425999  3.011673  0.024028
2     c  0.800198   4.965771  2.989898  0.014437
------------------
Actual

  group  coef  intercepte
0     a   1.0           3
1     b   0.4           4
2     c   0.8           5
------------------
```

Hey you made it this far for our demo settings. Congrats!    <br />

Now let the fun part begins! This example is specifically picked so that you know what it does, but you may not understand fully for some of the steps.


## pdb {#pdb}

The Python debugger `pdb` implements an ****interactive debugging environment**** that you can use with any of your programs written in Python.

With features that let you pause your program, look at what values your variables are set to, and go through program execution in a discrete step-by-step manner, you can more fully understand what your program is doing and find bugs that exist in the logic or troubleshoot known issues.

Here is a little Cheatsheet.

<br />

**Basic commands**

<div class="ox-hugo-table table table-striped table-dark">
<div></div>
<div class="table-caption">
  <span class="table-number">Table 1</span>:
  Basic commands
</div>

| Command                   | Description                 |
|---------------------------|-----------------------------|
| ****(h)**** help          | List all available commands |
| ****(q)**** quit          | Quit debug session          |
| ****(l)**** list          | List a few lines            |
| ****(b 10)**** breakpoint | Set break point at line 10  |

</div>

<div class="table-caption">
  <span class="table-number">Table 2</span>:
  Navigation and debugging
</div>

| Command                                  | Description                   |
|------------------------------------------|-------------------------------|
| ****(l)**** list                         | List a few lines              |
| ****(n)**** next                         | Move to next line             |
| ****(s)**** step                         | Step into function            |
| ****(j)**** jump                         | Jump                          |
| ****ENTER****                            | Repeat last command           |
| ****(p)**** print ****_expr_****         | Print variable _expr_         |
| ****(pp)**** pretty print ****_expr_**** | Pretty Print _expr_           |
| ****(b)**** break                        | Show all break point          |
| ****(b)**** break ****_num_****          | Set break point at line _num_ |
| ****(c)**** continue                     | Continue til next break point |
| ****locals()****                         | List local variables          |

Reference: [Python Debugger Cheatsheet](https://appletree.or.kr/quick%5Freference%5Fcards/Python/Python%20Debugger%20Cheatsheet.pdf)


## Demo Demo {#demo-demo}

Debugging plans

-


## Debugging in Visual Studio Code {#debugging-in-visual-studio-code}


## Final thoughts {#final-thoughts}

By now you should have a basic understanding of how debugging works in Python, as well as the tools available in pdb (standard library), to graphical debugging (IDE like Visual Studio Code). Using a debugger for your codebases is really going to supercharge your productivity. Let's give it a try today!

Happy Coding!

\_

Reference:

-   Photo by [Nubelson Fernandes](<https://unsplash.com/@nublson?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>) on [Unsplash](<https://unsplash.com/s/photos/debugging?utm%5Fsource=unsplash&utm%5Fmedium=referral&utm%5Fcontent=creditCopyText>)
