# Computer Systems for Data Science — Midterm Study Guide

---

## Table of Contents
1. [Performance Concepts](#1-performance-concepts)
1. [Data Centers](#2-data-centers)
1. [Relational Model & SQL Basics](#3-relational-model--sql-basics)
1. [Protocol Buffers & API Schema](#4-protocol-buffers--api-schema)
1. [Indexing & Query Optimization](#5-indexing--query-optimization)
1. [Transactions & ACID](#6-transactions--acid)
1. [Concurrency Control & Locking](#7-concurrency-control--locking)
1. [Indexing Data Structures & Bloom Filters](#8-indexing-data-structures--bloom-filters)
1. [Caching](#9-caching)
1. [Partitioning & Sharding](#10-partitioning--sharding)
1. [Replication](#11-replication)
1. [Distributed File Systems](#12-distributed-file-systems)
1. [Two-Phase Commit (2PC)](#13-two-phase-commit-2pc)
1. [TCP/IP Networking Model](#14-tcpip-networking-model)
1. [Distributed Systems Architecture](#15-distributed-systems-architecture)

---

## 1. Performance Concepts


### Metrics
- **Latency**: Time to complete a single task (length of the pipe)
  - Measured in time units (ns, µs, ms, s)
  - Example: How long until my pizza arrives?
- **Throughput**: Number of tasks completed per unit time (width of the pipe)
  - Measured in tasks/second
  - Example: How many pizzas is the shop making per hour?
- **Bandwidth**: Theoretical maximum throughput (the limit)
  - Throughput ≤ Bandwidth always
  - Example: How many pizzas _can_ the shop make per hour?
- **QPS/CPU**: How many requests per second processed per unit CPU consumption.
  - Measure of resource efficiency
- **Uptime**: What fraction of time is the system guaranteed to be working correctly?
  - Measure of reliabilty
  - Measured in 9s (e.g., 5-9s meaning 99.999%)

**Example**: Boeing 747 vs. Concorde

| Plane | DC→Paris | Speed | Passengers | Throughput (pmph) |
|-------|----------|-------|------------|-------------------|
| Boeing 747 | 6.5 hrs | 610 mph | 470 | 286,700 |
| Concorde | 3 hrs | 1350 mph | 132 | 178,200 |

Concorde has lower latency; 747 has higher throughput.


### Amdahl's Law
Predicts speedup when you improve part of a system.

**Formula**:
```
Speedup = 1 / ((1 - p) + p/s)
```
Where:
- `p` = fraction of work affected by the improvement
- `s` = speedup factor for that fraction

**Maximum speedup** (if improved part takes zero time): `1 / (1 - p)`

**Example**: Speed up 10% of queries by 2x
- `p = 0.1`, `s = 2`
- `Speedup = 1 / (0.9 + 0.1/2) = 1 / 0.95 = 1.053` (only 5.3% improvement!)
- Even with infinite speedup: `1 / 0.9 = 1.11` (max 11% improvement)


### Latency Numbers Every Programmer Should Know

| Operation | Latency | Order of Magnitude |
|-----------|---------|-------------------|
| L1 cache reference | 1 ns | nanoseconds |
| L2 cache reference | 4 ns | nanoseconds |
| Main memory access | 100 ns | nanoseconds |
| SSD random read | 20 µs (20,000 ns) | microseconds |
| HDD seek | 4 ms (4,000,000 ns) | milliseconds |
| Datacenter network round-trip | 500 µs | microseconds |
| Internet round-trip (same coast) | 30 ms | milliseconds |
| Internet round-trip (cross-country) | 100 ms | milliseconds |


---

## 2. Data Centers

### Evolution
- **1960s-70s**: Few large time-shared computers
- **1980s-90s**: Heterogeneous smaller machines
- **2000-2020**: Large numbers of identical machines, geographically distributed
- **2020s+**: AI-specific datacenters, clusters for massive model training

### Physical Structure
- **Rack**: 19" or 23" wide
  - Typical server: 128-192 cores, 256-512 GB RAM
  - Typical storage: 30 drives
  - Typical network switch: 72 × 100Gb/s ports
- **Row**: 30+ racks
- **Datacenter**: Multiple rows, football stadium-sized

### Network Topology
1. **Top-of-Rack (ToR) Switch**: Connects machines within a rack
2. **End-of-Row Router**: Aggregates racks in a row
3. **Core Router**: Connects datacenter to outside world

Higher in hierarchy → higher throughput, but also higher latency for across communication.
Multipath Routing provides extra capacity and redundancy.

### Power & Cooling

**Power Usage Effectiveness (PUE)**:
```
PUE = Total Facility Power / IT Equipment Power
```
- Inefficient (early): PUE of 1.7-2.0 (wasting 40-50%)
- Efficient (modern): PUE of 1.1-1.15 (wasting 10-15%)
- Best case: PUE of 1.04

Power is ~25% of monthly operating cost and often the limiting factor.

**Cooling methods**:
- Early: HVAC (borrowed from malls)
- Modern: Outside air, evaporative cooling
- Cutting edge: Liquid immersion cooling

**Backup Power**: Batteries for short glitches → diesel generators for longer outages

### Datacenter Location Factors
- Cheap, plentiful electricity (hydroelectric, wind, nuclear)
- Good network backbone access
- Inexpensive land
- Proximity to users (speed of light latency, data sovereignty laws)
- Available labor pool (less important now with automation)

### AI Datacenters vs. Traditional
**Similarities**: Same rack/row topology, cooling still critical

**Differences**:
- Compute: Thousands of GPUs, low CPU:GPU ratio
- Memory: High Bandwidth Memory (HBM) on-GPU, expensive
- Network: Much higher bandwidth requirements
  - Within server: NVLink (GPU-to-GPU)
  - Across servers: InfiniBand

### Common Failure Modes (per year for new datacenter)
- ~thousands of hard drive failures
- ~1000 individual machine failures
- ~dozens of minor DNS blips
- ~3 router failures
- ~20 rack failures (40-80 machines gone for 1-6 hours)
- ~1 PDU failure (500-1000 machines gone for ~6 hours)

Reliability must come from software!

---

## 3. Relational Model & SQL Basics

### Core Concepts
- **Entity/Relation**: A table (e.g., Students, Courses)
- **Attribute**: A column (e.g., name, cuid, gpa)
- **Tuple**: A row (one record)
- **Schema**: Structure definition (column names, types, constraints)

### Data Types

| Type | Description |
|------|-------------|
| INT32, INT64 | Integers (32 or 64 bit) |
| FLOAT32, FLOAT64 | Floating point numbers |
| CHAR(n) | Fixed-length string (padded) |
| VARCHAR(n) | Variable-length string (up to n) |
| DATE, TIMESTAMP | Date/time values |
| BOOLEAN | True/false |

### Set vs. Multiset vs. List

| Collection | Ordered? | Duplicates? |
|------------|----------|-------------|
| Set | No | No |
| Multiset (Bag) | No | Yes |
| List | Yes | Yes |

**SQL uses multisets** (duplicates allowed, order not guaranteed) because removing duplicates requires expensive sorting/hashing.

### Keys and Constraints
- **Primary Key**: Uniquely identifies each row; cannot be NULL
- **Foreign Key**: References primary key of another table
- **NOT NULL**: Column cannot have NULL value
- **UNIQUE**: All values in column must be distinct

### Data Independence
- **Logical data independence**: Applications unaffected by schema changes (via views)
- **Physical data independence**: Applications unaffected by storage changes

### Basic Query Structure
```sql
SELECT columns      -- what to return (projection)
FROM table          -- where to get data
WHERE condition     -- which rows to include (selection/filter)
GROUP BY column     -- how to group rows
HAVING condition    -- filter on groups (after aggregation)
ORDER BY column     -- how to sort results
LIMIT n             -- how many rows to return
```

### Aggregate Functions
- `COUNT(*)`      - number of rows
- `COUNT(column)` - number of non-NULL values
- `SUM(column)`   - sum of values
- `AVG(column)`   - average of values
- `MIN(column)`   - minimum
- `MAX(column)`   - maximum

- **WHERE**: Filters rows BEFORE grouping (cannot use aggregates)
- **HAVING**: Filters groups AFTER aggregation (can use aggregates)

```sql
-- WRONG: WHERE with aggregate
SELECT dept, AVG(salary) FROM employees WHERE AVG(salary) > 50000 GROUP BY dept;

-- CORRECT: HAVING with aggregate
SELECT dept, AVG(salary) FROM employees GROUP BY dept HAVING AVG(salary) > 50000;
```

### JOIN Types

**CROSS JOIN** (Cartesian Product):
```sql
SELECT * FROM A, B    -- every row of A paired with every row of B
```

**INNER JOIN**:
```sql
SELECT * FROM A INNER JOIN B ON A.key = B.key
```
Returns only rows where both sides have matching values.

**LEFT JOIN**:
```sql
SELECT * FROM A LEFT JOIN B ON A.key = B.key
```
Returns all rows from A, with NULLs for B columns when no match.

**RIGHT JOIN**: Like LEFT JOIN, but keeps all rows from B.

**FULL OUTER JOIN**: Keeps all rows from both tables.

### Subqueries

**IN**: Value in result set
```sql
SELECT * FROM employees WHERE dept_id IN (SELECT id FROM departments WHERE location = 'NYC');
```

**EXISTS**: Result set is non-empty
```sql
SELECT * FROM employees e WHERE EXISTS (SELECT 1 FROM projects p WHERE p.lead = e.id);
```

**ALL**: Comparison with all values
```sql
SELECT * FROM employees WHERE salary > ALL (SELECT salary FROM employees WHERE dept = 'Sales');
```

**ANY**: Comparison with any value
```sql
SELECT * FROM employees WHERE salary > ANY (SELECT salary FROM employees WHERE dept = 'Sales');
```

---

## 4. Protocol Buffers & API Schema

### API Schema
- Translation layer between external clients and internal database
- Defines request/response formats, endpoints, parameters
- Can hide sensitive database details
- Database schema can change without breaking API

### Data Serialization Formats

| Format | Type | Pros | Cons |
|--------|------|------|------|
| JSON | Text | Human-readable, flexible | Verbose, slow parsing |
| XML | Text | Structured, validated | Very verbose |
| Protocol Buffers | Binary | Compact, fast, typed | Not human-readable |

### Protocol Buffers (Protobuf)
API schema definition language from Google.

**Example**:
```protobuf
message Person {
  string name = 1;
  int32 id = 2;
  string email = 3;
  
  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }
  
  message PhoneNumber {
    string number = 1;
    PhoneType type = 2;
  }
  
  repeated PhoneNumber phones = 4;
}
```

### Why Protobuf is Smaller
- Field names replaced with small numeric tags (1, 2, 3...)
- Integers use variable-length encoding (small numbers = fewer bytes)
- No quotes, colons, commas, whitespace
- Binary encoding, not ASCII

### Serialization
- **Serialize**: Convert in-memory data to wire format (bytes)
- **Deserialize**: Convert wire format back to in-memory data

### Backward Compatibility
**Never mark fields as required** in Protobuf because:
- If you remove a required field, old software rejects new data
- If you add a required field, new software rejects old data
- Optional fields are safely ignored by older versions

---

## 5. Indexing & Query Optimization

### What is an Index?
A separate data structure that speeds up lookups on a column. Like an index in a book — instead of scanning every page, you look up the topic and jump directly.

### Creating Indexes
```sql
CREATE INDEX idx_name ON table(column);
CREATE INDEX idx_multi ON table(col1, col2);  -- composite index
```

### Index Trade-offs
**Benefits**:
- Lookup: O(log n) or even O(1) instead of O(n) full table scan
- Range queries much faster
- Can avoid touching table entirely (covering index)

**Costs**:
- Storage space (index is additional data structure)
- Slower writes (must update index on INSERT/UPDATE/DELETE)
- More indexes = more write overhead

### When Indexes Help
- Frequently filtered columns (WHERE clause)
- Join columns
- GROUP BY columns
- ORDER BY columns

### When Indexes Don't Help
- Small tables (full scan is fast enough)
- Low-cardinality columns (few distinct values)
- Write-heavy tables (update overhead dominates)
- When query scans most of the table anyway

### Query Planner
- **EXPLAIN**: Shows execution plan
- **EXPLAIN ANALYZE**: Runs query and shows actual times

Look for:
- **Sequential Scan**: Full table scan (often bad)
- **Index Scan**: Using index (usually good)
- **Index Only Scan**: All data from index (best)

### Things That Prevent Index Use
- Functions on indexed columns: `WHERE EXTRACT(YEAR FROM date) = 2024`
- Implicit type casts
- OR conditions (sometimes)
- Leading wildcards: `WHERE name LIKE '%smith'`

---

## 6. Transactions & ACID

### What is a Transaction?
A sequence of operations that forms a single logical unit of work.

```sql
START TRANSACTION;
  UPDATE accounts SET balance = balance - 100 WHERE id = 1;
  UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

### ACID Properties

| Property | Meaning | Mechanism |
|----------|---------|-----------|
| **A**tomicity | All or nothing; partial failures are rolled back | Write-ahead logging |
| **C**onsistency | Database moves from one valid state to another | Constraints, triggers |
| **I**solation | Concurrent transactions don't interfere | Locking, MVCC |
| **D**urability | Committed changes survive crashes | Write-ahead logging, replication |

### Write-Ahead Logging (WAL)
- Write changes to log BEFORE applying to database
- On crash: replay log to recover committed transactions
- Provides both Atomicity (can undo) and Durability (can redo)
- Sequential writes to log are faster than random writes to DB

### OLTP vs. OLAP

| Characteristic | OLTP | OLAP |
|----------------|------|------|
| Stands for | Online Transaction Processing | Online Analytical Processing |
| Operations | Read/write, real-time updates | Read-only, batch writes |
| Query size | Small, targeted | Large, scanning/aggregating |
| Concurrency | Many concurrent users | Fewer, longer queries |
| Examples | MySQL, Postgres, Redis | BigQuery, Snowflake, Redshift |
| Use cases | Banking, gaming, user settings | Business reports, analytics |

### Isolation Anomalies

| Anomaly | Description | Example |
|---------|-------------|---------|
| **Dirty Read** | Read uncommitted data | T1 writes, T2 reads, T1 aborts |
| **Lost Update** | Concurrent writes, one lost | T1 and T2 both read X=10, both write X=X+1, result is 11 not 12 |
| **Unrepeatable Read** | Same row, different values | T1 reads X, T2 updates X and commits, T1 reads X again |
| **Phantom Read** | Row set changes between reads | T1 counts rows, T2 inserts row, T1 counts again |

### Isolation Levels

| Level | Dirty Reads | Lost Updates | Unrepeatable Reads | Phantom Reads |
|-------|-------------|--------------|--------------------| --------------|
| Read Uncommitted | Possible | Possible | Possible | Possible |
| Read Committed | Prevented | Possible | Possible | Possible |
| Repeatable Read | Prevented | Prevented | Prevented | Possible |
| Serializable | Prevented | Prevented | Prevented | Prevented |

Most databases default to Read Committed for performance.

### Serializability
A schedule is **serializable** if its effect equals some serial execution of the same transactions.

**Conflict serializability**: Build a conflict graph; if acyclic, schedule is conflict serializable.

**Conflict types**:
- Read-Write (RW): T1 reads, T2 writes same item
- Write-Read (WR): T1 writes, T2 reads same item
- Write-Write (WW): Both write same item

---

## 7. Concurrency Control & Locking

### Why Concurrency?
- Visa processes 60,000+ transactions/second
- Can't make unrelated transactions wait for each other
- Must interleave while maintaining correctness

### Lock Types
- **Shared (S) Lock**: For reading; multiple transactions can hold simultaneously
- **Exclusive (X) Lock**: For writing; only one transaction can hold; blocks all others

| Request \ Held | None | S | X |
|----------------|------|---|---|
| S | Grant | Grant | Wait |
| X | Grant | Wait | Wait |

### Two-Phase Locking (2PL)
**Rule**: A transaction cannot acquire new locks after releasing any lock.

**Two phases**:
1. **Growing phase**: Acquire locks (no releases)
2. **Shrinking phase**: Release locks (no acquires)

**2PL guarantees conflict serializability** (if it completes).

### Strict 2PL
**Rule**: Hold ALL locks until COMMIT or ABORT.

**Benefits**:
- Prevents cascading aborts
- Simpler recovery
- Most databases use this

### Deadlocks
A cycle of transactions waiting for each other's locks.

**Example**:
- T1 holds lock on A, wants lock on B
- T2 holds lock on B, wants lock on A
- Neither can proceed!

**Detection**: Waits-for graph; cycle = deadlock

**Resolution**: Abort one transaction (usually youngest or one with least work)

**Prevention strategies**:
- **Conservative 2PL**: Acquire ALL locks upfront before starting
- **Wait-Die**: Older waits for younger; younger aborts if waiting for older
- **Wound-Wait**: Older "wounds" (aborts) younger; younger waits for older

---

## 8. Indexing Data Structures & Bloom Filters

### B-Tree / B+ Tree
Most common index structure.

**Properties**:
- Balanced tree (all leaves at same depth)
- Each node has many children (high fan-out)
- Lookup: O(log n)
- Supports range queries efficiently

### Dense vs. Sparse Indexes

| Type | Description | When to Use |
|------|-------------|-------------|
| Dense | Entry for every search key value | Required for secondary indexes |
| Sparse | Entry for some values only | Only works when data is sorted by search key |

**Why must secondary indexes be dense?**
Secondary indexes point to records that aren't physically sorted by that column, so you can't interpolate between entries.

### Bloom Filters
A probabilistic data structure for checking set membership very fast.

**Structure**: Array of m bits, initialized to 0; k hash functions

**Insert**: Hash item k times, set those bit positions to 1

**Lookup**: Hash item k times, check if ALL positions are 1
- If any position is 0 → definitely NOT in set (no false negatives)
- If all positions are 1 → LIKELY in set (may be false positive)

**Properties**:
- Space-efficient (bits, not actual items)
- False positives possible
- No false negatives
- Cannot delete (would affect other items sharing those bits)

**Use case**: Quickly filter out definite non-members before expensive lookup.

**Trade-off**: Larger filter (more bits) = fewer false positives but more memory

---

## 9. Caching

### Why Cache?
Avoid repeated expensive computation or data fetching.

### Cache Concepts
- **Cache hit**: Data found in cache (fast)
- **Cache miss**: Data not in cache, must fetch from source (slow)
- **Hit ratio**: hits / total requests

### Cache Problems
1. **Assignment**: Where to store data and how?
2. **Consistency**: What happens when data is updated or deleted?
3. **Eviction**: What to remove when cache is full?

### Eviction Policies

| Policy | Description | Pros | Cons |
|--------|-------------|------|------|
| **FIFO** | First in, first out | Simple | Doesn't consider access patterns |
| **LRU** | Least recently used | Good for temporal locality | Overhead to track recency |
| **LFU** | Least frequently used | Good for popularity | Doesn't adapt to changes |
| **OPT** | Evict item used furthest in future | Optimal (theoretical) | Requires future knowledge |

**OPT** (Belady's algorithm) is optimal but impractical — useful as benchmark.

### Cache Consistency
When underlying data changes:
- **Write-through**: Update cache and database together
- **Write-back**: Update cache now, database later
- **Invalidation**: Remove from cache on update

---

## 10. Partitioning & Sharding

### Why Partition?
- Single machine can't hold all data
- Spread load across multiple servers
- Enable parallel processing

### Partitioning Schemes

**Round-Robin**:
- Tuple i goes to server (i mod n)
- Even distribution but no locality

**Hash Partitioning**:
- hash(key) mod n
- Even distribution for uniform hash
- **Problem**: Changing n requires massive redistribution

**Range Partitioning**:
- Define ranges: [0-100) → Server 1, [100-200) → Server 2, etc.
- Good for range queries
- Requires partition vector stored somewhere
- Risk of hot spots if data skewed

### Execution Skew
- Access patterns change over time
- Some partitions become "hot"
- Solutions: Rebalancing, caching hot data, splitting hot partitions

### Consistent Hashing
Solves the redistribution problem.

**Concept**: Place servers on a ring (0 to 1). Each key hashes to a point on the ring and goes to the next server clockwise.

**Adding a node**: Only the new node's immediate neighbor transfers data.

**Removing a node**: Only its neighbor absorbs its data.

**Benefits**:
- No single point of failure (no master needed)
- Minimal data movement on topology changes

**Used by**: Cassandra, DynamoDB, Akamai

---

## 11. Replication

### Why Replicate?
- **Durability**: Survive hardware failures
- **Availability**: System stays up even if some nodes fail
- **Performance**: Read from nearest replica; reduce latency

### Replication Factor
Typically replicate to **3 nodes**. Why 3?
- 1: Single point of failure
- 2: No majority possible (can't resolve conflicts)
- 3: Smallest odd number allowing majority (2 of 3)

### Master-Replica (Primary-Copy)
- **Master/Leader**: Accepts all writes
- **Replicas/Followers**: Receive replicated writes, serve reads
- On master failure: Election to choose new master

### Replication Locations
- **Within datacenter**: Protects against disk/server failure
- **Across datacenters**: Protects against fires, earthquakes, power outages, government seizure

### Consistency Challenges
- Replicas may have different values temporarily
- Must decide: wait for sync (strong) or return immediately (eventual)?
- Trade-off: Consistency vs. availability vs. latency

---

## 12. Distributed File Systems

### What is a File System?
Provides logical concept of files with paths; supports read, write, create, delete.

### Distributed File System
File system spanning multiple machines with partitioning and replication.

### HDFS (Hadoop Distributed File System)
- **NameNode**: Master that stores metadata (file paths, block locations)
- **DataNodes**: Store actual file blocks
- Designed for large files, append-only workloads

**NameNode as single point of failure**:
- All queries go through NameNode first
- Trade-off: Simplicity vs. fault tolerance
- Mitigation: Standby NameNode, write-ahead logging

### File Systems vs. Databases
- File systems: Simpler, flexible schema, good base layer
- Can build document stores, key-value stores on top
- Some provide ACID-like properties (atomicity, durability via replication)

---

## 13. Two-Phase Commit (2PC)

### The Problem
Distributed transaction spans multiple servers. Must ensure atomicity: commit at ALL servers or abort at ALL servers. Cannot have partial commits.

### Roles
- **Transaction Coordinator (TC)**: Coordinates the commit
- **Transaction Manager (TM)**: One per node, manages local log

### Protocol Phases

**Phase 1: Prepare**
1. Coordinator sends `PREPARE` to all participants
2. Each participant:
   - Writes `PREPARE` record to local log
   - If can commit: vote `YES`
   - If cannot: vote `NO`

**Phase 2: Commit/Abort**
1. Coordinator collects votes:
   - ALL yes → send `COMMIT` to all
   - ANY no → send `ABORT` to all
2. Each participant writes `COMMIT` or `ABORT` to log
3. Participants acknowledge to coordinator

### Failure Handling
Uses write-ahead logging at each node.

**Participant crash before voting**: Coordinator times out, aborts transaction.

**Participant crash after voting YES**: On recovery, check log and ask coordinator for decision.

**Coordinator crash after collecting votes**: Participants may be "blocked" waiting for decision.

### Fail-Stop vs. Byzantine
- **Fail-stop**: Servers either work correctly or stop (no malicious behavior)
- **Byzantine**: Servers may behave arbitrarily (bugs, hacks)

2PC assumes fail-stop model.

---

## 14. TCP/IP Networking Model

### The 5-Layer Model

| Layer | Name | Function | Protocols/Examples |
|-------|------|----------|-------------------|
| 5 | Application | Software communication | HTTP, HTTPS, FTP, SMTP, SSH, DNS |
| 4 | Transport | App-to-app delivery, reliability | TCP, UDP, QUIC |
| 3 | Network | Global routing | IPv4, IPv6, BGP |
| 2 | Data Link | Local delivery | Ethernet, MAC, ARP, DHCP |
| 1 | Physical | Bits to signals | Fiber, copper, WiFi, 5G |

### Layer 1: Physical
- Converts bits to physical signals
- Media: fiber optic, copper wire, wireless
- **Key concept**: Protocol-agnostic; just moves signals

### Layer 2: Data Link
- Organizes bits into **frames**
- **MAC address**: 48-bit hardware identifier (burned in at manufacture)
  - Local only — invisible beyond the router
  - Format: `3C:22:FB:4A:9E:01`
- **Switch**: Layer 2 device; forwards based on MAC address
- **ARP**: (Address Resolution Protocol) Maps IP addresses to MAC addresses

### Layer 3: Network
- Routes **packets** across networks using **IP addresses**
- **IPv4**: 32-bit addresses (~4.3 billion); exhausted, extended via NAT
- **IPv6**: 128-bit addresses; dominant on mobile
- **Router**: Layer 3 device; forwards based on IP address
- **BGP**: Border Gateway Protocol; connects autonomous systems
- **DHCP**: (Dynamic Host Control Protocol) Automatically configures new devices joining network

### Layer 4: Transport
- End-to-end delivery between applications
- **Port numbers**: Identify processes on a machine (0-65535)
- **Segments**: Data units at this layer

**TCP vs. UDP**:

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Required (handshake) | None |
| Reliability | Guaranteed delivery | Best-effort |
| Ordering | Preserved | Not guaranteed |
| Speed | Slower | Faster |
| Use cases | HTTP, SSH, FTP | DNS, video streaming, games |

**TCP guarantees**:
- Delivery confirmation
- No duplicates
- Correct ordering
- Achieved via sequence numbers, acknowledgments, retransmission

**QUIC**: Modern protocol that runs over UDP but provides TCP-like reliability with less handshake overhead. Used by Google, YouTube, Meta.

### Layer 5: Application
- Where developers work
- Defines message formats, verbs, session rules
- **HTTP/HTTPS**: Web traffic (HTTPS = HTTP + TLS encryption)
- **DNS**: Hostname → IP address resolution
- **SMTP**: Email sending
- **SSH**: Secure remote terminal

---

## 15. Distributed Systems Architecture

### The 8-Layer Stack (from global to local)
One way to divide and understand a global scale system:

1. **DNS Routing**: Map domain name to nearest healthy datacenter
2. **API Gateway**: Authenticate, route requests, circuit breaker
3. **Microservices**: Parallel fan-out to multiple services
4. **Data & Caching**: Cache hits vs. database queries
5. **CDN**: Video served from ISP-local boxes
6. **Pods & Replication**: Leader election, consensus
7. **VMs & Containers**: Isolation and orchestration
8. **Cores & Threads**: Thread scheduling, parallelism

### DNS Routing
- User's ISP resolver queries main authoritative DNS
- DNS returns IP of nearest healthy datacenter
- **TTL problem**: Cached DNS may point to dead server
- **Solution**: Keep TTLs short (30s) for faster failover

### API Gateway
- Single entry point for all backend services
- **Service Registry**: Instances register on startup, heartbeat to stay registered
- **Load Balancing**: Route to healthy instances
- **Circuit Breaker Pattern**:
  - CLOSED: Traffic flows normally
  - OPEN: >50% errors → stop traffic, return fallback
  - HALF-OPEN: Test single request → success closes circuit

### Caching Layer
- **In-memory** (Memcached): Very fast, very expensive
- **On disk** (Cassandra) May serve as another cache layer, or may be "origin"

### Consistency Spectrum

| Level | Behavior | Use Case |
|-------|----------|----------|
| Eventual | Write returns before replication; replicas catch up eventually | Analytics, view counts |
| Session | User sees own writes; others may see stale data briefly | Watch lists, profiles |
| Strong | Write synchronously replicates everywhere | Billing, payments |

### CDN (Content Delivery Network)
- Serving boxes installed inside ISPs worldwide
- Pre-loaded overnight with popular content
- Cache miss → fetch from origin datacenter
- **Benefits**: Lower latency, reduced bandwidth costs, win-win for ISP and content provider

### Adaptive Bitrate Streaming
- Video encoded at multiple quality levels (480p, 720p, 1080p, 4K)
- Player monitors bandwidth, switches at chunk boundaries
- **Per-title encoding**: Static videos need lower bitrate than action scenes

### Concurrency vs. Parallelism
- **Concurrency**: Managing multiple tasks logically (may not run simultaneously)
- **Parallelism**: Actually executing multiple tasks at the same instant
- Example: 1000 concurrent streams, but only 16 running in parallel on 16 cores

### VMs vs. Containers

| Aspect | VM | Container |
|--------|----|-----------| 
| Isolation | Own OS kernel | Shared kernel |
| Overhead | Higher (full OS) | Lower (just process) |
| Security | Stronger (hypervisor boundary) | Weaker (kernel bug affects all) |

**Kubernetes**: Orchestrates containers — schedules, scales, heals, deploys.
**Thundering herd**: Sudden burst of incoming request that defies usual patterns.
**Retry storm**: Failing clients all keep retrying, increasing load and cascading failures.

---

*Good luck on the exam!*
