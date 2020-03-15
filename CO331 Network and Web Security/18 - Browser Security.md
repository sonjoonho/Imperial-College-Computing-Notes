# 18 - Browser Security

## Cookies

HTTP is **stateless**: each request-response exchange is *independent* of the previous one. This is contrast to TCP, for example, which keeps track of session numbers.

For web applications to track state on the client side, cookies can be used. They are key-value pairs stored in the browser on behalf of a web application. Cookies are used for

- Storing website preferences
- Storing session tokens
- Tracking users

The server sets each cookie using a response header

```
Set-Cookie: name = value; [(attribute [=value];)*]
```

and the browser includes relevant cookies for subsequent requests in one request header

```
Cookie: (name = value;)+ ...
```

### Attributes

- `Domain = <domain>`
  - Send cookie back when `<domain>` is suffix of requested domain.
  - Must be a non-trivial suffix (cannot set for `.com`, `.co.uk`, etc.)
  - Default to host of URL that caused the response.
- `Path = <path>`
  - Send cookie back when `<path>` is prefix of request path.
  - Cookie for `example.com/login` is not sent to `example.com`.
  - Defaults to path of URL that caused the response.
- `Expires = <date>`
  - Date in the **future**: store persistent cookie on file until `<date>`.
  - Date in the **past**: delete the cookie immediately.
  - `Null` (default): keep session-only cookie in memory until the browser is closed.
- `Secure`
  - Send the cookie only over HTTPS.
- `HttpOnly`
  - Prevent non-HTTP APIs (e.g. JavaScript) from accessing the cookie.
  - Mitigates risk of cookie theft via XSS.
- `SameSite`
  - Experimental feature that mitigates cross-origin information leakage (e.g. CSRF and tracking).

### In the Browser

Cookies are accessible to JavaScript in the browser. `document.cookie` provides access to all the cookies *in scope* for the document origin.

Examples

Write

```javascript
document.cookie = "userid=123; path=/; secure";
```

Delete

```javascript
document.cookie = "userid; path=/; expires=Thu, 01 Jan 1970 00:00:01 GMT";
```

Read (will not show `HttpOnly` cookies)

```javascript
alert(document.cookie);
```

### Scope

A cookie is identified by **name** and **origin**. A cookie's origin is made up of a **domain** and **path**. **Cookie scope** is determined by origin and secure attribute. Browser request sends all cookies that are in scope to the server. All the server sees are the `name=value` pairs, and attributes are not sent back.

### Security Considerations

Since the server does not see cookie attributes, `example.com` cannot tell if the cookie was set by `subdomain.example.com` and the server cannot tell if a cookie is effectively `HttpOnly` or was written by JavaScript.

The path does not restrict the visibility of cookies (the `path` in cookie origin is only meant to improve efficiency) so scripts from a different path can load an iframe with a page from a target path, and access `document.cookie` of an iframe thanks to SOP (which ignores path).

Cookie integrity is not guaranteed because the user can access cookies and any JavaScript from the same origin can set/edit cookies. Even `secure` does not guarantee cookie integrity because secure cookies can be set by JavaScript, and an active network attacker can intercept HTTPS responses and set a cookie with a spoofed HTTP response. The next request will send a tampered "secure" cookie.

## HTML5 Browser Storage

HTML5 provides **web storage** and **indexed database APIs**. We will be focusing on web storage. It implements client-side state using lists of key-value pairs.

Storage can be accessed via

- `window.localStorage`
  - Associated with a page origin. Data is kept until deleted explicitly.
- `window.sessionStorage`
  - Associated with a page origin *and* current tab.
  - Data is kept until the tab is closed.
- JavaScript API `xxx` = `localStorage` or `sessionStorage`
  - `xxx.length` return list length
  - `xxx.key(n)` read n-th key in list
  - `xxx.getItem(k)` or `xxx.k` read the value of `k`
  - `xxx.setItem(k, v)` set the value `v` for key `k`
  - `xxx.removeItem(k)` remove the entry with key `k`
  - `xxx.clear()` delete all entries

Unlike cookies, HTML5 storage is not sent over HTTP. Instead, it is up to the page if/how to involve the server (AJAX, GET/POST, etc.). The attacker model is script injection and XSS.

## Attacks

### Resident XSS

A variant of DOM-based XSS that exploits browser storage. RXSS remains effective *even after* the vulnerable page is patched - unlike DOM-based and Reflected XSS. RXSS cannot be detected by the server, unlike Reflected and Stored XSS.

Page

```html
<html><body>
    Welcome user:
    <script>
    	document.write(localStorage.getItem("user_name"));
    </script>
</body></html>
```

Attack

```javascript
localStorage.setItem("user_name", "<script>alert('XSS')</script>")
```

#### Countermeasures

- Do not trust values stored in the browser.
- Sanitise stored values, like user input.
- Periodically validate, refresh, or delete stored data.