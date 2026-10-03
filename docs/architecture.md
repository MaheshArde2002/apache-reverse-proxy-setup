# Apache Reverse Proxy Architecture

## Overview

This project contains two Apache servers.

### Server 1 — Reverse Proxy

```text
Hostname: techsupport.com
Role: Reverse Proxy
Document Root: /opt/TechSupport
```

Server 1 receives requests from the client and forwards them to Server 2 using Apache `ProxyPass`.

### Server 2 — Backend Web Server

```text
Hostname: travelexplorer.com
Role: Backend Web Server
Document Root: /opt/TravelExplorer
```

Server 2 hosts the TravelExplorer website.

## Request Flow

```text
Client Browser
      |
      | http://techsupport.com
      |
      v
+---------------------------+
| Server 1                  |
| Apache Reverse Proxy      |
|                           |
| ProxyPass                 |
| ProxyPassReverse          |
+-------------+-------------+
              |
              | HTTP Request
              v
+---------------------------+
| Server 2                  |
| Apache Backend Server     |
|                           |
| TravelExplorer Website    |
+---------------------------+
```

## Apache Configuration

Server 1 uses:

```apache
ProxyPass / http://travelexplorer.com/
ProxyPassReverse / http://travelexplorer.com/
```

This forwards requests received by `techsupport.com` to the TravelExplorer backend server.

## Result

The user opens:

```text
http://techsupport.com
```

The browser displays the TravelExplorer website while the browser URL remains:

```text
http://techsupport.com
```

