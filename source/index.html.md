---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

API-查询语法文档

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.


# 基础CRUD

## 查询文档

**Request Url+Method:** `GET   /api/search`

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |    查询文档类型 |   string
query | 必填（非空） |    查询语句 |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.list | 文档列表
 data.total | 文档总数
 message | 返回状态描述
 status | 返回状态码


```json
{
    "data": {
        "aggs": {},
        "list": [
            {
                "_external": {
                    "status": "ignored"
                },
                "_pipeline": {
                    "is_valid": true,
                    "labels": [
                        300
                    ],
                    "parameters": {
                        "390": {
                            "reasoning": "caused by..."
                        }
                    },
                    "risk_level": "high",
                    "source_location": {
                        "city": "杭州",
                        "country": "中国"
                    },
                    "tags": [
                        "300",
                        "200"
                    ],
                    "time_local": 1528945358
                },
                "app_id": "微软云",
                "event_id": "1528945358305.86218",
                "operation_description": "SUSPENDFAILURE",
                "operation_result": "FAILURE",
                "operation_type": "SUSPEND",
                "source_ip": "10.32.100.220",
                "tenant_id": "tn-xsy",
                "time_local": "2018-06-14T11:02:38",
                "uid": "user_476",
                "user_agent": "user_476 (HttpUrlConnection 1.8.0_102)"
            }
        ],
        "total": 10100
    },
    "message": "success",
    "status": 0
}
```


## 添加文档
首次添加的文档，会默认生成对应的映射表; 需要提供指定的文档ID和文档类型; 

1. 文档ID是同类型中文档的唯一标志；
2. 文档类型是标识一类文档的特殊关键词，在查询时也需要指定文档类型；

**Request Url+Method:** `POST   /api/create`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Content-Type | application/json

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
id | 必填（非空） |   索引ID |   string
source | 必填（非空） |    待创建文档内容 |   string(json-encoded)
type | 必填（非空） |    待创建文档类型 |   string

注意：时间戳以iso8601为标准，日期中必须携带时区信息，例如：2018-04-22T22:16:00+0800

```json
{
    "id": "0009",
    "source": "{ \"date\": \"2018-04-22T22:16:00+0800\", \"user_id\": \"user_123\", \"departments_id\": [\"dept_2\"], \"department_1\": \"dept_2\",\"security_level\": \"high\", \"risk_level\": \"mid\"}",
    "type": "insecure_users_by_day"
}
```
**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data | 空
 message | 返回状态描述
 status | 返回状态码


```json
{
    "data": {},
    "message": "success",
    "status": 0
}
```

## 批量添加文档
首次添加的文档，会默认生成对应的映射表; 需要提供指定的文档ID和文档类型; 

**Request Url+Method:** `POST   /api/create/bulk`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Content-Type | application/json

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
list | 必填（非空） |    待创建文档列表，包含{id, source} |   string(json-encoded)
type | 必填（非空） |    待创建文档类型 |   string

文档列表中的每一项文档(json-encoded)，均包含id和source字段：
1. id: 是同类型中文档的唯一标志；
2. source: 原始文档(json object)；

注意：时间戳以iso8601为标准，日期中必须携带时区信息，例如：2018-04-22T22:16:00+0800

```json
{
    "type": "insecure_users_by_day",
    "list": [
        {"id":"0000","source":"{ \"date\": \"2018-04-22T22:16:00+0800\", \"user_id\": \"user_123\", \"departments_id\": [\"dept_2\"], \"department_1\": \"dept_2\",\"security_level\": \"high\", \"risk_level\": \"mid\"}"},
        {"id":"0001","source":"{ \"date\": \"2018-04-22T22:16:00+0800\", \"user_id\": \"user_123\", \"departments_id\": [\"dept_2\"], \"department_1\": \"dept_2\",\"security_level\": \"high\", \"risk_level\": \"mid\"}"},
        {"id":"0002","source":"{ \"date\": \"2018-04-22T22:16:00+0800\", \"user_id\": \"user_123\", \"departments_id\": [\"dept_2\"], \"department_1\": \"dept_2\",\"security_level\": \"high\", \"risk_level\": \"mid\"}"},
        ...
        {"id":"0999","source":"{ \"date\": \"2018-04-22T22:16:00+0800\", \"user_id\": \"user_123\", \"departments_id\": [\"dept_2\"], \"department_1\": \"dept_2\",\"security_level\": \"high\", \"risk_level\": \"mid\"}"}
    ]
}
```

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.succeed | 成功写入文档数目
 message | 返回状态描述
 status | 返回状态码


```json
{
  "data": {
    "succeed": 1000
  }, 
  "message": "success", 
  "status": 0
}
```

## 更新文档
描述：根据查询语法过滤得到一列文档，并统一更新其中的一个或多个字段

**Request Url+Method:** `POST   /api/update`
  
**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |    查询文档类型  |   string
query | 必填（非空） |    查询语句  |   string 

更新单个字段：`SET _pipeline.risk_level=healthy WHERE _pipeline.uid=sample_uid`

更新多个字段：`SET _pipeline.risk_level=healthy AND _external.status=resolved WHERE _pipeline.uid=sample_uid`

**Response Parameters:** 仅解释部分有效字段

  字段   |   描述    
 --------- | ------- 
 data.total  | 查询获得文档数量
 data.updated | 更新文档数量
 data.failures  |  操作异常列表
 message | 返回状态描述
 status | 返回状态码



```json
{
    "data": {
        "failures": [],
        "total": 14,
        "updated": 14
    },
    "message": "success",
    "status": 0
}
```
 
注意：如果在更新中，发生版本冲突，会自动放弃更新操作；当更新文档数量较大，或者更新操作频繁时，容易发生版本冲突；


## 更新文档-带请求体（临时）

描述：根据查询语法过滤得到一列文档，并统一更新其中的一个或多个字段

**Request Url+Method:** `POST   /api/update`
  
**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |    查询文档类型  |   string
query | 必填（非空） |    查询语句  |   string 

query查询语句示例：`WHERE _pipeline.uid=sample_uid`

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
fields | 必填（非空） |    待创建文档列表，包含{field, value} |   list

