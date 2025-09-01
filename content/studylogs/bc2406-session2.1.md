---
title: "BC2016 Session 2.1: Fundamental Concepts"
date: 2025-09-01
type: "studylogs"
---
## Role of Visualization and Models

### Visualization
- Get easier understanding than analytics models
- Communicate/explain the results in an easier way
- Requires minimal training

### But is it okay to just depend on visualization? 
## Obviously no, but why?
Visualization at most show the relationship between 3 variables, preferably 2.  

3 Variables  
{{< figure src="/images/3variables.png" alt="RStudio" class="center" >}}

2 Variables  
{{< figure src="/images/2variables.png" alt="RStudio" class="center" >}}


Given that we are required to see what affects the sales of Amazon, there are definitely more than 3 variables, including price, shipping fee, consumer reviews, discount rate, return policy, competitors and etc.  
Thus, it is impossible to visualize all of these variables in our pathetic 3 dimensional space. :(  
This is where analytics models come into play.

## Analytics Models
Analytics Models are mathematical expressions, or representations of a system that provides insight in the form of prediction from raw data.  
The most common example would be the famous Linear Regression.  

### Linear Regression 
{{< figure src="/images/linearR.png" alt="RStudio" class="center" >}}

Of course, fundamentally, linear regression is still just a statistical model. It shows the relationship between the independant variables and the dependant variables. (we will get into more in Session 6)  
But, together with the aid of visualization, we get the best of both worlds. Analytics model in the form of the linear regression line, and visualization in the form of the scatterplot.  

### Conclusion? Analytics Models vs Visualization
It is impossible to separate the two. Best to work with both!  
(P.S. I can't find an image that doesn't look like the cooperate Google slop infographics. So just imagine more scatterplots and regression lines)

