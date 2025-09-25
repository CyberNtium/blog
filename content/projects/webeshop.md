---
title: "Web E-shop Project - BagusDrip - Part 1"
date: 2025-08-31
tags: ["IM2073", "Web Dev", "Java", "MySQL"]
type: "projects"
---
# OrderServlet.java
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
```Java
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
```Java
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
```Java
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
```Java
response.setContentType("text/html");
PrintWriter out = response.getWriter();
```

`response` here as mentioned before, is the `HttpServletResponse` object, which represents the message we want to sent to the user. When we say `.setContentType()`, we are essentially setting a header for the body of the data that we want to sent. You can kind of think of it as a type of metadata that lists down the specification of the data that you want to send. For example, when you send a parcel to someone, you need to have the recipient's address, your address, stamp or whatever on the parcel. Those stuff is this header.  
And, in our case, our "data address" aka header is set to be a HTML document. Without this label, the browser will just pass through the raw HTML codes with all its syntaxes.  
`PrintWriter out = response.getWriter()` here is the content inside the parcel. We first declare a PrintWriter object called "out" and assign it to `response.getWriter()` which means getting a PrintWriter object from `response`.  
For the rest of the code, if we do `out.println("...")`, we are basically writing a line of text (in HTML) into this stream, and the browser will receive it and renders into the final web page.

#### 2.1.3: Connecting to MySQL Database
```Java
try (
    Connection conn = DriverManager.getConnection (
        "jdbc:mysql://localhost:xxxx/BagusDrip?allowPublicKeyRetrieval=true&useSSL=false&serverTimezone=UTC",
        "admin69", "new_password69420");
    Statement stmt = conn.createStatement();
)
```
We first declare a Connection object call conn and initialise it as `DriverManager.getConnection(jdbc stuff)`. This is so that every time we do `conn.whatever` we are performing something that is directly related to the mySQL database, retrieving products information for example. Now, for the long line inside its bracket, it's essentially the JDBC URL which provides all the information you need to connect to the database. Note that we are using a very terrible way to connect to the database for simplicity sake, please do not do this in a professional setting, unless you want to get punched by your manager.  

`Statement stmt = conn.createStatement()` Once we establish the connection with the database, this line will create a Statement object which is used to excecute static SQL statements, which we will touch on later.  


#### 2.1.4: Getting User Inputs
```Java
String custName = request.getParameter("cust_name");
String custEmail = request.getParameter("cust_email");
String custPhone = request.getParameter("cust_phone");

String[] productIds = request.getParameterValues("id[]");
String[] models = request.getParameterValues("model[]");
String[] prices = request.getParameterValues("price[]");
String[] categories = request.getParameterValues("category[]");
String[] quantities = request.getParameterValues("quantity[]");
```
`request.getParameter()` is for retrieving a single piece of information, returning the user's input as a single `String`. In this case, we want to get the user's name, email and phone.  
`request.getParameterValues()` is basically the same thing but for multiple values that share the same name. That's why this method returns an array of `String`s, which we can loop through each one individually.


#### 2.1.5: Checking for Empty Cart
```Java
if (productIds == null || productIds.length == 0) {
    out.println("<h3>No items in the cart. Please go back and add products.</h3>");
    out.println("<p><a href='cart.html'>Back to Cart</a></p>");
    return;
}
```

So, after getting the inputs from user, we first check if the cart is empty. The if statement checks for two things:  
- `productIds == null`: This checks whether the `productIds` array that we initialised earlier even exist at all
- OR
- `productIds.length == 0`: This checks whether the array exists but contains zero items.
If either of these conditions is true, there are no products to order, so we will run the code body of the if statement:
- `out.println("<h3>No items in the cart. Please go back and add products.</h3>")`
Remember earlier when we talked about HTML stuff, this is exactly what I am refering to. `out.println()` prints out the HTML code inside to be render for the webpage format.  
`<h3>` means heading 3, but basically a heading.  
`<p>` means paragraph. The browser will automatically add a space before and after the text to separate it from other stuff.  
`<a>` means anchor, which is used for hyperlink. Thus, you see we put the `cart.html` there to direct the user to their cart page.  


#### 2.1.6: Update the Database

#### Insert Statement
```Java
String insertSql = "INSERT INTO order_records (product_id, cust_name, cust_email, cust_phone, model, category, price, quantity) VALUES (?, ?, ?, ?, ?, ?, ?, ?)";
PreparedStatement pstmt = conn.prepareStatement(insertSql);
```

`String insertSql` is a SQL query. You probably notice the weird ? symbols at the end of the line, they are actually used as placeholders for the values that will be inserted.  
You probably also noticed why did we declare a Statement which is vaguely similar to `stmt`, which is `PreparedStatement pstmt`.

> What is the difference between PreparedStatement and normal Statement?

Both `PreparedStatement` and `Statement` run SQL queries. However, `Statement` is only best for simple queries that do not deal with user inputs as it executes a query by concatenating strings together. If a user enters any malicious code, that code can be executed by the sql database. This is the infamous, **SQL Injection**.  
`PreparedStatement` on the other hand, use the ? symbol as a placeholder, where they are *literal value* to be inserted into the SQL databases. So, for example, if I want to destroy your database, I type in `'); DROP TABLE products;--`, `PreparedStatement` will not see this as a code, but just some string, aka literal values. 

