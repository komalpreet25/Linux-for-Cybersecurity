
# 14. Nginx Basics for Cybersecurity

## 1. Introduction to Nginx

**Nginx** (pronounced *engine-x*) is a high-performance web server that can also function as a reverse proxy, load balancer, HTTP cache, and mail proxy.

In cybersecurity, understanding Nginx helps you investigate web traffic, secure HTTP services, analyze access logs, configure HTTPS, and identify common web-server misconfigurations.

### Common Use Cases

- Hosting static websites and web applications.
- Acting as a reverse proxy for backend applications.
- Terminating TLS connections for HTTPS.
- Distributing requests across multiple backend servers.
- Serving as an entry point for application monitoring.
- Logging HTTP requests and responses.
- Applying basic request-rate limits and access restrictions.

### Nginx vs. Apache

| Feature | Nginx | Apache HTTP Server |
|---|---|---|
| Architecture | Event-driven, asynchronous | Process/thread-based models available |
| Static content | Efficiently served | Supported |
| Reverse proxy | Supported | Supported |
| Load balancing | Supported | Supported through modules |
| Configuration | Directive-based | Directive-based |
| Dynamic applications | Usually forwards requests to an application runtime | Can integrate with runtimes using modules or external services |

Neither server is automatically more secure. Security depends on configuration, software versions, application behavior, and operational practices.

---

## 2. How Nginx Works

Nginx commonly uses a master process and worker processes.

- **Master process:** Reads configuration, manages workers, and handles lifecycle operations.
- **Worker processes:** Handle client connections and process requests.
- **Client:** Browser, API client, monitoring system, or another HTTP client.
- **Backend application:** Optional application server that processes dynamic requests.
- **Log files:** Record configured access and error events.

### Basic Architecture

```text
                 Client / Browser
                        |
                        | HTTP / HTTPS
                        v
                +------------------+
                |      Nginx       |
                |  Reverse Proxy   |
                +------------------+
                        |
              +---------+----------+
              |                    |
              v                    v
       Static Content       Backend Application
       HTML / CSS / JS      API / Application
              |                    |
              +---------+----------+
                        |
                        v
                Response to Client
```

This is a conceptual architecture. A simple website may serve all content directly from Nginx without a backend application.

### Request Processing Overview

1. A client connects to the server's IP address and port.
2. Nginx selects the appropriate listening server and virtual host.
3. It evaluates the request URI and applicable configuration.
4. It serves a static resource or forwards the request to an upstream application.
5. It returns the response to the client.
6. Configured access and error logs record relevant events.

---

## 3. Install and Verify Nginx

The following commands are for Debian-based distributions, including Ubuntu, Kali Linux, and Parrot OS.

Update package information:

```bash
sudo apt update
```

Install Nginx:

```bash
sudo apt install nginx
```

Check the installed version:

```bash
nginx -v
```

Display detailed build information:

```bash
nginx -V
```

Check service status:

```bash
systemctl status nginx
```

Start Nginx:

```bash
sudo systemctl start nginx
```

Enable it at system startup:

```bash
sudo systemctl enable nginx
```

Start and enable it together:

```bash
sudo systemctl enable --now nginx
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

Reload configuration without a full service restart:

```bash
sudo systemctl reload nginx
```

Stop Nginx:

```bash
sudo systemctl stop nginx
```

**Security note:** Starting Nginx may expose a web service to other systems, depending on the network and firewall configuration. Use a lab environment or restrict access when testing.

---

## 4. Important Nginx Files and Directories

On many Debian-based systems, common paths include:

| Path | Purpose |
|---|---|
| `/etc/nginx/nginx.conf` | Main configuration |
| `/etc/nginx/sites-available/` | Available virtual-host configurations |
| `/etc/nginx/sites-enabled/` | Enabled virtual-host configurations, often symlinks |
| `/etc/nginx/conf.d/` | Additional configuration files |
| `/var/www/html/` | Common default website directory |
| `/var/log/nginx/access.log` | HTTP access log |
| `/var/log/nginx/error.log` | Error and diagnostic log |
| `/usr/sbin/nginx` | Common Nginx executable path |

Paths can vary by operating system, package, and installation method.

Inspect the main configuration:

```bash
sudo less /etc/nginx/nginx.conf
```

List available site configurations:

```bash
ls -l /etc/nginx/sites-available/
```

List enabled sites:

```bash
ls -l /etc/nginx/sites-enabled/
```

Inspect the complete effective configuration, including included files:

```bash
sudo nginx -T
```

The output may contain internal hostnames, paths, and configuration details. Review it before sharing.

---

## 5. Host a Basic Website

### Step 1: Create a Test Page

On a lab machine, create a simple HTML page:

```bash
sudo mkdir -p /var/www/labsite
sudo nano /var/www/labsite/index.html
```

Add:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Nginx Security Lab</title>
</head>
<body>
    <h1>Nginx is working</h1>
    <p>This is a local cybersecurity training page.</p>
</body>
</html>
```

