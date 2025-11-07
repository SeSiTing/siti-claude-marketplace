---
name: op_db_metadata
description: 提供v3_metadata数据库的SQL查询模板，包括对象编码（object_code）、对象名称、事件、按钮配置、自定义字段、元数据字段等表的查询。查询对象编码、对象名称、自定义对象时使用。使用 exec_sql 工具执行查询。
---

# v3_metadata 数据库查询

## 执行方式

所有查询使用 `exec_sql` 工具执行，参数替换为实际值。

**重要**：在执行 SQL 前，必须先打印出完整的目标 SQL 语句，然后再使用 `exec_sql` 工具执行。

**重要**：执行 SQL 后，需要提炼和总结查询结果，包括：
- 查询到的记录数量
- 关键字段的值（如对象编码、对象名称等）
- 如有多条记录，用表格或列表形式展示
- 避免原封不动地输出 JSON 响应

## 查询模板

### standard_business_object

**用途**：查询对象编码（object_code）和对象名称。根据对象名称查找对象编码，或根据对象编码查询对象信息。**常用表**。

**字段**：
- `object_code` - 对象编码
- `object_name` - 对象名称
- `org_id` - 租户ID（工厂号）
  - 预置对象：`org_id = -1`
  - 自定义对象：`org_id` 为对应的工厂号
- `object_category` - 对象类别（1=自定义对象，2=预设对象）

### mq_event

**用途**：查询业务事件配置信息。

**字段**：
- `id` - 事件ID

### mq_event_subscribe_target_topic

**用途**：查询事件转发配置信息。

**字段**：
- `event_id` - 事件ID

### mq_event_body_config

**用途**：查询事件体字段配置信息。

**字段**：
- `event_id` - 事件ID
- `biz_field_id` - 业务字段ID（关联 meta_field_config.id）

### meta_field_config

**用途**：查询字段类型配置信息，定义字段的数据类型和属性。

**字段**：
- `id` - 字段配置ID
- `biz_source` - 业务来源（如 'BIZ_EVENT'）

**关联关系**：
- `meta_field_config.id` = `mq_event_body_config.biz_field_id`

### button_config

**用途**：查询按钮配置信息，包括按钮分类、来源等。

**字段**：
- `object_code` - 对象编码
- `category` - 按钮分类（1=通用，2=新自定义，3=预置）
- `source` - 按钮来源（1=web，2=app）

### custom_field

**用途**：查询自定义字段元数据配置信息，可以查询对象下的自定义字段定义。**常用表**。

**字段**：
- `object_code` - 对象编码
- `field_id` - 字段ID（关联 meta_field_config.id）

## 注意事项

1. 参数替换：所有模板中的`{参数名}`都需要替换为实际值
2. 删除标记：所有查询都包含`deleted_at = 0`条件
3. 执行方式：必须通过 MCP 工具 `exec_sql` 执行
4. 表结构查询：使用 `DESC table_name` 或 `SHOW COLUMNS FROM table_name` 查询
