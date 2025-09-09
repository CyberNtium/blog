---
date: 2025-09-02
title: "BC2406 Session 2.3: Fundamental Data Exploration"
type: "studylogs"
---

# Section 1

Create a vector (combination) of baby's age in months, use any arbitrary number  
`age <- c(1,3,5,2,11,9,3,9,12,3)`

Create a corresponding vector of baby's weight in kg  
`weight <- c(4.4,5.3,7.2,5.2,8.5,7.3,6.0,10.4,10.2,6.1)`

To calculate the average weight of all babies, we can use the `mean()` function:  
`mean(weight)`  
Output: 7.06

To calculate the standard deviation of weight of all babies, we can use the `sd()` function:  
`sd(weight)`  
Output: 2.077498  

Now, let's see the correlation between age and weight using the `cor()` function:  
`cor(age, weight)`  
Output: 0.9075655  
Correlation essentially measures the linear relationship between any given variables:
-  1 = perfect positive linear relationship
- -1 = perfect negative linear relationship
-  0 = no linear relationship between given variables  

In our case, age has a correlation score of ~0.91 with weight, which is quite high. Logical reasoning aside, the data is just random numbers, so it does not really mean anything. But given a real IRL dataset, we can use `cor()` to figure out some meaning behind some variables.

---
Let's now plot a scatterplot with x-axis = age and y-axis = weight:  
`plot(age, weight, col = "red", main = "Plot of Baby's Age Against Weight)`

{{< figure src="https://i.imgur.com/iY4nVk0.jpeg" alt="babyWeightAge" class="center" >}}  

---
Let's now create a sequence:  
`id <- seq(1, 10)`  
`seq()` creates a sequences _(duh)_ starting from the 1st number you input and ends at your 2nd number.  
Output:   1  2  3  4  5  6  7  8  9 10

We can change the sequence by modifying the code:  
`id2 <- seq(1, 10, by = 2)`  
By adding the "by" and an arbitrary number, we can create a sequence with different increments, in this case would be increment of 2 starting from 1 and ending at 10
Output:  1 3 5 7 9

---
Let's now create a DataFrame dataset with 3 columns:  
`baby_data <- data.frame(id, age, weight)`

> What exactly is a data frame? 

A _data frame_ is the fundamental data structure in R, which is used to store data in a table format, similar to what you see in Excel spreadsheets or just a normal table in general.
- Columns: The columns in a data frame area called **_variables_**. In our case here, the variables are `id`, `age` and `weight`
- Rows: The rows in a data frame are called **_observations_**. In our case here, each row represents a specific baby with a specific ID, age and weight.  

Next, we create new variable names for the three columns in baby_data:  
`names(baby_data) <- c("ID", "Age", "Weight")`

We can then export this R data set into a CSV file. The file will be saved in your working directory.  
`write.csv(baby_data, file = "baby.csv")`

Additionally, if you don't want R to add row names to the Excel, we can do:
`write.csv(baby_data, file = "babyNoRowNames.csv", row.names = FALSE)`

---
# Section 2
{{< notice note >}}
_These excel files are from this particular NTU module. I'm not very sure about the copyrights about these issues, so I don't feel particularly comfortable sharing them online. I'm probably just going to post a screenshot._ :3
{{< /notice >}}

First off, let's import the baby data set and store as baby2_data:  
`baby2_data <- read.csv("baby2.csv")`

{{< figure src=/images/baby2.png  alt=baby2  class="center" >}}

Let's find the mean of the weight:  
`mean(baby2_data$Weight)`  
Output: NA  

Interestingly, if there exists NA values inside the dataset itself, we cannot simply use the mean function on it because all output will end up as "NA". However, we can modify the function to include a useful line `na.rm`:  
`mean(baby2_data$Weight, na.rm = TRUE)`  
Output: 7.788088

The difference here is that `na.rm` means removing NA columns, thus we can successfully compute the mean.  

This also works for standard deviation:  
`sd(baby2_data$Weight, na.rm = TRUE)`

---
We can find out how many columns contain NA values:  
`colSums(is.na(baby2_data))`  

`colSums` = the sum of columns  
`is.na` = something that has NA values  

Another simply way is to use the handy `summary()` function:  
`summary(baby2_data)`  
Output:
```text
       ID              Age             Weight      
 Min.   :   1.0   Min.   : 1.000   Min.   : 4.400  
 1st Qu.: 250.8   1st Qu.: 4.000   1st Qu.: 6.300  
 Median : 500.5   Median : 7.000   Median : 7.900  
 Mean   : 500.5   Mean   : 6.613   Mean   : 7.788  
 3rd Qu.: 750.2   3rd Qu.:10.000   3rd Qu.: 9.300  
 Max.   :1000.0   Max.   :40.000   Max.   :11.000  
                                   NA's   :1      
```