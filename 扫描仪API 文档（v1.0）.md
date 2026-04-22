# Alpcer扫描仪API 文档（v1.0）

# 一、基础信息

| 项目          | 具体内容                                        |
| ------------- | ----------------------------------------------- |
| API标题       | Alpcer扫描仪API                                 |
| API描述       | 扫描仪API文档                                   |
| API版本       | v1.0                                            |
| API请求根路径 | http://192.168.0.143:9996 （IP 替换为扫描仪IP） |

# 二、接口详情

## 2.1 连接设备

### 2.1.1 Client模式

手机开启热点，扫描仪连手机热点

#### 蓝牙指令

直接通过蓝牙发送以下字符串即可：

```shell
# 命令映射表：接收到的命令 -> 响应内容

# 连接wifi(使用空格分隔wifi名称和密码)
CONNECT_WIFI:MyHomeWiFi 12345678 → WIFI:CONNECTED

# 获取IP
GET_IP → IP:192.168.1.100

```

错误示例

```
# 连接wifi
CONNECT_WIFI:MyHomeWiFi 12345678 → ERROR:FAILED_AFTER_RETRIES

# 获取IP
GET_IP → ERROR:error msg
```



#### 获取WiFi列表

| 接口信息           | 具体内容                     |
| ------------------ | ---------------------------- |
| 请求路径           | /wifi/list                   |
| 请求方法           | GET                          |
| 功能描述           | 获取设备周边可连接的WiFi列表 |
| 请求参数           | 无                           |
| 响应说明（200 OK） | 返回WiFi列表信息             |

响应示例

saveList 是连接过的wifi列表

list 是扫描到的WiFi列表

```json
{
    "data": {
        "saveList": [
            "启量科技15",
            "netplan-eth0",
            "2979632348的Redmi Note 14 5G Network",
            "alpcer"
        ],
        "list": [
            {
                "ssid": "ChinaNet-hchJ-5G",
                "bssid": null,
                "rate": null,
                "signal": null,
                "inUse": false
            },
            {
                "ssid": "ChinaNet-cDBm-5G",
                "bssid": null,
                "rate": null,
                "signal": null,
                "inUse": false
            }
           
        ]
    },
    "code": 0,
    "msg": "success"
}
```



#### 连接WiFi

| 接口信息           | 具体内容                               |        |          |          |
| ------------------ | -------------------------------------- | ------ | -------- | -------- |
| 请求路径           | /wifi/connect                          |        |          |          |
| 请求方法           | POST                                   |        |          |          |
| 功能描述           | 让设备连接指定的WiFi网络               |        |          |          |
| 请求参数（Query）  | 参数名                                 | 类型   | 是否必填 | 说明     |
|                    | name                                   | string | 是       | WiFi名称 |
|                    | password                               | string | 是       | WiFi密码 |
| 响应说明（200 OK） | {data: null, code: 0, msg: "连接成功"} |        |          |          |

### 2.1.2 AP模式

扫描仪开启热点，手机连扫描仪热点

#### 开启热点

name 默认是 扫描仪版本 + MAC地址（例如 **T01488FCFC49**）

password 默认 **12345678**

热点固定IP **192.168.10.1**

重启后扫描仪默认是Client模式

| 接口信息           | 具体内容                              |        |          |          |
| :----------------- | ------------------------------------- | ------ | -------- | -------- |
| 请求路径           | /hostpot                              |        |          |          |
| 请求方法           | GET                                   |        |          |          |
| 功能描述           | 开启设备热点功能                      |        |          |          |
| 请求参数（Query）  | 参数名                                | 类型   | 是否必填 | 说明     |
|                    | name                                  | string | 否       | 热点名称 |
|                    | password                              | string | 否       | 热点密码 |
| 响应说明（200 OK） | {data: null, code: 0, msg: "success"} |        |          |          |



## 2.2 参数设置

设备扫面参数相关操作

### 2.2.1 获取参数设置信息

| 接口信息           | 具体内容                             |
| ------------------ | ------------------------------------ |
| 请求路径           | /setting                             |
| 请求方法           | GET                                  |
| 功能描述           | 获取设备当前的扫描设置及相关配置信息 |
| 请求参数           | 无                                   |
| 响应说明（200 OK） | 返回设备当前的扫描设置信息           |

响应示例

```json
{
    "data": {
        "name": "xhcs2",
        "index": "484",
        "scanMode": 1,
        "colorSwitch": true,
        "rainFogDenoise": true,
        "otherDenoise": true,
        "stitchDenoise": true,
        "minAvail": 32,
        "inclinometerSwitch": false,
        "hdrMode": false,
        "driftFilter": false,
        "driftFilterRange": 5.5,
        "driftFilterValue": 8.0
    },
    "code": 0,
    "msg": "success"
}
```

