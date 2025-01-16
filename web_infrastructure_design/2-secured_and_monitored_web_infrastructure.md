### Secured and Monitored Web Infrastructure for www.foobar.com

#### Infrastructure Overview
We are designing a secured and monitored web infrastructure for hosting the website www.foobar.com. This infrastructure will include security mechanisms (firewalls, HTTPS) and monitoring tools to ensure performance and reliability.

---

### Key Components and Their Roles

1. **Firewalls (3)**
   - **Purpose:** Protect the system by filtering unauthorized traffic.
   - **Placement:**
     - Between the internet and the load balancer.
     - Between the load balancer and the web/application servers.
     - Between the application servers and the database.
   - **Function:**
     - Inspect incoming and outgoing traffic.
     - Block unauthorized access and potential attacks.

2. **SSL Certificate**
   - **Purpose:** Serve traffic over HTTPS for secure communication.
   - **Function:**
     - Encrypts data between the client and server, ensuring confidentiality.
     - Validates the server’s identity to protect against man-in-the-middle attacks.

3. **Monitoring Clients (3)**
   - **Purpose:** Monitor the health and performance of the infrastructure.
   - **Placement:**
     - On the load balancer.
     - On the web/application servers.
     - On the database server.
   - **Function:**
     - Collect metrics such as CPU usage, memory usage, disk I/O, and QPS (Queries Per Second).
     - Forward metrics to a centralized monitoring service like Sumologic.

---

### Benefits of Each Element

1. **Firewalls:**
   - Protect against malicious traffic.
   - Segment the infrastructure to contain potential breaches.

2. **SSL Certificate:**
   - Encrypts traffic, ensuring sensitive information is secure.
   - Builds trust with users by enabling HTTPS.

3. **Monitoring Clients:**
   - Provide visibility into system health and performance.
   - Enable proactive detection of failures or performance degradation.

---

### How Monitoring Works

1. **Data Collection:**
   - Monitoring clients collect metrics from each component (load balancer, web servers, database).
   - Metrics include:
     - CPU, memory, disk usage.
     - Network traffic.
     - Web server QPS (Queries Per Second).

2. **Data Forwarding:**
   - Collected data is sent to a centralized monitoring service (e.g., Sumologic, Prometheus).

3. **Data Visualization:**
   - Metrics are displayed in dashboards for analysis.

4. **Alerting:**
   - Alerts are triggered based on predefined thresholds (e.g., high CPU usage or slow query performance).

---

### Key Issues with the Infrastructure

1. **SSL Termination at the Load Balancer Level:**
   - **Issue:** After SSL termination, traffic between the load balancer and web servers is unencrypted.
   - **Mitigation:** Use end-to-end encryption by enabling HTTPS on web servers.

2. **Single MySQL Write Server:**
   - **Issue:** The primary database node is a single point of failure for write operations.
   - **Mitigation:** Configure automated failover to a replica node in case the primary fails.

3. **Servers with All Components:**
   - **Issue:** Running all components (web server, application server, and database) on the same machine reduces scalability and increases risk.
   - **Mitigation:**
     - Separate web, application, and database layers to distribute load.
     - Use containerization (e.g., Docker) or virtualization to isolate services.

---

### Monitoring Web Server QPS

1. **Install Monitoring Client:**
   - Install a monitoring agent (e.g., Sumologic, Prometheus Node Exporter) on the web server.

2. **Configure Metrics Collection:**
   - Use tools like Nginx metrics exporter to collect QPS data.

3. **Forward Metrics:**
   - Send QPS metrics to a centralized monitoring system.

4. **Visualize and Alert:**
   - Create a dashboard to visualize QPS over time.
   - Configure alerts for high QPS to ensure the infrastructure can handle the load.

---

### Final Diagram of the Secured and Monitored Web Infrastructure

