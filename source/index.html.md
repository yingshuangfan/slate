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

# Create API 创建文档

描述：向elasticsearch中添加文档，要求


# 实时安全报告

API_Server Develop环境 地址：`http://svc.dev.allseeingsecurity.net:30052`

Console_Server_Rails Develop环境 地址：`54.222.166.143:30030`

# GEO-MAP 实时地图组件

## 绘图点 + 线 + 总操作数

描述：无过滤器模式下，返回地图的绘图信息；

<aside class="warning">优化（待完成） -> 将请求的N条数据集中选取M个具有特征的日志并返回（例如，不同的应用、部门、用户、地理位置等等）</aside>

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | LIMIT 100 ORDER BY time_local DESC
  
**Response Parameters:** 仅解释部分有效字段

  字段   |   描述    
 --------- | ------- 
 source_location.latitude  | 起点-纬度  
 source_location.longitude | 起点-经度  
 source_location.city | 起点-城市标签  
 host_location.latitude | 终点-经度  
 host_location.longitude | 终点-经度  
 operation_type | 边-操作类型标签  
 operation_result | 边-操作结果标签 
 
> response 

```json

    {
        "data": {
            "aggs": {},
            "list": [{
                 "device_id": "10142_deviceC",
                 "device_id_1d": "{\"10142_deviceC\":5013}",
                 "device_id_1h": "{\"10142_deviceC\":5013}",
                 "device_id_1month": "{\"10142_deviceC\":5013}",
                 "device_id_1w": "{\"10142_deviceC\":5013}",
                 "device_id_5m": "{\"10142_deviceC\":5013}",
                 "event_id": "12",
                 "host_ip": "220.181.16.184",
                 "host_location": {
                     "city": "北京",
                     "latitude": 39.9289,
                     "longitude": 116.3883
                 },
                 "http_user_agent": {
                     "browser": "Mobile Safari",
                     "browser_version": "6",
                     "device": "Mobile",
                     "operating_system": "iOS 6 (iPhone)"
                 },
                 "httpcode": "200",
                 "httpcode_1d": "{\"200\":5013}",
                 "httpcode_1h": "{\"200\":5013}",
                 "httpcode_1month": "{\"200\":5013}",
                 "httpcode_1w": "{\"200\":5013}",
                 "httpcode_5m": "{\"200\":5013}",
                 "is_handled": 1,
                 "is_safe": 0,
                 "label": {
                     "100": 0.0000039327715,
                     "101": 9.955179e-7,
                     "400": 1,
                     "000": 0.9999951
                 },
                 "label_account": 0,
                 "label_device": 0,
                 "label_operation": 1,
                 "label_user": 0.0000039327715,
                 "operation_result": "success",
                 "operation_type": "login",
                 "operation_type_1d": "{\"login\":{\"success\":5013}}",
                 "operation_type_1h": "{\"login\":{\"success\":5013}}",
                 "operation_type_1h_advanced": "{\"login\":{\"success\":5013}}",
                 "operation_type_1month": "{\"login\":{\"success\":5013}}",
                 "operation_type_1w": "{\"login\":{\"success\":5013}}",
                 "operation_type_5m": "{\"login\":{\"success\":5013}}",
                 "pre_location": "{\"pre_device_id\":\"10142_deviceC\",\"pre_time_local\":1519362384,\"distance\":0,\"city\":\"北京\",\"latitude\":39.9289,\"speed\":0,\"longitude\":116.3883}",
                 "source_ip": "220.181.16.84",
                 "source_location": {
                     "city": "北京",
                     "distance": 0,
                     "latitude": 39.9289,
                     "longitude": 116.3883,
                     "pre_device_id": "10142_deviceC",
                     "speed": 0
                 },
                 "tenant_id": "default",
                 "time_local": 1519362384,
                 "time_series_predict_result": 0,
                 "time_series_predict_result_1d": "{\"0\":5013}",
                 "uid": "testbychenlifei",
                 "user_name": "yufu_user_name",
                 "version": "1"
             }],
            "total": 72000
        },
        "message": "success",
        "status": 0
    }    

```

## 在线人数

描述：无过滤器模式下，返回在线人数统计值；

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | GROUP BY uid

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets.length  | 人数统计值  |

> response 

```json
    {
        "data": {
            "aggs": {
                "dimension_a": {
                    "buckets": [
                        {
                            "doc_count": 72000,
                            "key": "testbychenlifei"
                        }
                    ],
                    "doc_count_error_upper_bound": 0,
                    "sum_other_doc_count": 0
                }
            },
            "list": [],
            "total": 72000
        },
        "message": "success",
        "status": 0
    }
```

## 危险账号数 + 危险账号列表

描述：无过滤器模式下，返回危险账号列表和危险账号数统计值；

### 高危列表

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_user BETWEEN (0.9, 1) GROUP BY uid

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets.length  | 高危账号数统计值  |
| aggs.dimension_a.buckets  | 高危账号列表  |

> response 

