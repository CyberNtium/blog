---
title: "BC2016 Session 2.2: Introduction to R Programming Basics"
date: 2025-09-01
type: "studylogs"
---

# 1. Check and Set your Working Directory in R
_This was a very confusing part of RStudio for me when I first started since there are a lot of different ways to go about working directories. In our case here, what I will show is what worked for me in particular, so if you are not very comfortable with it, feel free to follow the professor’s method._

- There will always be one working directory (WD) during any R session.
- Your WD will be the path where you get your spreadsheets, R scripts or any dataset for that matter. 
- To check what is your current WD, type getwd() at your R console
- If you are using Windows, your WD is probably defaulted to your documents folder. Unless you are okay with that, let’s try to change that to the Unit 2 folder of this course for this showcase:
- - Type `setwd("your path address here")` and change the stuff inside to your own path
- - Unless you are very familiar with your paths, there is another way to do this. Simply open the Unit 2 folder like so:

{{< figure src="/images/rPath.png" alt="RStudio" class="center" >}}

- - Copy the top address bar, that’s your path name:

{{< figure src="/images/rAddress.png" alt="RStudio" class="center" >}}

- - Paste it into the brackets of `setwd()`
- - Change all the backslashes “ \ “ to forward slashes “ / “. Don’t forget the quotation sign. 
- - Finally you should have something like this:
- - - `setwd(“C:/Users/name/Desktop/…”)`
- - - Check with `getwd()` just in case


# 2: Getting Data into RStudio
- The reason we change our WD is to test this ection
- But first, let's get familiar with the _**Assignment Operator: `<-`**_

## 2.1: Assignment Operator

- As the name suggests, the assignment operator assigns a value to a name/object:  
` x <- value `  
` x <<- value `  
` value -> x `  
` value ->> x `  
- “x” is your variable name and “value” is the value you want to assign to the “x”
- Type `age <- 22`. You should see the code pop up in both the console and the environment panel
> What if you want to assign the same value to different variable names?  

`age1 <- age2 <- age3 <- 22`

- The assignment also works for an operation. For example: `sum <- 3+4`
- Type `sum` to see the sum. This works for every other mathematical operation, `+`, `-`, `x`, `/` and so on.


## 2.2: Naming Convention
- Like any other programming language that you are familiar with, R also has its own naming convention:  
`johnDoe`  
`john_doe`  
`john.doe`  

## 2.3: Vectors (Combination or whatever)
- Type `c()` with your values inside the brackets to create a vector:  
`c(1, 2, 3, 4, 5, 6)`  
`c(“john”, “david”, “mary”)`  
`c(TRUE, FALSE, TRUE)`  

## 2.4: Basic Data Types
- Type `class()` with your variable inside the brackets to check what type is your data
{{< figure src="/images/classR.png" alt="RStudio" class="center" >}}
[image source](https://www.w3schools.com/r/r_data_types.asp)
- For example:
- - `x <- TRUE`
- - `class(x)`
- - Output should be **_logical_**  

_yea I'm not sure why R decided to call boolean logical while literally every other languages out there uses boolean_