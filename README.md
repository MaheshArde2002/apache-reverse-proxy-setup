# Apache Reverse Proxy with ProxyPass Lab

## 📌 Project Overview

This project demonstrates how to configure an **Apache HTTP Server as a Reverse Proxy** using `ProxyPass` and `ProxyPassReverse`.

In this lab, there are two Linux servers:

* **Server 1:** Apache Reverse Proxy Server with the `TechSupport` website
* **Server 2:** Apache Backend Web Server with the `TravelExplorer` website

When a user opens:

```text
http://techsupport.com
```

the request is received by the Apache server on **Server 1** and forwarded to the backend Apache server on **Server 2**.

The browser continues to display:

```text
techsupport.com
```

while the content is served from the **TravelExplorer** backend server.

---

## 🏗️ Architecture

```text
                    Client Browser
                          |
                          | http://techsupport.com
                          |
                          v
              +-------------------------+
              |      Server 1           |
              |  Apache Reverse Proxy   |
              |                         |
              |    techsupport.com      |
              +-----------+-------------+
                          |
                          | ProxyPass
                          | ProxyPassReverse
                          |
                          v
              +-------------------------+
              |      Server 2           |
              |  Apache Web Server      |
              |                         |
              |    travelexplorer.com   |
              |                         |
              |    TravelExplorer       |
              +-------------------------+
```

---

## 🎯 Project Objective

The main objectives of this project are:

* Configure Apache HTTP Server.
* Create Apache Virtual Hosts.
* Configure an Apache server as a Reverse Proxy.
* Use `ProxyPass` to forward client requests.
* Use `ProxyPassReverse` for backend response handling.
* Host a website on a separate backend server.
* Test reverse proxy functionality using a web browser.
* Understand communication between a reverse proxy and backend web server.

---

## 🛠️ Technologies Used

* Linux
* Apache HTTP Server
* Apache VirtualHost
* `mod_proxy`
* `mod_proxy_http`
* ProxyPass
* ProxyPassReverse
* HTML
* CSS
* JavaScript

---

# 🖥️ Server Details

## Server 1 – Reverse Proxy Server

**Role:** Apache Reverse Proxy

```text
Hostname: Proxy-Server
Website: techsupport.com
Document Root: /opt/TechSupport
```

Files:

```text
/opt/TechSupport/
├── index.html
├── style.css
└── javascript.js
```

---

## Server 2 – Backend Web Server

**Role:** Apache Backend Web Server

```text
Hostname: Web-Server
Website: travelexplorer.com
Document Root: /opt/TravelExplorer
```

Files:

```text
/opt/TravelExplorer/
├── index.html
├── style.css
└── javascript.js
```

---

# 📂 Project Structure

```text
apache-reverse-proxy-proxypass-lab/
│
├── README.md
│
├──  httpd-server-reverse-proxy/
│   ├── apache/
│   │   └── httpd-vhosts.conf
│   │
│   └── TechSupport/
│       ├── index.html
│       ├── style.css
│       └── javascript.js
│
├── server-1/
│   ├── apache/
│   │   └── httpd-vhosts.conf
│   │
│   └── TravelExplorer/
│       ├── index.html
│       ├── style.css
│       └── javascript.js
│
├── screenshots/
│   ├── 01_install_httpd_proxy_server.png
│   ├── 02_create_techsupport_files.png
│   ├── 03_configure_proxy_vhost.png
│   ├── 04_install_httpd_backend_server.png
│   ├── 05_create_travelexplorer_files.png
│   ├── 06_configure_backend_vhost.png
│   └── 07_verify_proxypass_browser.png
│
└── docs/
    └── architecture.md
```

---

# ⚙️ Configuration

## 1. Install Apache

Install Apache HTTP Server on both servers.

### RHEL / CentOS

```bash
yum install httpd -y
```

Start and enable Apache:

```bash
systemctl start httpd
systemctl enable httpd
```

Check the service:

```bash
systemctl status httpd
```

---

# 🌐 Server 2 – Backend Configuration

Create the TravelExplorer directory:

```bash
cd /opt
mkdir TravelExplorer
```

Create the website files:

```bash
cd /opt/TravelExplorer

touch index.html
touch style.css
touch javascript.js
```

---

## Backend VirtualHost

Edit:

```bash
vim /etc/httpd/conf.d/httpd-vhosts.conf
```

Add:

```apache
<VirtualHost *:80>

    ServerAdmin webmaster@dummy-host.example.com

    DocumentRoot "/opt/TravelExplorer"

    ServerName travelexplorer.com
    ServerAlias www.travelexplorer.com

    ErrorLog "/var/log/httpd/travelexplorer-error_log"
    CustomLog "/var/log/httpd/travelexplorer-access_log" common

</VirtualHost>

<Directory "/opt/TravelExplorer">
    Require all granted
</Directory>
```

Test the Apache configuration:

```bash
httpd -t
```

Expected:

```text
Syntax OK
```

Restart Apache:

```bash
systemctl restart httpd
```

---

# 🔄 Server 1 – Reverse Proxy Configuration

Create the TechSupport directory:

```bash
cd /opt
mkdir TechSupport
```

Create website files:

```bash
cd /opt/TechSupport

touch index.html
touch style.css
touch javascript.js
```

---

## Enable Apache Proxy Modules

The Reverse Proxy uses Apache proxy modules such as:

```text
mod_proxy
mod_proxy_http
```

Verify them:

```bash
httpd -M | grep proxy
```

Expected modules include:

```text
proxy_module
proxy_http_module
```

---

## Reverse Proxy VirtualHost

Edit:

```bash
vim /etc/httpd/conf.d/httpd-vhosts.conf
```

