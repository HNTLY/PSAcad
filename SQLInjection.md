# SQL Injection
## What is SQLi?
  - Vulnerability allowing attackers to interfere with database queries
  - Typically allows an attacker to view data they can't normally see such as user information or information retrieved by the application
  - Sometime, the attacker is able to modify or delete this data
    
## Impact of SQLi?
  - Access to unauthorised sensitive data e.g passwords, bank details and personal information
  - Potential for backdoor persistance 
    
## Retrieving hidden data
### Scenario    
  - Shopping application using the URL `https://insecure-website.com/products?category=Gifts`
  - This URL is displayed when the user clicks on the 'Gifts' tab
### Setup
  - When this link is clicked, it makes the following SQL query `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`
  - Query disected:
    - Return all details (* / wildcard)
    - From the products table
    - Where the category is Gifts
    - And released is equal to 1
  - The released parameter is used to hide products that are unreleased. It is possible that if we set this value to `0`, we can view unreleased products
### Exploitation
  - An attacker can construct the following URL `https://insecure-website.com/products?category=Gifts'--`
  - This query is as follows `SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1`
  - The double dash (`--`) is a comment in SQL meaning the rest of the query is commented out and essentially removed. Therefore, all products, unreleased included, are displayed
  - An attacker can go one step further and display all products in all categories (perhaps some categories are hidden)
    - `https://insecure-website.com/products?category=Gifts'+OR+1=1--`

## Lab: WHERE clause vulnerability
<details> 
  <summary> Answer. Spoiler. </summary>
   {URL}/filter?category=Gifts' OR 1=1--
</details>

## Subverting application logic
  - Consider a login form resulting in the following query: `SELECT * FROM users WHERE username = 'HNTLY' AND password = 'securePass'`
  - If the details match, the user is logged in, otherwise it is rejected
  - To bypass the password, an attacker might enter the username `Administrator'--` and an empty password
  - The query is then only looking for a user called Administrator but no password is required
  
## Lab: Login bypass
<details> 
  <summary> Answer. Spoiler. </summary>
   Username = Administrator'--
   
   Password = password
</details>

## Retrieving data from other tables
  - The `UNION` keyword can be used to access data from other tables
  - It allows the attacker to use an additional `SELECT` query
  - Using the shopping site example, an attacker could input a `UNION SELECT` string into the category parameter to return even more information
  - `' UNION SELECT username, password FROM users--` would return the products as normal but also usernames and passwords

## Examining the database
  - Sometimes it can be useful to get more information about the database itself. However the queries will vary depending on database type
  - Examples to find db type:
    - `SELECT @@version` for MS SQL or MySQL
    - `SELECT version()` for PostGres
    - `SELECT * FROM v$version` for Oracle
  - You can also determine what tables exist and which columns they contain using `SELECT * FROM information_scheme.tables`
  
## Blind SQLi
  - A blind vulnerability is when the application doesn't return the results of the query or any errors in its responses
  - These are harder to exploit but still possible through the following techniques:
    - Trigger detectable difference in the response such as triggering errors or injecting new booleane logic
    - Trigger time delay to determine whether the query is successful based on the time to respond
    - Trigger out-of-band network interaction (using external servers and OAST techniques)
    
 ## Detecting SQLi vulnerabilities
  - Submitting single quote `'` and looking for errors
  - Submitting SQL-specific syntax evaluating to the original value and a different value
  - Submitting boolean conditions
  - Submitting time delay payloads
  - Submitting OAST techniques
  
## Injection in different parts of the query
  - Most vulnerabilites occur in the `WHERE` or `SELECT` part of the query but an attack could occur anywhere in the query
  - Other common locations are:
    - `UPDATE`
    - `INSERT`
    - `SELECT` within a table or column name
    - `SELECT` within the `ORDER BY` clause
    
## Second-order SQLi
  - First-order is where the application takes user input and incorporates it into an SQL query
  - Second-order (or stored) is where the application takes user input and stores it for future use
  - This occurs when the input is placed into a database but no vulnerability arises at the point of entry. Then, when another request is sent, the application retrieves the stored data
  
## SQLi prevention
  - Parameterised queries instead of string concatenation
  - Hard-coded constants 
  - Input validation
  - Input filtering