### 2.2.2 更新参数设置

| 接口信息 | 具体内容                                                     |
| -------- | ------------------------------------------------------------ |
| 请求路径 | /setting                                                     |
| 请求方法 | PUT                                                          |
| 功能描述 | 更新设备的扫描设置及相关配置信息                             |
| 请求参数 | 请求体（application/json），数据结构参考 TSetting模型，为必填项 |
| 响应说明 | {"data":{},"code":0,"msg":"更新设置成功"}                    |

请求参数示例

```json
#T01扫描仪
{
    "name": "xhcs2",
    "index": "486",
    "scanMode": 2,
    "colorSwitch": true,
    "rainFogDenoise": true,
    "otherDenoise": true,
    "stitchDenoise": true,
    "inclinometerSwitch": false,
    "hdrMode": true
}
#T02扫描仪
{
    "name": "xhcs2",
    "index": "486",
    "scanMode": 2,
    "colorSwitch": true,
    "driftFilter": true,
    "driftFilterRange": 5.5,
  	"driftFilterValue": 8,
    "inclinometerSwitch": false,
    "hdrMode": true
}
```



## 2.3 扫描管理

该标签下接口主要用于扫描操作、扫描状态查询、扫描文件管理等扫描相关功能。

### 2.3.1 开始扫描文件

| 接口信息          | 具体内容           |         |          |        |                        |
| ----------------- | ------------------ | ------- | -------- | ------ | ---------------------- |
| 请求路径          | /v1/start/scan     |         |          |        |                        |
| 请求方法          | POST               |         |          |        |                        |
| 功能描述          | 启动扫描仪扫描功能 |         |          |        |                        |
| 请求参数（Query） | 参数名             | 类型    | 是否必填 | 默认值 | 说明                   |
|                   | notCover           | boolean | 否       | true   | 是否不覆盖已有扫描文件 |
| 响应示例          | 返回时间和文件信息 |         |          |        |                        |

响应示例

```json
{
  "data":{
       "name":"xhcs2_485.ply", // 扫描结果文件名
       "time":1776332965346 // 开始扫描时间
  },
  "code":0,
  "msg":"success"
}
```

### 2.3.2 扫描续航测试

| 接口信息           | 具体内容         |         |          |        |                  |
| ------------------ | ---------------- | ------- | -------- | ------ | ---------------- |
| 请求路径           | /scan/test       |         |          |        |                  |
| 请求方法           | POST             |         |          |        |                  |
| 功能描述           | 进行扫描续航测试 |         |          |        |                  |
| 请求参数（Query）  | 参数名           | 类型    | 是否必填 | 默认值 | 说明             |
|                    | test             | boolean | 否       | true   | 是否执行续航测试 |
| 响应说明（200 OK） | 无额外响应内容   |         |          |        |                  |

### 2.3.3 终止扫描进程

| 接口信息           | 具体内容                             |
| ------------------ | ------------------------------------ |
| 请求路径           | /kill/process                        |
| 请求方法           | POST                                 |
| 功能描述           | 终止当前正在运行的扫描进程           |
| 请求参数           | 无                                   |
| 响应说明（200 OK） | {"data":{},"code":0,"msg":"success"} |

### 2.3.4 获取扫描文件状态

| 接口信息          | 具体内容               |        |          |                                                  |
| ----------------- | ---------------------- | ------ | -------- | ------------------------------------------------ |
| 请求路径          | /v1/scan/status        |        |          |                                                  |
| 请求方法          | GET                    |        |          |                                                  |
| 功能描述          | 获取扫描文件的当前状态 |        |          |                                                  |
| 请求参数（Query） | 参数名                 | 类型   | 是否必填 | 说明                                             |
|                   | scanPath               | string | 是       | 扫描文件的路径(/scanning/result_files/xhcs2_486) |

响应示例

```json
{
    "data": {
        "isError": false, // 是否扫描错误
        "isScanning": false, // 是否扫描中
        "startTime": 1776330306630, // 开始扫描时间
        "isFinish": true // 是否扫描完成
    },
    "code": 0,
    "msg": "success"
}
```

### 2.3.5 获取扫描仪状态（是否扫描中）

| 接口信息           | 具体内容                       |
| ------------------ | ------------------------------ |
| 请求路径           | /scanner/isScanning            |
| 请求方法           | GET                            |
| 功能描述           | 查询扫描仪当前是否处于扫描状态 |
| 请求参数           | 无                             |
| 响应说明（200 OK） | 返回扫描仪状态信息             |

