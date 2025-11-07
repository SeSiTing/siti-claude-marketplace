---
name: op_db_user
description: 查询租户和组织信息（v3_user数据库）。使用 exec_sql 工具执行查询。
---

# v3_user 数据库查询

## 执行方式

所有查询使用 `exec_sql` 工具执行，参数替换为实际值。

**重要**：在执行 SQL 前，必须先打印出完整的目标 SQL 语句，然后再使用 `exec_sql` 工具执行。

**重要**：执行 SQL 后，需要提炼和总结查询结果，包括：
- 查询到的记录数量
- 关键字段的值（如租户ID、工厂编号、工厂名称等）
- 如有多条记录，用表格或列表形式展示
- 避免原封不动地输出 JSON 响应

## 查询模板

### organization

**参数**：
- `{org_id}` - 租户ID
- `{code}` - 工厂编号
- `{organization_name}` - 工厂名称

```sql
-- 按租户ID查询
SELECT * FROM v3_user.organization WHERE id = {org_id};

-- 按工厂编号查询
SELECT * FROM v3_user.organization WHERE code LIKE '%{code}%';

-- 按工厂名称查询
SELECT * FROM v3_user.organization WHERE (organization_name LIKE '%{organization_name}%' OR display_name LIKE '%{organization_name}%');
```

## 注意事项

1. 参数替换：所有模板中的`{参数名}`都需要替换为实际值
2. 执行方式：必须通过 MCP 工具 `exec_sql` 执行
3. 表结构查询：使用 `DESC v3_user.organization` 或 `SHOW COLUMNS FROM v3_user.organization` 查询
