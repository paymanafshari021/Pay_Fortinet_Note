Email Address Pattern:
`\b\S+@\S+\.\S+\b`
IP Address Pattern:
`\b(?:\d{1,3}\.){3}\d{1,3}\b`
URL Pattern:
`\bhttps?:\/\/(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}(?::\d+)?(?:\/[^\s"'<>]*)?`
Domain Pattern:
`\b(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?\.)+[a-zA-Z]{2,}\b`

---
## `ss -tlnp`

`ss` = **Socket Statistics**  
It’s a modern replacement for `netstat`, used to inspect network connections.
### Breakdown of options

- `-t` → **TCP** sockets only
- `-l` → **Listening** sockets (services waiting for connections)
- `-n` → **Numeric** output (no DNS or service name lookup)
- `-p` → **Process info** (shows which program owns the socket)
### What it shows

This command lists **all TCP ports currently listening on the system**, along with:

- Local IP address
- Port number
- Process name and PID using that port
### Example output

```bash
LISTEN 0 128 0.0.0.0:80   0.0.0.0:*   users:(("nginx",pid=1234,fd=6))
```
### Meaning

- Port **80** is open
- It’s listening on **all interfaces**  
- The process is **nginx** (PID 1234)

📌 **Common use cases**

- Check which service is listening on a port
- Debug “port already in use” errors
- Verify that a server is actually running
## `curl -k IP:Port`

`curl` = command-line tool for making HTTP/HTTPS requests.
### Breakdown of options

- `-k` → **Insecure mode**
    - Skips SSL/TLS certificate verification
    - Useful for self-signed or expired certificates
- `IP:Port` → Target server and port (e.g. `192.168.1.10:8443`)
### What it does
It sends an **HTTP or HTTPS request** to the specified IP and port and prints the response.

### Example
```bash
curl -k https://192.168.1.10:8443
```
### Why `-k` is needed

If the server uses:

- Self-signed certificate
- Internal CA
- Wrong hostname in certificate

Without `-k`, curl would fail with an SSL error.

📌 **Common use cases**

- Test if a web service is reachable
- Verify HTTPS services on non-standard ports
- Debug API endpoints

⚠️ **Security note**  
`-k` disables certificate validation — **never use it in production scripts**.

---
## TLS Handshake
The TLS handshake creates a secure encrypted tunnel for HTTP traffic:

1. Client Hello: Client initiates secure connection with supported cipher suites
2. Server Hello: Server responds with selected cipher suite
3. Certificate: Server sends its SSL/TLS certificate
4. Client Key Exchange: Client sends parameters for shared secret calculation
5. Server Change Cipher Spec: Server indicates messages will be encrypted
6. Finished: Server confirms handshake completion
---
`ncat 127.0.12.25 24601`

---

Have you ever seen a link that looks like this: `https://awesome.website.thm/TrackPackage?packageID=1001`?

When you saw a link like this, have you ever wondered what would happen if you simply changed the packageID to 11 or 12? In its simplest form, this can be a potential case for IDOR. 

IDOR stands for **Insecure Direct Object Reference** and is a type of access control vulnerability. Web applications often use references to determine what data to return when you make a request. However, if the web server doesn't perform checks to ensure you are allowed to view that data before sending it, it can lead to serious sensitive information disclosure.

---
XSS is a web application vulnerability that lets attackers (or evil bunnies) inject malicious code (usually JavaScript) into input fields that reflect content viewed by other users (e.g., a form or a comment in a blog). When an application doesn’t properly validate or escape user input, that input can be interpreted as code rather than harmless text. This results in malicious code that can steal credentials, deface pages, or impersonate users. Depending on the result, there are various types of XSS. In today’s task, we focus on **Reflected XSS** and **Stored XSS**.

## Reflected XSS

You see reflected variants when the injection is immediately projected in a response. Imagine a toy search function in an online toy store, you search via:

`[https://trygiftme.thm/search?term=gift](https://trygiftme.thm/search?term=gift)`

But imagine you send this to your friend who is looking for a gift for their nephew (please don’t do this):

`https://trygiftme.thm/search?term=<script>alert( atob("VEhNe0V2aWxfQnVubnl9") )</script>`

If your friend clicks on the link, it will execute code instead.

**Impact**

You could act, view information, or modify information that your friend or any user could do, view, or access. It’s usually exploited via phishing to trick users into clicking a link with malicious code injected.

## Stored XSS

A Stored XSS attack occurs when malicious script is saved on the server and then loaded for every user who views the affected page. Unlike Reflected XSS, which targets individual victims, Stored XSS becomes a “set-and-forget” attack, anyone who loads the page runs the attacker’s script.
## Normal Comment Submission

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*Zxfl4hSXmPSBsrUEuCgGpw.png)

