# What Happens When You Type https://www.google.com and Press Enter?

This is a classic interview question that tests your knowledge of how the web stack works. Let’s walk through it step by step in **beginner-friendly language**, and include a **diagram** that shows the full request flow.

---

## 1. DNS Request (Domain Name System)

- You type `https://www.google.com` in your browser.  
- Your computer needs the **IP address** of Google’s servers (computers only talk in IPs, not names).  
- The browser asks the **DNS system**: “What is the IP of `www.google.com`?”  
- DNS servers reply with an IP address (example).

👉 **DNS = the Internet’s phonebook.**

---

## 2. TCP/IP (How computers talk on the Internet)

- Now that your computer has the IP, it needs to **connect**.  
- The Internet uses the **TCP/IP protocol**:
  - **IP** = address of the machine.  
  - **TCP** = rules to create a reliable connection (make sure packets arrive in order).  
- Your computer opens a **TCP connection** to Google’s server on port **443** (the port for HTTPS).  

---

## 3. Firewall (Security Guard)

- Traffic may go through **firewalls**:
  - On your computer
  - At your ISP
  - At Google’s data center  
- Firewalls act like **security guards**: they block bad traffic and only let through what’s allowed (for example, port 443 for HTTPS).

---

## 4. HTTPS/SSL (Encryption)

- The browser and Google’s server perform an **TLS/SSL handshake**.  
- They exchange keys and set up **encryption**.  
- From now on, all communication is **encrypted**, so no one can read or tamper with your data.  

👉 This is why you see the **padlock icon** in your browser.

---

## 5. Load Balancer (Traffic Director)

- Google runs on many servers.  
- Your request first hits a **load balancer**.  
- The load balancer decides which actual Google server should handle your request.

---

## 6. Web Server (First Stop)

- The request arrives at a **web server** (e.g., Nginx or Apache).  
- The web server:
  - Handles HTTP/HTTPS details.
  - Serves static files (like images or CSS).
  - For dynamic pages, it forwards your request to the **application server**.

---

## 7. Application Server (The Brain)

- The **application server** runs Google’s logic.  
- It decides what to show you (like search results).
- It may need data and will **query a database**.

---

## 8. Database (The Memory)

- The **database** stores and retrieves information quickly.
- The app server asks it for data (like your account info or the web index).
- The database returns the data to the app server.

---

## 9. Response Back to You

- The **application server** builds a response (HTML, JSON, etc.).  
- It passes it back through the **web server → load balancer → the Internet → your browser**.  
- Your browser receives HTML, CSS, and JavaScript, then **renders the page**.

---

## 🔎 Diagram — Full Request Flow

The diagram below shows:
- **DNS resolution**
- The request **hitting the server IP on the correct port (443)**
- **Encrypted traffic** (HTTPS/TLS)
- **Firewall** traversal
- **Load balancer** distributing the request
- **Web server** serving the page (or forwarding)
- **Application server** generating the page
- **Application server querying the database**

```mermaid
sequenceDiagram
    autonumber
    participant B as Browser
    participant DNS as DNS Resolver
    participant FW as Firewall
    participant LB as Load Balancer
    participant WS as Web Server
    participant AS as Application Server
    participant DB as Database

    Note over B: User types https://www.google.com and presses Enter

    B->>DNS: Resolve www.google.com (DNS query)
    DNS-->>B: IP address of Google (example)

    Note over B,LB: TCP connect to IP on port 443 (HTTPS)
    B->>FW: Outbound connection to 443
    FW-->>B: Allow if rules permit
    B->>LB: ClientHello (TLS handshake start)
    LB-->>B: ServerHello + Certificate (TLS)
    Note over B,LB: TLS session established (encrypted)

    B->>LB: HTTPS GET / (encrypted)
    LB->>WS: Forward request to healthy server
    alt Static content
        WS-->>LB: 200 OK (HTML/CSS/JS)
    else Dynamic content
        WS->>AS: Proxy dynamic request
        AS->>DB: Query for data
        DB-->>AS: Rows / documents
        AS-->>WS: Rendered HTML / JSON
        WS-->>LB: 200 OK
    end
    LB-->>B: 200 OK (encrypted response)

    Note over B: Browser renders the page (DOM, CSSOM, JS)