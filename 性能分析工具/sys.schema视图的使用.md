## sys schema 视图的使用

风险提示：通过 sys 库去查询时，MySQL 会消耗大量资源去收集相关信息，严重的可能会导致业务请求被阻塞，从而引起故障。建议不要在生产环境上频繁的去查询 sys、performance_schema 或者 information_schema 来完成监控、巡检等工作。

### 索引相关

```mysql
# 查询冗余索引
SELECT * FROM sys.schema_redundant_indexes;
# 查询未使用过的索引
SELECT * FROM sys.schema_unused_indexes;
# 查询索引的使用情况
SELECT * FROM sys.schema_index_statistics;
```

### 表相关

```mysql
# 查询表的访问量
SELECT table_schema, table_name, SUM(io_read_requests + io_write_requests) AS io_requests FROM sys.schema_table_statistics GROUP BY table_schema, table_name ORDER BY io_requests DESC;
# 查询占用 BufferPool 较多的表
SELECT * FROM sys.innodb_buffer_stats_by_table ORDER BY allocated;
# 查询表的全表扫描情况
SELECT * FROM sys.statements_with_full_table_scans;
```

### 语句相关

```mysql
# SQL执行频率
SELECT * FROM sys.statement_analysis ORDER BY exec_count DESC;
# 监控使用了排序的SQL
SELECT * FROM sys.statements_with_sorting;
# 监控使用了临时表或者磁盘临时表的SQL
SELECT db, exec_count, tmp_tables, tmp_disk_tables, query FROM sys.statement_analysis WHERE tmp_tables > 0 OR tmp_disk_tables > 0 ORDER BY (tmp_tables + tmp_disk_tables) DESC;
```

### IO 相关

```mysql
# 查看消耗磁盘 IO 的文件
SELECT file, avg_read, avg_write, avg_read + avg_write AS avg_io FROM sys.io_global_by_file_by_bytes ORDER BY avg_read;
```

### InnoDB 相关

```mysql
# 行阻塞情况
SELECT * FROM sys.innodb_lock_waits;
```

