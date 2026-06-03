# Web Infrastructure Design

This project covers the design and understanding of web infrastructures, from a simple single-server setup to a secured, monitored, and scalable distributed architecture.

Each task requires designing an infrastructure on a whiteboard, explaining every component and its role, and identifying the weaknesses of the design.

---

## Tasks & Diagrams

### 0. Simple Web Stack
A basic infrastructure hosting `www.foobar.com` on a single server.
Components: Nginx, application server, codebase, MySQL database.
Key concepts: SPOF, DNS A record, TCP/IP communication.

```mermaid
flowchart LR
    User("User\nwww.foobar.com") -->|DNS query| DNS["DNS\nwww → 8.8.8.8"]
    DNS -->|IP: 8.8.8.8| Server
    User -->|HTTP/HTTPS request| Server

    subgraph Server["SERVER 8.8.8.8"]
        WebServer["Web Server\n(Nginx)"]
        AppServer["Application Server"]
        Codebase["Codebase\n(Application Files)"]
        DB["Database\n(MySQL)"]

        WebServer -->|forwards request| AppServer
        AppServer -->|reads/writes| DB
        AppServer --- Codebase
    end

    Server -->|HTTP response| User
```

### 1. Distributed Web Infrastructure
A three-server infrastructure with a load balancer (HAproxy).
Components: 2 servers (Nginx + app server + codebase + MySQL), HAproxy with Round Robin.
Key concepts: Active-Active setup, Primary-Replica database cluster, redundancy.

```mermaid
flowchart LR
    User("User\nwww.foobar.com") -->|DNS query| DNS["DNS\nwww → 8.8.8.8"]
    User -->|HTTP/HTTPS request| LB["Load Balancer\n(HAproxy)"]
    DNS -->|IP: 8.8.8.8| LB

    LB -->|forwards to| Server1
    LB -->|forwards to| Server2

    subgraph Server1["SERVER 1"]
        WS1["Web Server\n(Nginx)"]
        AS1["Application Server"]
        CB1["Codebase"]
        DB1["Database\n(MySQL Primary)"]

        WS1 -->|forwards request| AS1
        AS1 --- CB1
        AS1 -->|reads/writes| DB1
    end

    subgraph Server2["SERVER 2"]
        WS2["Web Server\n(Nginx)"]
        AS2["Application Server"]
        CB2["Codebase"]
        DB2["Database\n(MySQL Replica)"]

        WS2 -->|forwards request| AS2
        AS2 --- CB2
        AS2 -->|reads only| DB2
    end

    DB1 -->|replicates to| DB2
```

### 2. Secured and Monitored Web Infrastructure
The distributed infrastructure secured and monitored.
Components: 3 firewalls, 1 SSL certificate, 3 monitoring clients (Sumo Logic).
Key concepts: HTTPS, firewall, QPS monitoring, SSL termination issue.

```mermaid
flowchart LR
    User("User - www.foobar.com") -->|HTTPS request| FW0["Firewall 0"]
    FW0 --> LB["Load Balancer - HAproxy + SSL"]
    DNS["DNS - www 8.8.8.8"] -->|IP 8.8.8.8| LB

    LB -->|forwards to| FW1["Firewall 1"]
    LB -->|forwards to| FW2["Firewall 2"]

    FW1 --> Server1
    FW2 --> Server2

    subgraph Server1["SERVER 1"]
        WS1["Web Server Nginx"]
        AS1["Application Server"]
        CB1["Codebase"]
        DB1["Database MySQL Primary"]
        MON1["Monitoring Client"]

        WS1 -->|forwards request| AS1
        AS1 --- CB1
        AS1 -->|reads/writes| DB1
    end

    subgraph Server2["SERVER 2"]
        WS2["Web Server Nginx"]
        AS2["Application Server"]
        CB2["Codebase"]
        DB2["Database MySQL Replica"]
        MON2["Monitoring Client"]

        WS2 -->|forwards request| AS2
        AS2 --- CB2
        AS2 -->|reads only| DB2
    end

    DB1 -->|replicates to| DB2
    MON1 -->|sends data| Sumo["Monitoring Service - Sumo Logic"]
    MON2 -->|sends data| Sumo
    LB --- MON0["Monitoring Client"]
    MON0 -->|sends data| Sumo
```

### 3. Scale Up
A fully split infrastructure with dedicated servers per component.
Components: 1 additional server, HAproxy cluster (active/passive), dedicated web server, application server, and database server.
Key concepts: elimination of SPOF at load balancer level, component isolation.

```mermaid
flowchart LR
    User("User - www.foobar.com") -->|HTTPS request| LB_Cluster

    subgraph LB_Cluster["LOAD BALANCER CLUSTER HAproxy"]
        LB1["Load Balancer 1 - active"]
        LB2["Load Balancer 2 - passive"]
        LB1 <-->|sync| LB2
    end

    LB_Cluster -->|forwards to| WS_Server
    LB_Cluster -->|forwards to| AS_Server
    LB_Cluster -->|forwards to| DB_Server

    subgraph WS_Server["SERVER - Web Server"]
        WS["Web Server Nginx"]
    end

    subgraph AS_Server["SERVER - Application Server"]
        AS["Application Server"]
        CB["Codebase"]
        AS --- CB
    end

    subgraph DB_Server["SERVER - Database"]
        DB["Database MySQL"]
    end

    WS -->|forwards dynamic requests| AS
    AS -->|reads/writes| DB
```

---

## Acronyms to know

| Acronym | Stands for |
|--------|------------|
| DNS | Domain Name System |
| HTTP | HyperText Transfer Protocol |
| HTTPS | HyperText Transfer Protocol Secure |
| SSL | Secure Sockets Layer |
| TCP/IP | Transmission Control Protocol / Internet Protocol |
| SPOF | Single Point of Failure |
| LAMP | Linux, Apache, MySQL, PHP |
| QPS | Queries Per Second |
| IP | Internet Protocol |
| URL | Uniform Resource Locator |

---

## Repository

- GitHub repository: `holbertonschool-system_engineering-devops`
- Directory: `web_infrastructure_design`
