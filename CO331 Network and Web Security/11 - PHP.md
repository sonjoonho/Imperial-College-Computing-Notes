# 11 - PHP

## SQL Injection

### `die()`

This is equivalent to `exit()` and simply kills the process, while outputting to STDOUT. It is bad practice to use it for error handling because it can expose error messages to the user e.g.
```php
$result = mysql_query($q) or die(mysql_error())
```

### `mysql_real_escape_string()`

This is used to sanitise user input by escaping special characters so that it is safe to place it in a `mysql_query()`.

An alternative is to use **prepared statements** instead of `mysql_query()`.

## `shell_exec??`

and backticks

exec shell exec escapeshell

### Error Handling

As mentioned, errors should leak as little information as possible. For example, the same error message should be displayed when a malformed SQL query is executed as when no matching users are found.

## XSS

## `preg_replace()`

Use this to remove special characters from user input.