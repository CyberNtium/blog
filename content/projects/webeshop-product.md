---
title: "Web E-shop Project - BagusDrip - Part 3"
date: 2025-09-10
tags: ["IM2073", "Web Dev", "Java", "MySQL"]
type: "projects"
---

### 2.2: ProductServlet.java

#### 2.2.1: Importing Libraries
```Java
import java.io.*;
import java.sql.*;
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import jakarta.servlet.annotation.*;
```

Same explanation like in Part 1, refer them for more [detail](https://cyberntium.github.io/projects/webeshop/)  
I'm just gonna speedrun through them like this:
- `java.io.*` - For input and output stuff like the PrintWriter thingie we use to send HTML code to the browser
- `java.sql.*` - For interacting with MySQL databases
- `jakarta.servlet.*` - servlet stuff, like literally anything that has the word servlet inside, like ServletException
- `jakarta.servlet.http.*` - HTTP request handling
- `jakarta.servlet.annotation.*` - Map to the URL

#### 2.2.2: Servlet Class
```Java
@WebServlet("/dripproduct")
public class ProductServlet extends HttpServlet {
```
- `@WebServlet("/dripproduct")` - This maps this particular servlet to the URL path of /dripproduct. For example, "www.realwebsite.com/dripproduct"
- `ProductServlet` extends to `HttpServlet` which we got from the library, to allow HTTP request like doGet and doPost

#### 2.2.3: doGet
```Java
@Override
public void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
```
- `HttpServletResquest request` - For methods that involves getting data from user
- `HttpServletResponse response` - For methods that involves sending response back to user, aka browser
- `throws ServletException, IOException` - Debugging purposes, sends an error log to the tomcat console if there's any errors with the servlet codes or input output codes

#### 2.2.4: Response to User
```Java
response.setContentType("text/html");
PrintWriter out = response.getWriter();
```
- `response.setContentType("text/html")` - Sets the browser response type to be HTML, meaning it would be expecting HTML
- `PrintWriter out = response.getWriter()` - Create a handy method to print out HTML codes using Printwriter, basically `out.println("blahblahblah")`


#### 2.2.5: HTML Code
#### 2.2.5.1: HTML Header
```Java
out.println("<!DOCTYPE html>");
out.println("<html lang="en">");
out.println("<head>");
out.println("<title>Product Details - BagusDrip</title>");
out.println("<link rel='stylesheet' href='styles.css'>");
out.println("</head>");
out.println("<body>");


```
- Did I mentioned setting the browser content type to HTML and printing them out using `out.println`? Cool
- Since this is essentially the same as just doing HTML, I'm just going to ignore the `out.println` statments for each line because it will be annoying to type out everything

This point onwards is more like a short HTML refresher:
- `<!DOCTYPE html>` - Declares this document to be HTML5
- `<html lang="en">` - Set default language to english. Fun fact, you can do just `<html>` and the code will still work. I'm not saying you should do it, just saying it still works
- `<head>` - Begins your head section to put your metadata
- `<title>Product Details - BagusDrip</title>` - Sets the title to, well, that
- `<link rel='stylesheet' href='styles.css'>` - Links the page to an external CSS stylesheet file called `styles.css`
- `</head>` - Closes your head section
- `<body>` - Beings your body section of the page, which contains the visible content
- 

#### 2.2.5.2: Header Logo
```Java
out.println("<div class='header-logo' style='position: absolute; top: 10px; left: 10px;'>");
out.println("  <a href='index.html'>");
out.println("    <img src='images/bagusdrip-logo.png' alt='BagusDrip' width='100'>");
out.println("    <h1>BagusDrip</h1>");
out.println("  </a>");
out.println("</div>");
```

- `<div class='header-logo' style='position: absolute; top: 10px; left: 10px;'>` - Let's unpack this:
- - A `div` is a container element in HTML. It does not bear any meaning on its own, but is used to group content with style
- - `class='header-logo` - Basically names the `div` "header-logo". A class lets you target it with CSS or javascript to style it 
- - `style='position: absolute; top: 10px; left: 10px;`:
- - - `style = "..."` is basically the inline CSS styling
- - - `position: absolute` means positioning this dic relative to its nearest positioned ancestor
- - - `top: 10px; left: 10px;` means placing it 10px from the top and 10px from the left from the ancestor  

I hate HTML because the syntax does not make sense when you read them, but to other people its very easy, I guess I'm just a bit dumb

- `  <a href='index.html'>` - Create a hyperlink that directs the user to the homepage which is named "index.html"
- You might notice there's a special `a` there, it's called an anchor tag: 
- - `a` - Wraps whatever inside just like a `div` but everything inside will be a clickable link.
- `    <img src='images/bagusdrip-logo.png' alt='BagusDrip' width='100'>` - Display an image from the "images" folder and an image called "bagusdrip-logo.png", `alt` provides an "alternative" wording description to the image in case the the image goes missing
- `    <h1>BagusDrip</h1>`:
- - `<h1>` is heading level 1, meaning its the biggest font size you can use in a HTML page
- - In this case, we are printing out "BagusDrip" in the biggest font, which usually means that this is the title

- `"  </a>"` - Close the anchor body
- `</div>` - Close the main div body


#### 2.2.5.3: Product IDs
```Java
String id = request.getParameter("id");
if (id == null) {
    out.println("<h3>No product selected!</h3>");
    out.println("<p><a href='index.html'>Back to Home</a></p>");
    out.println("</body></html>");
    return;
}
```
- `String id = request.getParameter("id")` - Using the HttpServlet object request, we get the value for the id parameter from the URL query string and store it inside a string called "id". For example, "...com/dripproduct?id=1". The "?" marks the start of each query statement
- Then we check if "id" is null, meaning no product is selected, we will then print out a statement telling the user that he/she did not pick a product, and provide a link to the main page, aka "index.html". Then, `return` which stops the doGet method from going further.


#### 2.2.5.4: MySQL Database Connection
```Java
try (
    Connection conn = DriverManager.getConnection(
        "jdbc:mysql://localhost:3306/BagusDrip?allowPublicKeyRetrieval=true&useSSL=false&serverTimezone=UTC",
        "username", "password123");
    PreparedStatement pstmt = conn.prepareStatement(
        "SELECT id, brand, model, category, price, qty, image_url FROM products WHERE id = ?")
) {
```

- `Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/BagusDrip....")`:
- - `DriverManager.getConnection` - This tries to establish a connection to a MySQL database
- - `jdbc:mysql://localhost:xxxx/BagusDrip` - Connects the the "BagusDrip" database on port 3306
- - `allowPublicKeyRetrieval=true&useSSL=false&serverTimezone=UTC` - Allows retrieving the public keys, disable SSL, and set timezone to UTC
- - `"username", "password123"` - Username and password

{{< notice warning >}}
Again, I wrote this in OrderServlet, but I will reiterate it here again. It is very terrible to hardcode your password and username like this.
{{< /notice >}}

- `conn.prepareStatement` - Prepares an SQL statement (hence the name duh) to select the details from:
- `"SELECT id, brand, model, category, price, qty, image_url FROM products WHERE id = ?"` these. The "?" is a placeholder for product ID


#### 2.2.5.5: SQL Query Parameter
```Java
pstmt.setInt(1, Integer.parseInt(id));
ResultSet rset = pstmt.executeQuery();
```
- `pstmt.setInt(1, Integer.parseInt(id))` - As I mentioned just now, the "?" is a placeholder for product ID. Hence, this line sets the placeholder to the integer value of the `id` parameter.
- `ResultSet rset = pstmt.executeQuery()` - Executes the query and stores the result in "rset", which is a ResultSet object


#### 2.2.5.6: Processing Query Results
```Java
if (rset.next()) {
    ...
}
```
This checks if a row exists when we move the cursor to the first row of the result set, meaning if it finds a product, enter the if block, if not, don't.

```Java
out.println("<div class='product-details' style='text-align: center; padding: 80px 20px 20px 20px;'>");
out.println("<h2>" + rset.getString("model") + "</h2>");
out.println("<img src='" + rset.getString("image_url") + "' width='300' alt='" + rset.getString("model") + "'>");
out.println("<p>Brand: " + rset.getString("brand") + "</p>");
out.println("<p>Category: " + rset.getString("category") + "</p>");
out.println("<p>Price: <span class='price' data-sgd='" + rset.getFloat("price") + "'>S$" + rset.getFloat("price") + "</span></p>");
out.println("<p>Stock: " + rset.getInt("qty") + "</p>");
```
_Again, I'm not typing out the `out.println` later to save time~_

When the product is found, we enter the if block:  
- `<div class='product-details' style='text-align: center; padding: 80px 20px 20px 20px;'>`:
- - Creates a new div container named "product-details" and some styling stuff

- `<h2>" + rset.getString("model") + "</h2>`:
- - Create a level 2 heading to display the product's model

- `<img src='" + rset.getString("image_url") + "' width='300' alt='" + rset.getString("model") + "'>`
- - Display the model image which we have put inside the directory, set the width, and alt text as the model of product

- `<p>Brand: " + rset.getString("brand") + "</p>`
- `<p>Category: " + rset.getString("category") + "</p>`:
- - New paragraphs to display the brands and category

- `<p>Price: <span class='price' data-sgd='" + rset.getFloat("price") + "'>S$" + rset.getFloat("price") + "</span></p>`
- - Another paragraph to display the price
- - Then we create a `span`, which means inline element, which means we stay at the same line and don't go to a new line
- - We name the inline "price", we set a new attribute called "data-sgd" and assign it to the price. Then we close the opening of the `span` element
- - Then for the body of `span`, we print out "S$" then concatenate it with the actual price. Finally we close the span and the paragraph.

- `<p>Stock: " + rset.getInt("qty") + "</p>`:
- - New paragraph again to display the stock left


#### 2.2.5.7: Inputing the Quantity
```Java
out.println("<label for='quantity'>Quantity: </label>");
out.println("<input type='number' id='quantity' min='1' max='" + rset.getInt("qty") + "' value='1' style='width: 50px;'>");
```
`<label for='quantity'>Quantity: </label>` - Creates a label for Quantity. Note that this label thing is quite interesting for the `for = 'quantity'` part. It tags this label as "quantity" for the next part:
- `<input type='number' id='quantity' min='1' max='" + rset.getInt("qty") + "' value='1' style='width: 50px;'>`:
- - Notice the id = 'quantity', same tag as in the label. This ensures that when you click on the Quantity word from the label, the cursor jumps to the input box. Speaking of input:
- - `Input` has a lot of variations, like `text`, `number`, `checkbox` and so on. When we use `number`, we set min and max, in this case max is the amount of stock left, and min is 1. `value='1'` just sets the default value


#### 2.2.5.8: Escaping Single Quote
```Java
String model = rset.getString("model").replace("'", "\\'");
```
As a precaution, we get the products model but replace any single quotes `'`with escaped single quotes `\'`. This is ensure that some products' name don't mess up the quotation marks. For example, "Iphone's Edition" --> "Iphone\'s Edition"


#### 2.2.5.9: Buttons
```Java
out.println("<div>");
out.println("<button onclick='addToCart(\"" + rset.getInt("id") + "\", \"" + model + "\", \"" + rset.getFloat("price") + "\", \"" + rset.getString("category") + "\", document.getElementById(\"quantity\").value)'>Add to Cart</button>");
out.println("<button onclick='buyNow(\"" + rset.getInt("id") + "\", \"" + model + "\", \"" + rset.getFloat("price") + "\", \"" + rset.getString("category") + "\", document.getElementById(\"quantity\").value)'>Buy Now</button>");
```
- First we create a new div container for 2 buttons, 'Add to Cart' and 'Buy Now'
- `<button onclick='addToCart(\"" + rset.getInt("id") + "\", \"" + model + "\", \"" + rset.getFloat("price") + "\", \"" + rset.getString("category") + "\", document.getElementById(\"quantity\").value)'>Add to Cart</button>` - This seems quite complex, let's break it down part by part:
- - Outer element: `<button>   Buy Now </button>` - This is a HTML element for a clickable button which says 'Add to Cart' on the button
- - `onclick=..` - Execute whatever JavaScript is inside the quote, in this case, would be addToCart(...)
- - `addToCart(\"" + rset.getInt("id") + "\", \"" + model + "\", \"" + rset.getFloat("price") + "\", \"" + rset.getString("category") + "\", document.getElementById(\"quantity\").value)`:
- - - `rset.getInt("id")` - Product ID
- - - `mode` - Product model, but we did the escape quote just now so no need to do it again here
- - - `rset.getFloat("price")` - Price of the product
- - - `rset.getString("category")`  -Category of the product
- - - `document.getElementById(\"quantity\").value` - Gets the current value made by the user in the quantity input box, remember the tag thing? :3
- - Second button is legit the same thing but called "Buy Now" instead
- The actual scripts for "Add to Cart" and "Buy Now" will be explained later


#### 2.2.5.10: Back Button
```Java
String brandParam = request.getParameter("brand") != null ? request.getParameter("brand") : "";
String categoryParam = request.getParameter("category") != null ? request.getParameter("category") : "";
out.println("<button onclick=\"window.location.href='dripquery?brand=" + brandParam + "&category=" + categoryParam + "&sort=desc'\">Back to Products</button>");
out.println("</div>");
out.println("</div>");
```
- `request.getParameter("brand")` - Gets the brand of the product ID from the URL
- `.... != null ? request.getParameter("brand") : ""` - If the parameter is missing, default to an empty string
- Same stuff with categoryParam
- `<button onclick=\"window.location.href='dripquery?brand=" + brandParam + "&category=" + categoryParam + "&sort=desc'\">Back to Products</button>`:
- - Create a clickable button called Back to Products, onclick to execute a JavaScript file
- `window.location.href` - Redirects users to a specific URL
- - A dynamically generated URL, cool huh":
- - ExampleL: "http://localhost:9999/BagusDrip/dripproduct?id=18&brand=Adidas&category=Joggers/Shorts"


#### 2.2.5.11: Product not Found
```Java
} else {
    out.println("<h3>Product not found!</h3>");
    out.println("<p><a href='index.html'>Back to Home</a></p>");
}
```
If `rset.next()` returns false, print out "Product not found!" and links them back to "index.html"


#### 2.2.5.12: Exceptions
```Java
} catch (SQLException ex) {
    out.println("<p>Error: " + ex.getMessage() + "</p>");
    out.println("<p>Check Tomcat console for details.</p>");
    ex.printStackTrace();
}
```
Refer to the end part in ProductServlet in Part 1. Literally the same thing, and I also don't really get it, sorry. :3


#### 2.2.5.13: JavaScript Functions - Add to Cart
```Java
out.println("<script>");
out.println("function addToCart(id, model, price, category, quantity) {");
out.println("  let cart = JSON.parse(localStorage.getItem('cart')) || [];");
out.println("  let existingItem = cart.find(item => item.id === id);");
out.println("  if (existingItem) {");
out.println("    existingItem.quantity += parseInt(quantity);");
out.println("  } else {");
out.println("    cart.push({ id: id, model: model, price: price, category: category, quantity: parseInt(quantity) });");
out.println("  }");
out.println("  localStorage.setItem('cart', JSON.stringify(cart));");
out.println("  alert('Added ' + quantity + ' ' + model + ' to cart!');");
out.println("}");
```
Interesting, when we use the `<script>` tag, the browser will know that it is a JavaScript function.

- `<script>  ... </script>` - Wrap your script body using the script element

- `function addToCart(id, model, price, category, quantity)` - Define our Add to Cart function to take in 5 variables, id, model, price, category and quantity -> important above

- `let cart = JSON.parse(localStorage.getItem('cart')) || []`:
- - `localStorage` is the browser storage that stores stuff even after we refresh or close it
- - `.getItem('cart')` means we try to load the cart from said storage
- - `JSON.parse(...)` means we convert the store JSON string into a JavaScript object
- - `|| []` means if nothing was found, aka nothing in cart, use an empty array

- `let existingItem = cart.find(item => item.id === id)`:
- - `cart.find(...)` is a built-in JavaScript array function where it looks through every element in an array and returns the first one that matches your given conditions. If it does not find any that matches, return "undefined"
- - `item => ...` is a very intuitive modern JavaScript thing called *arrow function* because of how short it is compared to how one would write a Java function. Essentially, `item` serves as a temporary variable that represents every single elements in the `cart` array as `.find()` checks one by one.
- - `item.id === id` is the condition we set for `.find()`. It compares the `id` of the current `item` with the `id` that was passed into the `addToCart` function. 
- - `===` is a strict equality operator which checks if both value and the type are the same

```Java
if (existingItem) {
  existingItem.quantity += parseInt(quantity);
} else {
  cart.push({ id: id, model: model, price: price, category: category, quantity: parseInt(quantity) });
}
```
After checking through every single elements in the array and found any item, we check to see if we have a matching item, then we run the if block
- `existingItem.quantity += parseInt(quantity)` - Only runs is there is an existing item:
- - Takes the `quantity` of `existingItem` and adds the new quantity to it. Basically, if there's already a same item in the cart, just increase the quantity of the item.
- Else, we can't find a similar item in the cart. Then, we add a new object with the 5 properties as a new object to the cart array


- `localStorage.setItem('cart', JSON.stringify(cart))`:
- - `.setItem` is a method from `localStorage` to store stuff in your browser's  storage.
- - The 'cart' here is the name we are giving to the data stored
- - `JSON.stringify(cart)` basically is the data stored. However, `cart` is a JavaScript array, and `localStorage` only stores strings. That's why we did `JSON.stringify()` to convert the array and its content into a string.

- `alert('Added ' + quantity + ' ' + model + ' to cart!')`
- `alert()` is a neat built-in browser function that shows a small pop-up dialog box with a message and an "OK" button.
- We basically just tell the user that we have added their quantity of their chosen product in their cart


#### 2.2.5.14: JavaScript Functions - Buy Now
```Java
out.println("function buyNow(id, model, price, category, quantity) {");
out.println("  let cart = JSON.parse(localStorage.getItem('cart')) || [];");
out.println("  cart = [{ id: id, model: model, price: price, category: category, quantity: parseInt(quantity) }];");
out.println("  localStorage.setItem('cart', JSON.stringify(cart));");
out.println("  window.location.href = 'cart.html';");
out.println("}");
out.println("</script>");
```

Same as before, we define another function called "buyNow" that takes in 5 of the same arguments as before.

- However, the only difference in this code is:
- `window.location.href = 'cart.html'` which takes us straight to the cart page to finish their purchase.

As compared to the "Add to Cart" function, users can continue shopping while the the "Buy Now" function just goes to the checkout page immediately.

