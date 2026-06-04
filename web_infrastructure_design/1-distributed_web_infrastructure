![Task 1](assets/Task_1.png)


---


EXPLANATIONS

Why each additional element:
- Load balancer (HAproxy): distributes incoming traffic across multiple servers to avoid overloading a single one and improve availability.
- Server 2: adds redundancy — if Server 1 goes down, Server 2 can still handle requests.
- MySQL Replica: offloads read queries from the Primary and provides a data backup.

Load balancer distribution algorithm:
Round Robin — each incoming request is forwarded to the next server in turn.
Server 1 gets request 1, Server 2 gets request 2, Server 1 gets request 3, and so on.

Active-Active vs Active-Passive:
- Active-Active: both servers handle traffic simultaneously. This is what HAproxy with Round Robin enables.
- Active-Passive: one server handles all traffic, the other stays on standby and only takes over if the first one fails.
This setup is Active-Active.

How a Primary-Replica (Master-Slave) cluster works:
The Primary node handles all write operations. Every write is then replicated to the Replica node.
The Replica node stays synchronized with the Primary and can handle read operations.

Difference between Primary and Replica for the application:
- Primary: receives all write queries (INSERT, UPDATE, DELETE) from the application.
- Replica: receives only read queries (SELECT), reducing the load on the Primary.

---

ISSUES

SPOF:
- The load balancer is a single point of failure — if it goes down, the entire infrastructure becomes unreachable.
- The Primary database is a SPOF for writes — if it goes down, no data can be written.

Security issues:
- No firewall — servers are exposed directly to the internet.
- No HTTPS — traffic between the user and the infrastructure is not encrypted.

No monitoring:
- There is no system in place to detect failures, performance issues, or abnormal traffic.
