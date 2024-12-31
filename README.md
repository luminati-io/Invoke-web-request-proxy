# PowerShell Invoke-WebRequest Proxy Guide

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/blob/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/proxy-types) 

At the end of this **Invoke-WebRequest PowerShell proxy guide**, you will know:

1. [What Is PowerShell Invoke-WebRequest?](#what-is-powershell-invoke-webrequest)
2. [Installing Invoke-WebRequest](#installing-invoke-webrequest)  
   2.1. [Windows](#windows)  
   2.2. [macOS and Linux](#macos-and-linux)
3. [Prerequisites to Get Started with a Proxy in PowerShell](#prerequisites-to-get-started-with-a-proxy-in-powershell)
4. [How to Specify an HTTP Proxy in Invoke-WebRequest](#how-to-specify-an-http-proxy-in-invoke-webrequest)  
   4.1. [Using a Command Line Option](#using-a-command-line-option)  
   4.2. [Using Environment Variables](#using-environment-variables)
5. [How To Use HTTPS and SOCKS Proxies in PowerShell](#how-to-use-https-and-socks-proxies-in-powershell)
6. [Tips and Tricks You Need to Know](#tips-and-tricks-you-need-to-know)  
   6.1. [Ignore the PowerShell Proxy Configuration](#ignore-the-powershell-proxy-configuration)  
   6.2. [Avoid SSL Certificate Errors](#avoid-ssl-certificate-errors)
7. [Which PowerShell Proxy Should You Use?](#which-powershell-proxy-should-you-use)

Let’s dive in!

---

## What Is PowerShell Invoke-WebRequest?

**Invoke-WebRequest** is a PowerShell cmdlet for sending **HTTP, HTTPS, and FTP** requests to web servers and web services. By default, it automatically parses the response produced by the server and returns collections of forms, links, images, or other significant HTML elements.

Usually, it is used for accessing REST APIs, downloading files from the web, or interacting with web services. Below is the basic syntax of an **Invoke-WebRequest** request:

```powershell
Invoke-WebRequest [-Uri] <Uri> [-Method <WebRequestMethod>] [-Headers <IDictionary>] [-Body <Object>]
```

**Key parameters to remember**:

- Uri: The URI of the web resource to which the request is sent.
- Method: The HTTP method to use for the request (e.g., GET, POST, PUT, DELETE).
- Invoke-WebRequest sends GET requests by default.
- Headers: The additional HTTP headers to include in the request.
- Body: The body of the request to send to the server.
 
As you can see, the only required argument is <Uri>. Thus, in short, the simplest syntax to perform a GET request to a given URI is:

```powershell
Invoke-WebRequest <Uri>
```

## Installing Invoke-WebRequest

To use Invoke-WebRequest, you need PowerShell. Let’s learn how to install PowerShell and get access to the Invoke-WebRequest cmdlet!

### Windows

First, understand that Windows PowerShell and PowerShell are two different things. Windows PowerShell is the version of PowerShell that ships with Windows (latest version 5.1). It provides the Invoke-WebRequest cmdlet. If you are on a modern Windows release, you’re ready to go! For older versions, follow the official PowerShell installation guide.

Some features of Invoke-WebRequest are only available starting with PowerShell 7.x. For details on how to install it, follow the official migration guide from Windows PowerShell 5.1 to PowerShell 7. Note that PowerShell 7.x installs to a new directory and runs side-by-side with Windows PowerShell 5.1.

You can verify the current version of PowerShell on your Windows machine with:

```powershell
$PSVersionTable
```

On PowerShell 7.x, that might print something like:

```powershell
PSVersion                   7.4.1
PSEdition                   Core
GitCommitId                 7.4.1
OS                          Microsoft Windows 10.0.22631
Platform                    Win32NT
PSCompatibleVersions        {1.0, 2.0, 3.0, 4.0…}
PSRemotingProtocolVersion   2.3
SerializationVersion        1.1.0.1
WSManStackVersion           3.0
```

### macOS and Linux

PowerShell 7.x can be installed on both macOS and Linux. However, it does not make much sense to install the entire PowerShell ecosystem on these OSes just to access the Invoke-WebRequest cmdlet. Instead, you might use curl, which comes preinstalled on macOS and most Linux distributions, offering the same capabilities. Learn more in our [curl proxy guide](https://brightdata.com/blog/proxy-101/curl-with-proxies).

## Prerequisites to Get Started with a Proxy in PowerShell

A proxy acts as an intermediary between a client and the destination server: it intercepts your requests, forwards them to the server, receives the responses, and sends them back to you. This way, the destination server sees the requests as coming from the IP and location of the chosen proxy server—not from you.

To get started using a PowerShell proxy with Invoke-WebRequest, you need to understand what a proxy server URL looks like.

This is the URL of a PowerShell Invoke-WebRequest proxy:

```powershell
<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]
```

**That consists of:**

- `PROTOCOL`: The protocol to use to connect to the proxy server.
- `HOST`: The IP address or URL of the proxy server’s hostname.
- `PORT`: The port number the proxy server listens to.
- `USERNAME`: The optional username for proxy authentication.
- `PASSWORD`: The optional password for proxy authentication.

💡 **Important:** The <PROTOCOL>:// part is required by Invoke-WebRequest. If you omit it, the request fails with:
Invoke-WebRequest : This operation is not supported for a relative URI.

In PowerShell 5.1, Invoke-WebRequest only supports HTTP, while in PowerShell 7.x it also supports HTTPS and SOCKS.

Time to retrieve a valid HTTP proxy!

You can find one for free online, for example:

```
Protocol: HTTP; IP Address: 190.6.23.219; Port: 999
```

Compose that into:

```
http://190.6.23.219:999
```

⚠️ **Warning**

Free proxies are okay for learning, but cannot be relied upon in real-world scenarios:

> **Free proxies are unreliable, error-prone, slow, data-greedy, and short-lived. Do not use them!**

Solution? Premium proxies from Bright Data, the best provider in the market. Subscribe and try our reliable proxies for free.

[Bright Data’s proxy services](https://brightdata.com/proxy-types) are protected by authentication so that only trusted users can access them. 

Suppose:

Protocol: `HTTP`
Host: `45.103.203.109`
Port: `9571`
Username: `admin-4521`
Password: `rUuH3tJqf`

Then the Invoke-WebRequest proxy URL is:

```powershell
http://admin-4521:@rUuH3tJqf45.103.203.109:9571
```

## How to Specify an HTTP Proxy in Invoke-WebRequest

Before starting, run:

```powershell
Invoke-WebRequest "https://httpbin.org/ip"
```

That might print:

```yaml
StatusCode         : 200
StatusDescription  : OK
Content           : {
                      "origin": "194.34.233.12"
                    }
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Access-Control-Allow-Origin: *
                    Access-Control-Allow-Credentials: true
                    Content-Length: 32
                    Content-Type: application/json
                    Date: Thu, 01 Feb 2024 10:46:14 GMT...
Forms            : {}
Headers          : {[Connection, keep-alive], [Access-Control-Allow-Origin, *], 
                   [Access-Control-Allow-Credentials, true], [Content-Length, 32]...}
Images           : {}
InputFields      : {}
Links            : {}
ParsedHtml       : mshtml.HTMLDocumentClass
RawContentLength : 32
```

Focus on the **Content** field. That contains **your IP**.

Why? Because `https://httpbin.org/ip` returns the origin IP of the request. So that’s your machine’s IP if no proxy is set.

If you only want the Content field:

```powershell
$response = Invoke-WebRequest "https://httpbin.org/ip"
$response.Content
```

Printing something like:

```json
{
  "origin": "194.34.233.12"
}
```

If you route that request through a proxy, you’ll see the proxy server IP instead. This is a great test to confirm that PowerShell Invoke-WebRequest is indeed using your proxy.

There are a couple of ways to set a PowerShell proxy in Invoke-WebRequest:

## Using a Command Line Option

Invoke-WebRequest offers the `-Proxy` flag:

```powershell
Invoke-WebRequest -Proxy "<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]" <Uri>
```

So:

```powershell
Invoke-WebRequest -Proxy "http://190.6.23.219:999" "https://httpbin.org/ip"

Invoke-WebRequest -Uri "http://httpbin.org/ip" `
  -Proxy "http://brd.superproxy.io:22225" `
  -ProxyCredential (
    New-Object System.Management.Automation.PSCredential(
      "brd-customer-CUSTOMER_ID-zone-ZONE’S_NAME",
      ("ZONE’S_PASSWORD" | ConvertTo-SecureString -AsPlainText -Force)
    )
  )
```

And the result:

```yaml
StatusCode         : 200
StatusDescription  : OK
Content           : {
                      "origin": "190.6.23.219"
                    }
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Access-Control-Allow-Origin: *
                    Access-Control-Allow-Credentials: true
                    Content-Length: 31
                    Content-Type: application/json
                    Date: Thu, 01 Feb 2024 12:36:56 GMT...
Forms            : {}
Headers          : {[Connection, keep-alive], [Access-Control-Allow-Origin, *], 
                   [Access-Control-Allow-Credentials, true], [Content-Length, 31]...}
Images           : {}
InputFields      : {}
Links            : {}
ParsedHtml       : mshtml.HTMLDocumentClass
RawContentLength : 31
```
Here, `origin` matches the proxy server IP, showing the request was proxied. Perfect!

> **Note:** Free proxies are short-lived. If that one fails, pick another.

## Using Environment Variables

Since PowerShell 7.0, Invoke-WebRequest supports proxy configuration via environment variables.

Set two envs:

- `HTTP_PROXY:` Proxy URL for HTTP requests.
- `HTTPS_PROXY:` Proxy URL for HTTPS requests.

On Windows:

```powershell
$env:HTTP_PROXY  = "<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]"
$env:HTTPS_PROXY = "<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]"
```

For example: 

```powershell
$env:HTTP_PROXY  = "http://190.6.23.219:999"
$env:HTTPS_PROXY = "http://190.6.23.219:999"
```

On macOS / Linux:

```bash
export HTTP_PROXY="<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]"
export HTTPS_PROXY="<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]"
```

For example:

```bash
export http_proxy="http://190.6.23.219:999"
export https_proxy="http://190.6.23.219:999"
```

All Invoke-WebRequest calls now go through these proxies automatically. Run:

```powershell
Invoke-WebRequest "https://httpbin.org/ip"
```

You’ll see the proxy IP again in `origin`. Perfect.

To turn off these proxies, unset them:

```powershell
$env:HTTP_PROXY  = ""
$env:HTTPS_PROXY = ""
```

macOS / Linux:

```bash
unset HTTP_PROXY
unset HTTPS_PROXY
```

Then Invoke-WebRequest `"https://httpbin.org/ip"` shows your IP again.

## How To Use HTTPS and SOCKS Proxies in PowerShell

For HTTPS or [SOCKS proxies](https://brightdata.com/solutions/socks5-proxies), you must use PowerShell 7.x+. Otherwise, you get:

```
Invoke-WebRequest : The ServicePointManager does not support proxies with the https scheme.
```

Or for SOCKS:

```
Invoke-WebRequest : The ServicePointManager does not support proxies with the socks scheme.
```

In PowerShell 7.x, the structure is still:

```powershell
Invoke-WebRequest -Proxy "<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]" <Uri>
```

But `<PROTOCOL>` can be `https`, `socks4`, `socks4a`, `socks5`, `socks5a` (instead of just `http`).

If you try a protocol outside these, you get:

```
Invoke-WebRequest: Only the 'http', 'https', 'socks4', 'socks4a' and 'socks5' schemes are allowed for proxies.
```

Example of a SOCKS proxy:

```powershell
Invoke-WebRequest -Proxy "socks5://94.14.109.54:3567" "http://httpbin.org/ip"
```

Output might be:

```yaml
StatusCode         : 200
StatusDescription  : OK
Content           : {
                      "origin": "94.14.109.54"
                    }
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Access-Control-Allow-Origin: *
                    Access-Control-Allow-Credentials: true
                    Content-Length: 31
                    Content-Type: application/json
                    Date: Thu, 01 Feb 2024 12:47:56 GMT...
Forms            : {}
Headers          : {[Connection, keep-alive], [Access-Control-Allow-Origin, *],
                   [Access-Control-Allow-Credentials, true], [Content-Length, 31]...}
Images           : {}
InputFields      : {}
Links            : {}
ParsedHtml       : mshtml.HTMLDocumentClass
RawContentLength : 31
```

## Tips and Tricks You Need to Know

See useful tricks for working with a PowerShell Invoke-WebRequest proxy like a pro.

### Ignore the PowerShell Proxy Configuration

Use `-NoProxy` to skip the configured environment variable proxy:

```powershell
Invoke-WebRequest -NoProxy <Uri>
```

This contacts `<Uri>` without a proxy.

Verify by setting a proxy in envs, then:

```powershell
Invoke-WebRequest -NoProxy "https://httpbin.org/ip"
```

You’ll see your IP, not the proxy’s.

### Avoid SSL Certificate Errors

Using HTTP proxies might fail due to SSL certificate errors. Use -SkipCertificateCheck:

```powershell
Invoke-WebRequest -SkipCertificateCheck -Proxy "<PROTOCOL>://[<USERNAME>:<PASSWORD>]@<HOST>[:<PORT>]" <Uri>
```

`-SkipCertificateCheck` allows insecure server connections. It’s not secure, so only use with trusted hosts.

Example:

```powershell
Invoke-WebRequest -SkipCertificateCheck -Proxy "http://190.6.23.219:999" "https://httpbin.org/ip"
```

## Which PowerShell Proxy Should You Use?

It depends on your Invoke-WebRequest goals. Consider the main proxy types:

- [**Datacenter proxies:**](https://brightdata.com/proxy-types/datacenter-proxies) Fast, cheap, but easily blocked if identified.
- [**Residential proxies:**](https://brightdata.com/proxy-types/residential-proxies) Rotating real IP addresses from devices. Perfect for geo-blocked content or anti-bot evasion.
- [**ISP proxies:**](https://brightdata.com/proxy-types/isp-proxies) Secure, fast, static IPs from ISPs—ideal for SEO or market research.
- [**Mobile proxies:**](https://brightdata.com/proxy-types/mobile-proxies) Real mobile devices, best for mobile-specific apps/sites.

See more in our [guide to proxy IP types](https://brightdata.com/blog/proxy-101/ultimate-guide-to-proxy-types).
