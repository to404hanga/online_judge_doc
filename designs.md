# OnlineJudge 设计稿

## 模块划分

| 模块名称                          | 功能描述                                         |
| --------------------------------- | ------------------------------------------------ |
| 前端模块                          | 负责用户交互，展示题目、提交代码、查看结果等功能 |
| 统一网关模块                      | 负责接收前端请求并鉴权，路由到后端模块           |
| 后端 controller 模块              | 负责处理前端请求                                 |
| 后端 judger-master 模块           | 负责接收并分配判题请求                           |
| 后端 judger-worker 模块           | 负责从队列获取判题任务并分发到具体的评测机执行   |
| 后端 judger-result-collector 模块 | 负责接收评测机执行结果并写入数据库               |
| 后端 auto-scaler 模块             | 负责根据系统负载自动调整评测机数量               |

## 数据存储

### 题目数据

#### 题目元数据与题面

存储与 mysql 中

#### 题目测试用例

统一存储 onlinejudge_testcase_data 卷上, 形式如下:

```plaintext
<problem_id>/
    <testcase_id>.in
    <testcase_id>.out
```

#### 用户数据

存储与 mysql 中

#### 比赛数据

存储与 mysql 中

#### 提交数据

存储与 mysql 中

## 表结构设计

### 题目表 problem

| 字段名       | 类型                    | 描述                                         |
| ------------ | ----------------------- | -------------------------------------------- |
| id           | uint64: bigint unsigned | 题目 id ( 自增主键 )                         |
| title        | string: varchar(255)    | 题目标题                                     |
| description  | string: text            | 题目描述                                     |
| status       | \*int8: tinyint         | 题目状态 ( 0: 未发布, 1: 已发布, 2: 已删除 ) |
| visible      | \*int8: tinyint         | 非比赛时是否可见 ( 0: 不可见, 1: 可见 )      |
| time_limit   | int: int                | 时间限制 ( 单位: 毫秒 )                      |
| memory_limit | int: int                | 内存限制 ( 单位: MB )                        |
| creator_id   | uint64: bigint unsigned | 创建者 ID                                    |
| updater_id   | uint64: bigint unsigned | 更新者 ID                                    |
| created_at   | time.Time: datetime(3)  | 创建时间                                     |
| updated_at   | time.Time: datetime(3)  | 更新时间                                     |

### 用户表 user

| 字段名     | 类型                    | 描述                                |
| ---------- | ----------------------- | ----------------------------------- |
| id         | uint64: bigint unsigned | 用户 id ( 自增主键 )                |
| username   | string: varchar(50)     | 用户名 ( 学号 )                     |
| realname   | string: varchar(50)     | 真实姓名                            |
| password   | string: varchar(255)    | 密码 ( bcrypt，不参与序列化 )       |
| role       | \*int8: tinyint         | 用户角色 ( 0: 普通用户, 1: 管理员 ) |
| status     | \*int8: tinyint         | 用户状态 ( 0: 正常, 1: 禁用 )       |
| created_at | time.Time: datetime(3)  | 创建时间                            |
| updated_at | time.Time: datetime(3)  | 更新时间                            |

### 比赛表 competition

| 字段名     | 类型                    | 描述                                         |
| ---------- | ----------------------- | -------------------------------------------- |
| id         | uint64: bigint unsigned | 比赛 id ( 自增主键 )                         |
| name       | string: varchar(255)    | 比赛名称                                     |
| start_time | time.Time: datetime(3)  | 比赛开始时间                                 |
| end_time   | time.Time: datetime(3)  | 比赛结束时间                                 |
| status     | \*int8: tinyint         | 比赛状态 ( 0: 未发布, 1: 已发布, 2: 已删除 ) |
| creator_id | uint64: bigint unsigned | 创建者 ID                                    |
| updater_id | uint64: bigint unsigned | 更新者 ID                                    |
| created_at | time.Time: datetime(3)  | 创建时间                                     |
| updated_at | time.Time: datetime(3)  | 更新时间                                     |

### 比赛题目表 competition_problem

| 字段名         | 类型                    | 描述                              |
| -------------- | ----------------------- | --------------------------------- |
| id             | uint64: bigint unsigned | 比赛题目 id ( 自增主键 )          |
| competition_id | uint64: bigint unsigned | 比赛 id                           |
| problem_id     | uint64: bigint unsigned | 题目 id                           |
| problem_title  | string: varchar(255)    | 题目标题                          |
| status         | \*int8: tinyint         | 比赛题目状态 ( 0: 禁用, 1: 启用 ) |
| created_at     | time.Time: datetime(3)  | 创建时间                          |
| updated_at     | time.Time: datetime(3)  | 更新时间                          |

### 提交表 submission

| 字段名         | 类型                    | 描述                                                                                                                                                                 |
| -------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id             | uint64: bigint unsigned | 提交 id ( 自增主键 )                                                                                                                                                 |
| competition_id | uint64: bigint unsigned | 比赛 id                                                                                                                                                              |
| user_id        | uint64: bigint unsigned | 用户 id                                                                                                                                                              |
| problem_id     | uint64: bigint unsigned | 题目 id                                                                                                                                                              |
| code           | string: text            | 提交代码                                                                                                                                                             |
| stderr         | \*string: text          | 标准错误输出                                                                                                                                                         |
| language       | \*int8: tinyint         | 提交语言 ( 0: C/C++, 1: Python, 2: Java, 3: Go )                                                                                                                     |
| status         | \*int8: tinyint         | 提交状态 ( 0: 待判题, 1: 判题中, 2: 已判题 )                                                                                                                         |
| result         | \*int8: tinyint         | 判题结果 ( 0: 未判题, 1: Accepted, 2: Wrong Answer, 3: Compile Error, 4: Runtime Error, 5: Time Limit Exceeded, 6: Memory Limit Exceeded, 7: Output Limit Exceeded ) |
| time_used      | \*int: int              | 判题时间 ( 单位: 毫秒 )                                                                                                                                              |
| memory_used    | \*int: int              | 判题内存 ( 单位: KB )                                                                                                                                                |
| created_at     | time.Time: datetime(3)  | 创建时间                                                                                                                                                             |
| updated_at     | time.Time: datetime(3)  | 更新时间                                                                                                                                                             |

### 比赛用户表 competition_user

| 字段名         | 类型                    | 描述                      |
| -------------- | ----------------------- | ------------------------- |
| id             | uint64: bigint unsigned | 比赛用户 id ( 自增主键 )  |
| competition_id | uint64: bigint unsigned | 比赛 id                   |
| user_id        | uint64: bigint unsigned | 用户 id                   |
| username       | string: varchar(50)     | 用户名 (学号)             |
| realname       | string: varchar(50)     | 真实姓名                  |
| status         | \*int8: tinyint         | 状态 ( 0: 正常, 1: 禁用 ) |
| pass_count     | int: int                | 通过题目数                |
| total_time     | int64: bigint           | 总耗时 ( 单位: 毫秒 )     |
| retry_count    | int: int                | 重试次数                  |
| start_time     | time.Time: datetime(3)  | 比赛开始时间              |
