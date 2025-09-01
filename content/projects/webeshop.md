---
title: "Web E-shop Project - BagusDrip"
date: 2025-08-31
tags: ["IM2073", "Web Dev", "Java", "MySQL"]
type: "projects"
---
{{< figure src="/images/bagusdrip-logo.png" alt="alt text" width="200px" >}}

_This was a academic project on developing a full-stack web-app using Java Servlets on Tomcat and MySQL as backend. The course code is IM2073._  
_I give thanks to Professor Chua Hock Chuan for guiding us and providing useful information on said project with his wonderful website [here](https://www3.ntu.edu.sg/home/ehchua/programming/index.html)._  

## Directory of Java Servlets
The first thing we do is to set up a clear directory in the Jakarta Servlet folder directory for our shop names and where we want to insert our java class codes.  
The usual convention goes like this:  

```text
WebAppName/
 ├── WEB-INF/
 │      ├── classes/
 │             └── JavaCode.class
 │── images/   
 │      └── image1.png
 │── index.html  
```
_Note that this is the convienent way that I was using when I did the project so it might not be "indsutry standard". Please forgive me~_

Regardless, as we can see, all your server related java class codes should live in the nested folder of "classes" and your UI/UX files like html is just in the "WebAppName" folder. The images folder is the asset folder that I used to store the images of the products of my webapp.

## Server-Side Logic using Java Servlets 