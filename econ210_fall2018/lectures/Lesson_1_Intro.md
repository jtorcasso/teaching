---
title: "Lesson 1"
subtitle: "Introduction to Causal Inference"
output:
  # pdf_document: default
  html_document:
    keep_md: true
  pdf_document:
    number_sections: true
    includes:
      in_header: preamble.tex
urlcolor: blue
linkcolor: blue
---

# Overview of Course
Data or summaries of data--"facts"--never speak for themselves. All facts must be interpreted. 

In this course, students learn how to interpret empirical facts, applying theory to data to enhance their understanding of the mechanisms that generate observed behavior.

To motivate the course, we will show how understanding mechanisms is important for predicting the impact of policy and how the inherent selection of data makes this prediction difficult. 

Some key topics include: causal analysis and the fundamental problem of causal inference; statistical inference, including estimators, their distributions, confidence intervals and hypothesis testing; and constructing estimators for causal inference.

# Introduction to Causal Inference
We'll start with an example. Let $Y_i(d)$ be the earnings of individual $i$ at age 30 had she made college decision $d$. Let $d=1$ denote college graduate and $d=0$ otherwise. 
Then $Y_i(1)$ is individual $i$'s earnings had she gone to college and $Y_i(0)$
if she had not gone to college. The word "had" in the previous sentence indicates that
$Y_i(1)$ and $Y_i(0)$ are **potential outcomes**. Potential outcomes are the "mathy"
way of representing outcomes from hypothetical scenarios, i.e., counterfactuals. 
In this example, the two hypothetical
scenarios are (0) $i$ doesn't graduate from college and (1) $i$ graduates from college.

The **causal effect** of college graduation on $i$'s earnings is the difference in
potential outcomes $Y_i(1) - Y_i(0)$. But
we observe at most one of these potential outcomes, because $i$ either graduated from
college or did not graduate. This lack of information about all counferfactuals is often called
the **fundamental problem of causal inference**.

The variable $d$ is sometimes referred to as the **treatment**. And the causal effect
$Y_i(1) - Y_i(0)$ the individual **treatment effect**.

While we've been working with discrete (binary) treatments,
treatments can have multiple values--like college major--and can even be continuous. For instance, if $x$ is a continuous variable,
like years of education, then we could define potential outcomes $Y_i(x)$ as 
the potential earnings at age 40 with $x$ years of education. Then we could define
an infinite number of treatment effects corresponding to pairs of different values
of $x$. We'll often introduce concepts using binary treatments, but a lot of the
fundamentals and intuition will carry over to the multi-valued treatment
case.

If we can't observe more than one counterfactual for an individual, how can
we learn about causal effects? Are we unable to say anything about the causal effect of
college graduation? A pragmatic friend might say, "well, can't we use the fact that
some people go to college and others don't to learn about counterfactuals?" And he'd be
right. But we have to be careful. To see why, let's explore an example.

## Empirical Example
Let $D_i$ be individual $i$'s actual college attendance. $D_i=1$ if $i$ graduated from college
and $D_i=0$ if $i$ did not. Actual earnings are then $Y_i=Y_i(1)D_i + Y_i(0)[1-D_i]$. Suppose
we have data on earnings at age 30 and college graduation for $N=10000$ people. That is, we have
a sample $\{(Y_i,X_i)\}_{i=1}^{10000}$. Suppose we've loaded this data into R as a dataframe
called `df`. We print the first 5 rows of `df` below.




```r
head(df[,c("Y", "D")], 5)
```

```
      Y D
1 78161 1
2 78076 0
3 88110 0
4 48303 1
5 60325 1
```

Take the difference in sample means of earnings between the graduates and non-graduates.


```r
mean(df$Y[df$D==1]) - mean(df$Y[df$D==0])
```

```
[1] 33311.39
```

In our sample, college graduates earn substantially more on average than those that don't 
graduate from college. We'll call this an **empirical fact**. But how do we interpret this
empirical fact? Does it reflect a relationship that holds in the population or is it just
statistical noise? Does it reflect
a causal relationship or is it just correlation? These different questions affect whether
the mean difference we observe in the sample
is a good estimate of the causal effect of graduating from college. Unfortunately,
it probably isn't. 

Even if the difference
is statistically significant, we probably would not interpret the difference as the causal effect
of education because the people who go to college might be different from those that don't. 
The inherent selection in the data makes the mean difference a biased estimator for
the causal effect. In other words, correlation is not causation. 

People who go to college
may be smarter, which should have a direct affect on their potential earnings. Part
of the mean difference may be attributed to differences in ability between the two groups.
Ability is a **confounding variable** or **omitted variable**. To see this graphically, consider 
the directed acyclic graph (DAG) below, which depicts the causal relationship between earnings $Y$, college graduation $D$, and ability $A$. Other variables are omitted for simplicity.