```json
{
    "fields": [
        {
            "field": "_external.feedback_risk_level",
            "value": "high"
        },
        {
            "field": "_external.tagged_by",
            "value": "user"
        },
        {
            "field": "_external.feedback_description",
            "value": "海外网6月25日电。蔡英文今（25日）上午出席“台湾民主基金会”创会十五周年开幕典礼。在蔡英文办公室发布的新闻稿中，竟然将台外事部门负责人吴钊燮称为“美国外交部部长”。据台湾《联合新闻网》报道，蔡英文办公室在新闻稿中指出，包括“美国外交部部长”吴钊燮、“民主基金会”执行长葛胥曼(Carl Gershman)、“台湾民主基金会”执行长徐斯俭、多位专家学者都出席了该项活动。此事一出，立即引发网友骂声一片。有网友表示，“想当美国人想疯了吗？要当部长也轮不到他啊！”还有网友直呼相关工作人员“没常识”，“美国还有外交部长吗？”还有网友称蔡办此举“是想往美国脸上贴金，十分愚蠢！”"
        }
    ]
}
```

**Response Parameters:** 仅解释部分有效字段

  字段   |   描述    
 --------- | ------- 
 data.total  | 查询获得文档数量
 data.updated | 更新文档数量
 data.failures  |  操作异常列表
 message | 返回状态描述
 status | 返回状态码

```json
{
    "data": {
        "failures": [],
        "total": 1,
        "updated": 1
    },
    "message": "success",
    "status": 0
}
```

## 删除文档类型

删除指定类型的所有文档；注意这一接口仅在develop环境中允许使用；

**Request Url+Method:** `DELETE   /api/delete/<doc_type>`

例如：`curl -X DELETE http://127.0.0.1:5000/api/delete/test-0419`

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data | 空
 message | 返回状态描述
 status | 返回状态码

```json
{
    "data": {
       "deleted": 1
    },
    "message": "success",
    "status": 0
}
```

如果所指定删除的类型并不存在，返回错误提示：

```json
{
    "data": {
        "deleted": 0
    },
    "message": "cannot find index for type(s): [test_042415]",
    "status": 2
}
```

如果在非develop环境下调用，返回错误提示：

```json
{
    "data": {},
    "message": "no access right to delete index:['test-0419']",
    "status": 1
}
```


## 时间序列聚合

**Request Url+Method:** `GET   /api/search`
  
1>. 日期以iso8601为标准，才能支持对日期的聚合查询；日期中必须携带时区信息，例如：2018-04-22T22:16:00+0800

2>. 支持三种粒度的时间序列聚合：自然日、自然周（以周一为开始）、自然月，且默认为东八区时区；

  粒度  |    描述   |   查询语句
  -----------|-------------|---------------
  自然日 | 对日取整 |   GROUP BY field_name INTER day
  自然周 | 对周取整，周一为开始 |   GROUP BY field_name INTER week  
  自然月 | 对月取整 |   GROUP BY field_name INTER month  

2.1> 扩展：支持以自然日的倍数为粒度的时间序列聚合

  粒度  |    描述   |   查询语句
  -----------|-------------|---------------
  自然日倍数 | 对日取整 |   GROUP BY field_name INTER Nday

例如：`GROUP BY field_name INTER 3day`

3>. 以时间为粒度聚合时，需严格限定起始和结束时间，否则返回报错 

例如： `WHERE field_name BETWEEN(start_time, end_time) GROUP BY field_name INTER day`

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |    查询文档类型  |   string
query | 必填（非空） |    查询语句  |   string 

```json
{
    // 时间序列聚合结果示例
    "data": {
        "aggs": [
            {
                "doc_count": 0,
                "key": 1530547200000
            },
            {
                "doc_count": 12,
                "key": 1530633600000
            },
            {
                "doc_count": 0,
                "key": 1530720000000
            }
        ],
        "list": [],
        "total": 12
    },
    "message": "success",
    "status": 0
}
```

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.aggs  | 时间聚合序列
 data.aggs[i].doc_count  | 区间内统计数
 data.aggs[i].key  | 区间开始时间戳(毫秒单位)
 message | 返回状态描述
 status | 返回状态码


## 文本&列表字段聚合
**Request Url+Method:** `GET   /api/search`
  
**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |    查询文档类型  |   string
query | 必填（非空） |    查询语句  |   string 

1> 字段为文本类型，可以对该字段聚合：
例如：`GROUP BY department_1`

2> 字段为列表类型，列表中包含多个文本（注意不是内嵌对象），可以对该字段聚合：
例如：`GROUP BY departments_id`

3> 嵌套聚合：支持在聚合结果上再次聚合
例如：`GROUP BY department_1, risk_level`
例如：在时间序列聚合结果之上的聚合：`GROUP BY date INTER day, risk_level`

```json
{
    // 部门聚合结果示例
    "data": {
        "aggs": [
            {
                "doc_count": 2,
                "key": "dp-test-4-5717"
            },
            {
                "doc_count": 1,
                "key": "dp-test-2-2245"
            },
            {
                "doc_count": 1,
                "key": "dp-test-8-7506"
            },
            ...
        ],
        "list": [],
        "total": 12
    },
    "message": "success",
    "status": 0
}
```

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.aggs  | 部门统计列表
 data.aggs[i].doc_count  | 部门统计值
 data.aggs[i].key  | 部门ID
 message | 返回状态描述
 status | 返回状态码

# 查询语句示例

## 安全报告: 报告评分

<aside class="notice">
注意：以用户为单位的统计和查询应当以业务数据库DB为准；具体包含：
1. 用户总数
2. 危险用户数
3. 未处理危险用户数
</aside>

### 日志总数

   Parameter  | Example
-----------|-------------
type | log
query | `LIMIT 0`

```json
{
    // 日志总数
    "data": {
        "aggs": [],
        "list": [],
        "total": 1104
    },
    "message": "success",
    "status": 0
}
```
返回结果：

  Field   |   Description    
 --------- | ------- 
 data.total  | 日志总数
 message | 返回状态描述
 status | 返回状态码

### 危险日志总数
   Parameter  | Example
-----------|-------------
type | log
query | `WHERE NOT _pipeline.risk_level=healthy LIMIT 0`


### 未处理危险日志总数
   Parameter  | Example
-----------|-------------
type | log
query | `WHERE (NOT _pipeline.risk_level=healthy) AND _external.status=unresolved LIMIT 0`


## 安全报告: 危险账户

<aside class="notice">
注意：此处采用自定义用户表daily-risk-users；表结构和字段设计由前端决定，此处仅提供参考查询语句；
</aside>

```json
{
    // daily-risk-users字段结构
    "count": 93,
    "date": "2018-05-12T00:05:00+00:00",    // 风险等级计算时间
    "user": {
        "appSensitivity": 0,    // 应用敏感度
        "departmentPath": [     // 部门列表
            "dp-test-3-3069"
        ],
        "dept0": "dp-test-3-3069",  // 第一级部门
        "positionLevel": 2,     
        "riskLevel": "high",    // 风险等级
        "securityLevel": "high",    // 安全等级
        "tenantUuid": "tn-test-integration",    // 所属租户ID
        "uuid": "us-test-200-1526460508"        // 用户唯一标识UUID
    }
}
```