#### Components:
1. **Firewalls:** Protect traffic at each layer (internet, load balancer, database).
2. **SSL Certificate:** Enables secure HTTPS communication.
3. **Monitoring Clients:** Collect and forward metrics to monitoring services.
4. **Load Balancer (HAProxy):** Distributes traffic among web servers.
5. **Web Servers (Nginx):** Serve static files and forward dynamic requests.
6. **Application Servers:** Process dynamic requests.
7. **Database Cluster (MySQL):** Provides redundancy with primary and replica nodes.

---

### Repository
- **GitHub Repository:** `holbertonschool-system_engineering-devops`
- **Directory:** `web_infrastructure_design`
- **File:** `2-secured_and_monitored_web_infrastructure`


[task2](task2ds.mmd)
![alt text](<task2_Capture d’écran 2025-01-15 235350.png>)
```mermaid

sequenceDiagram
    participant User as User
    participant DNS as DNS (Domain Name Server)
    participant Firewall1 as Firewall (Inbound Traffic)
    participant LoadBalancer as Load Balancer (HAProxy + SSL Termination)
    participant Firewall2 as Firewall (Between Load Balancer and Servers)
    participant Server1 as Web Server 1 (Nginx)
    participant Server2 as Web Server 2 (Nginx)
    participant Firewall3 as Firewall (Database Protection)
    participant DBPrimary as Database Primary (MySQL Master)
    participant DBReplica as Database Replica (MySQL Slave)
    participant Monitoring as Monitoring Tools (Sumologic/Prometheus)

    User->>DNS: Resolve www.foobar.com
    DNS-->>User: Returns Load Balancer IP
    User->>Firewall1: HTTPS Request to Load Balancer
    Firewall1->>LoadBalancer: Allow HTTPS Traffic
    LoadBalancer->>Firewall2: Forward Traffic
    alt Server1 is available
        Firewall2->>Server1: Pass request to Web Server 1
    else Server1 is unavailable
        Firewall2->>Server2: Pass request to Web Server 2
    end
    Server1->>DBPrimary: Query database for data
    DBPrimary->>Firewall3: Protect database
    DBPrimary->>DBReplica: Replicate data
    DBPrimary-->>Server1: Return data
    Server1-->>LoadBalancer: HTTP Response
    LoadBalancer-->>User: Encrypted HTTPS Response

    Monitoring->>LoadBalancer: Collect QPS metrics
    Monitoring->>Server1: Collect logs and performance metrics
    Monitoring->>DBPrimary: Monitor database queries
    Monitoring->>DBReplica: Monitor replication status

    Note over Firewall1, LoadBalancer: Protect against unauthorized inbound traffic
    Note over LoadBalancer: SSL termination with HTTPS
    Note over Monitoring: Tracks server health, QPS, and performance
    Note over Firewall3, DBPrimary: Prevent unauthorized database access


```
[task2](task2.mmd)
![alt text](<task2_graphtd_Capture d’écran 2025-01-16 001334.png>)

```mermaid

graph TD
    %% Internet Layer
    subgraph Internet
        A[User Browser]
    end
    A -->|HTTPS| LB[Load Balancer]

    %% Secured Infrastructure
    subgraph Secured_Infrastructure
        LB -->|Secure Traffic| FW1[Firewall - App Server 1]
        LB -->|Secure Traffic| FW2[Firewall - App Server 2]
        LB -->|Secure Traffic| FW3[Firewall - Database Server]
        FW1 --> AS1[Application Server 1]
        FW2 --> AS2[Application Server 2]
        FW3 --> DB[Database Server MySQL]
    end

    %% Monitoring Layer
    subgraph Monitoring
        M1[Monitoring Agent - App Server 1]
        M2[Monitoring Agent - App Server 2]
        M3[Monitoring Agent - Database Server]
        M1 --> AS1
        M2 --> AS2
        M3 --> DB
    end

    %% SSL Certificate
    LB -.->|SSL Termination| HTTPS[SSL Certificate]

```
