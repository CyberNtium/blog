---
title: "Web E-shop Project - BagusDrip"
date: 2025-08-31
tags: ["IM2073", "Web Dev", "Java", "MySQL"]
type: "projects"
---
{{< figure src="/images/bagusdrip-logo.png" alt="alt text" width="200px" >}}

_This was a academic project on developing a full-stack web-app using Java Servlets on Tomcat and MySQL as backend. The course code is IM2073._  
_I give thanks to Professor Chua Hock Chuan for guiding us and providing useful information on said project with his wonderful website [here](https://www3.ntu.edu.sg/home/ehchua/programming/index.html)._  

## 1: Directory of Java Servlets
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

## 2: Server-Side Logic using Java Servlets 

### 2.1: OrderServlet.java

#### 2.1.1: Libraries
First of all, we need to import some Java libraries, namely these ones:
```text
import java.io.*;
import java.sql.*;
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import jakarta.servlet.annotation.*;
```
`java.io.*` handles the classic input output operations, namely the PrintWriter class which we will get to later.  

`java.sql.*` provides the Java Database Connectivity (JDBC) classes for connecting to databases and other SQL operations. We will see something like `Connection`, `Statement`, `PreparedStatement` and `SQLException` later.  


`jakarta.servlet.*` is the core package, which is just part of the Jakarta Servlet API that provides the servlet framework. Classes used are `Servlet` and `ServletException`.

`jakarta.servlet.http.*` is the http package of jakarta servlet. This provides the HttpServlet class which we need to extend every class to enable HTTP request handling. Classes like `HttpServletRequest` and `HTTPServletResponse` are used to process incoming requests and send responses. 

Finally, `jakarta.servlet.annotation.*` is required for a line above our public class which we are going to see later, aka `@WebServlet`

#### 2.1.2: The Servlet Class

#### Defining Our Class and Annotation
```text
@WebServlet("/driporder")
public class OrderServlet extends HttpServlet {
    ...
    ...
    ...
}
```
`@WebServlet("/driporder")` maps this particular servlet to the url that happens to contain `"/driporder"`, something like this: `http://localhost:xxxx/driporder`.   
Essentially, after we host site on Tomcat and when a request comes in with the URL path containing `/driorder`, the `OrderServlet` class will execute.   
If you are familiar with SpringBoot, it is very similar to the `@GetMapping` method.  

Next up is defining the public class of OrderServlet, which I assume everyone knows about at this point. More interestingly though, let's look at the extend:
`public class OrderServlet extends HttpServlet`  
As mentioned in the libraries section, `HttpServlet` is a pre-defined class from the http package. We **NEED** to include this so that we can allow `doGet` and `doPost` stuff later.  

And finally, we can go into the first public class and see what's inside:  
spoilers more class lmao 😞  

#### doGet Request and Response
```text
@Override
public void doGet(HttpServletRequest request, HttpServletResponse response)
        thorws ServletException, IOException {

        }
```
So, Java OOP 101 here: We are overriding a method from the parent class which in this case would be the `HttpServlet`, and making our own version. 
> Interestingly, I didn't know this when I was studying, this is a very healthy practice for programmers because in the case where you did not include `@Override` and misspelt `doGet()` with `doget()` or whatever, Java will just create a new method which is not very good. With the `@Override`, Java will check if the parameter is the same as your parent's and gives you an error if it exists. 

Next, we define a public method named doGet, which will be called by Tomcat when an HTTP GET request is received using any URL with `/driporder`. 

> What is an HTTP Get request?

It is simply a way of saying _I want to get or retrieve some data from a server_. This can be:
- Clicking a HyperLink on the web page
- Refreshing the page
- Typing a URL and pressing enter

Regardless, all of these are just getting data, nothing will be changed.

> What is HttpServletRequest?

The request object we declare is all about the incoming request from the user. Later in the main body, we will see `request.getParameter("something")` which basically just means we are getting something from the user like `ID` or `Name`

> What is HttpServletResponse?

The response's role is to send data back to the user's browser, opposite to request. For example, redirecting the user to somewhere using `response.sendRedirect("link")` to rickroll them or something lol. Of course, we can also send HTML code using `response.getWriter()` to tell the user something.

Finally the `thorws ServletException, IOException` error detection. I will be very honest with you, I still don't really understand what this line means, all I can grasp is if there's some error, something will show up the user side as HTTP 500 error or something. Probably need to update this section in the future lol. TODO


#### Configuring Responses