Configure:

```apache
<VirtualHost *:80>

    ServerAdmin webmaster@dummy-host.example.com

    DocumentRoot "/opt/TechSupport"

    ServerName techsupport.com
    ServerAlias www.techsupport.com

    ProxyPass / http://travelexplorer.com/
    ProxyPassReverse / http://travelexplorer.com/

    ErrorLog "/var/log/httpd/techsupport-error_log"
    CustomLog "/var/log/httpd/techsupport-access_log" common

</VirtualHost>

<Directory "/opt/TechSupport">
    Require all granted
</Directory>
```

Test the configuration:

```bash
httpd -t
```

Expected:

```text
Syntax OK
```

Restart Apache:

```bash
systemctl restart httpd
```

---

# 🧩 How ProxyPass Works

The important configuration is:

```apache
ProxyPass / http://travelexplorer.com/
```

This tells Apache:

> Forward incoming requests to the backend server.

For example:

```text
Client
  |
  | http://techsupport.com
  |
  v
Server 1
  |
  | ProxyPass
  |
  v
http://travelexplorer.com/
  |
  v
Server 2
```

---

## ProxyPassReverse

```apache
ProxyPassReverse / http://travelexplorer.com/
```

`ProxyPassReverse` helps Apache handle HTTP response headers from the backend server correctly, particularly redirects.

---

# 📝 Hosts File Configuration

For the domain names to work in a lab environment, add entries to `/etc/hosts`.

### Server 1

```bash
vim /etc/hosts
```

Example:

```text
192.168.0.107 travelexplorer.com
```

Replace `192.168.0.107` with the actual IP address of Server 2.

For local client testing, add:

```text
192.168.0.106 techsupport.com
```

Replace `192.168.0.106` with the actual IP address of Server 1.

---

# 🧪 Testing

## Test Backend Server

From Server 2:

```bash
curl http://localhost/
```

Test the website files:

```bash
curl -I http://localhost/style.css
```

```bash
curl -I http://localhost/javascript.js
```

---

## Test Domain Resolution

From Server 1:

```bash
getent hosts travelexplorer.com
```

Expected:

```text
192.168.0.107 travelexplorer.com
```

---

## Test Backend Through Proxy Server

From Server 1:

```bash
curl http://travelexplorer.com/
```

Then test the proxy:

```bash
curl http://techsupport.com/
```

---

# 🌍 Browser Testing

Open a browser and enter:

```text
http://techsupport.com
```

The browser should display the **TravelExplorer** website.

Expected flow:

```text
Browser
   |
   | http://techsupport.com
   |
   v
Apache Reverse Proxy
   |
   | ProxyPass
   |
   v
TravelExplorer Backend
```

The browser URL remains:

```text
http://techsupport.com
```

while the page content comes from:

```text
http://travelexplorer.com
```

---

# 🔧 Troubleshooting

## Check Apache Configuration

```bash
httpd -t
```

Expected:

```text
Syntax OK
```

---

## Check Apache Status

```bash
systemctl status httpd
```

---

## Check Listening Port

```bash
ss -lntp | grep :80
```

Apache should be listening on port `80`.

---

## Check Proxy Modules

```bash
httpd -M | grep proxy
```

Look for:

```text
proxy_module
proxy_http_module
```

---

## Check Apache Logs

Reverse proxy error log:

```bash
tail -f /var/log/httpd/techsupport-error_log
```

Backend access log:

```bash
tail -f /var/log/httpd/travelexplorer-access_log
```

---

## CSS or JavaScript Not Loading

Check the files on Server 2:

```bash
ls -l /opt/TravelExplorer/
```

Expected:

```text
index.html
style.css
javascript.js
```

Test:

```bash
curl -I http://travelexplorer.com/style.css
```

```bash
curl -I http://travelexplorer.com/javascript.js
```

Also make sure the HTML uses the correct relative paths:

```html
<link rel="stylesheet" href="style.css">
<script src="javascript.js"></script>
```

---

# 📸 Screenshots

The `screenshots/` directory contains screenshots showing:

1. Installing Apache on the Reverse Proxy Server
2. Creating the TechSupport website
3. Configuring the Apache VirtualHost
4. Installing Apache on the Backend Server
5. Creating the TravelExplorer website
6. Configuring the Backend VirtualHost
7. Configuring ProxyPass and ProxyPassReverse
8. Testing the website through the browser

---

# 📚 Key Apache Directives

| Directive             | Purpose                             |
| --------------------- | ----------------------------------- |
| `VirtualHost`         | Creates a virtual host              |
| `ServerName`          | Defines the hostname                |
| `ServerAlias`         | Defines additional hostnames        |
| `DocumentRoot`        | Defines website files location      |
| `ProxyPass`           | Forwards requests to backend server |
| `ProxyPassReverse`    | Handles backend response headers    |
| `ErrorLog`            | Stores Apache error logs            |
| `CustomLog`           | Stores Apache access logs           |
| `Require all granted` | Allows access to the directory      |

---

# 🎓 What I Learned

Through this project, I practiced:

* Apache HTTP Server installation
* Apache VirtualHost configuration
* Web server configuration
* Reverse Proxy configuration
* `ProxyPass` and `ProxyPassReverse`
* Apache proxy modules
* Hostname resolution using `/etc/hosts`
* HTTP request forwarding
* Web server troubleshooting
* Apache logs
* Linux service management
* Testing using `curl`
* HTML, CSS and JavaScript deployment

---

# 👨‍💻 Author

**Mahesh Arde**

BCA Computer Science
Linux | AWS | Apache | IT Support

GitHub:

`https://github.com/MaheshArde2002`
