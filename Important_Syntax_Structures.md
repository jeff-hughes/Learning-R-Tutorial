# Important Syntax Structures
Jeff Hughes  
August 16, 2016  





# Learning R Tutorial: A Process-Focused Approach

## Important Syntax Structures

In this lesson, we are going to learn about some of the tools that will unlock the true power of R. Any statistical program can run a t-test, or a regression, and many have even more complex tools. But one of the great advantages of R is that it is a full-fledged programming language. (Some other statistical programs have syntax that approximates some of these structures we'll talk about, but in most cases, the syntax is so convoluted that it makes proper programming incredibly difficult.) By harnessing the tools that the R programming language provides, you can greatly increase your effectiveness and efficiency in your analyses. When I moved from SPSS to R, after I got the hang of R, I was literally able to cut in half the time it took me to do analyses. I can't guarantee similar results, but I hope to impress upon you how one can build the time-saving tools you need from the building blocks we will learn in this lesson.

### Functions

We'll start off with functions. We have already been using functions, of course, but with R we can also define our own functions. Remember that functions have two major advantages:

1. They let us abstract away implementation details, so that we no longer need to worry about the nitty-gritty details of how the function operates; and

2. We can reuse a function over and over, whenever we want.

With this in mind, it seems clear that the best use for a function is when you have a task that you commonly do, and this task generally takes similar inputs and produces similar outputs. Sometimes functions can be incredibly complex (for example, the `lm()` function must pull the correct variables out of the data, make sure they're in the right format, create a covariance matrix, calculate the least squares to fit the model, and then organize and format the coefficients). However, the best functions are ones for which you could easily state a one-sentence description of the function's purpose. Because a function abstracts away the implementation details, the complex `lm()` function can still be summarized as a function that "fits linear models using ordinary least squares regression". Of course, there are always conditions about what inputs it may support, and what arguments can be passed, but the point is that the `lm()` function fits linear models -- it doesn't reorganize your data for you, it doesn't automatically provide graphs, and it doesn't even easily provide the coefficients! (That's what the `summary()` function is for.) No, it just does *one thing*. And even in the process of doing that one thing, it calls other sub-functions that also do just one thing. By compartmentalizing things in this way, it becomes easier to mix and match what you need. As you learn to think in this way, you'll be able to find tasks in your own research that you can create functions for.

Let's try creating a simple function for ourselves. In our dataset, we have a number of scales that we had to calculate the reliability for one at a time. This is a pain, and maybe we aren't so interested in all those details that the `alpha()` function gives us. Who cares about Guttman's Lambda 6, anyway? (Well, presumably Dr. Guttman did.) So we want a function that will let us pass in a number of sets of scales, and it will print out a table with just the information we want. We'll start out with a function that can handle three sets of scales, and then we'll refine it further as we go. So let's look at the basic structure for defining a function:


```r
func_name <- function(arg1, arg2, arg3=TRUE, arg4=NULL) {
    # here's where the function code goes
}
```

That's all there is to it! It's pretty simple -- we pick a name, we assign a function to that name, and we indicate what arguments the function can take. Note that each of the arguments has a specific name (e.g., arg1, arg2) -- that's so we can refer to them from within our function. We can also specify default values for our arguments, as we did with `arg3` and `arg4`. By convention, arguments with default values should come at the *end* of the list of arguments, because that makes them easier to just leave out when calling the function, if we are okay with the default values.

Of course, the important part of the function is the stuff inside the curly braces. Those braces define an *environment* (sometimes called a "scope" in other programming languages). What that means is that our arguments are available within that environment -- from inside the function, it works exactly as if we had assigned a variable like `arg1 <- 42`. But when the function is done running, those argument references disappear into the ether. That's a good thing, because it means we don't pollute our main R session with a whole lot of variables. What if we had a variable called "data" with our dataset in it, and then a function created its own variable called "data" and it just overwrote ours?? That would be bad! So this way, we can have our "data" variable, and the function can create its own "data" variable if it needs to, and those won't interfere in any way. If you need to get some data *into* a function, the best way is to pass it in through the arguments.

So let's start creating the function we want to make. We'll start off with the basic definition.


```r
alpha_table <- function(scale1, scale2, scale3) {
    return(NULL)
}
```

Here we go. I decided on the name `alpha_table()`. Like I said, for now we'll set up our function to accept three sets of scale items, so we have three arguments. We also introduce the `return()` function. This is a special function that tells R what the output of the function is. Right now we're returning nothing (NULL), but we can put whatever we want in here and our function will spit that out at the end. But remember -- once we call `return()`, the function *ends*. So usually this comes at the very end.

<aside>*Note*: R has a default that is unlike most other programming languages. By default, if there is no explicit `return()` in a function, R will return the last expression evaluated in the function. So if your function does some complex calculation and then always returns the value it stored in a variable called "result", the last line of your function could just say `result` rather than `return(result)`. But to me, the explicit `return()` makes it much clearer what the function is doing. Whether you choose to use this is largely a matter of personal preference, but in this tutorial we'll use explicit return statements. Because that's what I like, and I'm writing this tutorial! So there!</aside>

Now, let's figure out what we want to put inside the function. For each set of scale items, we'll call the `alpha()` function to calculate the reliability. (For simplicity, we'll assume the "psych" package is always already installed and loaded.) We need to store that information, pull out just the Cronbach's alpha and nothing else, and then combine those alphas into a nice, neat table. I'd encourage you to try to figure this out on your own before you go any further. Give it a shot, see if you can figure out a way to do this, and then read on.

Okay, so let's try it out:


```r
alpha_table <- function(scale1, scale2, scale3) {
    # calculate reliabilities
    alpha1 <- alpha(scale1)
    alpha2 <- alpha(scale2)
    alpha3 <- alpha(scale3)
    
    # get just the output we want, under $total$raw_alpha
    output <- c('Scale 1'=alpha1$total$raw_alpha,
                'Scale 2'=alpha2$total$raw_alpha,
                'Scale 3'=alpha3$total$raw_alpha)
    return(output)
}
```

Note that to find just the Cronbach's alpha, we need to use the dollar sign notation to get the right piece of the output. I usually find it helpful to use the `str()` function -- try `str(alpha(select(data, MTS_1:MTS_9)))`, and it will show you exactly what the `alpha()` function outputs. You can also generally find this information in the documentation for functions.

Now, let's try it out! We'll ask for the reliability of our three maximizing subscales, which we had calculated earlier. Make sure that you load the "psych" package first!


```r
library(psych)
```

```
## Warning: package 'psych' was built under R version 3.2.5
```

```r
MTS_scale <- select(data, MTS_1:MTS_9)
MI.AS_scale <- select(data, MI_23:MI_34)
MI.DD_scale <- select(data, MI_11:MI_21, MI_22.R)

alpha_table(MTS_scale, MI.AS_scale, MI.DD_scale)
```

```
##   Scale 1   Scale 2   Scale 3 
## 0.8366968 0.9094313 0.9107741
```

As you can see, we can call our newly-created `alpha_table()` function in the same way that we could with pre-existing R functions. And we get a nice little vector indicating what the Cronbach's alpha is for each of the three scales. Once the function is created, the hard work is done, and you can use it over and over again, whenever you want!

### Loops

Loops are another key feature of programming. Like the name suggests, loops are a way of repeating a block of code multiple times. This can have numerous uses&mdash;for example, if you have multiple variables in a dataset, you could perform the same functions over each of them one by one; or if you have data in multiple files, you could create a list of the filenames, and then loop through each filename one by one to read and import the data.

The primary feature of the loop is the index. This is the part of the loop that changes from iteration to iteration. It also defines how long the loop will continue. Let's take a look at a simple loop:


```r
for (i in 1:10) {
    print(i)
}
```

```
## [1] 1
## [1] 2
## [1] 3
## [1] 4
## [1] 5
## [1] 6
## [1] 7
## [1] 8
## [1] 9
## [1] 10
```

Here we can see a few things. First, we define the name of our index variable (it's a common convention to use the symbol `i`, though if you have a more descriptive name, you should definitely prefer that). We also give the loop the parameters of the index: We define a sequence from 1 to 10, which means the first time through the loop, `i` will equal 1, the second time it will equal 2, and so on. Given this, we should expect our loop to run 10 times.

The other important part of the loop is the actual code block, which defines the code that gets run repeatedly. Here, it's a simple `print()` function that prints out our index variable. Not really the most amazing thing ever. But you can run any block of code inside a loop. The only constraint is that the code is the same, other than the index variable changing. Thus, it is often helpful to use conditional statements, like so:


```r
for (i in 1:10) {
    if (i <= 5) {
        print("loops are fun!")
    } else {
        print("yeah okay, we get it, it's looping")
    }
}
```

```
## [1] "loops are fun!"
## [1] "loops are fun!"
## [1] "loops are fun!"
## [1] "loops are fun!"
## [1] "loops are fun!"
## [1] "yeah okay, we get it, it's looping"
## [1] "yeah okay, we get it, it's looping"
## [1] "yeah okay, we get it, it's looping"
## [1] "yeah okay, we get it, it's looping"
## [1] "yeah okay, we get it, it's looping"
```

Here, even though the repeating code is identical, we use conditional statements to create different output depending on the value of the index.

Let's use a loop now to simplify the function we have been working on a little bit:


```r
alpha_table <- function(scale1, scale2, scale3) {
    scales <- list(scale1, scale2, scale3)
    output <- numeric(3)  # create an empty vector with 3 elements
    
    # calculate reliabilities
    for (i in 1:3) {
        alpha <- alpha(scales[[i]])
        
        # get just the output we want, under $total$raw_alpha
        output[i] <- alpha$total$raw_alpha
        
        names(output)[i] <- paste('Scale', i)
    }
    
    return(output)
}

alpha_table(MTS_scale, MI.AS_scale, MI.DD_scale)
```

```
##   Scale 1   Scale 2   Scale 3 
## 0.8366968 0.9094313 0.9107741
```

Okay, we're introducing a number of things here, so let's take it one step at a time. First, we take our scales, and put them into a list. (We haven't covered lists very much, but they are essentially a "catch-all" container that you can put anything into. Unlike a vector, lists can hold objects of different types.) That's just so we can loop through them more easily, using our index variable. Second, we set up the vector we will eventually output. Then, we create our loop: We have three scales, so our index variable is given a sequence from 1 to 3. Within the loop code block, we first calculate the reliability for the scale. Then we grab the information we want and insert it into our `output` vector. Lastly, we set the name for that vector element. This just makes the output a little nicer. Notice that the `names(output)[i]` allows us to set the name for that element in the `output` vector. Finally, once the loop is finished, we return the output vector.

This may seem like a big change for very little improvement. The output from this function compared to the original one is identical. But now let's examine one feature of functions that makes using a loop worth it.

### Functions Redux

If you've spent some time looking through any documentation for R functions, you may have come across the "dots" notation. For example, in the `data.frame()` documentation, it shows this: `data.frame(..., row.names = NULL, check.rows = FALSE, check.names = TRUE, stringsAsFactors = default.stringsAsFactors())`. What are those three dots all about?

The dots notation allows a function to accept an arbitrary number of arguments. In other words, it's a way to say, "I don't care whether you give me one thing or thirty things, I'll handle everything you give me." So for a data frame, you can pass in as many vectors as you want, and each of those vectors will become a column of the data frame. This is a really, really useful feature of R functions. For instance, if you were creating a function to calculate the mean of a set of numbers, you don't want to restrict the amount of numbers that can be passed in! And you certainly don't want to create a new function for each size of your number set. What you want to create is a function that says, "Give me as many numbers as you want, and I'll calculate the mean of them."

So that's what we want to do with our `alpha_table()` function. We don't want to limit ourselves to three scales. We want to say, "Give me as many scales as you want, and I'll calculate the reliability for each of them." So let's do that now:


```r
alpha_table <- function(...) {
    scales <- list(...)
    output <- numeric(length(scales))  # create an empty vector the size we need it
    
    # calculate reliabilities
    for (i in 1:length(scales)) {
        alpha <- alpha(scales[[i]])
        
        # get just the output we want, under $total$raw_alpha
        output[i] <- alpha$total$raw_alpha
        
        names(output)[i] <- paste('Scale', i)
    }
    
    return(output)
}
```

First, note that we've replaced the arguments to our function with the three dots. Now we can accept any number of scales! Next, we take whatever is in those dots and put them into a list. We also create our `output` vector as before, but now we don't know exactly how big to make that vector. But that's okay! We just need to use `length(scales)`, which will tell us exactly how many scales were passed in each time the function is run. We also use the same thing for the index variable in our loop: Instead of a preset sequence, we now use `1:length(scales)`, which just means "start at 1, and count up to whatever the number of scales is."

And then that's it! The rest of the code is exactly the same. Now our function can accept an arbitrary number of scales, and because we've put in a loop, we don't have to hardcode everything. This makes our function very flexible. Let's try it out with five scales:


```r
MTS_scale <- select(data, MTS_1:MTS_9)
MI.AS_scale <- select(data, MI_23:MI_34)
MI.DD_scale <- select(data, MI_11:MI_21, MI_22.R)
Prom_scale <- select(data, RFQ_1.R, RFQ_3, RFQ_7, RFQ_9.R, RFQ_10, RFQ_11.R)
Assess_scale <- select(data, RM_2.R, RM_6, RM_7, RM_9, RM_10.R, RM_11, RM_15, RM_19, RM_20, RM_22, RM_27.R, RM_30)

alpha_table(MTS_scale, MI.AS_scale, MI.DD_scale, Prom_scale, Assess_scale)
```

```
##   Scale 1   Scale 2   Scale 3   Scale 4   Scale 5 
## 0.8366968 0.9094313 0.9107741 0.6755077 0.8146225
```

Perfect! This works just how we want it to. Try it out yourself, passing in a different number of scales each time.

<aside>For those of you familiar with other programming languages, you may be wondering if R has other types of loops other than the `for` loop. Indeed it does. It also has a `while` loop which loops until the condition statement is FALSE; and a `repeat` loop, which simply repeats a code block until it receives the command to `break`. For further information, feel free to look up the documentation for each of these commands.</aside>

### The "apply" Family

For those of you who have never done any programming before, loops can sometimes be a challenging topic. Sometimes the idea of an index variable starts getting a little abstract. Fortunately, in R, there is another way of running code repeatedly, and sometimes this can be a little more intuitive. This is the "apply" family of functions. I call it a family because it has many members: `apply()`, `lapply()`, `sapply()`, `vapply()`, `tapply()`, `mapply()`, `rapply()`, `eapply()`... Yeah, it gets confusing. To be honest, half the time I still need to look up which is which. But all these functions have a similar purpose of "applying" a function repeatedly to something. Let's look at `lapply()` and `sapply()`, which are probably the most commonly used.

The `lapply()` function takes a group of things (like a vector, list, or data frame) and applies a function to each element in that group. It returns a list, with the output from each time the function was run. As an example, let's calculate the mean for some of the variables in our dataset:


```r
regret <- select(data, regret1:regret6)

lapply(regret, mean, na.rm=TRUE)
```

```
## $regret1
## [1] 3.048128
## 
## $regret2
## [1] 3.756757
## 
## $regret3
## [1] 3.284153
## 
## $regret4
## [1] 3.491892
## 
## $regret5
## [1] 3.429348
## 
## $regret6
## [1] 4.956989
```

First, we just created a subset of our data, just grabbing the variables we want. Then, we pass that data to `lapply()`, and also provide the name of the function we want to use, `mean`. The last argument of `lapply()` is our new friend, the three dots. All this does is passes along any arguments we want to give to the `mean` function&mdash;in this case, we gave it `na.rm=TRUE` so it will ignore missing values when calculating the mean. As our output, we get a list, of length 6, with each value being the mean of the variable. In other words, `lapply()` went through each variable in the data one by one, and ran the `mean()` function on it. Pretty neat!

The `sapply()` function is very similar to `lapply()`&mdash; the only difference is that `sapply()` simplifies the result if possible. What do I mean by "simplify"? Well, `lapply()` *always* outputs a list. But as you can see in the example above, there's no reason we needed a list for our output. We just get six numbers, which could easily have been put in a numeric vector. So `sapply()` does the same thing as `lapply()`, but will simplify it to a numeric vector. If it can't simplify to a vector, it will try simplifying to a matrix. If it can't do either, it keeps it as a list. Let's compare the following:


```r
lapply(regret, mean, na.rm=TRUE)
```

```
## $regret1
## [1] 3.048128
## 
## $regret2
## [1] 3.756757
## 
## $regret3
## [1] 3.284153
## 
## $regret4
## [1] 3.491892
## 
## $regret5
## [1] 3.429348
## 
## $regret6
## [1] 4.956989
```

```r
sapply(regret, mean, na.rm=TRUE)
```

```
##  regret1  regret2  regret3  regret4  regret5  regret6 
## 3.048128 3.756757 3.284153 3.491892 3.429348 4.956989
```

```r
lapply(regret, quantile, na.rm=TRUE)
```

```
## $regret1
##   0%  25%  50%  75% 100% 
##    1    2    3    4    7 
## 
## $regret2
##   0%  25%  50%  75% 100% 
##    1    2    4    5    7 
## 
## $regret3
##   0%  25%  50%  75% 100% 
##    1    2    3    5    7 
## 
## $regret4
##   0%  25%  50%  75% 100% 
##    1    2    3    5    7 
## 
## $regret5
##   0%  25%  50%  75% 100% 
##    1    2    4    5    7 
## 
## $regret6
##   0%  25%  50%  75% 100% 
##    1    4    5    6    7
```

```r
sapply(regret, quantile, na.rm=TRUE)
```

```
##      regret1 regret2 regret3 regret4 regret5 regret6
## 0%         1       1       1       1       1       1
## 25%        2       2       2       2       2       4
## 50%        3       4       3       3       4       5
## 75%        4       5       5       5       5       6
## 100%       7       7       7       7       7       7
```

In the first comparison, `lapply()` produces a list, while `sapply()` produces a numeric vector. In the second comparison, `lapply()` again produces a list, while `sapply()` produces a numeric matrix. But in both cases, we get the same *information*, just in a different format.

Keep in mind, now that we know how to create functions, that we can also pass along our own custom-made function to `lapply()` or `sapply()`. So for example, let's say we wanted to standardize our "regret" variables. The process involves taking each of the participants' responses, subtracting the mean, and then dividing by the standard deviation. (In reality, we could use the `scale()` function, but let's pretend that didn't exist.) We could come up with a function to do that, and then run it on each variable separately, but we can also use `lapply()` to run it on all the variables at once.


```r
standardize <- function(var) {
    mean <- mean(var, na.rm=TRUE)
    sd <- sd(var, na.rm=TRUE)
    
    new_var <- (var - mean) / sd
    return(new_var)
}

regret_std <- as.data.frame(lapply(regret, standardize))

head(regret_std)
```

```
##      regret1    regret2    regret3    regret4    regret5     regret6
## 1  1.1556802  0.7050931  1.0278135  0.8308447  0.3253205  0.02911771
## 2 -1.2126727 -1.5634674 -1.3682359 -1.3728295 -1.3849357  0.70610452
## 3  2.3398566  1.2722333  2.2258382  1.9326818  2.0355766 -1.32485590
## 4 -0.6205844 -0.9963273 -0.7692235 -0.8219109 -0.8148503  0.70610452
## 5  1.1556802  1.2722333  1.0278135  1.3817633  0.8954058  0.02911771
## 6 -0.6205844 -0.9963273 -0.7692235 -0.2709924 -0.2447649  0.70610452
```

So what did we just do here? We created a function that standardizes our variable. (Note that because `var` is a vector, we can very easily subtract the mean and divide by the SD for each individual element.) Then we use `lapply()` to apply our new function to our data frame. Note that the output of `lapply()` will produce a list, but we can easily convert that back to a data frame using the `as.data.frame()` function. (In general, if you want the output to be a data frame, getting a list as the output is the easiest conversion process.) Now we've got ourselves a data frame where all our regret variables are standardized!

### The "plyr" Package

Hopefully you can see how useful the "apply" family of functions is. Being able to really easily run the same function over a vector, data frame, or list is very handy. Because of this usefulness, there is a package called "plyr" that does an even better job than the "apply" functions. The basic idea is still the same (run a function over a group of data), but the inputs and outputs are more consistent, it's a little more useful when dealing with data frames, and the logic is a little better-defined. The logic of "plyr" is the split-apply-combine process. In other words, you **split** your data apart using some criteria, then you **apply** a function to each of those new split groups, then finally you **combine** the results back together.

Let's use a concrete example to make this clearer. Here's a common task: In our study, participants were in one of three conditions: Promotion, Assessment, or Control. What we might want to know are the within-group means and SDs of some variable(s) in our dataset. In other words, we don't want to know the overall mean of the variable, but the mean for participants within each of our three conditions. Let's take a look at one variable, "frustration". We want to know what the means and SDs are for frustration within each condition. So what we need to is **split** our data by condition, **apply** a function that calculates the mean and SD, and then **combine** that data back together to make it easy for us to use it. The "plyr" package makes that process really simple.

The package has a number of functions, but mostly they are designated by *inputs* and *outputs*. Most often, we deal with data frames and lists, so I'll focus on those. (For more information, see [this paper](https://www.jstatsoft.org/article/view/v040i01) by the package author.) The first letter of the function designates the input you are giving it: "d" for data frame, "l" for list. The second letter of the function designates the output you want: "d" for data frame, "l" for list, and "_" if the output is discarded. (You might discard the output if, say, the function is also creating a graph, or saving a file, and you don't need the output in your code as well.) So, if you are inputting a data frame, and want a data frame back, you would use `ddply()`. If you start with a list, and want a data frame as output, use `ldply()`. So, in our case of within-group means and SDs, we start with a data frame, and we want a data frame back. Let's see, then, how we do that. (Note that if you have not yet installed the "plyr" package, you'll need to do that first.)


```r
library(plyr)
```


```r
means_sds <- function(df) {
    return(c(mean=mean(df$frustration, na.rm=TRUE), sd=sd(df$frustration, na.rm=TRUE)))
}

ddply(data, .(condition), means_sds)
```

```
##    condition     mean       sd
## 1  Promotion 2.857143 1.624978
## 2 Assessment 2.822581 1.722790
## 3    Control 3.163934 1.762388
```

First, we create a custom function that calculates what we want. We're interested in the "frustration" variable, so just to keep things simple, I've hard-coded that into the function, but we could easily generalize this to handle an arbitrary number of variables in the dataset.

Next, we use our `ddply()` function. Our first argument is the data frame we are using. The second argument looks a little strange: The argument is used to specify the variable(s) to split the data frame on, but there's a weird dot and parentheses around it. This is actually a plyr function that basically allows you to pass in the *name* of the variable for `ddply()` to use. You could also just put it in quotation marks so the name is passed in as text. Finally, the third argument specifies the function that `ddply()` will use.

So let's review: We **split** the data frame called "data" by the variable called "condition". Then we **apply** the `means_sds()` function to each group that we created by splitting the data. (In this case, we end up with three groups, one for each condition.) Finally, once we get the output from the function, `ddply()` **combines** it back together into a data frame for us. We end up with a nicely formatted data frame, with the mean and SD for each condition. Here it looks like our control condition was slightly more frustrated by the task, but also showed a higher SD as well. This could indicate some slight skew that we might want to take a look at in more detail. (Hint: The `describe()` or `describeBy()` functions in the "psych" package would work well for this task.)

### A Final Note about Loops in R

Loops, the "apply" functions, and "plyr" can be valuable tools in the toolbelt of an R user. But the downside is that they can be slow. In contrast, R is exceptionally good at doing *vector operations*. For instance, here are two ways of doing the same thing:


```r
vect <- 1:10

# using vector operations
vect - 3
```

```
##  [1] -2 -1  0  1  2  3  4  5  6  7
```

```r
# using loops
out <- numeric(length(vect))
for (i in 1:length(vect)) {
    out[i] <- vect[i] - 3
}
out
```

```
##  [1] -2 -1  0  1  2  3  4  5  6  7
```

Obviously, not only is the vector operation easier to understand (and more concise!), but it will also operate orders of magnitude more quickly. With a vector this size, you won't notice a difference in performance, but for larger operations, the difference can be quite noticeable. As a short example:


```r
vect <- 1:10000000

# using vector operations
system.time(out1 <- vect - 3)
```

```
##    user  system elapsed 
##    0.06    0.00    0.06
```

```r
# using loops
out2 <- numeric(length(vect))
system.time(
    for (i in 1:length(vect)) {
        out2[i] <- vect[i] - 3
    }
)
```

```
##    user  system elapsed 
##   15.95    0.05   16.23
```

The `system.time()` function tells us how long R took to run a piece of code, in seconds. (The "elapsed" part is most relevant.) On my machine, it took less than a second to run the vector operation; it took 12.8 seconds to do the same thing using a loop. Using `lapply()` isn't much different:


```r
test_func <- function(x) {
    return(x - 3)
}

system.time(out3 <- lapply(vect, test_func))
```

```
##    user  system elapsed 
##   28.85    0.16   29.16
```

These are simple examples, but the general principle is this: **Always, always, always prefer a vector operation over a loop/apply().** If you can do something with vector operations, do it. It's not always possible to do so, but often it can be. If you find yourself thinking about using a loop or an "apply" function, take a step back for a moment and ask yourself if you could do the same thing using a vector operation. If your data is gigantic, make sure you're extra thoughtful about using your loops. Again, it is not always possible to replace loops with vector operations, but it's worth a careful thought.

### Conclusion

In this lesson, we've taken a whirlwind tour through some of the basic programming structures of R. Functions are a critical component of using R, and creating your own user-defined functions lets you easily create abstracted tools that you can reuse whenever you want. Loops are also a critical feature, letting you create code designed to be used repeatedly. Loops can save you the work of typing out more than you need to (which some would call laziness, but I call efficiency!), and when combining loops and functions together, you can really expand the power of your data analysis.

As a final note, I should say that those who primarily use R for interactive data analysis (rather than creating self-contained programs to be run all at once) can easily fall into a habit of just cutting and pasting code over and over. Certainly, space isn't limited, and if you want to graph seven analyses, you can simply cut and paste the code to graph it and then make the necessary changes for each iteration. But this fails to utilize the true power of R. If you find yourself doing a common task over and over again, think about whether a function or a loop could be useful. Often, researchers have tasks they do frequently. These tasks may be idiosyncratic to your data (meaning perhaps no one has created a function to do it for you), but they may still be something you do all the time. If that's the case, you can reap long-term rewards by spending a little extra time up front creating a function for yourself. If you end up with a group of similar functions, you might consider taking the extra time to create a package, to make the process even simpler. (Just load the package, and all the functions are loaded and ready to go.) For more information about creating packages, see this [online book](http://r-pkgs.had.co.nz/). The bottom line, though, is this: The power of R is that it is a full-fledged programming language. Learning to program effectively can take time and practice, but to the extent that you treat it like a programming language, you can make yourself into a more efficient and effective researcher.

In the next lesson, we'll dive into the world of graphing in R. Stay tuned for beautiful visuals to go along with your data!