\centering
![](mydag.pdf)<!-- -->

\raggedright
Ability affects college graduation and earnings.
To the degree both of these statements are true, the difference in means between college graduates
and nongraduates is partly due to differences in ability between the two groups.
Later in the course we'll describe this bias mathematically.

So where do we go from here? How can we improve our estimate of the causal effect
of college? We'll answer this question and other questions you might have as we proceed
through the course. I've listed some of these questions below.

**Important Questions**

1. What do we mean by a good estimate?
    * When is an estimate statistically significant?
    * When can an estimate be interpreted as a causal effect?
2. What do we mean by "the causal effect" if we can define a causal effect for each individual $i$?
    * When we use data across individuals or across time with variation in the
    treatment, our goal is usually to estimate an average causal effect, i.e., 
    $E[Y_i(1) - Y_i(0)]$, where $E[X_i]$ denotes the expectation or population average
    of $X_i$. Other averages may be important too. We'll see later.
3. What can we do to get better estimates of causal effects?
4. What does all of this have to do with our introductory drivel about the interpretation of "facts"?
5. How does this relate to anything you've learned in economics?
6. How is this useful in the real world?

In the remainder of this lesson, we'll discuss questions 5 and 6 to help motivate the course. You'll
see how you've already been exposed to potential outcomes in your ECON200 courses. 
And how causal effects are the key input to making optimal decisions.

## Other Examples in the Media

