# 测试平台文档
# 测试平台简析

```c
├── Conf            /* 存放配置文件、定义初始化数据 */
│   ├── database.json       
│   ├── protoc.go           
│   └── protoc.json
├── Database        /* 存放MySQL、Redis数据库代码 */      
│   ├── init.go
│   ├── mysql.go           
│   ├── redis.go
│   └── table_struct.go
├── Form            /* 存放API（HTTP）接口的参数 */
│   ├── ohp.go          
│   ├── pmm.go  
│   └── vci.go 
├── FSM             /* 存放FSM的测试代码 */
│   ├── ohp_client.go           /* OHP客户端的业务逻辑 */
│   ├── pmm_client.go           /* PMM客户端的业务逻辑 */
│   ├── udp_server.go           /* 模拟FSM转发数据逻辑 */
│   └── vci_client.go           /* VCI客户端的业务逻辑 */
├── Pool            /* TCP、UDP链接池 */
│   ├── pool.go
│   └── udp_pool.go   
├── Proto            /* 存放protoc文件 */
├── Protoc          /* 存放模块之间的通信协议 */
│   ├── fsmohp
│   │    ├── GCU_FSM_OHP_v0.1.pb.go
│   │    └── GCU_FSM_OHP_v0.2.pb.go
│   ├── fsmpmm
│   │    ├── GCU_FSM_PMM_v0.2.pb.go
│   │    ├── GCU_FSM_PMM_v0.3.pb.go
│   │    ├── GCU_FSM_PMM_v0.4.pb.go
│   │    └── GCU_FSM_PMM_v0.6.pb.go
│   ├── fsmvci
│   │    ├── GCU_FSM_VCI_v0.2.pb.go
│   │    ├── GCU_FSM_VCI_v0.4.pb.go
│   │    ├── GCU_FSM_VCI_v0.6.pb.go
│   │    ├── GCU_FSM_VCI_v0.8.pb.go
│   │    └── GCU_FSM_VCI_v0.9.pb.go
├── Upd             /* 存放以UDP协议为基础构建的Client、Server基类 */
│   ├── client.go
│   └── server.go
├── Utils           /* 存放公共方法 */
│   ├── file.go
│   ├── private.go
│   ├── protoc.go
└   └── utils.go
```


# 测试平台接口文档

## VCI
### 1.1 注册线程（Register Thread）

#### 1.1.1 接口地址 127.0.0.1:10001/api/vci-register/build

##### 接口描述 创建VCI注册线程

##### 请求方法 POST
##### 请求参数

| 参数 | 类型   | 是否必要 | 描述         |
| ---- | ------ | -------- | ------------ |
| id   | Number | 必选     | 注册线程的Id |

