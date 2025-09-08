---
date: '2025-09-03T19:19:21+08:00'
title: 'BC2406 Session 4 - Plots and Other Stuff'
type: "studylogs"
---

# Visualization with ggplot2 package

`library(ggplot2)`  
`ggplot(data = ins.dt[STATE == 'NY'], aes(x = EARNINGS, y = INCOME)) + geom_points() + labs(title = "Figure 4.3: Income vs Earnings in NY")`

- This plot can be decomposed into 3 layers:
- - First Layer: Data and the XY plane
- - Second Layer: Scatter Points
- - Third Layer: Figure Title