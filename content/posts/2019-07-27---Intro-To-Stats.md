---
title: Intro To Statistics
date: "2019-07-01T23:46:37.121Z"
template: "post"
draft: false
slug: "/posts/intro-to-statistics/"
category: "Math"
tags:
  - "Statistics"
  - "Basics"
description: "Intro to statistics. Learning the basics of different types of variables, 
 distribution, central limit theorem, and sampling."
---
### VARIABLES:

#### Random variables
Random variables are variables that have ranges of possible outcomes for a particular random process/phenomenon. In math notation it usually derives in a big `X`. An example would be a six sided die. `X = x` such that `x` may be between `[1, 2, 3, 4, 5, 6]`.

In math, here’s how it reads. 
`𝑃(𝑋=𝑥)=1/6` for  `𝑥∈[1,2,3,4,5,6]`
This denotes that there are 6 possible equal outcomes. Between ranges `1` to `6`. 


#### Discrete variables

Discrete random variables are variables that can take a set of countable numbers. An example would be the 6 sided die that has 6 equal outcomes. A good rule of thumb is that a discrete random variable is that the sum of all the probabilities(across all discrete options) has to be exactly 1.

Here's an example of a discrete variables.
`[2, 4, 6, 8, 10, 12]`


#### Discrete variables:

Continuous random variable are variables that can take a continues set of infinite number of values. For example a question maybe ask as to what is the exact winning time that winner of 2019 olympics won ? 

It could be `9.7123s` or `9.7122s` or `9.81s`. Unless the timing is rounded up or down. The variables are continuous.

### MATH INTERVALS:

#### Close interval = [] 
 Means the endpoints are closed
 For example, `[1, 5]`. The line starts with `1` and closes at `5`.

#### Open interval = ()
Means the endpoints are open 
 For example (1, 5). The line starts at `1.1~` to `4.9~`. `~` means that the number can be continuous. 

#### Close open interval
 Means the endpoints are open and close.
 For example `[1, 5)`. The line starts at `1` and ends to `4.9~`.

### DISTRIBUTION:

#### Normal distribution

<img src="https://upload.wikimedia.org/wikipedia/commons/7/74/Normal_Distribution_PDF.svg"/>

This is called the a normal distribution or gaussian distribution. The formula for this is:
<img src="https://wikimedia.org/api/rest_v1/media/math/render/svg/8aa9ff808602c27f1d9d63d7b2c115388a34f190"/>

In this case, a formula will always be use in data science. There's no need to remember the formula. A few things are needed to understand the normal distribution. 

`μ` or `mew` is the middle of the curve. In the above example, the `μ` of the green line is 
-2.

`σ` or `sigma` is the standard deviation of the bell curve. It means, how far apart are the points are too each other. In the above example, the `σ` of the green line is 0.5.