#### Processing Products
```Java
for (int i = 0; i < productIds.length; i++) {
    int productId = Integer.parseInt(productIds[i]);
    String model = models[i];
    float price = Float.parseFloat(prices[i]);
    String category = categories[i];
    int quantity = Integer.parseInt(quantities[i]);
    ...
```
Time for the leetcode practice to shine lol (I regret not doing more leetcode...)

The `for` loop starts with i = 0 and iterating through the `productIds` array and goes up to the total number of items in the cart which is `productIds.length - 1`.  We do not include the minus 1 because "<" symbol is not inclusive of the max value, which means the same thing as minus 1.

Since `request.getParameterValues()` from before only returns value in `String`s, we need to convert them into `int` or `float` to perform mathematical operation on them:
- `int productId = Integer.parseInt(productIds[i])`: `Integer.parseInt` essentially converts a string into an int
- `float price = Float.parseFloat(prices[i])`: Same thing but for float


#### Updating Product Inventory
{{< notice note >}}
_Note that there's some SQL table and database names that are not mentioned beforehand, just stick with it for now, I will make a separate blog dedicated to the SQL and JDBC server configuration_
{{< /notice >}}  


```Java
String updateSql = "UPDATE products SET qty = qty - " + quantity + " WHERE id = " + productId + " AND qty >= " + quantity;
int updateCount = stmt.executeUpdate(updateSql);
if (updateCount == 0) {
    out.println("<p>Error: Not enough stock for product ID: " + productId + " (Requested: " + quantity + ")</p>");
    continue;
}
out.println("<p>" + updateCount + " record updated for product ID: " + productId + "</p>");
```

Let's slow down for the first SQL line, which is an UPDATE statement to modify any existing record:
- `UPDATE products` - Specify which table we are modifying, in this case is the `products` table
- `SET qty = qty - quantity`- Decrease the `qty` column by the amount ordered
- `WHERE id = productId AND qty >= quantity` - Most important part. Only if the database qty has enough products, it will decrease by subtracting the requested quantity. Basically saying, if there is stock, let the customer add to their cart, if not, don't.

`stmt.executeUpdate(updateSql)` - This essentially executes the SQL line above. Since it returns an int, the number of rows that is modified, we store it as a int called updateCount.  

- `if (updateCount == 0)` basically means if there is no stock since no rows are modified.
- We go into the if statement, and print out the no stock message. Recall that we will return in HTML format, so remember your `<p>` encapsulating your print statement in the middle.
- `continue` allows the for loop to stop the current iteration and go to the next item (productId)


{{< notice warning >}}
_Note that as I mentioned few sections ago, it is really not safe to use the normal `Statement` because it allows SQL injections. Instead, we should use `PreparedStatement` like what we did for the  `insertsql` String. I just did it this way because I forgor and only noticed it 2 month after I made the code. The more safe code will be below:_
```Java
String updateSql = "UPDATE products SET qty = qty - ? WHERE id = ? AND qty >= ?";
PreparedStatement updateStmt = conn.prepareStatement(updateSql);
updateStmt.setInt(1, quantity);
updateStmt.setInt(2, productId);
updateStmt.setInt(3, quantity);
int updateCount = updateStmt.executeUpdate();
```
...and rests are the same.
Could I have deleted the one above? Probably. But it's better to reflect on my mistakes :3
{{< /notice >}}  


#### Insert Order Records
```Java
pstmt.setInt(1, productId);
pstmt.setString(2, custName);
pstmt.setString(3, custEmail);
pstmt.setString(4, custPhone);
pstmt.setString(5, model);
pstmt.setString(6, category);
pstmt.setFloat(7, price);
pstmt.setInt(8, quantity);
int insertCount = pstmt.executeUpdate();
out.println("<p>" + insertCount + " record inserted into order_records for product ID: " + productId + "</p>");
```

