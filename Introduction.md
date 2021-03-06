# Introduction
Jeff Hughes  
August 5, 2016  



# Learning R Tutorial: A Process-Focused Approach

## Introduction: The Middle Way

Welcome to the Learning R tutorial!

There are a huge number of tutorials out there for learning R, many of them available online for free. So why use this one? Well, when I first learned R, I was frustrated that there didn't seem to be a tutorial covering all I needed to know. Most tutorials can be categorized into one of two types:

* R taught from a programming angle, or
* R taught from a statistical angle.

The first type of tutorial covers the basic "building blocks" of R, such as variables, functions, and loops. However, most don't do a really good job of indicating how one uses those simple structures when dealing with complex datasets. It treats R as a programming language (which it is) without really giving the information that researchers would need to use it effectively. Researchers may say, "Yes, it's interesting that I can create logical vectors of TRUE and FALSE, but I don't deal with that type of data...so how does this help me?"

The latter type of tutorial tends to cover statistical tools (e.g., t-tests, regression, correlation) as a bunch of cut-and-paste codes; there is typically little explanation of how to flexibly translate that into something useful for your own research. For example, what happens when a particular functions wants a vector, but your data is in a list format? If you don't even understand what these structures are and how they work, you end up having to go back and learn all those basics anyway. This type of tutorial treats R like a statistical program (which it is) without really getting into any of the programming principles that make R useful and efficient.

So if you haven't guessed, my approach to this tutorial is to try to provide a "Middle Way" that incorporates both these strategies. It is written with researchers in mind, but is designed to show you the basic programming tools along the way that will help you get the most out of R. Quite frankly, if you don't learn the programming principles of R, you might as well use a different statistical program. The fact that R is a full-fledged programming language is what makes using R so powerful. But what we will do in this tutorial is teach these programming principles "along the way" as we try to analyze a set of data. By using this "process-focused" approach, we can see immediately why these basic building blocks are relevant! And hopefully we won't get bogged down in some of the arcane details of programming that aren't as relevant for researchers.

## Prerequisites

This tutorial already assumes that you have R installed on your machine. The most I'll say is that the installation can be found at the [Comprehensive R Archive Network (CRAN)](https://cran.r-project.org). I will also recommend that you install and use [RStudio](https://www.rstudio.com). This is a graphical interface for using R, and it is the most widely used way of interfacing with R. You can get by without it, but it makes using R much, much easier. Plus it just looks nice!

This tutorial also assumes that you have some knowledge of statistical concepts like regression, ANOVA, t-tests, etc. We won't be getting into anything too complex, but this is a tutorial for learning R, not a tutorial for learning statistics. I assume that if I mention the standard deviation, that you know what that is.

With all that out of the way, let's begin!

## The Problem at Hand

Our goal for the duration of this tutorial will be to analyze a dataset. It is available [here](PLACEHOLDER), and will be our overarching problem to be solved. We'll cover how to import the data into R, how to tidy it up, how to check the distributions of variables, and then how to analyze the data in such a way that answers the hypotheses we have about it. Along the way, I'll include additional information that is relevant, and include exercises for you to try on your own and check your answers.

The dataset was produced from a study I ran examining the idea of maximization. (It combines both real and fake data for the purposes of this tutorial.) Maximizers have typically been defined as individuals who seek the best option (vs. an option that is just "good enough") and search exhaustively through the alternatives in the decision to do so (Schwartz et al., 2002). Chronic maximizing has, in previous research, been linked with more negative outcomes, such as lower life satisfaction, greater depression, and greater regret (Schwartz et al., 2002; though see Diab et al., 2008). For this study, we were interested in looking at how particular motivational orientations might influence the extent to which individuals maximized on a choice task, and thus how much frustration and regret they would feel afterwards. In particular, we were interested in promotion focus, and assessment mode.

Promotion focus is a motivational orientation concerned with advancement and growth goals (Higgins, 1997). Promotion-focused individuals strive to attain ideals and are particularly concerned with approaching gains and avoiding non-gains (Scholer & Higgins, 2011; Zou & Scholer, 2016). However, promotion focus is also associated with a preference for eager (vs. vigilant) strategies to achieve goals (Scholer & Higgins, 2011). Eager strategies are approach-oriented strategies such as initiation of goals (Fuglestad, Rothman, & Jeffery, 2008), searching for matches vs. mismatches (Crowe & Higgins, 1997; Higgins et al., 2001), optimism (Grant & Higgins, 2003), and prioritizing speed over accuracy (Förster, Higgins, & Bianco, 2003). Thus, we predicted that promotion focus should be associated with wanting the best in the decision task, but not necessarily with looking through more options to find it. Thus, we predicted that promotion focus should not lead to higher frustration or regret with a decision.