### Step 2: Configure a Virtual Host

Create a configuration file:

```bash
sudo nano /etc/nginx/sites-available/labsite
```

Add the following:

```nginx
server {
    listen 8080;
    server_name localhost;

    root /var/www/labsite;
    index index.html;

    access_log /var/log/nginx/labsite_access.log;
    error_log /var/log/nginx/labsite_error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

This example listens on TCP port `8080`, rather than the default HTTP port 80.

### Step 3: Enable the Site

On Debian-based systems that use `sites-available` and `sites-enabled`:

```bash
sudo ln -s /etc/nginx/sites-available/labsite \
/etc/nginx/sites-enabled/labsite
```

If the link already exists, do not create a duplicate.

### Step 4: Test and Reload

Validate the configuration:

```bash
sudo nginx -t
```

If validation succeeds:

```bash
sudo systemctl reload nginx
```

### Step 5: Test the Website

```bash
curl -i http://127.0.0.1:8080/
```

Expected behavior: the response contains an HTTP success status, usually `200 OK`, and the HTML content.

If the service is inaccessible, check its listening socket, firewall rules, service state, and error log.

---

## 6. Nginx Configuration Fundamentals

Nginx configuration consists of directives organized into contexts.

### Common Contexts

- **Main context:** Global settings.
- **Events context:** Connection-processing settings.
- **HTTP context:** HTTP-related configuration.
- **Server context:** A virtual host for a listening address or hostname.
- **Location context:** Rules for matching request URIs.

Example:

```nginx
http {
    server {
        listen 8080;
        server_name localhost;

        location / {
            root /var/www/labsite;
            index index.html;
        }
    }
}
```

This illustrates the hierarchy. Existing installations already define the outer `http` context in their main configuration; do not blindly paste another outer block into an included site file.

### Important Directives

| Directive | Purpose |
|---|---|
| `listen` | Defines the address and port |
| `server_name` | Matches a requested hostname |
| `root` | Defines the document root |
| `index` | Defines default index files |
| `location` | Matches request URI patterns |
| `try_files` | Checks candidate files and handles fallback |
| `access_log` | Configures request logging |
| `error_log` | Configures diagnostic logging |
| `client_max_body_size` | Limits the maximum accepted request-body size |
| `proxy_pass` | Forwards requests to an upstream server |
| `return` | Returns a status code or redirects |
| `limit_req` | Applies a request-rate limit |

A directive's effect depends on its context, inheritance, and the complete configuration.

---

## 7. Nginx Access and Error Logs

Logs are valuable for troubleshooting and security monitoring.

### Access Logs

Access logs can record:
- Client IP address.
- Request timestamp.
- HTTP method and requested URI.
- HTTP response status.
- Response size.
- Referrer.
- User-Agent.
- Request processing time, if configured.

View recent access events:

```bash
sudo tail -n 50 /var/log/nginx/access.log
```

Follow incoming requests:

```bash
sudo tail -f /var/log/nginx/access.log
```

Search for HTTP 404 responses:

```bash
sudo grep '" 404 ' /var/log/nginx/access.log
```

Search for HTTP 500 responses:

```bash
sudo grep '" 500 ' /var/log/nginx/access.log
```

Search for suspicious-looking request paths:

```bash
sudo grep -Ei '(\.\./|/\.env|/wp-admin|/phpmyadmin)' \
/var/log/nginx/access.log
```

These strings are investigative indicators, not proof of a successful attack.

### Error Logs

View recent errors:

```bash
sudo tail -n 50 /var/log/nginx/error.log
```

Follow error events:

```bash
sudo tail -f /var/log/nginx/error.log
```

Search for permission-related messages:

```bash
sudo grep -i "permission denied" /var/log/nginx/error.log
```

### Security Investigation

When reviewing suspicious traffic, correlate:
1. Source IP and request timestamp.
2. HTTP method and requested URI.
3. Response status and response size.
4. Repeated requests and request frequency.
5. Nginx error messages.
6. Backend application logs.
7. Authentication and operating-system logs.

An HTTP `404` often means a resource was not found. An HTTP `403` generally indicates that access was forbidden. Neither status alone confirms malicious activity.

---

## 8. Understanding HTTP Status Codes

| Status | Meaning | Security / Troubleshooting Context |
|---|---|---|
| `200` | OK | Request succeeded |
| `301` | Moved Permanently | Permanent redirect |
| `302` | Found | Temporary redirect |
| `400` | Bad Request | Invalid or malformed request |
| `401` | Unauthorized | Authentication is required or failed |
| `403` | Forbidden | Request is not permitted |
| `404` | Not Found | Requested resource is unavailable |
| `405` | Method Not Allowed | HTTP method is not supported for that resource |
| `413` | Content Too Large | Request body exceeds a configured limit |
| `429` | Too Many Requests | Rate limiting or application throttling |
| `500` | Internal Server Error | Server-side processing failure |
| `502` | Bad Gateway | Invalid response from an upstream server |
| `503` | Service Unavailable | Service temporarily unavailable |
| `504` | Gateway Timeout | Upstream server did not respond in time |

**Important:** Status codes provide context, but security conclusions require additional evidence.

---

## 9. Reverse Proxy Configuration

A reverse proxy receives requests from clients and forwards them to backend services.

Common benefits include:
- Hiding backend service details from direct client access.
- Centralizing TLS configuration.
- Routing requests to different applications.
- Applying request limits and other controls.
- Collecting HTTP access logs.

### Example

Assume a backend application runs on the same machine at `127.0.0.1:3000`.

Create a server block such as:

```nginx
server {
    listen 8081;
    server_name localhost;

    location / {
        proxy_pass http://127.0.0.1:3000;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Test and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Test the proxy:

```bash
curl -i http://127.0.0.1:8081/
```

The backend must already be running and listening on the specified address and port.

### Trusted Proxy Considerations

Forwarded headers can carry client-address and protocol information, but they are not automatically trustworthy.

- Configure the application to trust forwarding headers only from known proxy addresses.
- Prevent untrusted clients from directly reaching the backend where possible.
- Understand how the proxy and application interpret `X-Forwarded-For`.
- Avoid assuming a client IP supplied in an HTTP header is genuine.

Incorrect trust settings can undermine IP-based access controls and audit logs.

---

## 10. HTTPS and TLS Basics

HTTPS uses HTTP over TLS to provide confidentiality and integrity in transit, together with server authentication when certificate validation succeeds.

### Typical Requirements

- A certificate appropriate for the hostname.
- A corresponding private key.
- A valid certificate chain.
- Correct hostname and expiry validation.
- A secure TLS configuration.
- Renewal and monitoring procedures.

A common production pattern is:
1. The client connects using HTTPS.
2. Nginx presents its certificate.
3. The client validates the certificate and hostname.
4. TLS establishes protected communication.
5. Nginx serves the request or forwards it to the backend.

### Inspect a Certificate

For a local certificate file:

```bash
openssl x509 -in certificate.crt -noout -subject -issuer -dates
```

For a server you are authorized to test:

```bash
openssl s_client -connect example.com:443 -servername example.com
```

Replace `example.com` with your actual hostname.

This command displays TLS connection details. Verify the certificate chain and hostname rather than treating a successful TCP connection as proof of valid HTTPS.

### Local Lab Certificates

Self-signed certificates can be useful for local training, but browsers will not automatically trust them. Do not use a self-signed certificate as a substitute for proper certificate management on a public production service.

**Security note:** TLS protects data in transit; it does not automatically secure vulnerable application code, weak authentication, exposed backend ports, or sensitive information written to logs.

---

## 11. Nginx Security Hardening

Security hardening reduces information exposure and helps control how the web server handles requests.

### 11.1 Hide the Nginx Version

In the appropriate `http`, `server`, or `location` context:

```nginx
server_tokens off;
```

This reduces version disclosure in some server responses and error pages. It does not patch vulnerabilities or replace software updates.

### 11.2 Disable Directory Listing

Avoid enabling automatic directory indexes unless they are explicitly required.

For a location where directory listing must be disabled:

```nginx
location / {
    autoindex off;
    try_files $uri $uri/ =404;
}
```

### 11.3 Limit Request-Body Size

For an application with a known upload-size requirement:

```nginx
server {
    client_max_body_size 10m;
}
```

Choose a limit appropriate for the application. This can reduce oversized-request exposure, but application-level validation is still necessary.

### 11.4 Restrict Access to Sensitive Paths

Example for a site where hidden files should not be served:

```nginx
location ~ /\.(?!well-known/) {
    deny all;
}
```

Review the effect carefully for the application's needs. A rule should not be treated as a substitute for keeping secrets outside the web document root.

### 11.5 Set Security Headers

A starting example for a simple HTTPS website is:

```nginx
add_header X-Content-Type-Options "nosniff" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header X-Frame-Options "SAMEORIGIN" always;
```

Header requirements depend on the application. For example, Content Security Policy requires a policy compatible with the site's scripts, styles, and other resources.

Do not enable HSTS blindly on a domain: first ensure HTTPS works correctly across the intended hostnames and subdomains.

### 11.6 Restrict HTTP Methods Where Appropriate

If a static resource should support only GET and HEAD:

```nginx
location /static/ {
    limit_except GET HEAD {
        deny all;
    }
}
```

This is not a universal rule for API routes. Applications may legitimately require POST, PUT, PATCH, DELETE, or other methods.

### 11.7 Apply Changes Safely

Back up the relevant configuration:

```bash
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.backup
```

Validate:

```bash
sudo nginx -t
```

Reload only after successful validation:

```bash
sudo systemctl reload nginx
```

Then test normal functionality and the intended security restrictions.

---

## 12. Basic Rate Limiting

Rate limiting can reduce excessive request rates and help protect selected endpoints from abuse.

Define a request zone in the `http` context of the main configuration:

```nginx
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=5r/s;
```

Apply it to a suitable endpoint in a server configuration:

```nginx
location /api/ {
    limit_req zone=api_limit burst=10 nodelay;
    proxy_pass http://127.0.0.1:3000;
}
```

Then validate and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### How It Works

- `$binary_remote_addr` identifies the client address used by the rate limiter.
- `zone=api_limit:10m` defines shared storage for tracking request state.
- `rate=5r/s` sets the configured average request rate.
- `burst=10` allows a limited burst above the average rate.
- `nodelay` controls how burst requests are handled.

Rate limits must be tuned for real traffic and application requirements. Behind a reverse proxy, `$remote_addr` may identify the proxy instead of the original client unless real-client IP handling is configured securely.

Rate limiting is not a complete DDoS defense and may require upstream network protections.

---

## 13. Common Nginx Troubleshooting

### Problem 1: Nginx Fails to Start

Check service status:

```bash
sudo systemctl status nginx
```

Validate configuration:

```bash
sudo nginx -t
```

Inspect logs:

```bash
sudo journalctl -u nginx --no-pager -n 50
```

Common causes:
- Syntax errors.
- Port already in use.
- Missing certificate or key files.
- Incorrect paths.
- Invalid permissions.

### Problem 2: Address Already in Use

Check listening ports:

```bash
sudo ss -tlnp
```

Identify which process is listening on the required port. Do not stop an unrelated service without confirming its purpose.

### Problem 3: HTTP 403 Forbidden

Possible causes:
- File or directory permissions.
- Incorrect document root.
- Access-control directives.
- Missing directory index with directory listing disabled.
- Security policy restrictions.

Inspect:

```bash
namei -l /var/www/labsite/index.html
sudo tail -n 50 /var/log/nginx/error.log
```

Nginx needs suitable access to the relevant files and parent directories. Avoid making web directories world-writable as a quick fix.

### Problem 4: HTTP 404 Not Found

Check:
- The requested URI.
- `root` or `alias` configuration.
- Whether the file exists.
- `try_files` behavior.
- Which virtual host handled the request.

Test locally:

```bash
curl -i http://127.0.0.1:8080/
```

### Problem 5: HTTP 502 Bad Gateway

Common causes:
- Backend application is stopped.
- Incorrect upstream address or port.
- Connection refused by the backend.
- Backend socket permissions.
- Network or protocol mismatch.

Check the backend service and its listening port:

```bash
sudo ss -tlnp
```

Review the Nginx error log and test the upstream directly when appropriate.

### Problem 6: Configuration Changes Do Not Work

Display the complete configuration:

```bash
sudo nginx -T
```

Check for:
- Configuration files not included.
- Duplicate or conflicting server blocks.
- Incorrect hostname matching.
- Unexpected `location` precedence.
- Failed reloads.
- Browser or proxy caching.

Always run `nginx -t` before applying changes.

---

## 14. Practical Cybersecurity Labs

Run these exercises on a local VM or an authorized test system.

### Lab 1: Install and Analyze Nginx

**Objective:** Understand the service and its listening ports.

Tasks:
1. Install Nginx.
2. Start the service.
3. Identify its version.
4. Identify its listening port.
5. Retrieve the default page using `curl`.
6. Inspect the access and error logs.

Commands:

```bash
sudo apt update
sudo apt install nginx
sudo systemctl enable --now nginx
nginx -v
sudo ss -tlnp
curl -i http://127.0.0.1/
```

**Expected result:** You can identify the running service and verify an HTTP response.

### Lab 2: Configure a Virtual Host

**Objective:** Serve a custom HTML page.

Tasks:
1. Create `/var/www/labsite`.
2. Add an HTML file.
3. Create a server block on port 8080.
4. Enable the configuration.
5. Validate and reload Nginx.
6. Test the page with `curl`.

**Expected result:** The custom page is returned from the configured virtual host.

### Lab 3: Investigate Web Logs

**Objective:** Identify different request outcomes.

Generate requests to the local test server:

```bash
curl -i http://127.0.0.1:8080/
curl -i http://127.0.0.1:8080/missing-page
```

Inspect the access log:

```bash
sudo tail -n 50 /var/log/nginx/labsite_access.log
```

Tasks:
1. Find the successful request.
2. Find the missing-page request.
3. Compare status codes and requested URIs.
4. Correlate the requests with timestamps.

**Expected result:** You can explain the difference between a successful response and a not-found response.

### Lab 4: Apply Security Headers

**Objective:** Add basic response-security headers.

Tasks:
1. Add the example headers from Section 11 to your test server.
2. Validate and reload Nginx.
3. Inspect the response headers.

Command:

```bash
curl -sSI http://127.0.0.1:8080/
```

**Expected result:** The configured headers appear in the response.

### Lab 5: Test a Reverse Proxy

**Objective:** Forward requests to a local backend application.

Tasks:
1. Start a simple HTTP application on `127.0.0.1:3000`.
2. Configure Nginx to listen on port 8081.
3. Forward requests to the backend.
4. Test the endpoint with `curl`.
5. Stop the backend and observe the resulting error.
6. Review the Nginx error log.

**Expected result:** You can distinguish a proxy configuration issue from an unavailable backend.

---

## 15. Nginx and Incident Response

When investigating a potentially compromised website, Nginx logs may reveal reconnaissance, automated scanning, application errors, or suspicious request patterns.

### Investigation Workflow

1. Preserve relevant access and error logs.
2. Record the incident time range and affected hostname.
3. Identify unusual request paths, methods, source addresses, and response codes.
4. Correlate Nginx events with application and operating-system logs.
5. Check for unexpected configuration changes.
6. Review file modifications and application deployments.
7. Determine whether requests reached the backend.
8. Contain the issue and apply approved fixes.
9. Verify service health and retain incident evidence.

### Important Limitations

- A request in the access log does not prove the requested action succeeded.
- A `200` response does not prove that a request was benign.
- Source IP addresses may represent proxies, NAT gateways, or shared networks.
- Logs may omit details depending on the configured log format.
- Sensitive data may appear in URLs or request logs, so protect logs from unauthorized access.

---

## 16. Best Practices

1. Keep Nginx and the operating system updated.
2. Remove unused default sites and unnecessary modules or services.
3. Use HTTPS with correct certificate validation and renewal.
4. Keep private keys and application secrets outside public web directories.
5. Restrict backend services to trusted networks or local interfaces where appropriate.
6. Disable directory listing unless explicitly needed.
7. Avoid disclosing unnecessary server-version information.
8. Configure suitable request-size and rate limits.
9. Apply security headers that match the application's requirements.
10. Protect logs and monitor suspicious patterns.
11. Validate configuration changes before reloading.
12. Back up configurations and maintain a recovery procedure.
13. Avoid overly permissive file permissions.
14. Verify application behavior after every security change.
15. Treat reverse-proxy headers as untrusted unless they come from a trusted proxy.

---

## 17. Common Nginx Commands Cheat Sheet

| Task | Command |
|---|---|
| Check version | `nginx -v` |
| Show build details | `nginx -V` |
| Check service status | `systemctl status nginx` |
| Start Nginx | `sudo systemctl start nginx` |
| Enable at boot | `sudo systemctl enable nginx` |
| Validate configuration | `sudo nginx -t` |
| Display full configuration | `sudo nginx -T` |
| Reload configuration | `sudo systemctl reload nginx` |
| Restart service | `sudo systemctl restart nginx` |
| View access log | `sudo tail -f /var/log/nginx/access.log` |
| View error log | `sudo tail -f /var/log/nginx/error.log` |
| Check listening ports | `sudo ss -tlnp` |
| Test HTTP response | `curl -i http://127.0.0.1/` |
| Inspect response headers | `curl -sSI http://127.0.0.1/` |
| Review service logs | `sudo journalctl -u nginx` |

---

## 18. Interview Questions and Answers

### Q1. What is Nginx?

Nginx is a web server that can also provide reverse proxying, load balancing, caching, and other network services.

### Q2. What is a reverse proxy?

A reverse proxy receives client requests and forwards them to backend services, returning the responses to clients.

### Q3. What is the difference between a web server and an application server?

A web server handles HTTP connections and can serve static content. An application server or runtime executes application logic. A deployment may use both.

### Q4. What is the purpose of `nginx -t`?

It checks the Nginx configuration for syntax errors and attempts to open referenced files.

### Q5. What is the difference between access and error logs?

Access logs record HTTP requests and their configured details. Error logs record operational issues and diagnostic messages.

### Q6. What does HTTP 502 mean?

It generally means a gateway or proxy received an invalid response from an upstream server or could not successfully communicate with it.

### Q7. Why is HTTPS important?

HTTPS protects HTTP communication in transit and supports server authentication when certificate validation is successful.

### Q8. What does `server_tokens off` do?

It reduces disclosure of the Nginx version in certain responses and error pages. It does not replace patching.

### Q9. What is rate limiting?

Rate limiting restricts how frequently requests are accepted or processed according to configured rules.

### Q10. Why should backend applications not always listen on public interfaces?

Restricting backend access can prevent clients from bypassing the reverse proxy's intended controls and logging.

### Q11. How would you investigate repeated 404 responses?

Review access logs, requested paths, source addresses, timestamps, and surrounding requests. Determine whether the pattern reflects normal browsing, misconfiguration, or suspicious scanning.

### Q12. How do you troubleshoot a 403 error?

Check file permissions, document-root paths, location rules, access controls, and the error log.

### Q13. Does Nginx automatically protect an application from SQL injection?

No. Nginx is not a substitute for secure application code, parameterized database queries, input handling, and appropriate application security controls.

### Q14. What is the purpose of `proxy_pass`?

It specifies the upstream destination to which Nginx forwards matching requests.

### Q15. Why should you avoid blindly copying Nginx hardening configurations?

A configuration that is secure for one application may break another. Changes must be tested against the application's functionality, security requirements, and deployment architecture.

---

## 19. Knowledge Check

Answer these questions without referring to the earlier sections:

1. What are the main responsibilities of Nginx's master and worker processes?
2. What is the difference between a reverse proxy and a forward proxy?
3. Which command validates the Nginx configuration?
4. What information can access logs provide during an investigation?
5. What are common causes of HTTP 403, 404, and 502 responses?
6. Why should a backend service be restricted to trusted interfaces where possible?
7. Why is HTTPS alone insufficient to secure a web application?
8. How can rate limiting help protect an API?
9. Why must proxy forwarding headers be configured carefully?
10. What steps should be taken before applying a production configuration change?

---

## 20. Summary

Nginx is an important component of modern web infrastructure. For cybersecurity work, focus on understanding its configuration hierarchy, virtual hosts, reverse proxy behavior, HTTPS, access controls, request limits, and log analysis.

**Key takeaway:** A secure Nginx deployment requires a properly configured web server, protected backend services, valid TLS, appropriate access controls, reliable logging, and continuous patching.

