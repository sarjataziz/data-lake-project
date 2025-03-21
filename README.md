# 📦 Data Lake Project

Welcome to the **Data Lake Project** — a full on-premise, Docker-based data lake architecture built using open-source tools including Trino, Iceberg, Hive Metastore, MinIO, and PostgreSQL.

---

## 📐 Architecture Overview

```
                ┌────────────┐
                │   Trino    │ ◀────────┐
                └────┬───────┘          │
                     │                  │ SQL Queries
             ┌───────▼────────┐         │
             │  Iceberg Table │ ◀───────┘
             └───────┬────────┘
                     │
                     ▼
        ┌──────────────────────────┐
        │ Hive Metastore (Postgre) │
        └────────────┬─────────────┘
                     │
                     ▼
        ┌────────────────────────┐
        │     MinIO (S3 API)     │
        └────────────────────────┘
```

---

## 🚀 Quick Start

### ✅ Requirements

- Docker Desktop with WSL 2
- Internet Connection
- Docker Hub login (`docker login`)

### 🔄 Clone Repository

```bash
git clone https://github.com/sarjataziz/data-lake-project.git
cd data-lake-project
```

### 📁 Project Structure

```
data-lake-project/
├── docker-compose.yml
├── trino-config/
│   ├── catalog/
│   │   └── iceberg.properties
│   ├── config.properties
│   ├── jvm.config
│   └── node.properties
├── trino-data/               # Local volume for Trino
```

---

## 🛠️ Run Services

```bash
docker-compose up -d
```

First-time run may take 2–5 minutes to download and start containers.

---

## 🖥 Web Interfaces

### 🪣 MinIO Dashboard

- URL: [http://localhost:9090](http://localhost:9090)
- Username: `admin`
- Password: `admin123`

📝 Create a **bucket** named `data-lake` inside MinIO (with optional folder `warehouse`).

### 🧠 Trino UI

- URL: [http://localhost:8080](http://localhost:8080)

---

## ⚙️ Trino Configuration

### 📌 `trino-config/catalog/iceberg.properties`

```properties
connector.name=iceberg
iceberg.catalog.type=HIVE

# Hive Metastore Connection
iceberg.hive-catalog.metastore.uri=thrift://hive-metastore:9083
iceberg.hive-catalog.warehouse=s3://data-lake/warehouse/

# MinIO (S3) Access for Iceberg
iceberg.hive-catalog.s3.client-type=AWS
iceberg.hive-catalog.s3.access-key=admin
iceberg.hive-catalog.s3.secret-key=admin123
iceberg.hive-catalog.s3.endpoint=http://minio:9000
iceberg.hive-catalog.s3.path-style-access=true
```

### 📌 `trino-config/config.properties`

```properties
coordinator=true
node-scheduler.include-coordinator=true
http-server.http.port=8080
query.max-memory=5GB
query.max-memory-per-node=1GB
memory.heap-headroom-per-node=1GB
```

### 📌 `trino-config/jvm.config`

```properties
-server
-Xmx16G
-XX:+UseG1GC
-XX:G1HeapRegionSize=32M
-XX:+ExplicitGCInvokesConcurrent
-XX:+HeapDumpOnOutOfMemoryError
-XX:+ExitOnOutOfMemoryError
-XX:ReservedCodeCacheSize=512M
```

### 📌 `trino-config/node.properties`

```properties
node.environment=dev
node.id=ffffffff-ffff-ffff-ffff-ffffffffffff
node.data-dir=/var/trino/data
```

---

## 🧪 Try it Out

### Show Iceberg Schemas
```sql
SHOW SCHEMAS FROM iceberg;
```

### Create Iceberg Table
```sql
CREATE TABLE iceberg.default.test_table (
    id INT,
    name VARCHAR
)
WITH (
    format = 'PARQUET'
);
```

---

## 🧼 Clean Up

```bash
docker-compose down -v
```

---

## 📌 Notes

- ✅ Uses latest Trino v.473+ compatible Iceberg setup
- ✅ Supports Hive Metastore as metadata backend
- ✅ MinIO used as AWS S3-compatible storage layer
- ✅ All services run isolated using Docker Compose