The final part of the insufferably long `for` loop statement.  
- The `pstmt` from 1 to 8 is where we fill in the placeholders "?" for the `insertSql` query 3 sections ago. Essentially, 1 is pointing to the first "?", and 2 points to the second "?" and so on. Make sure to use the correct data type, int or string or whatever
-  Final step is `int insertCount = pstmt.executeUpdate()`:
- - `pstmt.executeUpdate()` sends the complete INSERT query to the database to do its thing
- - Since the method returns the numbers of rows, we store it as an int. Since we are incerting a single record for each products, this value would be "1" if the insertion did go through.
- Finally, we print an HTML mesage to the user telling them that they have succefully added some product id into their cart


#### Confirmation
```Java
response.sendRedirect("orderconfirm.html");
```
This line redirects the user to a confirmation page named "orderconfirm.html" that probably just prints out something like "Thank you for purchasing" or whatever.


#### 2.1.7: Error Handling
```Java
catch (SQLException ex) {
            out.println("<p>Error: " + ex.getMessage() + "</p>");
            out.println("<p>Check Tomcat console for details.</p>");
            ex.printStackTrace();
...
```
`SQLException` is a type of exception thrown by Java when the database operation goes to shit. We reference it to a variable call ex in this case:
- For the user, we print out whatever is from the ex using `ex.getMessage()` and tell the user something's gone awry with SQL
- For us, we do `ex.printStackTrace()` as this prints a detailed trace of the error in the Tomcat console so we can go and find whatever error it is.  

## Finalised Code
```Java
// To save as "BagusDrip\WEB-INF\classes\OrderServlet.java"
import java.io.*;
import java.sql.*;
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import jakarta.servlet.annotation.*;

@WebServlet("/driporder")
public class OrderServlet extends HttpServlet {
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        try (
            Connection conn = DriverManager.getConnection(
                "jdbc:mysql://localhost:xxxx/BagusDrip?allowPublicKeyRetrieval=true&useSSL=false&serverTimezone=UTC",
                "username", "password12334");
            Statement stmt = conn.createStatement();
        ) {
            String custName = request.getParameter("cust_name");
            String custEmail = request.getParameter("cust_email");
            String custPhone = request.getParameter("cust_phone");

            String[] productIds = request.getParameterValues("id[]");
            String[] models = request.getParameterValues("model[]");
            String[] prices = request.getParameterValues("price[]");
            String[] categories = request.getParameterValues("category[]");
            String[] quantities = request.getParameterValues("quantity[]");

            if (productIds == null || productIds.length == 0) {
                out.println("<h3>No items in the cart. Please go back and add products.</h3>");
                out.println("<p><a href='cart.html'>Back to Cart</a></p>");
                return;
            }

            // Update order_records to include quantity
            String insertSql = "INSERT INTO order_records (product_id, cust_name, cust_email, cust_phone, model, category, price, quantity) VALUES (?, ?, ?, ?, ?, ?, ?, ?)";
            PreparedStatement pstmt = conn.prepareStatement(insertSql);

            for (int i = 0; i < productIds.length; i++) {
                int productId = Integer.parseInt(productIds[i]);
                String model = models[i];
                float price = Float.parseFloat(prices[i]);
                String category = categories[i];
                int quantity = Integer.parseInt(quantities[i]);

                // Update the quantity in the products table
                String updateSql = "UPDATE products SET qty = qty - " + quantity + " WHERE id = " + productId + " AND qty >= " + quantity;
                int updateCount = stmt.executeUpdate(updateSql);
                if (updateCount == 0) {
                    out.println("<p>Error: Not enough stock for product ID: " + productId + " (Requested: " + quantity + ")</p>");
                    continue;
                }
                out.println("<p>" + updateCount + " record updated for product ID: " + productId + "</p>");

                // Insert into order_records
                pstmt.setInt(1, productId);
                pstmt.setString(2, custName);
                pstmt.setString(3, custEmail);
                pstmt.setString(4, custPhone);
                pstmt.setString(5, model);
                pstmt.setString(6, category);
                pstmt.setFloat(7, price);
                pstmt.setInt(8, quantity);
                int insertCount = pstmt.executeUpdate();
                out.println("<p>" + insertCount + " record inserted into order_records for product ID: " + productId + "</p>");
            }

            response.sendRedirect("orderconfirm.html");

        } catch (SQLException ex) {
            out.println("<p>Error: " + ex.getMessage() + "</p>");
            out.println("<p>Check Tomcat console for details.</p>");
            ex.printStackTrace();
        }
        out.close();
    }
}
```

To be continued in Part 3...