---
title: SQL Queries
description: All SQL queries that can come in handy for admin works
date: 2025-01-29 23:00:00 +0530
categories: [SQL, Database, Admin]
tags: [Test5]
pin: true
---

# SQL Queries

## Check active queries

```sql
SELECT 
    r.session_id,
    r.status,
    r.start_time,
    r.cpu_time,
    r.total_elapsed_time,
    r.logical_reads,
    r.writes,
    r.blocking_session_id,
    r.wait_type,
    r.wait_time,
    r.last_wait_type,
    r.transaction_isolation_level,
    t.text AS sql_text
FROM sys.dm_exec_requests r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) t
WHERE r.session_id <> @@SPID  -- Exclude current session
ORDER BY r.start_time ASC;

```

## Check table size

```sql
SELECT 
    s.name AS SchemaName,
    t.name AS TableName,
    SUM(a.total_pages) * 8 / 1024 AS Size_MB,
    SUM(a.total_pages) * 8 / 1024.0 / 1024 AS Size_GB
FROM sys.tables t
JOIN sys.schemas s ON t.schema_id = s.schema_id
JOIN sys.indexes i ON t.object_id = i.object_id
JOIN sys.partitions p ON i.object_id = p.object_id AND i.index_id = p.index_id
JOIN sys.allocation_units a ON p.partition_id = a.container_id
GROUP BY s.name, t.name
ORDER BY Size_MB DESC;

```

## Check available schemas

```sql
SELECT 
    s.schema_id, 
    s.name AS schema_name, 
    p.name AS schema_owner
FROM sys.schemas s
JOIN sys.database_principals p ON s.principal_id = p.principal_id
ORDER BY s.name;
```

