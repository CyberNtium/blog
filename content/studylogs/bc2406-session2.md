---
title: "BC2406 Session 2: Analytics Introduction"
date: 2025-08-31
type: "studylogs"
---

# 1: Introduction to RStudio  

### 1.1: Install R and RStudio  
- Download base R from their official website here: [https://cran.r-project.org/](https://cran.r-project.org/)  
- Make sure to choose the version corresponding to your OS. I'm using Windows for this part. (I hate it)

{{< figure src="/images/baseR.png" alt="baseR" class="center" >}}

- Follow the instructions listed and download the correct file. For Windows, download the “base” file.  
- Run the installer and keep everything default, you can change it later if you want.  

### 1.2: Install RStudio Desktop  

- Install RStudio Desktop here: [https://posit.co/download/rstudio-desktop/](https://posit.co/download/rstudio-desktop/)  
- This is your IDE for R programming

- Again, find your own OS and download the correct file

{{< figure src="/images/RStudio.png" alt="RStudio" class="center" >}}

- Run the installer and keep everything default

### 1.3: RStudio Interface  

- When you first launched into Rstudio, you will see the following (might be different colour):

{{< figure src="/images/RStudioInterface.png" alt="RStudioUI" class="center" >}}

- Don’t worry if it is different from your professor’s or friend’s

- The big panel from the left is your console, where you can execute your code line by line and see the output. Try typing `3 + 3` and press enter

- The next panel at the top right is your environment tab. In this course, we will probably only use the environment and the history tab. The environment tab is when you initialise a variable or a dataset or a spreadsheet or an object, which we will touch on later. History just includes your previous codes.

- The next panel at the bottom right is where you can see your plotted graph/chart. You will also see detailed explanations for commands of R. Try typing `?print` and wait for the documentation to pop up

- Now, to see the 4th panel, you need to have a R script running. Either use an existing one, or create a new one. In our case, let’s create a new R script.

- File > New File > R Script

- Now you will see your 4th panel, the script panel, where you can type and edit in.

- Your next question will probably be: 
> What’s the difference between this and the console?

- Well, the script panel allows you to store multiple lines of codes for your next session of use. Meanwhile the console is only for immediate usage, it does not store any codes under any circumstances.  