### 部门过滤器（单选）
  Parameter  | Example
-----------|-------------
type | daily-risk-users
query | `WHERE user.dept0=dept_2jd8`

### 时间过滤器
  Parameter  | Example
-----------|-------------
type | daily-risk-users
query | `WHERE date BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)`

### 高危用户总数
   Parameter  | Example
-----------|-------------
type | daily-risk-users
query | `WHERE user.riskLevel=high LIMIT 0`

```json
{
    // 高危用户数统计值
    "data": {
        "aggs": [],
        "list": [],
        "total": 183
    },
    "message": "success",
    "status": 0
}
```
返回结果：

  Field   |   Description    
 --------- | ------- 
 data.total  | 日志总数
 message | 返回状态描述
 status | 返回状态码

### 中危用户总数
   Parameter  | Example
-----------|-------------
type | daily-risk-users
query | `WHERE user.riskLevel=medium LIMIT 0`


### 低危用户总数
   Parameter  | Example
-----------|-------------
type | daily-risk-users
query | `WHERE user.riskLevel=low LIMIT 0`


### 全部危险用户总数
   Parameter  | Example
-----------|-------------
type | daily-risk-users
query | `LIMIT 0`


### 时序图-各风险等级用户总数序列

   Parameter  | Example
-----------|-------------
type | daily-risk-users
query | `WHERE date BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800) GROUP BY date INTER day, user.riskLevel`

```json
{
    "data": {
        "aggs": [
            {
                "buckets": [],
                "doc_count": 0,
                "key": 1529510400000
            },
            {
                "buckets": [
                    {
                        "doc_count": 97,
                        "key": "high"
                    },
                    {
                        "doc_count": 96,
                        "key": "low"
                    },
                    {
                        "doc_count": 89,
                        "key": "medium"
                    }
                ],
                "doc_count": 282,
                "key": 1529596800000
            },
            {
                "buckets": [
                    {
                        "doc_count": 89,
                        "key": "high"
                    },
                    {
                        "doc_count": 73,
                        "key": "medium"
                    },
                    {
                        "doc_count": 71,
                        "key": "low"
                    }
                ],
                "doc_count": 233,
                "key": 1529683200000
            },
            ...
        ],
        "list": [],
        "total": 1486
    },
    "message": "success",
    "status": 0
}
```
返回结果解析：

Field  | Description
--------- | ------- 
data.aggs  | 时间序列
data.aggs[i].doc_count  | 某时刻危险用户总数
data.aggs[i].key  | 某区间开始时间戳
data.aggs[i].buckets    |   当前时刻用户风险等级序列
data.aggs[i].buckets[j].key    |   用户风险等级
data.aggs[i].buckets[j].doc_count    |   当前时刻指定用户风险等级的用户数
message | 返回状态描述
status | 返回状态码

## 安全报告: 安全预警

### 部门过滤器（单选）
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _user.department_ids=dept_2jd8`

### 时间过滤器
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)`

### 风险等级过滤器（单选）
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _pipeline.risk_level=high`

1. 高危日志：`WHERE _pipeline.risk_level=high`
2. 中危日志：`WHERE _pipeline.risk_level=medium`
3. 低危日志：`WHERE _pipeline.risk_level=low`
4. 全部日志：`空`

### 管理员操作总数
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _user.role=admin LIMIT 0`

```json
{
    // 管理员操作数统计值
    "data": {
        "aggs": [],
        "list": [],
        "total": 1
    },
    "message": "success",
    "status": 0
}
```
返回结果：

  Field   |   Description    
 --------- | ------- 
 data.total  | 日志总数
 message | 返回状态描述
 status | 返回状态码

### 危险日志总数
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE NOT _pipeline.risk_level=healthy LIMIT 0`


### 地址异常日志总数
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (NOT _pipeline.risk_level=healthy) AND _pipeline.risk_type=source_ip LIMIT 0`


### 设备异常日志总数
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (NOT _pipeline.risk_level=healthy) AND _pipeline.risk_type=user_agent LIMIT 0`


### 时序图-管理员操作数序列
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _user.role=admin AND (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) GROUP BY time_local INTER day`

```json
{
    // 时序图-管理员操作数序列
    "data": {
        "aggs": [
            {
                "doc_count": 0,
                "key": 1530374400000
            },
            {
                "doc_count": 0,
                "key": 1530460800000
            },
            {
                "doc_count": 0,
                "key": 1530547200000
            },
            {
                "doc_count": 1,
                "key": 1530633600000
            },
            {
                "doc_count": 0,
                "key": 1530720000000
            },
            {
                "doc_count": 0,
                "key": 1530806400000
            },
            {
                "doc_count": 0,
                "key": 1530892800000
            }
        ],
        "list": [],
        "total": 1
    },
    "message": "success",
    "status": 0
}
```
返回结果：

Field  | Description
--------- | ------- 
data.aggs  | 时间序列
data.aggs[i].doc_count  | 某时刻管理员操作数
data.aggs[i].key  | 某区间开始时间戳(毫秒级)
message | 返回状态描述
status | 返回状态码

### 时序图-危险日志数序列
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (NOT _pipeline.risk_level=healthy) AND (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) GROUP BY time_local INTER day`


### 时序图-地址异常数序列
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800) AND _pipeline.risk_type=location GROUP BY time_local INTER day`


### 时序图-设备异常数序列
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800) AND _pipeline.risk_type=device GROUP BY time_local INTER day`


### 过滤器叠加下的聚合统计
不同过滤器之间互相独立，使用AND链接
例如：

1. 部门dept_0在过去一周中风险等级为高危的地址异常日志数统计值：`WHERE _user.department_ids=dept_2jd8 AND _pipeline.risk_level=high AND (time_local BETWEEN(2018-06-23T00:00:00+0800, 2018-06-29T00:00:00+0800)) AND _pipeline.risk_type=location`
2. 部门dept_0在过去一周中风险等级为高危的地址异常日志数时序聚合列表：`WHERE _user.department_ids=dept_2jd8 AND _pipeline.risk_level=high AND (time_local BETWEEN(2018-06-23T00:00:00+0800, 2018-06-29T00:00:00+0800)) AND _pipeline.risk_type=location GROUP BY time_local INTER day`


## 安全分析: 查询接口

### 危险行为最多的用户TOP10统计图(柱状图)
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY _pipeline.uid, _pipeline.risk_level`