```json
    {
        "data": {
            "aggs": {
                "dimension_a": {
                    "buckets": [
                        {
                            "doc_count": 22,
                            "key": "testbychenlifei"
                        }
                    ],
                    "doc_count_error_upper_bound": 0,
                    "sum_other_doc_count": 0
                }
            },
            "list": [],
            "total": 22
        },
        "message": "success",
        "status": 0
    }
```

### 中危列表

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_user BETWEEN (0.5, 9) GROUP BY uid

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets.length  | 中危账号数统计值  |
| aggs.dimension_a.buckets  | 中危账号列表  |

### 低危列表

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_user BETWEEN (0.3, 0.5) GROUP BY uid

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets.length  | 低危账号数统计值  |
| aggs.dimension_a.buckets  | 低危账号列表  |


## 危险操作数

描述：无过滤器模式下，返回危险操作数统计值，不必记录危险操作列表；查询时添加对label_operation的条件即可；

### 高危操作数

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_operation BETWEEN (0.9, 1) LIMIT 0

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| total  | 高危操作数  |

> response 

```json
    {
        "data": {
            "aggs": {},
            "list": [],
            "total": 5
        },
        "message": "success",
        "status": 0
    }
```

### 中危操作数

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_operation BETWEEN (0.5, 9) LIMIT 0

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| total  | 中危操作数  |

### 低危操作数

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_operation BETWEEN (0.3, 0.5) LIMIT 0

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| total  | 低危操作数  |


## 应用列表

描述：由当前时间段中的日志动态生成应用列表的取值范围，并提供针对应用的过滤器；

### STEP-ES

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | GROUP BY app_id

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets  | 应用ID列表  |

> response

```json
{
    "data": {
        "aggs": {
            "dimension_a": {
                "buckets": [
                    {
                        "doc_count": 72000,
                        "key": "sample-app-id"
                    }
                ],
                "doc_count_error_upper_bound": 0,
                "sum_other_doc_count": 0
            }
        },
        "list": [],
        "total": 72000
    },
    "message": "success",
    "status": 0
}
```

### STEP-MariaDB 暂缺

**HTTP Reqeust:** `GET   /users/:user_id/apps`
  
**Request Parameters:**

   Parameter  |   Value  
-----------|--------------
user_id | sample_uid

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| ...  | ...  |

> response

```json
{
    "list": []
}
```


## 部门列表

描述：由当前时间段中的日志动态生成部门列表的取值范围，并提供针对部门的过滤器；

### STEP-ES

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | GROUP BY dept_id

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets  | 部门ID列表  |

> response

```json
{
    "data": {
        "aggs": {
            "dimension_a": {
                "buckets": [
                    {
                        "doc_count": 72000,
                        "key": "human-resource"
                    }
                ],
                "doc_count_error_upper_bound": 0,
                "sum_other_doc_count": 0
            }
        },
        "list": [],
        "total": 72000
    },
    "message": "success",
    "status": 0
}
```

### STEP-MariaDB 暂缺

**HTTP Reqeust:** `GET   /users/:user_id/departments`
  
**Request Parameters:**

   Parameter  |   Value  
-----------|--------------
user_id | sample_uid

**Response Parameters:**

|  字段   |   描述    |
| --------- | ------- |
| ...  | ...  |

> response

```json
{
    "list": []
}
```


## 过滤器

查询语法为以下几类的随意组合：

1. 危险账号过滤器：
  * 高危：`WHERE label_user BETWEEN (0.9, 1)`
  * 中危：`WHERE label_user BETWEEN (0.5, 0.9)`
  * 低危：`WHERE label_user BETWEEN (0.3, 0.5)`

2. 危险操作过滤器：
  * 高危：`WHERE label_operation BETWEEN (0.9, 1)`
  * 中危：`WHERE label_operation BETWEEN (0.5, 0.9)`
  * 低危：`WHERE label_operation BETWEEN (0.3, 0.5)`

3. 应用过滤器：`WHERE app_id = sample_app_id`

4. 部门过滤器：`WHERE department_id = sample_department_id`

例如：应用app_0的dept_1部门所有高危账户产生的高危操作：

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_user BETWEEN (0.9, 1) AND label_operation BETWEEN (0.9, 1) AND app_id = app_0 AND WHERE department_id = dept_1

**Response Parameters:**

  字段   |   描述    
 --------- | ------- 
 source_location.latitude  | 起点-纬度  
 source_location.longitude | 起点-经度  
 source_location.city | 起点-城市标签  
 host_location.latitude | 终点-经度  
 host_location.longitude | 终点-经度  
 operation_type | 边-操作类型标签  
 operation_result | 边-操作结果标签 
 
> response 

