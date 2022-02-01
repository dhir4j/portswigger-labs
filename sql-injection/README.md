<!-- omit in toc -->
# SQL Injection

<!-- omit in toc -->
## Table of Contents

- [SQL injection UNION attack, determining the number of columns returned by the query](#sql-injection-union-attack-determining-the-number-of-columns-returned-by-the-query)
- [SQL injection UNION attack, finding a column containing text](#sql-injection-union-attack-finding-a-column-containing-text)
- [SQL injection UNION attack, retrieving data from other tables](#sql-injection-union-attack-retrieving-data-from-other-tables)

## SQL injection UNION attack, determining the number of columns returned by the query
Reference: https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns

<!-- omit in toc -->
### Quick Solution
The ``category`` parameter is vulnerable to SQL Injection, use a **UNION** attack to retrieve the number of columns, the payload is simply:
```
# Keep adding NULL until the error disappears
'+UNION+SELECT+NULL,NULL--
```
<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify the request that sets the product category filter.
2. Modify the ``category`` parameter, giving it the value ``'+UNION+SELECT+NULL--``. Observe that an error occurs.
3. Modify the category parameter to add an additional column containing a null value: 
```
'+UNION+SELECT+NULL,NULL--
```
4. Continue adding null values until the error disappears and the response includes additional content containing the null values.

## SQL injection UNION attack, finding a column containing text
Reference: https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text

<!-- omit in toc -->
### Quick Solution
The ``category`` parameter is vulnerable to SQL Injection, combine the previous payload to retrieve the number of columns and then change the ``NULL`` value one by one with a random string to find a column that contains text. Payload in the next section

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify the request that sets the product category filter.
2. Determine the number of columns that are being returned by the query. Verify that the query is returning three columns, using the following payload in the ``category`` parameter: 
```
'+UNION+SELECT+NULL,NULL,NULL--
```
3. Try replacing each null with the random value provided by the lab, for example: 
```
'+UNION+SELECT+'abcdef',NULL,NULL--
```
4. If an error occurs, move on to the next null and try that instead.

## SQL injection UNION attack, retrieving data from other tables
Reference: https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables

<!-- omit in toc -->
### Quick Solution
Use the previous payloads to retrieve the number of columns and which columns contain text data. The description says that there is a ``users`` table with columns called ``username`` and ``password``. Use the following payload to retrieve the contents of ``users`` table:
```
'+UNION+SELECT+username,+password+FROM+users--
```

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify the request that sets the product category filter.
2. Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, both of which contain text, using a payload like the following in the category parameter: 
```
'+UNION+SELECT+'abc','def'--.
```
3. Use the following payload to retrieve the contents of the users table: 
```
'+UNION+SELECT+username,+password+FROM+users--
```
4. Verify that the application's response contains usernames and passwords.