```json
{
    // 危险行为最多的用户TOP10统计图(柱状图)
    "data": {
        "aggs": [
            {
                "buckets": [
                    {
                        "doc_count": 1000,
                        "key": "healthy"
                    },
                    {
                        "doc_count": 1,
                        "key": "medium"
                    }
                ],
                "doc_count": 1001,
                "key": "us-test-102-1530699833"
            },
            {
                "buckets": [
                    {
                        "doc_count": 15,
                        "key": "healthy"
                    },
                    {
                        "doc_count": 1,
                        "key": "medium"
                    }
                ],
                "doc_count": 16,
                "key": "us-test-204-1530699853"
            },
            {
                "buckets": [
                    {
                        "doc_count": 14,
                        "key": "healthy"
                    },
                    {
                        "doc_count": 1,
                        "key": "high"
                    }
                ],
                "doc_count": 15,
                "key": "us-test-301-1530699860"
            },
            ...
        ],
        "list": [],
        "total": 1104
    },
    "message": "success",
    "status": 0
}
```
返回结果解析：

Field  | Description
--------- | ------- 
data.aggs  | 用户序列
data.aggs[i].key  | 某用户UUID
data.aggs[i].doc_count  | 某用户危险日志数
data.aggs[i].buckets    |   日志风险等级序列
data.aggs[i].buckets[j].key    |   某类日志风险等级
data.aggs[i].buckets[j].doc_count    |  某用户的指定危险等级日志数
message | 返回状态描述
status | 返回状态码 

### 危险行为最多的用户TOP10统计图(饼状图)
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY _pipeline.risk_level, _pipeline.uid`


### 危险行为最多的用户TOP10统计表
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY _pipeline.uid`

```json
{
    // 危险行为最多的用户TOP10统计表
    "data": {
        "aggs": [
            {
                "doc_count": 1001,
                "key": "us-test-102-1530699833"
            },
            {
                "doc_count": 16,
                "key": "us-test-204-1530699853"
            },
            {
                "doc_count": 15,
                "key": "us-test-301-1530699860"
            },
            ...
        ],
        "list": [],
        "total": 1104
    },
    "message": "success",
    "status": 0
}
```
返回结果解析：

Field  | Description
--------- | ------- 
data.aggs  | 用户序列
data.aggs[i].key  | 某用户UUID
data.aggs[i].doc_count  | 指定用户危险日志数
message | 返回状态描述
status | 返回状态码

### 异常访问最多的应用TOP10统计图(柱状图)
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY app_id, _pipeline.risk_level`

### 异常访问最多的应用TOP10统计图(饼状图)
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY _pipeline.risk_level, app_id`

### 异常访问最多的应用TOP10统计表
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY app_id`

### 危险行为最多的部门TOP10统计图(柱状图)
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY _user.critical_department_ids, _pipeline.risk_level`

### 危险行为最多的部门TOP10统计图(饼状图)
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY _pipeline.risk_level, _user.critical_department_ids`

### 危险行为最多的部门TOP10统计表
  Parameter  | Example
-----------|-------------
type | log
query | `WHERE (time_local BETWEEN(2018-06-21T00:00:00+0800, 2018-06-27T00:00:00+0800)) AND (NOT _pipeline.risk_level=healthy) GROUP BY _user.critical_department_ids`


## 安全日志: 日志查询

接口调用方法见：[基础CRUD-查询文档](#38645019ef)

```cURL
curl -X POST \
  'http://analytic_service:5000/api/update?type=log&query=WHERE%20_pipeline.risk_level%3Dhealthy%20WHERE%20event_id%3Devent-d83h'
