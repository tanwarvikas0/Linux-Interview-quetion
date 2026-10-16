# NGINX Zero to Hero: Study Notes & Q&A

A concise study guide and cheat sheet covering core NGINX concepts: serving static content as a web server, acting as a reverse proxy, distributing load, and terminating SSL/TLS.

---

## 1. What is NGINX & Why Use It?

- **Definition:** An open-source, high-performance web server, reverse proxy, load balancer, and HTTP cache.
- **NGINX vs. Apache HTTPD:**
  - **Apache:** Relies on a process/thread-per-connection model, leading to higher memory consumption under heavy traffic.
  - **NGINX:** Uses an asynchronous, **event-driven architecture**, handling thousands of concurrent requests with low memory footprint.
- **Modern Use:** Standard choice for microservices, cloud deployments, and Kubernetes ingress controllers.

---

## 2. NGINX as a Web Server (Static Content)

- **Default Root Directory:** `/var/www/html/` (used to store static assets like HTML, CSS, JS, and media).
- **Core Configuration Directory:** `/etc/nginx/`
  - `nginx.conf`: Global configurations (worker processes, logging, events).
  - `sites-available/`: Stores virtual host configurations for available domains.
  - `sites-enabled/`: Contains symlinks pointing to active configurations in `sites-available/`.
- **Key Commands:**
  - Verify syntax: `sudo nginx -t`
  - Reload without downtime: `sudo systemctl reload nginx`
  - Restart service: `sudo systemctl restart nginx`

---

## 3. NGINX as a Reverse Proxy

- **Role:** Sits between external clients and internal backend services (Node.js, Python, Java, Go).
- **Benefits:**
  - Shields backend IPs and internal ports from public exposure.
  - Centralizes SSL termination, DDoS protection, rate limiting, and caching.
- **Configuration Pattern:**
  - Defined inside a `location` block using the `proxy_pass` directive:
    ```nginx
    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    ```
- **Common Error:** Returns a `502 Bad Gateway` if the upstream service is stopped or unreachable.

---

## 4. NGINX as a Load Balancer

- **Role:** Distributes incoming HTTP traffic across a pool of backend servers using an `upstream` block.
- **Example Configuration:**
  ```nginx
  upstream backend_pool {
      server 10.0.0.1:8080;
      server 10.0.0.2:8080;
  }

  server {
      listen 80;
      location / {
          proxy_pass http://backend_pool;
      }
  }
  ```
- **Algorithms:**
  - **Round Robin (Default):** Distributes incoming traffic sequentially across all available nodes.
  - **Least Connections (`least_conn`):** Routes requests to the server with the fewest active connections.
  - **IP Hash (`ip_hash`):** Routes requests based on client IP for sticky session persistence.

---

## 5. SSL/TLS Termination

- **Role:** Decrypts inbound HTTPS requests at the proxy layer so backend services receive plain HTTP, offloading CPU-intensive cryptography.
- **Directives:**
  - Configured within the `server` block using:
    - `listen 443 ssl;`
    - `ssl_certificate /path/to/cert.pem;`
    - `ssl_certificate_key /path/to/key.pem;`

---

## Frequently Asked Questions

### Q1: How does NGINX differ architecturally from Apache HTTPD?
**Answer:** Apache creates a separate thread or process for each connection, which consumes significant memory under heavy loads. NGINX utilizes an asynchronous, non-blocking, event-driven loop that can handle tens of thousands of concurrent requests within a single worker process at very low resource cost.

---

### Q2: What is the purpose of `sites-available` vs. `sites-enabled`?
**Answer:** 
- `sites-available`: Holds configuration files for all available sites/virtual hosts.
- `sites-enabled`: Contains symlinks to files in `sites-available`. This allows you to enable or disable individual sites quickly without altering or deleting actual configuration files.

---

### Q3: What causes a `502 Bad Gateway` error in an NGINX reverse proxy setup?
**Answer:** A `502 Bad Gateway` occurs when NGINX is reachable and functioning as a proxy, but cannot establish a connection to or receive a valid response from the backend application specified in `proxy_pass`.

---

### Q4: Which load balancing algorithm in NGINX maintains session persistence?
**Answer:** The `ip_hash` algorithm hashes the client's IP address to map them consistently to the same backend server, ensuring session state continuity.

---

### Q5: What command validates NGINX configuration syntax before applying changes?
**Answer:** Run `sudo nginx -t` to test the configuration syntax. If valid, run `sudo systemctl reload nginx` to apply changes safely without dropping active connections.