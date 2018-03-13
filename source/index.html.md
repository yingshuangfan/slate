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

# 实时安全报告

## GEO-MAP 实时地图组件

### 绘图点 + 线 + 总操作数

描述：无过滤器模式下，返回地图的绘图信息；

<aside class="warning">优化（待完成） -> 将请求的N条数据集中选取M个具有特征的日志并返回（例如，不同的应用、部门、用户、地理位置等等）</aside>

请求头:

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | LIMIT 100 ORDER BY time_local DESC
  
返回结果字段解析：

  字段   |   描述    
 --------- | ------- 
 source_location.latitude  | 起点-纬度  
 source_location.longitude | 起点-经度  
 source_location.city | 起点-城市标签  
 host_location.latitude | 重点-经度  
 host_location.longitude | 重点-经度  
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

### 在线人数

描述：无过滤器模式下，返回在线人数统计值；

请求头:

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | GROUP BY uid

返回结果字段解析：

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

### 危险账号数 + 危险账号列表

#### 高危

描述：无过滤器模式下，返回高危账号列表和高危账号数统计值；

请求头:

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_user BETWEEN (0.9, 1) GROUP BY uid

返回结果字段解析：

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

#### 中危

描述：无过滤器模式下，返回中危账号列表和中危账号数统计值；

请求头:

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_user BETWEEN (0.5, 9) GROUP BY uid

返回结果字段解析：

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets.length  | 中危账号数统计值  |
| aggs.dimension_a.buckets  | 中危账号列表  |

> response 

```json
    {
        "data": {
            "aggs": {
                "dimension_a": {
                    "buckets": [
                        {
                            "doc_count": 44,
                            "key": "testbychenlifei"
                        }
                    ],
                    "doc_count_error_upper_bound": 0,
                    "sum_other_doc_count": 0
                }
            },
            "list": [],
            "total": 44
        },
        "message": "success",
        "status": 0
    }
```

#### 低危

描述：无过滤器模式下，返回低危账号列表和低危账号数统计值；

请求头:

   Header  |   Value  
-----------|--------------
Tenant-Id | default
Start-Time  | 20180201T090000+0800
End-Time  | 20180301T000000+0000
Query-Param | WHERE label_user BETWEEN (0.3, 0.5) GROUP BY uid

返回结果字段解析：

|  字段   |   描述    |
| --------- | ------- |
| aggs.dimension_a.buckets.length  | 低危账号数统计值  |
| aggs.dimension_a.buckets  | 低危账号列表  |

> response 

```json
    {
        "data": {
            "aggs": {
                "dimension_a": {
                    "buckets": [
                        {
                            "doc_count": 88,
                            "key": "testbychenlifei"
                        }
                    ],
                    "doc_count_error_upper_bound": 0,
                    "sum_other_doc_count": 0
                }
            },
            "list": [],
            "total": 88
        },
        "message": "success",
        "status": 0
    }
```