```
<aside class="notice">
日志查询字段列表：

1. 原始日志字段在最外层，不做解析处理；
2. 内部字段：实际存储在日志内部的字段，支持过滤和聚合；
3. 外部字段：实际存储在其他位置的字段，支持过滤和部分聚合，详见"当前支持的外部字段聚合语法"；
</aside>


```json
{
    "tenant_id":"tn-yufu",  // 租户id
    "operation_type":"CREATE", // 操作类型
    "operation_result":"SUCCESS", // 操作结果
    "time_local":"2018-06-11T02:17:03.355Z", // 时间
    "source_ip":"192.168.2.179", // 用户ip
    "internal_ip":"", // 内部ip， 默认为空， 未使用
    "uid":"koucunhu@yufuid.com", // uid
    "path":"/log-dir/admin-app.log", // 玉符日志 log path
    "host_domain":"", // 访问的域名， 默认为空，未使用
    "event_id":"92e7f7b5-cad4-4abd-8c39-df5d360cfdd1", // 日志唯一id
    "_external":{
        "resolved_method":"",  // 日志已处理的手段：停用(suspend)/封禁(lock)；默认为空
        "status":"", // 日志处理状态: 已处理(resolved)/未处理(unresolved)/忽略(ignored)，健康日志默认为空，危险日志默认为未处理
        "feedback_risk_level":"default", // 用户反馈的日志危险等级，默认值为default, , 取值[default, low, medium, high, healthy]
        "feedback_description":"", // 用户输入的预警原因：可取值为长度不超过100个汉字的字符串，初始值为缺省；用于记录用户在前端手动输入的预警原因；
        "tagged_by":"system" // 用户标记：默认值为system, 可取值[system, user]；用于区分每条日志展示的为系统判断结果还是用户判断结果；
    },
    "_pipeline":{
        "tenant_id":"tn-yufu", // 租户id
        "user_agent":{
            "browser":"Chrome",
            "browser_version":"53",
            "operating_system":"Mac OS X",
            "device":"Computer"
        }
        "source_location":{ // 用户地理位置信息
            "country":"中国",
            "pre_device_id":"-1486793979", // 上一次device_id
            "distance":0,
            "city":"北京",
            "latitude":39.54,
            "time_local":1528683423,
            "speed":0,
            "longitude":116.23
        },
        "operation_type":"CREATE", // 操作类型
        "city_set":"{"北京":35}", // 登录地点集合
        "operation_type_1w":"{"DELETE":{"SUCCESS":12},"CREATE":{"SUCCESS":22},"LOGIN":{"SUCCESS":1}}", // 1周内操作汇总
        "device_id_1d":"{"-1486793979":6}", // 一天内使用设备
        "source_ip":"192.168.2.179", // 用户ip
        "operation_type_1h":"{"CREATE":{"SUCCESS":5},"LOGIN":{"SUCCESS":1}}", // 1小时操作汇总
        "uid":"koucunhu@yufuid.com", // uid
        "operation_type_5m":"{"CREATE":{"SUCCESS":5}}", // 5分钟操作汇总
        "device_id_5m":"{"-1486793979":5}", // 5分钟设备汇总
        "operation_type_1d":"{"CREATE":{"SUCCESS":5},"LOGIN":{"SUCCESS":1}}", // 1天操作汇总
        "device_id_1h":"{"-1486793979":6}", // 1小时设备汇总
        "ip_risk":"1", // ip属于黑名单的级别
        "app_id":"dp-448161e21abb46478aa88a5705e93232", // 操作对象。 用户id或者是应用id
        "user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.79 Safari/537.36",
        "timestamp":1528683423, // 日志发生时间戳
        "device_id":"-1486793979", // 设备id
        "operation_type_4w":"{"DELETE":{"SUCCESS":12},"CREATE":{"SUCCESS":22},"LOGIN":{"SUCCESS":1}}", // 4周操作汇总
        "device_id_1w":"{"-1486793979":35}", // 1周设备汇总
        "operation_result":"SUCCESS", // 操作结果
        "time_local":1528683423, // 日志发生时间戳
        "basic_parameter":"{}", // 临时参数表
        "label":{ // data-pipe为日志打的标签
 
        },
        "pre_location":"{"country":"中国","pre_device_id":"-1486793979","pre_time_local":1528683423,"distance":0,"city":"北京","latitude":39.54,"time_local":1528683423,"speed":0,"longitude":116.23}", // 用户上次操作的地理位置信息
        "labels":[ // data-pipe打的标签列表，用于日志回溯索引
 
        ],
        "tags":[ // data-pipe打的tags，用于日志回溯索引
 
        ],
        "risk_level":"healthy", // data-pipe判断的日志风险等级, 取值[low, medium, high, healthy]
        "event_id":"92e7f7b5-cad4-4abd-8c39-df5d360cfdd1", // 日志id
        "operation_obj_1d":"{}", // 1天内操作对象统计
        "is_valid":true, // 日志是否合法
        "operation_description":"None", // 操作描述
        "device_id_4w":"{"-1486793979":35}", // 4周内设备统计
        "parameters":{ // 危险行为的参数，用于前端生成危险行为的描述文案
 
        }
    },
    "operation_description":"None", // 操作描述
    "app_id":"dp-448161e21abb46478aa88a5705e93232", // 操作对象。 用户id或者是应用id
    "user_agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.79 Safari/537.36"
}
```

### 内部字段（部分字段）
此处仅罗列部分业务相关字段，全部字段见：[玉符全量日志字段格式](https://allseeingsecurity.atlassian.net/wiki/spaces/SPC/pages/223019009)

Field     |     Type      |     Description
------------|-----------------|-------------------
event_id    |     keyword     |     日志ID
tenant_id   |     keyword     |     租户ID
time_local    |       date      |     日志触发时间
uid       |       keyword     |       用户ID
source_ip   |     keyword     |     源IP
app_id |    keyword     |     操作对象=应用ID或用户ID
user_agent    |     keyword     |     设备(浏览器、操作系统)
operation_type  |     keyword     |     操作类型
operation_result|     keyword     |     操作结果
operation_description|    text      |     操作描述
_external.status      |     keyword     |     日志处理状态
_external.resolved_method   | keyword   |   日志已处理的手段
_external.feedback_risk_level|  keyword     |   用户反馈日志风险等级
_external.feedback_description|text     |   用户输入预警原因
_external.tagged_by|    keyword |   用户标记
_pipeline.user_agent.browser|   keyword |   浏览器类型
_pipeline.source_location.city |     keyword     |     城市
_pipeline.source_location.country |     keyword     |     国家
_pipeline.is_valid    |     boolean     |     格式是否合法  
_pipeline.risk_level    |     keyword     |     日志风险等级
_pipeline.labels    |     keyword     |     预警标签的列表（优先级由高到低）
_pipeline.label.xxx   |     float     |     某预警标签置信概率
_pipeline.tags|      keyword     |    与某预警类型相关的列表
_pipeline.risk_type|    keyword     |   预警类型

部分核心字段补充说明：

1. 日志处理状态：已处理(resolved)/未处理(unresolved)，健康日志默认为空，危险日志默认为未处理
2. 日志处理手段：停用(suspend)/封禁(lock)；默认为空
3. 日志风险等级：高危(high)/中危(medium)/低危(low)/健康(healthy)，优先级高于label.xxx
4. 用户反馈风险等级：默认值为default, 取值[default, low, medium, high, healthy]
5. 用户标记：默认值为system, 可取值[system, user]；用于区分每条日志展示的为系统判断结果还是用户判断结果
6. 预警类型：默认值为空，可取值=[location,device,account,operation,ipRisk]

### 外部字段（部分字段）

Field     |     Type      |     Description
----------|----------------|--------------------
_user.department_ids  |     keyword     |     部门ID列表
_user.critical_department_ids  |     keyword     |     部门ID列表（用于聚合）
_user.username   |     text      |     用户名
_user.display_name      |     text      |     姓名
_user.risk_level    |     keyword     |     账户风险等级=high/mid/low/healthy
_user.role    |     keyword     |       账户角色=admin/user


### 当前支持的外部字段聚合语法

<aside class="warning">
语法不完整！待补全语法：双维度-外部和外部字段聚合；
</aside>

1. 单维度字段聚合: `GROUP BY _user.critical_department_ids`
2. 双维度-内部和外部字段聚合: `GROUP BY _pipeline.risk_level, _user.critical_department_ids`
3. 双维度-外部和内部字段聚合: `GROUP BY _user.critical_department_ids, _pipeline.risk_level`

### 危险、健康日志过滤器

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE  _pipeline.risk_level=healthy`


1. 健康日志：`WHERE  _pipeline.risk_level=healthy`
2. 危险日志：`WHERE NOT _pipeline.risk_level=healthy`


### 部门过滤器

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _user.department_ids=dept_2jd8`


1. 单个部门：`WHERE _user.department_ids=dept_2jd8`
2. 多个部门：`WHERE _user.department_ids=dept_2jd8 OR _user.department_ids=dept_9sjw`


### 时间过滤器

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE time_local BETWEEN(2018-06-17T00:00:00+0800, 2018-07-01T00:00:00+0800)`


### 用户名或姓名模糊检索过滤器

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _user.username~王 OR _user.display_name~王`


### 城市或IP模糊检索过滤器

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _pipeline.source_location.city~上海 OR source_ip~上海`


