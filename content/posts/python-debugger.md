+++
title = "Python Debugging with pdb"
author = ["Billy Lam"]
date = 2021-06-21
lastmod = 2021-07-05
tags = ["python", "workflow"]
categories = ["python"]
draft = true
weight = 15
nolastmod = true
[menu.main]
  weight = 2002
  identifier = "python-debugging-with-pdb"
+++

I don't know where you are, I don't know how you work. But I will find you, And.. <br />

I will fix you - TheRaxTon

<!--more-->


## Introduction {#introduction}

The only way to be a zero bug programmer is to ****not write any code****.

All jokes aside, tooling plays an important roles in debugging. I’ve seen many practitioners try to spot bugs using only print statements instead of actual debugging tools. Sometimes it is a personal preference, but sometimes you just do not know it exists.

This post is a gentle introduction of the debugging tools `pdb` and `debugging mode` in Visual Studio Code. Hopefully you will have a better understanding on what functionalities a good debugger offers.


## Functionalities {#functionalities}


## pdb {#pdb}

The Python debugger `pdb` implements an ****interactive debugging environment**** that you can use with any of your programs written in Python.

With features that let you pause your program, look at what values your variables are set to, and go through program execution in a discrete step-by-step manner, you can more fully understand what your program is doing and find bugs that exist in the logic or troubleshoot known issues.

Cheatsheet

Basic

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


## Demo {#demo}


## Debugging in visual studio code {#debugging-in-visual-studio-code}


## Final thoughts {#final-thoughts}

By now you should have a basic understanding of how debugging works in Python, as well as the tools available in pdb in standard library, to graphical debugging in IDE like visual studio code. Using a debugger for your own codebases is really going to supercharge your productivity.

Happy Coding!

\_

Reference:

-