Assessment mode is a motivational orientation concerned with evaluating and comparing entities, such as goals and means, in pursuit of doing the "right" thing (Higgins et al., 2003). It is also associated with an emphasis on careful and comprehensive evaluation. Assessors prefer full comparison decision strategies (Avnet & Higgins, 2003) and take time to measure and evaluate alternatives on their relative quality (Mauro, Pierro, Mannetti, Higgins, & Kruglanski, 2009). Thus, we predicted that assessment mode should show the typical pattern that maximizers have shown, with greater frustration and regret after making a decision.

### Our Study

In this study, we manipulated promotion or assessment by giving participants a writing task that either got them to think about their hopes and aspirations, or about times in their life when they engaged in comparison and evaluation. (These are fairly typical manipulations for priming promotion and assessment.) We also had a third condition for a control group in which participants did not write anything.

After this, we gave participants a decision task -- we showed them a table with information about a number of different cars, and asked them to imagine they wished to purchase a car, and to select which car they would prefer. We timed how long they took on this. Then we asked them a number of questions afterwards about their difficulty with the task, about the strategies they used to decide, and about the regret they felt. We also had established chronic measures of promotion focus, assessment mode, and maximizing.

### Hypotheses

So here's what we hypothesized:

1. The manipulation should have an effect on decision difficulty and on regret, such that (a) the assessment condition should result in greater difficulty and regret than the control condition, but (b) the promotion condition should not show differences from the control condition.

2. Chronic maximizing should also be associated with greater decision difficulty and regret (using the control condition only).

We will also do some other exploratory analyses in this tutorial in order to gain experience with different statistical methods. We'll ask and answer these questions as we go along.

### A Few Notes

Before we dive right in, it's important to note a few things about what you'll see in the coming lessons. Often, I will present R code, which will appear like so:


```r
paste("This is some", "sample code.", "Isn't R fun?")
```

```
## [1] "This is some sample code. Isn't R fun?"
```

In the grey box above, you can see the code that you can type, or copy and paste, into R yourself. When you see the ">" symbol in R, that means R is ready for you to give it your next command. Try typing the above code into R and press Enter. What you should see is the output shown in the white box above.

The hash marks on the left of the output in the white box above designate code comments. In R, comments are designated by a hash mark (#; or "hashtag" as the kids call 'em). Anything after the hash mark will be ignored by R. But they won't be ignored by people! Use comments often; it will greatly benefit your future self, when you go back in six months and wonder what you were trying to do. It will also benefit anyone else who reads your code, if you share it with others. I include hash marks at the start of my output here so that you can copy and paste that too if you want. That way, if you accidentally run that line, R will just ignore it anyway.

As a general note, when writing comments, try to be explanatory with your comments -- talk about what the purpose of the code is, rather than just describing exactly what it does. But when you're starting out with programming, lots of comments, of any kind, are good :)

If you are using RStudio, you can also open a new script file by clicking File > New File > R Script (or using the button in the toolbar). This will let you type all your code into a file that is easily saved and kept. R script files have the suffix ".R". I'd recommend putting the code you will learn about here into a script, so you can keep it and easily refer to it later. Once you have a script, you can run the entire thing all at once, or run selected lines, by clicking the "Run" button in the corner of the RStudio script pane (or using the drop-down button beside it). Or, you can also press Ctrl+Enter to run the selected line.

#### Help!

Finally, it is important before we begin to say a word about finding help in R. R is a powerful statistical tool, but sometimes it can be downright cryptic with its error messages. This means that knowing how to find help and documentation is important!

To find documentation about a particular function, you can type a question mark before the function name, like so: `?mean`. If you are running basic R, this will pull up a browser window. If you are running RStudio, it will pop up the documentation in the bottom-right pane (by default) -- definitely handy! The documentation for R functions is typically pretty good, and will give you a short description of what the function does, show you the various values you can use, provide some details about any special calculations that are important to know, tell you the type of output the function will provide, provide links to similar functions, and typically provide some examples of how to use the function. All helpful stuff!

There is also a wealth of information online about R functions, error codes, etc. Chances are, if you run into an error, typing the error into Google will provide you with some useful information about what's going on and what to try. There are some online communities that will also provide help, and you can ask questions: [Stack Overflow](http://stackoverflow.com) is a fantastic resource, and their answers will typically be at the top of Google search results as well. It's a great community that answers all sorts of programming questions, with the best answers getting voted to the top by others. The community on Reddit for [R stats](https://www.reddit.com/r/rstats) is also very helpful, and may sometimes be a little less intimidating for newcomers than Stack Overflow.

But ultimately, one thing I would like to impress upon you is that in R, because of the way it is designed, it is very difficult to really, permanently screw up your data. Unlike many other statistical programs, R imports data to your current R session, and then won't touch the data file again until you tell it to save it back. This means you can import it, play around all you want with it, and if you mess everything up, just re-import it and start again! So my advice to you, as you start learning R, is to play around as much as possible! If you think of an approach that might work, try it and see how it goes! If it doesn't work, just take a step back and try again. Be bold and adventurous -- that is how you will get the most out of your R experience!
