# 其他业务库查询模板

## 执行方式

所有查询使用 `exec_sql` 工具执行，参数替换为实际值。

**重要**：在执行 SQL 前，必须先打印出完整的目标 SQL 语句，然后再使用 `exec_sql` 工具执行。

**重要**：执行 SQL 后，需要提炼和总结查询结果，包括：
- 查询到的记录数量
- 关键字段的值（如订单ID、租户ID等）
- 如有多条记录，用表格或列表形式展示
- 避免原封不动地输出 JSON 响应

## v3_order 数据库

### sales_order

**参数**：
- `{sales_order_id}` - 销售订单ID
- `{org_id}` - 租户ID

```sql
-- 按订单ID查询
SELECT * FROM v3_order.sales_order WHERE id = {sales_order_id} and deleted_at = 0;

-- 按租户查询订单列表
SELECT * FROM v3_order.sales_order WHERE org_id = {org_id} and deleted_at = 0 order by created_at DESC;
```

### custom_field_ext

**参数**：
- `{sales_order_id}` - 销售订单ID（对应 sales_order.id）
- `{org_id}` - 租户ID（关联条件：sales_order.org_id = custom_field_ext.org_id）
- `{object_code}` - 对象编码（如需查询对象编码，使用 [op_db_metadata](../op_db_metadata/SKILL.md) 的 `standard_business_object` 表）

**关联关系**：
- `sales_order.id` = `custom_field_ext.instance_id`
- `sales_order.org_id` = `custom_field_ext.org_id`

**注意**：查询自定义字段时，如需根据对象名称查找对象编码（`object_code`），请使用 [op_db_metadata](../op_db_metadata/SKILL.md) 查询 `standard_business_object` 表。

```sql
-- 按订单ID和租户ID查询自定义字段
SELECT * FROM v3_order.custom_field_ext WHERE instance_id = {sales_order_id} AND org_id = {org_id} and deleted_at = 0;

-- 关联查询订单及其自定义字段
SELECT so.*, cfe.* 
FROM v3_order.sales_order so
LEFT JOIN v3_order.custom_field_ext cfe ON so.id = cfe.instance_id AND so.org_id = cfe.org_id
WHERE so.id = {sales_order_id} AND so.org_id = {org_id} and so.deleted_at = 0 and (cfe.deleted_at = 0 OR cfe.deleted_at IS NULL);
```

## 注意事项

1. 参数替换：所有模板中的`{参数名}`都需要替换为实际值
2. 删除标记：所有查询都包含`deleted_at = 0`条件
3. 表结构查询：使用 `DESC table_name` 或 `SHOW COLUMNS FROM table_name` 查询