The server stores this information and displays it whenever someone visits that blog post.

## Malicious Comment Submission (Stored XSS Example)

If the application does not sanitize or filter input, an attacker can submit JavaScript instead of a comment:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*pbXD0Y3SCT6IjUek5zqIDw.png)

Because the comment is saved in the database, every user who opens that blog post will automatically trigger the script.
This lets the attacker run code as if they were the victim in order to perform malicious actions such as:

- Steal session cookies
- Trigger fake login popups
- Deface the page
---
### Encoding and Decoding

**Encoding** is a method to transform data to ensure compatibility between different systems. It differs from encryption in purpose and process.

**Decoding** is the process of converting encoded data back to its original, readable, and usable form.

![](https://miro.medium.com/v2/resize:fit:700/1*N0Xw0ieH0ODD2oDi-CWfAQ.png)

---
# HTTP Headers

We have seen examples of HTTP requests and response headers in the previous section. Such HTTP headers pass information between the client and the server. Some headers are only used with either requests or responses, while some other general headers are common to both.

Headers can have one or multiple values, appended after the header name and separated by a colon. We can divide headers into the following categories:

1. `General Headers`
2. `Entity Headers`
3. `Request Headers`
4. `Response Headers`
5. `Security Headers`

Let's discuss each of these categories.

---

## General Headers

[General headers](https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html) are used in both HTTP requests and responses. They are contextual and are used to `describe the message rather than its contents`.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Date`|`Date: Wed, 16 Feb 2022 10:38:44 GMT`|Holds the date and time at which the message originated. It's preferred to convert the time to the standard [UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) time zone.|
|`Connection`|`Connection: close`|Dictates if the current network connection should stay alive after the request finishes. Two commonly used values for this header are `close` and `keep-alive`. The `close` value from either the client or server means that they would like to terminate the connection, while the `keep-alive` header indicates that the connection should remain open to receive more data and input.|

---

## Entity Headers

Similar to general headers, [Entity Headers](https://www.w3.org/Protocols/rfc2616/rfc2616-sec7.html) can be `common to both the request and response`. These headers are used to `describe the content` (entity) transferred by a message. They are usually found in responses and POST or PUT requests.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Content-Type`|`Content-Type: text/html`|Used to describe the type of resource being transferred. The value is automatically added by the browsers on the client-side and returned in the server response. The `charset` field denotes the encoding standard, such as [UTF-8](https://en.wikipedia.org/wiki/UTF-8).|
|`Media-Type`|`Media-Type: application/pdf`|The `media-type` is similar to `Content-Type`, and describes the data being transferred. This header can play a crucial role in making the server interpret our input. The `charset` field may also be used with this header.|
|`Boundary`|`boundary="b4e4fbd93540"`|Acts as a marker to separate content when there is more than one in the same message. For example, within a form data, this boundary gets used as `--b4e4fbd93540` to separate different parts of the form.|
|`Content-Length`|`Content-Length: 385`|Holds the size of the entity being passed. This header is necessary as the server uses it to read data from the message body, and is automatically generated by the browser and tools like cURL.|
|`Content-Encoding`|`Content-Encoding: gzip`|Data can undergo multiple transformations before being passed. For example, large amounts of data can be compressed to reduce the message size. The type of encoding being used should be specified using the `Content-Encoding` header.|

---

## Request Headers

The client sends [Request Headers](https://tools.ietf.org/html/rfc2616) in an HTTP transaction. These headers are `used in an HTTP request and do not relate to the content` of the message. The following headers are commonly seen in HTTP requests.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Host`|`Host: www.inlanefreight.com`|Used to specify the host being queried for the resource. This can be a domain name or an IP address. HTTP servers can be configured to host different websites, which are revealed based on the hostname. This makes the host header an important enumeration target, as it can indicate the existence of other hosts on the target server.|
|`User-Agent`|`User-Agent: curl/7.77.0`|The `User-Agent` header is used to describe the client requesting resources. This header can reveal a lot about the client, such as the browser, its version, and the operating system.|
|`Referer`|`Referer: http://www.inlanefreight.com/`|Denotes where the current request is coming from. For example, clicking a link from Google search results would make `https://google.com` the referer. Trusting this header can be dangerous as it can be easily manipulated, leading to unintended consequences.|
|`Accept`|`Accept: */*`|The `Accept` header describes which media types the client can understand. It can contain multiple media types separated by commas. The `*/*` value signifies that all media types are accepted.|
|`Cookie`|`Cookie: PHPSESSID=b4e4fbd93540`|Contains cookie-value pairs in the format `name=value`. A [cookie](https://en.wikipedia.org/wiki/HTTP_cookie) is a piece of data stored on the client-side and on the server, which acts as an identifier. These are passed to the server per request, thus maintaining the client's access. Cookies can also serve other purposes, such as saving user preferences or session tracking. There can be multiple cookies in a single header separated by a semi-colon.|
|`Authorization`|`Authorization: BASIC cGFzc3dvcmQK`|Another method for the server to identify clients. After successful authentication, the server returns a token unique to the client. Unlike cookies, tokens are stored only on the client-side and retrieved by the server per request. There are multiple types of authentication types based on the webserver and application type used.|

A complete list of request headers and their usage can be found [here](https://tools.ietf.org/html/rfc7231#section-5).

---

## Response Headers

[Response Headers](https://tools.ietf.org/html/rfc7231#section-7) can be `used in an HTTP response and do not relate to the content`. Certain response headers such as `Age`, `Location`, and `Server` are used to provide more context about the response. The following headers are commonly seen in HTTP responses.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Server`|`Server: Apache/2.2.14 (Win32)`|Contains information about the HTTP server, which processed the request. It can be used to gain information about the server, such as its version, and enumerate it further.|
|`Set-Cookie`|`Set-Cookie: PHPSESSID=b4e4fbd93540`|Contains the cookies needed for client identification. Browsers parse the cookies and store them for future requests. This header follows the same format as the `Cookie` request header.|
|`WWW-Authenticate`|`WWW-Authenticate: BASIC realm="localhost"`|Notifies the client about the type of authentication required to access the requested resource.|

---

## Security Headers

Finally, we have [Security Headers](https://owasp.org/www-project-secure-headers/). With the increase in the variety of browsers and web-based attacks, defining certain headers that enhanced security was necessary. HTTP Security headers are `a class of response headers used to specify certain rules and policies` to be followed by the browser while accessing the website.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Content-Security-Policy`|`Content-Security-Policy: script-src 'self'`|Dictates the website's policy towards externally injected resources. This could be JavaScript code as well as script resources. This header instructs the browser to accept resources only from certain trusted domains, hence preventing attacks such as [Cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting).|
|`Strict-Transport-Security`|`Strict-Transport-Security: max-age=31536000`|Prevents the browser from accessing the website over the plaintext HTTP protocol, and forces all communication to be carried over the secure HTTPS protocol. This prevents attackers from sniffing web traffic and accessing protected information such as passwords or other sensitive data.|
|`Referrer-Policy`|`Referrer-Policy: origin`|Dictates whether the browser should include the value specified via the `Referer` header or not. It can help in avoiding disclosing sensitive URLs and information while browsing the website.|

**Note:** This section only mentions a small subset of commonly seen HTTP headers. There are many other contextual headers that can be used in HTTP communications. It's also possible for applications to define custom headers based on their requirements. A complete list of standard HTTP headers can be found [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers).

---

## cURL

In the previous section, we saw how using the `-v` flag with cURL shows us the full details of the HTTP request and response. If we were only interested in seeing the response headers, then we can use the `-I` flag to send a `HEAD` request and only display the response headers. Furthermore, we can use the `-i` flag to display both the headers and the response body (e.g. HTML code). The difference between the two is that `-I` sends a `HEAD` request (as will see in the next section), while `-i` sends any request we specify and prints the headers as well.

The following command shows an example output of using the `-I` flag:

HTTP Headers

```shell-session
pay021@htb[/htb]$ curl -I https://www.inlanefreight.com

Host: www.inlanefreight.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/605.1.15 (KHTML, like Gecko)
Cookie: cookie1=298zf09hf012fh2; cookie2=u32t4o3tb3gg4
Accept: text/plain
Referer: https://www.inlanefreight.com/
Authorization: BASIC cGFzc3dvcmQK

Date: Sun, 06 Aug 2020 08:49:37 GMT
Connection: keep-alive
Content-Length: 26012
Content-Type: text/html; charset=ISO-8859-4
Content-Encoding: gzip
Server: Apache/2.2.14 (Win32)
Set-Cookie: name1=value1,name2=value2; Expires=Wed, 09 Jun 2021 10:18:14 GMT
WWW-Authenticate: BASIC realm="localhost"
Content-Security-Policy: script-src 'self'
Strict-Transport-Security: max-age=31536000
Referrer-Policy: origin
```

**Exercise:** Try to go through all of the above headers, and see whether you can recall the usage for each of them.

In addition to viewing headers, cURL also allows us to set request headers with the `-H` flag, as we will see in a later section. Some headers, like the `User-Agent` or `Cookie` headers, have their own flags. For example, we can use the `-A` to set our `User-Agent`, as follows:

HTTP Headers

```shell-session
pay021@htb[/htb]$ curl https://www.inlanefreight.com -A 'Mozilla/5.0'

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

**Exercise:** Try to use the `-I` or the `-v` flags with the above example, to ensure that we did change our User-Agent with the `-A` flag.

---

## Browser DevTools

Finally, let's see how we can preview the HTTP headers using the browser devtools. Just as we did in the previous section, we can go to the `Network` tab to view the different requests made by the page. We can click on any of the requests to view its details: ![Network tab showing two GET requests to 188.166.146.97:31122. Status 304 for '/' with headers displayed, and 404 for 'favicon.ico'.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/35/devtools_network_requests_details.jpg)

In the first `Headers` tab, we see both the HTTP request and HTTP response headers. The devtools automatically arrange the headers into sections, but we can click on the `Raw` button to view their details in their raw format. Furthermore, we can check the `Cookies` tab to see any cookies used by the request, as discussed in an upcoming section.

---
# HTTP Methods and Codes

HTTP supports multiple methods for accessing a resource. In the HTTP protocol, several request methods allow the browser to send information, forms, or files to the server. These methods are used, among other things, to tell the server how to process the request we send and how to reply.

We saw different HTTP methods used in the HTTP requests we tested in the previous sections. With cURL, if we use `-v` to preview the full request, the first line contains the HTTP method (e.g. `GET / HTTP/1.1`), while with browser devtools, the HTTP method is shown in the `Method` column. Furthermore, the response headers also contain the HTTP status code, which indicates the result of processing our HTTP request.
## Request Methods

The following are some of the commonly used methods:

|**Method**|**Description**|
|---|---|
|`GET`|Requests a specific resource. Additional data can be passed to the server via query strings in the URL (e.g. `?param=value`).|
|`POST`|Sends data to the server. It can handle multiple types of input, such as text, PDFs, and other forms of binary data. This data is appended in the request body present after the headers. The POST method is commonly used when sending information (e.g. forms/logins) or uploading data to a website, such as images or documents.|
|`HEAD`|Requests the headers that would be returned if a GET request was made to the server. It doesn't return the request body and is usually made to check the response length before downloading resources.|
|`PUT`|Creates new resources on the server. Allowing this method without proper controls can lead to uploading malicious resources.|
|`DELETE`|Deletes an existing resource on the webserver. If not properly secured, can lead to Denial of Service (DoS) by deleting critical files on the web server.|
|`OPTIONS`|Returns information about the server, such as the methods accepted by it.|
|`PATCH`|Applies partial modifications to the resource at the specified location.|

The list only highlights a few of the most commonly used HTTP methods. The availability of a particular method depends on the server as well as the application configuration. For a full list of HTTP methods, you can visit this [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods).

**Note:** Most modern web applications mainly rely on the `GET` and `POST` methods. However, any web application that utilizes REST APIs also rely on `PUT` and `DELETE`, which are used to update and delete data on the API endpoint, respectively. Refer to the [Introduction to Web Applications](https://academy.hackthebox.com/module/details/75) module for more details.
## Status Codes

HTTP status codes are used to tell the client the status of their request. An HTTP server can return five classes of status codes:

|**Class**|**Description**|
|---|---|
|`1xx`|Provides information and does not affect the processing of the request.|
|`2xx`|Returned when a request succeeds.|
|`3xx`|Returned when the server redirects the client.|
|`4xx`|Signifies improper requests `from the client`. For example, requesting a resource that doesn't exist or requesting a bad format.|
|`5xx`|Returned when there is some problem `with the HTTP server` itself.|

The following are some of the commonly seen examples from each of the above HTTP status code classes:

|**Code**|**Description**|
|---|---|
|`200 OK`|Returned on a successful request, and the response body usually contains the requested resource.|
|`302 Found`|Redirects the client to another URL. For example, redirecting the user to their dashboard after a successful login.|
|`400 Bad Request`|Returned on encountering malformed requests such as requests with missing line terminators.|
|`403 Forbidden`|Signifies that the client doesn't have appropriate access to the resource. It can also be returned when the server detects malicious input from the user.|
|`404 Not Found`|Returned when the client requests a resource that doesn't exist on the server.|
|`500 Internal Server Error`|Returned when the server cannot process the request.|

For a full list of standard HTTP status codes, you can visit this [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status). Apart from the standard HTTP codes, various servers and providers such as [Cloudflare](https://support.cloudflare.com/hc/en-us/articles/115003014432-HTTP-Status-Codes) or [AWS](https://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/APIError.html) implement their own codes.

---