### 处理状态过滤器（可多选）

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _external.status=unresolved`

1. 已处理日志：`WHERE _external.status=resolved`
2. 未处理日志：`WHERE _external.status=unresolved`
3. 多选（已处理+未处理）：`WHERE _external.status=resolved OR _external.status=unresolved` 或 `空`

### 访问时间排序

  Parameter  | Example
-----------|-------------
type | log
query | `ORDER BY time_local DESC`

1. 时间倒序：`ORDER BY time_local DESC`
2. 时间正序：`ORDER BY time_local ASC` 

### 管理员过滤器（可多选）

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _user.role=admin`

1. 管理员：`WHERE _user.role=admin`
2. 普通用户：`WHERE _user.role=user` 
3. 多选（管理员+普通用户）： `WHERE _user.role=admin OR _user.role=user` 或 `空`

### 浏览器过滤器（可多选）

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _pipeline.user_agent.browser=chrome`

1. 多选（Safari+Chrome）： `WHERE _pipeline.user_agent.browser=chrome OR _pipeline.user_agent.browser=safari`

### 风险等级（可多选）

  Parameter  | Example
-----------|-------------
type | log
query | `WHERE _pipeline.risk_level=high`


1. 高危日志：`WHERE _pipeline.risk_level=high`
2. 中危日志：`WHERE _pipeline.risk_level=mid`
3. 低危日志：`WHERE _pipeline.risk_level=low`
4. 多选（高危+中危）：`WHERE _pipeline.risk_level=high OR _pipeline.risk_level=mid`

### 分页器

  Parameter  | Example
-----------|-------------
type | log
query | `LIMIT 10`

1. offset=0, pageSize=10：`LIMIT 10`
2. offset=50, pageSize=10：`LIMIT 50, 10`

### 危险日志相关日志

  Parameter  | Example
-----------|-------------
type | log
query | `RELATED BY event_id, label`

指定危险日志event_id和危险类型标签：`RELATED BY event-d83h, 100`

对健康日志或不存在的event_id回溯的返回结果：

```json
{
    // 危险日志回溯=健康日志或不存在event_id
    "data": {},
    "message": "cannot trace back on healthy logs",
    "status": 1
}
```

### 过滤器叠加

1. 不同过滤器的叠加由AND运算符实现；
2. 当前OR运算不允许出现复合嵌套，例如（不合法实例！）：`WHERE (a=1 AND b=2) OR c=3`; 
3. 在以上基础上，当AND和OR同时出现，请在OR运算外部加括号表明优先级：`WHERE (a=1 OR b=2) AND c=3`;
4. BETWEEN语句在AND连用情况下，请在外部加括号表明优先级：`WHERE (range BETWEEN(low, high)) AND a=1`;

<aside class="warning">
注意：2.3.4均属于语法设计不足，07-13后版本语法树会对此做进一步优化更新；
</aside>

实例：日志类型=健康 + 部门=dept_1 + 时间区间 + 用户名/姓名%王 + 时间倒序 + 第二页:pagesize=10

`WHERE _pipeline.risk_level=healthy AND _user.department_ids=dept_0 AND (time_local BETWEEN(2018-06-17T00:00:00+0800, 2018-07-01T00:00:00+0800)) AND (_user.username~王 OR _user.display_name~王) ORDER BY time_local DESC LIMIT 10, 10`


## 安全日志: 日志更新

接口调用方法见：[基础CRUD-更新文档](#fe2cc3e076)

```cURL
curl -X POST \
  'http://analytic_service:5000/api/update?type=log&query=WHERE%20_pipeline.risk_level%3Dhealthy%20WHERE%20event_id%3Devent-d83h'
