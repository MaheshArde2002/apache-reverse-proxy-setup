# Apache Reverse Proxy with ProxyPass Lab

## 📌 Project Overview

This project demonstrates how to configure **Apache as a Reverse Proxy** using `ProxyPass` and `ProxyPassReverse`.

In this lab, I used **two Linux servers**:

* **Server 1:** Apache Reverse Proxy – `techsupport.com`
* **Server 2:** Apache Backend Server – `travelexplorer.com`

When a user opens:

```text
http://techsupport.com
```

Apache forwards the request to the backend server.

```text
Client
   |
   v
Server 1
Apache Reverse Proxy
   |
   | ProxyPass
   v
Server 2
Apache Web Server
```

---

## 🛠️ Technologies Used

* Linux
* Apache HTTP Server
* VirtualHost
* ProxyPass
* ProxyPassReverse
* HTML
* CSS
* JavaScript

---

# 🖥️ Server Details

### Server 1 – Reverse Proxy

```text
Website: techsupport.com
Directory: /opt/TechSupport
```

### Server 2 – Backend Server

```text
Website: travelexplorer.com
Directory: /opt/TravelExplorer
```

---

# ⚙️ Configuration

## 1. Install Apache

Install Apache on both servers:

```bash
yum install httpd -y
```

Start Apache:

```bash
systemctl start httpd
systemctl enable httpd
```

---

## 2. Create Website Directories

### Server 1

```bash
mkdir /opt/TechSupport
```

Create:

```text
index.html
style.css
javascript.js
```

### Server 2

```bash
mkdir /opt/TravelExplorer
```

Create:

```text
index.html
style.css
javascript.js
```

---

## 3. Configure Backend Server

Edit:

```bash
vim /etc/httpd/conf.d/httpd-vhosts.conf
```

Example:

```apache
<VirtualHost *:80>

    DocumentRoot "/opt/TravelExplorer"
    ServerName travelexplorer.com

</VirtualHost>

<Directory "/opt/TravelExplorer">
    Require all granted
</Directory>
```

---

## 4. Configure Reverse Proxy

On Server 1, edit:

```bash
vim /etc/httpd/conf.d/httpd-vhosts.conf
```

Add:

```apache
<VirtualHost *:80>

    DocumentRoot "/opt/TechSupport"
    ServerName techsupport.com

    ProxyPass / http://travelexplorer.com/
    ProxyPassReverse / http://travelexplorer.com/

</VirtualHost>

<Directory "/opt/TechSupport">
    Require all granted
</Directory>
```

---

## 5. Configure `/etc/hosts`

Add the server IP addresses to `/etc/hosts`.

Example:

```text
192.168.0.106 techsupport.com
192.168.0.107 travelexplorer.com
```

Use your actual server IP addresses.

---

## 6. Test Apache Configuration

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

# 🌐 Testing

Open the browser and enter:

```text
http://techsupport.com
```

The request should go through:

```text
techsupport.com
       |
       v
Reverse Proxy
       |
       v
travelexplorer.com
```

The browser stays on:

```text
http://techsupport.com
```

but the content is served by the backend server.

---

# 📸 Screenshots

The `screenshots` folder contains the step-by-step screenshots of this project:

```text
01_install_httpd_of_proxy_server.png
02_create_techsupport_dir_and_files.png
03_navigate_to_confd_and_open_vhosts.png
04_configure_vhost.png
05_install_httpd_server.png
06_create_travelexplorer_dir_and_files.png
07_open_vhost_config.png
08_configure_backend_vhost.png
09_configure_reverse_proxy_vhost.png
10_verify_server1_ip.png
11_test_httpd_config_and_restart_service.png
12_configure_proxy_hosts_file.png
13_verify_direct_techsupport_site_browser.png
14_verify_active_proxypass_browser.png
```

---

# 🎓 What I Learned

* Installing Apache
* Creating VirtualHosts
* Configuring a Reverse Proxy
* Using `ProxyPass`
* Using `ProxyPassReverse`
* Configuring `/etc/hosts`
* Hosting websites on Apache
* Testing Apache configuration
* Managing Apache services

---

# 👨‍💻 Author

**Mahesh Arde**

Linux | AWS | Apache | IT Support

GitHub: `github.com/MaheshArde2002`
