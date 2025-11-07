---
name: op_db
description: 提供通用的SQL查询方法和数据库查询基础。使用 exec_sql 工具执行查询。
---

# 通用数据库查询工具

## 执行方式

所有查询使用 `exec_sql` 工具执行，参数替换为实际值。

**重要**：在执行 SQL 前，必须先打印出完整的目标 SQL 语句，然后再使用 `exec_sql` 工具执行。

**重要**：执行 SQL 后，需要提炼和总结查询结果，包括：
- 查询到的记录数量
- 关键字段的值
- 如有多条记录，用表格或列表形式展示
- 避免原封不动地输出 JSON 响应

## 通用查询方法

### 表结构查询

```sql
-- 查询表结构
DESC table_name;
SHOW COLUMNS FROM table_name;
SHOW TABLE STATUS LIKE 'table_name';
```

### 数据库信息查询

```sql
-- 查询所有数据库
SHOW DATABASES;

-- 查询当前数据库
SELECT DATABASE();

-- 查询指定数据库的所有表
SHOW TABLES FROM database_name;

-- 查询表的创建语句
SHOW CREATE TABLE table_name;
```

### 基础查询技巧

```sql
-- 查询前 N 条记录
SELECT * FROM table_name LIMIT N;

-- 查询去重
SELECT DISTINCT column_name FROM table_name;

-- 查询排序
SELECT * FROM table_name ORDER BY column_name DESC;

-- 查询统计
SELECT COUNT(*) FROM table_name WHERE condition;
```

## 专用数据库查询

### 常用数据库（有专用 Skill）

- **v3_user**：查询组织/租户信息，使用 [op_db_user](../op_db_user/SKILL.md)
- **v3_openapi**：查询连接器/API配置，使用 [op_db_openapi](../op_db_openapi/SKILL.md)
- **v3_metadata**：查询事件/按钮/元数据配置，使用 [op_db_metadata](../op_db_metadata/SKILL.md)

## 注意事项

1. 参数替换：所有模板中的`{参数名}`都需要替换为实际值
2. 删除标记：查询时需注意 `deleted_at = 0` 条件
3. 执行方式：必须通过 MCP 工具 `exec_sql` 执行