```

日志待更新字段：

1. 内部字段：实际存储在日志内部的字段，支持更新；
2. 外部字段：实际存储在其他位置的字段，不支持更新！业务数据由专用接口完成更新；

* 内部字段

Field     |     Type      |     Description
------------|-----------------|-------------------
_external.status      |     keyword     |     日志处理状态=已处理(resolved)/未处理(unresolved)，健康日志默认为空，危险日志默认为未处理
_external.resolved_method   | keyword   |   日志已处理的手段=停用(suspend)/封禁(lock)；默认为空
_pipeline.risk_level    |     keyword     |     日志风险等级=high/mid/low/healthy(优先级高于label.xxx)


### 日志反馈

  Parameter  | Example
-----------|-------------
type | log
query | `SET _pipeline.risk_level=healthy WHERE event_id=event-d83h`


设置用户反馈=危险（危险程度=high）：`SET _pipeline.risk_level=high WHERE event_id=event-d83h`

### 日志处理状态设置

  Parameter  | Example
-----------|-------------
type | log
query | `SET _external.status=resolved AND _external.resolved_method=lock WHERE event_id=event-d83h`


1. 设置处理状态=忽略：`SET _external.status=ignored WHERE event_id=event-d83h`
2. 设置处理状态=已处理：`SET _external.status=resolved AND _external.resolved_method=lock WHERE event_id=event-d83h`
3. 设置处理状态=未处理：`SET _external.status=unresolved WHERE event_id=event-d83h`

注意：日志处理方法仅对处理状态=已处理时有效；

## UEBA-报告页

### 系统评分

```json
{   
    // 系统评分
    "data": {
        "score": 98,
        "description": "系统安全状况整体良好"
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/v1/security/search/systemScore`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id | 必填（非空） |   租户ID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.score | 系统评分
 message | 返回状态描述
 status | 返回状态码

### 未处理危险事件数

```json
{   
    // 未处理危险事件数
    "data": {
        "count": 17
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/v1/security/search/unresolvedInsecureEventsCount`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id | 必填（非空） |   租户ID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.count | 未处理危险事件数
 message | 返回状态描述
 status | 返回状态码

### 危险事件数

```json
{   
    // 危险事件数
    "data": {
        "count": 17
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/v1/security/search/insecureEventsCount`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id | 必填（非空） |   租户ID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.count | 危险事件数
 message | 返回状态描述
 status | 返回状态码

### 危险用户数

```json
{   
    // 危险用户数
    "data": {
        "count": 45
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/v1/security/search/insecureUsersCount`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id | 必填（非空） |   租户ID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.count | 危险用户数
 message | 返回状态描述
 status | 返回状态码

### 总事件数

```json
{   
    // 总事件数
    "data": {
        "count": 507
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/v1/security/search/eventsCount`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id | 必填（非空） |   租户ID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.count | 总事件数
 message | 返回状态描述
 status | 返回状态码 

### 总用户数

```json
{   
    // 总用户数
    "data": {
        "count": 74
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/v1/security/search/usersCount`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id | 必填（非空） |   租户ID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.count | 总用户数
 message | 返回状态描述
 status | 返回状态码

### 系统风险趋势图

```json
{
    // 系统风险趋势图-请求体
    "tenant_id": "tn-test-integration",
    "start_time": "2018-07-15T00:00:00+08:00",
    "end_time": "2018-07-20T00:00:00+08:00"
}
```

```json
{
    // 系统风险趋势图-返回结果
    "data": {
        "aggs": [],
        "list": [
            {
                "date": "2018-07-16T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-17T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-18T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-19T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-20T00:00:00+08:00",
                "risk_score": 84,
                "risk_score_diff": 84,
                "tenant_id": "tn-test-integration"
            }
        ],
        "total": 5
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `POST   /api/v1/security/search/systemScoreTimeseries`  

**Request Headers:**

   Header  |   Value  
-----------|--------------
Content-Type | application/json

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id       |    必填（非空） |   租户D  | list
start_time | 必填（非空） |   开始时间戳ISO-8601格式 |   string
end_time | 必填（非空） |    结束时间戳ISO-8601格式 |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.list | 系统风险值时间序列
 data.list[i].date | 时间戳
 data.list[i].risk_score | 风险值
 data.list[i].risk_score_diff | 风险值差分
 data.list[i].tenant_id | 租户ID
 message | 返回状态描述
 status | 返回状态码

### 风险值增长最快的账户TOP10

```json
{
    // 风险值增长最快的账户TOP10
    "data": {
        "aggs": [],
        "list": [
            {
                "critical_department_ids": [
                    "default"
                ],
                "department_ids": [
                    "default"
                ],
                "display_name": "袁鸿煊",
                "risk.is_timeseries": true,
                "risk.timeseries_prop": 0.9999998807907104,
                "risk_level": "healthy",
                "risk_score": 99,
                "risk_score_diff": 99,
                "role": "member",
                "status": "active",
                "tenant_id": "tn-test-integration",
                "update_time": "2018-07-18T17:57:32+08:00",
                "user_id": "us-test-100-1531921624",
                "username": "23@yahoo.com"
            }
            ...
        ],
        "total": 74
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/search`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |   文档类型 |   string
query | 必填（非空） |  查询语句  |   string

type: `user`
query: `WHERE tenant_id=tn-test-integration ORDER BY risk_score_diff DESC LIMIT 10`

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.list | 系统风险值时间序列
 data.list[i].user_id | 用户ID
 data.list[i].username | 用户名
 data.list[i].display_name | 姓名
 data.list[i].risk_score | 用户风险值
 data.list[i].risk_score_diff | 用户风险值差分
 message | 返回状态描述
 status | 返回状态码


### 风险值最高的账户TOP10

```json
{
    // 风险值最高的账户TOP10
    "data": {
        "aggs": [],
        "list": [
            {
                "critical_department_ids": [
                    "default"
                ],
                "department_ids": [
                    "default"
                ],
                "display_name": "袁鸿煊",
                "risk.is_timeseries": true,
                "risk.timeseries_prop": 0.9999998807907104,
                "risk_level": "healthy",
                "risk_score": 99,
                "risk_score_diff": 99,
                "role": "member",
                "status": "active",
                "tenant_id": "tn-test-integration",
                "update_time": "2018-07-18T17:57:32+08:00",
                "user_id": "us-test-100-1531921624",
                "username": "23@yahoo.com"
            }
            ...
        ],
        "total": 74
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/search`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |   文档类型 |   string
query | 必填（非空） |  查询语句  |   string

type: `user`
query: `WHERE tenant_id=tn-test-integration ORDER BY risk_score DESC LIMIT 10`

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.list | 系统风险值时间序列
 data.list[i].user_id | 用户ID
 data.list[i].username | 用户名
 data.list[i].display_name | 姓名
 data.list[i].risk_score | 用户风险值
 data.list[i].risk_score_diff | 用户风险值差分
 message | 返回状态描述
 status | 返回状态码

### 重点关注账户

<aside class="info">
注意：重点关注账户存储在业务DB中，仅风险值需要从此服务中抓取；注意此查询结果不是分页的；
</aside>

```json
{
    // 重点关注账户-请求体
    "users": [
        {
            "tenant_id": "tn-test-integration",
            "user_id": "us-test-100-1531921624"
        },
        {
            "tenant_id": "tn-test-integration",
            "user_id": "us-test-100-1531921658"
        },
        {
            "tenant_id": "tn-test-integration",
            "user_id": "us-test-100-1531921572"
        },
        ...
    ]
}
```

```json
{
    // 重点关注账户-返回结果
    "data": {
        "aggs": [],
        "list": [
            {
                "critical_department_ids": [
                    "default"
                ],
                "department_ids": [
                    "default"
                ],
                "display_name": "袁鸿煊",
                "risk.is_timeseries": true,
                "risk.timeseries_prop": 0.9999998807907104,
                "risk_level": "healthy",
                "risk_score": 99,
                "risk_score_diff": 99,
                "role": "member",
                "status": "active",
                "tenant_id": "tn-test-integration",
                "update_time": "2018-07-18T17:57:32+08:00",
                "user_id": "us-test-100-1531921624",
                "username": "23@yahoo.com"
            }
            ...
        ],
        "total": 74
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `POST   /api/users/search/bulk`  

**Request Headers:**

   Header  |   Value  
-----------|--------------
Content-Type | application/json

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
users       |    必填（非空） |   用户ID列表  | list
users[i].tenant_id | 必填（非空） |   租户ID |   string
users[i].user_id | 必填（非空） |    用户ID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.list | 系统风险值时间序列
 data.list[i].user_id | 用户ID
 data.list[i].username | 用户名
 data.list[i].display_name | 姓名
 data.list[i].risk_score | 用户风险值
 data.list[i].risk_score_diff | 用户风险值差分
 message | 返回状态描述
 status | 返回状态码

## UEBA 用户列表页

### 用户列表
```json
{
    // 用户列表页
    "data": {
        "aggs": [],
        "list": [
            {
                "critical_department_ids": [
                    "dp-test-9-795"
                ],
                "department_ids": [
                    "dp-test-9-795"
                ],
                "display_name": "何展鹏",
                "risk": {
                    // 时间序列账户异常相关参数
                    "timeseries": {
                        "average": [0, 10, 5, ...],
                        "current": [2, 125, 3, ...],
                        "count": 3624,
                        "probability": 0.997,
                        "is_alerted": true
                    },
                    // 休眠账户异常相关参数
                    "sleeping": {
                        "date_range": 30,
                        "is_alerted": true
                    }
                },
                "risk_level": "healthy",
                "risk_score": 0,
                "risk_score_diff": 0,
                "role": "member",
                "status": "active",
                "tenant_id": "tn-test-integration",
                "update_time": "2018-07-21T06:07:46+08:00",
                "user_id": "us-test-AI-8000",
                "username": "3@yahoo.com",
                "latest_alerted_time": "2018-07-21T06:07:46+08:00"  // 最近告警时间
            }
        ],
        "total": 16
    },
    "message": "success",
    "status": 0
}
```
**Request Url+Method:** `GET   /api/search`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |   文档类型 |   string
query | 必填（非空） |  查询语句  |   string

#### 按照风险值排序

type: `user`
query: `ORDER BY risk_score_diff DESC`

#### 按照最近报警时间排序

type: `user`
query: `ORDER BY latest_alerted_time DESC`

#### 部门过滤器

type: `user`
query: `WHERE department_ids=dp-test-9-795`

#### 用户名或姓名模糊检索

type: `user`
query: `WHERE username~yahoo.com OR display_name~yahoo.com`

#### 用户名或姓名模糊检索

type: `user`
query: `WHERE username~yahoo.com OR display_name~yahoo.com`

### 最近告警事件文案（暂缺）


## UEBA 用户详情页

### 常用设备列表TOP10

```json
{
    // 常用设备列表TOP10
    "data": {
        "aggs": [
            {
                "doc_count": 114,
                "key": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36"
            },
            {
                "doc_count": 57,
                "key": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_2) AppleWebKit/604.4.7 (KHTML, like Gecko) Version/11.0.2 Safari/604.4.7"
            },
            ...
        ],
        "list": [],
        "total": 181
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/search`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |   文档类型 |   string
query | 必填（非空） |  查询语句  |   string

type: `log`
query: `WHERE _pipeline.uid=us-test-101-1531749128 GROUP BY user_agent`

### 常用地址列表TOP10

```json
{
    // 常用地址列表TOP10
    "data": {
        "aggs": [
            {
                "doc_count": 113,
                "key": "东莞市"
            },
            {
                "doc_count": 68,
                "key": "北京"
            }
        ],
        "list": [],
        "total": 181
    },
    "message": "success",
    "status": 0
}
```
**Request Url+Method:** `GET   /api/search`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |   文档类型 |   string
query | 必填（非空） |  查询语句  |   string

type: `log`
query: `WHERE _pipeline.uid=us-test-101-1531749128 GROUP BY _pipeline.source_location.city`

### 用户风险趋势图

```json
{
    // 用户风险趋势图-请求体
    "tenant_id": "tn-test-integration",
    "user_id": "user-id2k",
    "start_time": "2018-07-15T00:00:00+08:00",
    "end_time": "2018-07-20T00:00:00+08:00"
}
```

```json
{
    // 用户风险趋势图-返回结果
    "data": {
        "aggs": [],
        "list": [
            {
                "date": "2018-07-16T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "user_id": "user-id2k",
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-17T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "user_id": "user-id2k",
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-18T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "user_id": "user-id2k",
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-19T00:00:00+08:00",
                "risk_score": 0,
                "risk_score_diff": 0,
                "user_id": "user-id2k",
                "tenant_id": "tn-test-integration"
            },
            {
                "date": "2018-07-20T00:00:00+08:00",
                "risk_score": 84,
                "risk_score_diff": 84,
                "user_id": "user-id2k",
                "tenant_id": "tn-test-integration"
            }
        ],
        "total": 5
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `POST   /api/v1/security/search/userRiskScoreTimeseries`  

**Request Headers:**

   Header  |   Value  
-----------|--------------
Content-Type | application/json

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
tenant_id       |    必填（非空） |   租户ID  | string
user_id       |    必填（非空） |   用户ID  | string
start_time | 必填（非空） |   开始时间戳ISO-8601格式 |   string
end_time | 必填（非空） |    结束时间戳ISO-8601格式 |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data.list | 用户风险值时间序列
 data.list[i].date | 时间戳
 data.list[i].risk_score | 风险值
 data.list[i].risk_score_diff | 风险值差分
 data.list[i].tenant_id | 租户ID
 message | 返回状态描述
 status | 返回状态码

### 用户信息

**Request Url+Method:** `GET   /api/search`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |   文档类型 |   string
query | 必填（非空） |  查询语句  |   string

type: `user`
query: `WHERE user_id=us-test-101-1531749128`

### 最近告警事件文案（暂缺）

### 事件列表

```json
{
    // 事件列表
    "data": {
        "list": [
            {
                "tenant_id": "tenant-3jd9",
                "uid": "location_1",
                "event_type": "log",    // 事件类型
                "risk_score": 12,   // 事件风险值    
                "security_event_id": "d7fee2e1-b371-43d8-b34f-903789065457",    // 事件ID
                "details": {
                    "related_events": [
                        "100-1518537602058" // 关联日志ID列表
                    ]
                },
                "timestamp": 1522648800 // 日期格式，单位=秒
            }
        ],
        "aggs": [],
        "total": 1
    },
    "message": "success",
    "status": 0
}
```

**Request Url+Method:** `GET   /api/search`  

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
type | 必填（非空） |   文档类型 |   string
query | 必填（非空） |  查询语句  |   string

#### 按照触发时间排序
type: `event`
query: `ORDER BY timestamp DESC`

#### 按照风险值排序
type: `event`
query: `ORDER BY risk_score DESC `


### 事件描述信息(暂缺)


## UEBA 安全系统架构

### 系统+用户风险值更新计算

```json
{
    // 风险值更新计算-请求体
    "tenant_id": "tenant-s8k3", // 租户ID
    "user_id": "user-d93k",     // 用户UUID
    "timestamp": "2018-07-19T20:29:00+08:00"    // 触发异常时间
}
```

```json
{
    // 风险值更新计算-返回结果
    "status": 0,    
    "message": "success",       
    "data": {}  
}
```

**Request Url+Method:** `POST  /api/v1/security/update/calculateRiskScore`  

**Request Headers:**

   Header  |   Value  
-----------|--------------
Content-Type | application/json

**Request Parameters:**

   Parameter  |  Required   |    Description   |   Type 
-----------|-------------|--------------------|---------------
timestamp  |    必填（非空） |   触发异常时间 | string
tenant_id | 必填（非空） |   租户ID |   string
user_id | 必填（非空） |    用户UUID |   string

**Response Parameters:** 

  字段   |   描述    
 --------- | ------- 
 data | 空
 message | 返回状态描述
 status | 返回状态码


