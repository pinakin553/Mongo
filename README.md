# MongoDB: Architecture, Core Components, and Real-World Operational Patterns

MongoDB is a distributed, document-oriented NoSQL database designed for high availability, horizontal scalability, and flexible schema design. This guide provides an in-depth overview of its architecture, core components, configuration best practices, scaling patterns, and real-world operational use cases.

---

## 1. Core Concepts

### 1.1 Document & Collection
* **Document**: The primary unit of data in MongoDB, stored as BSON (Binary JSON). Each document has a unique `_id`.
* **Collection**: A grouping of documents, roughly analogous to a table in relational databases.

### 1.2 Database
* A logical container for collections. MongoDB supports multiple databases per instance.

---

## 2. Replica Sets: High Availability

A **replica set** is a group of MongoDB servers that maintain the same dataset to ensure redundancy and failover.  

### Components
| Component | Role |
|-----------|------|
| Primary | Handles all writes and reads (unless read preference allows secondary reads). |
| Secondary | Replicates data from primary asynchronously. Can serve reads if configured. |
| Arbiter | Participates in elections but does not store data. Used for odd number of voting members. |

### Key Concepts
* **Oplog**: A special capped collection in primary that logs all operations. Secondaries replicate from it.
* **Automatic Failover**: If primary fails, secondaries vote to elect a new primary.
* **Write Concern**: Ensures data durability. Example: `w: majority` waits for majority of nodes to acknowledge writes.
* **Read Preference**: Determines which members serve reads (primary, secondary, nearest).

### Example Replica Set Initialization
```bash
# Start 3 mongod instances for replica set
mongod --port 27017 --dbpath /data/rs0 --replSet rs0 --bind_ip localhost
mongod --port 27018 --dbpath /data/rs1 --replSet rs0 --bind_ip localhost
mongod --port 27019 --dbpath /data/rs2 --replSet rs0 --bind_ip localhost

# Initialize replica set
mongo --port 27017
> rs.initiate({
    _id: "rs0",
    members: [
        {_id: 0, host: "localhost:27017"},
        {_id: 1, host: "localhost:27018"},
        {_id: 2, host: "localhost:27019"}
    ]
})
```