1. You observe that [union workers earn more](https://money.cnn.com/2015/02/24/news/economy/union-wages/index.html) and conclude
that if you want a raise you should join a union. What could be wrong with this conclusion?
2. [People making the classic mistake](https://www.theatlantic.com/education/archive/2018/09/why-is-college-so-expensive-in-america/569884/) that the college degree is responsible for the full difference
in earnings between college graduates and nongraduates.
3. Kidnapping: You're a kid walking on the street. You're lost. Consider two scenarios.
Scenario 1: A van pulls up next to you as you're walking and the driver asks if
you want a ride. Scenario 2: As you're walking, you see a van and ask the driver
for a ride. Which scenario do you think is safer and how does it relate to
selection bias? What or who is being selected?
4. A higher test score implies greater aptitude.

# The Policy Relevance of Counterfactuals

Policy-makers, managers, business-owners, consumers, and workers--everyone--is interested in
counterfactuals, and in particular, the difference among two or more potential outcomes. Why?
Because for every action we have a potential outcome. To find the action
that maximizes utility we have to predict potential outcomes, or at least
the difference between any pair. Below we list a few examples of potential
outcomes and/or causal effects between a pair of potential outcomes.

* the Governor of Illinois might ask, "what would be the impact on violent crime and drug overdoses from the decriminalization of drug-use in Illinois?"
* Or the CEO of Walmart, "what is my expected profit if I open a new Walmart in Mexico City?"
* What would be the share price of AOL and Time Warner had they not merged?
* How many donations would the Clinton campaign receive if an email blitz includes
a picture of Hillary with a dog? A cat?
* How many likes will I get if I post a selfie using the Warm Glow filter vs. grayscale?
* The Chicago Bulls GM might ask, "How would a top 1 pick in the 2018 draft increase the chances of winning an NBA Championship in the next 5 years?"
* A high school student: "What would my annual earnings be at age 40 if I was an engineer?
A lawyer? A teacher? An artist? How satisfying of a life would I have?" 
* What is the probability of contracting a disease with or without the vaccine?

Learning about the difference between any two counterfactual outcomes can help the
decision-maker choose the optimal policy. For instance, the college premium (the
causal effect of college) is used to determine whether the government should promote
college attendance. Likewise, campaigns and marketing departments perform A/B testing
to increase voter/customer acquisition. Companies pour millions of dollars into analysts
and lawyers to determine if a merger will increase profits.

As a brief aside, I want to discuss an assumption we've already made about potential
outcomes because it can be important for studying policies that affect many people. 
Up until now we've worked with potential outcomes that depend on a single decision.
In our college example, potential earnings $Y_i(d_i)$ depend only on $i$'s college
choice. But we could have written potential earnings as $Y_i((d_1,d_2,...,d_N))$ 
so that $i$'s earnings depend
on the choices of an entire cohort. Why might the earnings of
individual $i$ depend on whether other people graduated from college?

* knowledge spillovers (network effects) that increase your own productivity
* a greater supply of college graduates lowers the college premium through general equilibrium
(i.e., supply side economics)

Similarly, why might opening Walmarts in adjacent cities affect the expected profit of 
opening a Walmart in Mexico City?

* costs of opening one Walmart might lower costs of opening and running a second Walmart
* other Walmarts might lower demand for Walmart in an adjacent city

The assumption that potential outcomes don't depend on the treatment of other units
is called the **SUTVA** (Stable Unit Treatment Value Assumption). It says that
$Y_i((d_1,d_2,...,d_N))=Y_i(d_i)$. We will typically make this assumption for
simplicity. Many of the principles we'll learn still apply even if we
don't make this assumption.

# Potential Outcomes in Economics
What is the relationship between potential outcomes and what you've learned so
far in economics? 

## Example 1: Consumer Demand
You've seen many economic models in consumer and firm
theory, game theory and macro. Take for instance the two-good consumer problem:

\begin{align}
\max_{x,y} \quad & x^{\alpha}y^{\beta} \\ s.t \quad & m = p_xx + p_yy
\end{align}

where we obtain Marshallian demands

\begin{align}
x(p_x,p_y,m) & = \frac{\alpha m}{(\alpha + \beta)p_x} \\
y(p_x,p_y,m) & = \frac{\beta m}{(\alpha + \beta)p_y}.
\end{align}

These demands are potential outcomes. We could ask, how much of good $x$ would be consumed if
prices were $p=(p_x,p_y)=(5, 3)$ and income $m=10$. From the individual demands we
could derive the aggregate demands $X(p_x,p_y,M)$ and $Y(p_x,p_y,M)$. Consider the aggregate
demand for good $x$, and for simplicity, fix the price of $y$ and total income $M$ to
some values and
consider potential outcomes from manipulations of $p_x$ alone. Now consider aggregate
supply, $X^s(p_x)$. We will write demand with superscript $d$ to indicate demand, i.e., $X^d(p_x)$.
The causal effect of price on
aggregate demand is the price elasticity of demand. It is informative for how firms
set prices, and how the government calculates revenue from sales taxes. 
How can we estimate the elasticities
of demand and supply? Suppose we have data on quantities and prices over time.
We'll print the first 5 rows of the data below. 




```r
head(df.x, 5)
```

```
      x    p
1 157.0 49.0
2 157.5 51.5
3 157.0 52.0
4 157.0 48.0
5 153.0 50.0
```

Let's see a scatter plot of the data.


```r
plot(x, p)
```

![](Lesson_1_Intro_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Maybe we could estimate the elasticities by finding the slope of the line of best fit.


```r
plot(x, p)
params <- lm(p ~ x)$coefficients
lines(seq(150, 160, 0.01), params["(Intercept)"] + seq(150, 160, 0.01)*params["x"])
```

![](Lesson_1_Intro_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

It appears that there is no relationship between quantities and prices. Have we just blown
up economic theory? Not at all. Though part of this course involves using empirical
evidence to assess economic theory, what we did
doesn't make any sense. There are two elasticities but we only have one slope.
How could this be? In the same way that our difference in means was a biased estimate
of the causal effect of education, the slope of the line above is a biased estimate of
both the supply and demand elasticities. We can solve this problem if we have an instrumental
variable, but that is for later in the course.

## Example 2: Interpreting ``Effects''

In some cases, our goal is not estimating causal effects but interpreting
a correlation in terms of economic parameters. Consider the regression of
(log) child's earnings $\log Y_{i,t}$ on (log) parent's earnings $\log Y_{i,t-1}$.
The slope coefficient of a univariate regression is very related to correlation,
so just think of it as correlation for now (we'll learn about regression later
in the course.)

You can show that the correlation is proportional to
\begin{align*}
\beta = \frac{(1-\gamma)\theta p + \lambda}{1 + (1-\gamma)\theta p\lambda},
\end{align*}
where $\theta$ is the productivity of human capital investment, $\lambda$ is
the heritability of ability, $p$ is the return to human capital and $\gamma$
indexes the progressivity of education policy. For derivations, see
``A Model of Intergenerational Mobility Variation over Time and Place,'' by
Gary Solon. 

Instead of finding the causal effect of parental earnings on child earnings,
we used an economic model to interpret
the correlation in terms of a bunch of structural economic parameters. I just
don't want students to think that econometrics is only about finding causal
effects. Sometimes its just about interpreting the data.

# The Need for Statistics
The fundamental problem of causal inference says that we only observe one counterfactual,
one potential outcome. But we need to learn something about the relationship between two
potential outcomes in order to learn about causal effects. In several examples we saw data with variation
in the treatment and considered how we could use this variation to form estimates of
causal effects. We took a difference in means between college graduates and nongraduates to
try to estimate the causal effect of college graduation. And
we fit a line to quantity and price data to estimate the elasticity of demand. These were our
empirical facts.

We discussed several problems with interpreting our empirical facts as causal effects. To understand
these problems we need to have a theory that defines our goal precisely
and the assumptions we're making about the data. We need a statistical foundation. This will
be the focus of the next lesson.