```json

    {
        "data": {
            "aggs": {},
            "list": [{
                 "device_id": "10142_deviceC",
                 "device_id_1d": "{\"10142_deviceC\":5013}",
                 "device_id_1h": "{\"10142_deviceC\":5013}",
                 "device_id_1month": "{\"10142_deviceC\":5013}",
                 "device_id_1w": "{\"10142_deviceC\":5013}",
                 "device_id_5m": "{\"10142_deviceC\":5013}",
                 "event_id": "12",
                 "host_ip": "220.181.16.184",
                 "host_location": {
                     "city": "北京",
                     "latitude": 39.9289,
                     "longitude": 116.3883
                 },
                 "http_user_agent": {
                     "browser": "Mobile Safari",
                     "browser_version": "6",
                     "device": "Mobile",
                     "operating_system": "iOS 6 (iPhone)"
                 },
                 "httpcode": "200",
                 "httpcode_1d": "{\"200\":5013}",
                 "httpcode_1h": "{\"200\":5013}",
                 "httpcode_1month": "{\"200\":5013}",
                 "httpcode_1w": "{\"200\":5013}",
                 "httpcode_5m": "{\"200\":5013}",
                 "is_handled": 1,
                 "is_safe": 0,
                 "label": {
                     "100": 0.0000039327715,
                     "101": 9.955179e-7,
                     "400": 1,
                     "000": 0.9999951
                 },
                 "label_account": 0,
                 "label_device": 0,
                 "label_operation": 1,
                 "label_user": 0.0000039327715,
                 "operation_result": "success",
                 "operation_type": "login",
                 "operation_type_1d": "{\"login\":{\"success\":5013}}",
                 "operation_type_1h": "{\"login\":{\"success\":5013}}",
                 "operation_type_1h_advanced": "{\"login\":{\"success\":5013}}",
                 "operation_type_1month": "{\"login\":{\"success\":5013}}",
                 "operation_type_1w": "{\"login\":{\"success\":5013}}",
                 "operation_type_5m": "{\"login\":{\"success\":5013}}",
                 "pre_location": "{\"pre_device_id\":\"10142_deviceC\",\"pre_time_local\":1519362384,\"distance\":0,\"city\":\"北京\",\"latitude\":39.9289,\"speed\":0,\"longitude\":116.3883}",
                 "source_ip": "220.181.16.84",
                 "source_location": {
                     "city": "北京",
                     "distance": 0,
                     "latitude": 39.9289,
                     "longitude": 116.3883,
                     "pre_device_id": "10142_deviceC",
                     "speed": 0
                 },
                 "tenant_id": "default",
                 "time_local": 1519362384,
                 "time_series_predict_result": 0,
                 "time_series_predict_result_1d": "{\"0\":5013}",
                 "uid": "testbychenlifei",
                 "user_name": "yufu_user_name",
                 "version": "1"
             }],
            "total": 72000
        },
        "message": "success",
        "status": 0
    }    

```

注意：应用列表和部门列表如果是依照日志动态生成，同样受过滤器选择的制约，查询条件同上

# 用户数 + 操作数时序图

## 用户数时序图 

描述：用户数在时间区间内和时间粒度中的分布序列；注意GROUP BY的顺序是时间序列在前，用户在后；

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | GROUP BY (time_local INTER 3600), uid

**Response Parameters:**

  字段   |   描述    
 --------- | ------- 
 aggs.dimension_a.buckets  | 时间序列-列表
 aggs.dimension_a.buckets[i].key  | 时间戳
 aggs.dimension_a.buckets[i].dimension_b.buckets | 用户序列-列表 
 aggs.dimension_a.buckets[i].dimension_b.buckets.length | 用户数
 aggs.dimension_a.buckets[i].dimension_b.buckets[j].key | 用户ID
 
> response

```json
{
    "data": {
        "aggs": {
            "dimension_a": {
                "buckets": [
                    {
                        "dimension_b": {
                            "buckets": [
                                {
                                    "doc_count": 72000,
                                    "key": "testbychenlifei"
                                }
                            ],
                            "doc_count_error_upper_bound": 0,
                            "sum_other_doc_count": 0
                        },
                        "doc_count": 72000,
                        "key": 1519362000
                    },
                    {
                        "dimension_b": {
                            "buckets": [],
                            "doc_count_error_upper_bound": 0,
                            "sum_other_doc_count": 0
                        },
                        "doc_count": 0,
                        "key": 1519365600
                    },
                    {
                        "dimension_b": {
                            "buckets": [],
                            "doc_count_error_upper_bound": 0,
                            "sum_other_doc_count": 0
                        },
                        "doc_count": 0,
                        "key": 1519369200
                    }
                ]
            }
        },
        "list": [],
        "total": 72000
    },
    "message": "success",
    "status": 0
}
```

## 操作数时序图 

描述：操作数在时间区间内和时间粒度中的分布序列；

**HTTP Reqeust:** `GET   /api`
  
**Request Headers:**

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | GROUP BY time_local INTER 3600

**Response Parameters:**

  字段   |   描述    
 --------- | ------- 
 aggs.dimension_a.buckets  | 时间序列-列表
 aggs.dimension_a.buckets[i].doc_count  | 操作数
 aggs.dimension_a.buckets[i].key  | 时间戳
 
> response

```json
{
    "data": {
        "aggs": {
            "dimension_a": {
                "buckets": [
                    {
                        "doc_count": 72000,
                        "key": 1519362000
                    },
                    {
                        "doc_count": 0,
                        "key": 1519365600
                    },
                    {
                        "doc_count": 0,
                        "key": 1519369200
                    }
                ]
            }
        },
        "list": [],
        "total": 72000
    },
    "message": "success",
    "status": 0
}
```