响应示例

```json
{
    "data": false, // 是否扫描中
    "code": 0,
    "msg": "success"
}
```

### 2.3.6 获取统计信息

| 接口信息           | 具体内容                                             |
| ------------------ | ---------------------------------------------------- |
| 请求路径           | /scan/stat                                           |
| 请求方法           | GET                                                  |
| 功能描述           | 获取扫描操作的相关统计信息（如扫描次数、文件数量等） |
| 请求参数           | 无                                                   |
| 响应说明（200 OK） | 返回扫描统计信息                                     |

响应示例

```json
{
    "data": {
        "fileNum": 642, // 扫描文件数量
        "totalSize": 122379051008, // 扫描仪存储大小（byte）
        "usedSize": 76684853248 // 扫描仪已使用存储
    },
    "code": 0,
    "msg": "success"
}
```



### 2.3.7 获取扫描文件列表

| 接口信息           | 具体内容             |
| ------------------ | -------------------- |
| 请求路径           | /scan/list           |
| 请求方法           | GET                  |
| 功能描述           | 获取获取扫描文件列表 |
| 请求参数           | 无                   |
| 响应说明（200 OK） | 返回扫描文件列表     |

响应示例

```
{
    "data": [
        {
            "id": 648,
            "fileName": "xhcs2_485",s
            "filePath": null,
            "fileSize": 81543520,
            "fileTime": "2026-04-16 17:52:05"
        },
        {
            "id": 647,
            "fileName": "xhcs2_484",
            "filePath": null,
            "fileSize": 83493198,
            "fileTime": "2026-04-16 14:48:59"
        }
    ],
    "code": 0,
    "msg": "success"
}
```



### 2.3.8 删除扫描文件

| 接口信息           | 具体内容                                 |
| ------------------ | ---------------------------------------- |
| 请求路径           | /scan/{id}                               |
| 请求方法           | DELETE                                   |
| 功能描述           | 通过ID删除文件                           |
| 请求参数           | 文件ID，通过获取列表接口返回             |
| 响应说明（200 OK） | {data: null, code: 0, msg: "删除成功！"} |

## 2.4 文件管理

### 读取文件

扫描仪文件存储在根目录下的 **scanning/result_file** 文件夹中，可通过http链接下载文件

访问路径为 http://${扫描仪IP}/result_file/${项目名}_${序号}/${文件名}

示例：http://192.168.0.143/result_files/bzcs_001/bzcs_001.ply

### 修改配置

配置文件为 **/etc/nginx/sites-enabled/default**

```nginx
# 文件访问配置
location / {
  root /scanning;
  # First attempt to serve request as file, then
  # as directory, then fall back to displaying a 404.
  try_files $uri $uri/ =404;
}
# 网页配置
location /dist {
   root /var/www/html;
   index index.html;
}


```



# 三、数据模型

## TSetting(扫描设置类)

### 示例

```json
{
  "name": "bzcs",
  "index": "001",
  "scanMode": 0,
  "colorSwitch": true,
  "rainFogDenoise": true,
  "otherDenoise": true,
  "stitchDenoise": true,
  "inclinometerSwitch": true,
  "hdrMode": true,
  "driftFilter": true,
  "driftFilterRange": 5.5,
  "driftFilterValue": 8
}

```

### 属性

| 名称               | 类型           | 必选  | 说明             |
| ------------------ | -------------- | ----- | ---------------- |
| id                 | integer(int32) | false | id               |
| name               | string         | false | 名称             |
| index              | string         | false | 序号             |
| scanMode           | integer(int32) | false | 扫描模式         |
| colorSwitch        | boolean        | false | 彩色             |
| rainFogDenoise     | boolean        | false | 去噪(雨雾，灰尘) |
| otherDenoise       | boolean        | false | 去噪(其他)       |
| stitchDenoise      | boolean        | false | 去噪(黏连)       |
| minAvail           | integer(int32) | false |                  |
| inclinometerSwitch | boolean        | false | 倾角仪开关       |
| hdrMode            | boolean        | false | HDR开关          |
| driftFilter        | boolean        | false | 去噪             |
| driftFilterRange   | number(double) | false | 去噪范围         |
| driftFilterValue   | number(double) | false | 去噪值           |

## R（返回结果类）

示例

```json
{
  "data": {},
  "code": 0,
  "msg": "string"
}

```

### 属性

| 名称 | 类型           | 必选  | 说明 |
| ---- | -------------- | ----- | ---- |
| data | object         | false | none |
| code | integer(int32) | false | none |
| msg  | string         | false | none |