```json
{
    "id":1
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 1.1.2 接口地址 127.0.0.1:10001/api/v1/vci-register/change-state

##### 接口描述 修改注册线程发送心跳的间隔

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述           |
| ----------------- | ------ | -------- | -------------- |
| heart_beat_period | Number | 必选     | 主线程心跳间隔 |

```json
{
    "heart_beat_period":200
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 1.1.3 接口地址 127.0.0.1:10001/api/v1/vci-register/register

##### 接口描述 注册线程发送注册信息

##### 请求方法 POST
##### 请求参数

| 参数             | 类型   | 是否必要 | 描述                 |
| ---------------- | ------ | -------- | -------------------- |
| self_check_state | Number | 必选     | 车桩交互模块自检状态 |

```json
{
    "self_check_state":1
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```



#### 1.1.4 接口地址 127.0.0.1:10001/api/v1/vci-register/heart

##### 接口描述 注册线程发送心跳数据

##### 请求方法 POST
##### 请求参数

| 参数               | 类型    | 是否必要 | 描述                                         |
| ------------------ | ------- | -------- | -------------------------------------------- |
| gun_id      | Number  | 必选     | Charge线程id                                 |
| heart_beat_cnt     | Number  | 可选     | 心跳计数，默认是当前计数                     |
| heart_beat_period  | Number  | 可选     | 心跳间隔， 默认是当前心跳间隔                |
| time_now           | Number  | 可选     | 心跳时间戳，默认当前时间戳                   |
| gun_basic_state    | Number  | 可选     | VCI枪头基础状态信息                          |
| --link_state       | Boolean | 可选     | 插枪状态,false表示空闲,true表示已插枪        |
| --positioned_state | Boolean | 可选     | 在位/归位状态,false表示未归位,true表示已归位 |

```json
{
    "gun_id":1,
    "heart_beat_cnt":1,
    "heart_beat_period":1,
    "time_now":1,
    "gun_basic_state":{
    	"link_state":true,
        "positioned_state":false
    }
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 1.1.5 接口地址 127.0.0.1:10001/api/v1/vci-register/delete

##### 接口描述 删除VCI注册线程

##### 请求方法 DELETE
##### 请求参数

| 参数 | 类型   | 是否必要 | 描述         |
| ---- | ------ | -------- | ------------ |
| id   | Number | 必选     | 注册线程的Id |

```json
{
    "id":1
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```


### 1.2. 充电线程（Charge Thread）

#### 1.2.1 接口地址 127.0.0.1:10001/api/v1/vci-charger/build

##### 接口描述 创建充电线程

##### 请求方法 POST
##### 请求参数

| 参数   | 类型   | 是否必要 | 描述          |
| ------ | ------ | -------- | ------------- |
| gun_id | Number | 必选     | 充电线程的 Id |

```json
{
    "gun_id":1
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```



#### 1.2.2 接口地址 127.0.0.1:10001/api/v1/vci-charger/change-interval

##### 接口描述 修改充电线程发送心跳数据的间隔

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述               |
| ----------------- | ------ | -------- | ------------------ |
| gun_id     | Number | 必选     | 充电线程的 Id      |
| heart_beat_period | Number | 必选     | 充电线程的心跳间隔 |

```json
{
    "gun_id":1
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```



#### 1.2.3 接口地址 127.0.0.1:10001/api/v1/vci-charger/change-rtstatus

##### 接口描述 修改充电线程RT数据

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
|----------------- | ------ | -------- | ------------------------------------ |
| status            | bool   | 必选     | 充电线程的的状态，true时触发RT数据包 |
| gun_id            | Number | 必选     | 充电线程的 id                        |
| push_cnt          | Number | 可选     | RT推送计数，默认当前计数             |
| heart_beat_period | Number | 可选     | RT的心跳间隔，默认当前心跳间隔       |

```json
{
    "status":true,
    "gun_id":1,
    "push_cnt":500,
    "heart_beat_period":52
}
```

##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```



#### 1.2.4 接口地址 127.0.0.1:10001/api/v1/vci-charger/delete

##### 接口描述 删除充电线程

##### 请求方法 DELETE
##### 请求参数

| 参数   | 类型   | 是否必要 | 描述          |
| ------ | ------ | -------- | ------------- |
| gun_id | Number | 必选     | 充电线程的 Id |

```json
{
    "gun_id":1
}
```

## PMM

### 2.1 PMM主线程通讯（Matrix Thread）

#### 2.1.1 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/build

##### 接口描述 创建主通讯线程

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number   | 必选     | 主线程id  |


```json
{
    "id":1
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.1.2 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/register-matrix
##### 接口描述 发送功率矩阵注册信息帧

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id  |
| state             | Number | 必选      | 功率矩阵模块自检状态 |

```json
{
    "id":1,
    "state": 1
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.1.3 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/register-model
##### 接口描述 发送模块注册信息帧

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |
| is_alarm          | Bool   | 可选      | 是否设置模块的alarm故障  |
| alarm_length      | Number | 可选      | 设置模块的alarm故障长度  |

```json
{
    "id":1,
    "is_alarm": 1,
    "alarm_length": 2
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.1.4 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/heart
##### 接口描述 发送心跳数据帧

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |
| interval          | Number | 可选      | 心跳间隔               |
| heartbeat_ctr     | Number | 可选      | 心跳计数               |

```json
{
    "id":1,
    "interval": 100,
    "heartbeat_ctr": 2
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.1.5 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/register-change
##### 接口描述 修改当前心跳数据

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |
| interval          | Number | 可选      | 心跳间隔               |
| heartbeat_ctr     | Number | 可选      | 心跳计数               |

```json
{
    "id":1,
    "heart_beat_period": 100,
    "heartbeat_ctr": 2
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.1.6 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/delete
##### 接口描述 删除主通讯线程

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |

```json
{
    "id":1
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

### 2.1 PMM主接触器通讯（Contactor Thread）

#### 2.2.1 接口地址 127.0.0.1:10001/v1/api/pmm-matrix/build
##### 接口描述 创建主接触器通讯线程

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |

```json
{
    "id":1
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```
#### 2.2.2 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/heart
##### 接口描述 发送心跳数据

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |
| interval          | Number | 可选      | 心跳间隔               |
| heartbeat_ctr     | Number | 可选      | 心跳计数               |
| alarm_state       | Array  | 可选      | 故障码                 |

```json
{
    "id":1,
    "interval": 200,
    "heartbeat_ctr":2,
    "alarm_state": [1, 2, 3]
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.2.3 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/rt
##### 接口描述 发送RT数据

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |

```json
{
    "id":1
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.2.4 接口地址 127.0.0.1:10001/api/v1/pmm-matrix/change-state
##### 接口描述 修改心跳数据

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |

```json
{
    "id":1
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.2.5 接口地址 127.0.0.1:10001/api/v1/pmm-contactor/change-rtstate
##### 接口描述 修改 RT消息参数

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| status            | bool   | 必选     | 充电线程的的状态，true时触发RT数据包 |
| id                | Number | 必选     | 充电线程的 id                        |
| push_cnt          | Number | 可选     | RT推送计数，默认当前计数             |
| interval          | Number | 可选     | RT的心跳间隔，默认当前心跳间隔       |

```json
{
    "status": true, 
    "id":1,
    "push_cnt": 5,
    "interval":100
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```

#### 2.2.6 接口地址 127.0.0.1:10001/api/v1/pmm-contactor/delete
##### 接口描述 删除线程

##### 请求方法 POST
##### 请求参数

| 参数              | 类型   | 是否必要 | 描述                                 |
| ----------------- | ------ | -------- | ------------------------------------ |
| id                | Number | 必选      | 主线程id               |

```json
{
    "id":1
}
```
##### 返回参数

| 参数 | 类型   | 描述                                |
| ---- | ------ | ----------------------------------- |
| code | Number | 状态码（0 ：执行成功、1：执行失败） |
| msg  | String | 返回信息                            |

```json
{
    "code":0,
    "msg":"success"
}
```
