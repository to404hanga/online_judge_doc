# Online Judge API 文档

## 认证相关

### 登录

- **URL**: `/auth/login`
- **方法**: `POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | username | string | 是 | 用户名 |
  | password | string | 是 | 密码 |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | message | string | 仅登录成功时包含，值为 `login Success` |
  | error | string | 仅登录失败时包含，值为具体错误信息 |
- **响应头**：
  `X-JWT-Token`: 包含 Bearer Token，格式为 `Bearer <token>`，用于作为后续请求自动携带的 `Authorization` 头。
- **Cookie**:
  `X-JWT-Token`: 包含 Bearer Token，格式为 `Bearer <token>`，用于作为后续请求自动携带的 `Authorization` 头。

### 登出

- **URL**：`/auth/logout`
- **方法**：`POST`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | message | string | 仅登出成功时包含，值为 `logout success` |
  | error | string | 仅登出失败时包含，值为具体错误信息 |

### 获取用户信息

- **URL**：`/auth/info`
- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | username | string | 用户名 |
  | realname | string | 真实姓名 |
  | role | integer | 用户角色，0 为普通用户，1 为管理员 |
  | status | integer | 用户状态，0 为正常，1 为禁用 |

## OJ 控制台相关

所有的 OJ 控制台相关 API 都需要遵循以下规则：

1. 请求地址统一为 `/api/online-judge-controller`, 各个 API 具体的转发路径为 `cmd` 查询参数指定；
2. 在请求头中包含 `Authorization` 头，值为 `Bearer <token>`，其中 `<token>` 为登录时获取的 `X-JWT-Token` 的值。

### 上传题目测试用例

- **方法**：`POST`
- **请求体**：`form-data` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | file | file | 是 | 包含题目测试用例的压缩文件，文件类型为 `zip`，文件名任意，文件内容建议为编号从数字 0 开始的多个文件，每个编号拥有 `<编号>.in` 和 `<编号>.out` 两个文件，分别为题目输入和答案。 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | multipart/form-data | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UploadProblemTestcase` |
  | problem_id | integer | 是 | 指定要上传测试用例的题目 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 创建题目

- **方法**：`POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | title | string | 是 | 题目标题 |
  | description | string | 是 | 题目描述 |
  | time_limit | integer | 是 | 题目时间限制，单位为毫秒，取值范围为 [50, 30000] |
  | memory_limit | integer | 是 | 题目内存限制，单位为 MB，取值范围为 [128, 1024] |
  | visible | integer | 是 | 非比赛期间题目是否可见，0 为不可见，1 为可见 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | multipart/form-data | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Description-Hash | 题目描述的 `SHA256` 哈希值，`HEX` 字符串，字母为小写 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `CreateProblem` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 更新题目

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | problem_id | integer | 是 | 指定要更新的题目 ID |
  | title | string | 否 | 题目标题 |
  | description | string | 否 | 题目描述 |
  | status | integer | 否 | 题目状态，0 为未发布，1 为已发布，2 为已删除 |
  | time_limit | integer | 否 | 题目时间限制，单位为毫秒，取值范围为 [50, 30000] |
  | memory_limit | integer | 否 | 题目内存限制，单位为 MB，取值范围为 [128, 1024] |
  | visible | integer | 否 | 非比赛期间题目是否可见，0 为不可见，1 为可见 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | multipart/form-data | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Description-Hash | 题目描述的 `SHA256` 哈希值，`HEX` 字符串，字母为小写 | 仅当 `description` 字段被更新时必填 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UpdateProblem` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 获取题目列表

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetProblemList` |
  | page | integer | 是 | 分页页码，最小值为 1 |
  | page_size | integer | 是 | 分页每页题目数量，取值范围为 [10, 100] |
  | desc | boolean | 否 | 是否按 order_by 字段降序排序 |
  | order_by | string | 否 | 排序字段，可选值为 `id`, `created_at`, `updated_at`，默认值为 `id` |
  | title | string | 否 | 按题目标题全模糊匹配查询 |
  | status | integer | 否 | 按题目状态精准查询，可选值为 `0`, `1`, `2`，分别为未发布，已发布，已删除 |
  | visible | integer | 否 | 按非比赛期间题目是否可见精准查询，可选值为 `0`, `1`，分别为不可见，可见 |
  | time_limit | integer | 否 | 按题目时间限制精准查询，单位为毫秒 |
  | memory_limit | integer | 否 | 按题目内存限制精准查询，单位为 MB |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据 |
  | data.total | integer | 符合查询条件的总题目数量 |
  | data.list | array | 题目列表 |
  | data.list[i].id | integer | 题目 ID |
  | data.list[i].title | string | 题目标题 |
  | data.list[i].description | string | 题目描述 |
  | data.list[i].status | integer | 题目状态，0 为未发布，1 为已发布，2 为已删除 |
  | data.list[i].time_limit | integer | 题目时间限制，单位为毫秒 |
  | data.list[i].memory_limit | integer | 题目内存限制，单位为 MB |
  | data.list[i].visible | integer | 非比赛期间题目是否可见，0 为不可见，1 为可见 |
  | data.list[i].creator_id | integer | 题目创建者 ID |
  | data.list[i].updater_id | integer | 题目更新者 ID |
  | data.list[i].created_at | string | 题目创建时间，格式为带毫秒的 `RFC3339` |
  | data.list[i].updated_at | string | 题目更新时间，格式为带毫秒的 `RFC3339` |

### 获取指定题目详情

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetProblem` |
  | problem_id | integer | 是 | 指定要查询的题目 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据 |
  | data.id | integer | 题目 ID |
  | data.title | string | 题目标题 |
  | data.description | string | 题目描述 |
  | data.status | integer | 题目状态，0 为未发布，1 为已发布，2 为已删除 |
  | data.time_limit | integer | 题目时间限制，单位为毫秒 |
  | data.memory_limit | integer | 题目内存限制，单位为 MB |
  | data.visible | integer | 非比赛期间题目是否可见，0 为不可见，1 为可见 |
  | data.creator_id | integer | 题目创建者 ID |
  | data.creator_realname | string | 题目创建者真实姓名 |
  | data.updater_id | integer | 题目更新者 ID |
  | data.updater_realname | string | 题目更新者真实姓名 |
  | data.created_at | string | 题目创建时间，格式为带毫秒的 `RFC3339` |
  | data.updated_at | string | 题目更新时间，格式为带毫秒的 `RFC3339` |

### 创建比赛

- **方法**：`POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | name | string | 是 | 比赛名称 |
  | start_time | string | 是 | 比赛开始时间，格式为带毫秒的 `RFC3339` |
  | end_time | string | 是 | 比赛结束时间，格式为带毫秒的 `RFC3339` |
  | problem_ids | array | 是 | 比赛包含的题目 ID 列表 |
  | problem_ids[i] | integer | 是 | 第 i 个题目 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `CreateCompetition` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 更新比赛

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | id | integer | 是 | 指定要更新的比赛 ID |
  | name | string | 否 | 比赛名称 |
  | start_time | string | 否 | 比赛开始时间，格式为带毫秒的 `RFC3339` |
  | end_time | string | 否 | 比赛结束时间，格式为带毫秒的 `RFC3339` |
  | status | integer | 否 | 比赛状态，0 为未发布，1 为已发布，2 为已删除 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UpdateCompetition` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 添加比赛题目

- **方法**：`POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要添加题目到的比赛 ID |
  | problem_ids | array | 是 | 要添加的题目 ID 列表 |
  | problem_ids[i] | integer | 是 | 第 i 个题目 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `AddCompetitionProblem` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 删除比赛题目

- **方法**：`DELETE`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要删除题目从的比赛 ID |
  | problem_ids | array | 是 | 要删除的题目 ID 列表 |
  | problem_ids[i] | integer | 是 | 第 i 个题目 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `RemoveCompetitionProblem` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 启用比赛题目

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要启用题目的比赛 ID |
  | problem_ids | array | 是 | 要启用的题目 ID 列表 |
  | problem_ids[i] | integer | 是 | 第 i 个题目 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `EnableCompetitionProblem` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 禁用比赛题目

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要禁用题目的比赛 ID |
  | problem_ids | array | 是 | 要禁用的题目 ID 列表 |
  | problem_ids[i] | integer | 是 | 第 i 个题目 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `DisableCompetitionProblem` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 开始比赛

- **方法**：`POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要开始的比赛 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `StartCompetition` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
- **响应头**：
  `X-Competition-JWT-Token`: 一个包含比赛相关信息的 JWT Token，用于后续请求验证，不包含 `Bearer` 前缀。
- **Cookie**:
  `X-Competition-JWT-Token`: 一个包含比赛相关信息的 JWT Token，用于后续请求验证，不包含 `Bearer` 前缀。

### 初始化比赛排行榜

- **方法**：`POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要初始化排行榜的比赛 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `InitRanking` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 获取比赛排行榜

- **方法**：`GET`
- **请求体**：无
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Competition-JWT-Token | 具体值为 [开始比赛](#开始比赛) 响应头中的 `X-Competition-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetCompetitionRankingList` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据 |
  | data.total | integer | 总数 |
  | data.page | integer | 当前页码 |
  | data.page_size | integer | 每页数量 |
  | data.list | array | 排行榜列表 |
  | data.list[i] | object | 第 i 个用户的排行榜信息 |
  | data.list[i].user_id | integer | 用户 ID |
  | data.list[i].username | string | 用户名（学号） |
  | data.list[i].realname | string | 真实姓名 |
  | data.list[i].total_accepted | integer | 总通过数 |
  | data.list[i].total_time_used | integer | 总耗时（单位：毫秒，距离比赛开始时间的偏移量） |
  | data.list[i].problems | array | 题目列表 |
  | data.list[i].problems[j] | object | 第 j 个题目信息 |
  | data.list[i].problems[j].problem_id | integer | 题目 ID |
  | data.list[i].problems[j].result | integer | 题目状态（`0` 表示未提交，`1` 表示尝试中，`2` 表示通过） |
  | data.list[i].problems[j].accepted_at | integer | 通过时间（单位：毫秒，距离比赛开始时间的偏移量） |
  | data.list[i].problems[j].retries | integer | 重试次数 |
  | data.list[i].problems[j].is_fastest | boolean | 是否为最快通过 |

### 获取比赛题目最快提交者列表

- **方法**：`GET`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | problem_ids | array | 是 | 指定要查询的题目 ID 列表 |
  | problem_ids[i] | integer | 是 | 第 i 个题目 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Competition-JWT-Token | 具体值为 [开始比赛](#开始比赛) 响应头中的 `X-Competition-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetCompetitionFastestSolverList` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据 |
  | data.total | integer | 总数 |
  | data.list | array | 最快提交者列表 |
  | data.list[i] | object | 第 i 个最快提交者信息 |
  | data.list[i].user_id | integer | 用户 ID |
  | data.list[i].problem_id | integer | 题目 ID |

### 导出比赛数据

- **方法**：`GET`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要导出数据的比赛 ID |
  | export_type | integer | 是 | 导出数据类型（`1` 表示 CSV 排行榜，`2` 表示 XLSX 排行榜，`3` 表示 CSV 比赛详情数据） |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `ExportCompetitionData` |
- **响应体**：
  - `json` 格式，仅在错误时返回
    | 参数 | 类型 | 描述 |
    | --- | --- | --- |
    | code | integer | 状态码 |
    | message | string | 状态描述 |
  - 文件格式，仅在成功时返回

### 获取用户列表

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetUserList` |
  | page | integer | 是 | 页码，最小值为 `1` |
  | page_size | integer | 是 | 每页数量，取值范围为 [1, 100]，默认为 `10` |
  | order_by | string | 否 | 排序字段，仅支持 `id`、`username`、`realname`，默认为 `id` |
  | desc | boolean | 否 | 是否降序排序，默认为 `false` |
  | username | string | 否 | 按学号查询，前缀匹配 |
  | realname | string | 否 | 按真实姓名查询，全模糊匹配 |
  | role | integer | 否 | 按角色查询，`0` 表示普通用户，`1` 表示管理员 |
  | status | integer | 否 | 按状态查询，`0` 表示正常，`1` 表示禁用 |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据 |
  | data.total | integer | 总数 |
  | data.page | integer | 当前页码 |
  | data.page_size | integer | 每页数量 |
  | data.list | array | 用户列表 |
  | data.list[i] | object | 第 i 个用户信息 |
  | data.list[i].id | integer | 用户 ID |
  | data.list[i].username | string | 学号 |
  | data.list[i].realname | string | 真实姓名 |
  | data.list[i].role | integer | 角色（`0` 表示普通用户，`1` 表示管理员） |
  | data.list[i].status | integer | 状态（`0` 表示正常，`1` 表示禁用） |
  | data.list[i].created_at | string | 创建时间, RFC3339 格式 |
  | data.list[i].updated_at | string | 更新时间, RFC3339 格式 |

### 将用户添加至参赛名单

- **方法**：`POST`
- **请求体**：
  - `json` 格式
    | 参数 | 类型 | 是否必填 | 描述 |
    | --- | --- | --- | --- |
    | user_id_list | array | 是 | 指定要添加至参赛名单的用户 ID 列表 |
    | user_id_list[i] | integer | 是 | 第 i 个用户 ID |
  - `file` 格式
    | 参数 | 类型 | 是否必填 | 描述 |
    | --- | --- | --- | --- |
    | file | binary | 是 | 包含用户学号列表的 CSV 文件，第一列应为用户学号，第一行应为表头 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `AddUsersToCompetition` |
  | competition_id | integer | 是 | 指定要添加用户的比赛 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据，仅在成功时返回 |
  | data.insert_success | integer | 成功添加的用户数量 |

### 允许用户参赛

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要允许用户参赛的比赛 ID |
  | user_id_list | array | 是 | 指定要允许参赛的用户 ID 列表 |
  | user_id_list[i] | integer | 是 | 第 i 个用户 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `EnableUsersInCompetition` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 禁用用户参赛

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | competition_id | integer | 是 | 指定要禁用用户参赛的比赛 ID |
  | user_id_list | array | 是 | 指定要禁用参赛的用户 ID 列表 |
  | user_id_list[i] | integer | 是 | 第 i 个用户 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `DisableUsersInCompetition` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 提交比赛题目代码

- **方法**：`POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | code | string | 是 | 提交的代码 |
  | language | integer | 是 | 提交的代码的编程语言, 0: C, 1: C++, 2: Python, 3: Java, 4: Go |
  | problem_id | integer | 是 | 提交的题目 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Competition-JWT-Token | 具体值为 [开始比赛](#开始比赛) 响应头中的 `X-Competition-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `SubmitCompetitionProblem` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 获取指定题目最晚的提交记录

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Content-Type | application/json | 是 |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Competition-JWT-Token | 具体值为 [开始比赛](#开始比赛) 响应头中的 `X-Competition-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetLatestSubmission` |
  | problem_id | integer | 是 | 指定要获取最晚提交记录的题目 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据，仅在成功时返回 |
  | data.id | integer | 最晚提交记录的 ID |
  | data.code | string | 最晚提交记录的代码 |
  | data.stderr | string | 最晚提交记录的标准错误输出 |
  | data.language | integer | 最晚提交记录的编程语言, 0: C, 1: C++, 2: Python, 3: Java, 4: Go |
  | data.status | integer | 最晚提交记录的状态, 0: 待判题, 1: 判题中, 2: 已判题 |
  | data.result | integer | 最晚提交记录的结果, 0: 未判题, 1: Accepted, 2: Wrong Answer, 3: Compile Error, 4: Runtime Error, 5: Time Limit Exceeded, 6: Memory Limit Exceeded, 7: Output Limit Exceeded |
  | data.time_used | integer | 最晚提交记录的运行时间, 单位为毫秒 |
  | data.memory_used | integer | 最晚提交记录的内存使用, 单位为 MB |
  | data.created_at | string | 最晚提交记录的提交时间，RFC3339 格式 |

### 获取比赛列表 - 管理员用

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetCompetitionList` |
  | page | integer | 是 | 指定要获取的页码，最小值为 1 |
  | page_size | integer | 是 | 指定每页返回的比赛数量，最小值为 10，最大值为 100 |
  | desc | boolean | 否 | 是否按创建时间降序排序，默认值为 `false` |
  | order_by | string | 否 | 指定排序字段，默认值为 `id`, 可选值为 `id`, `start_time`, `end_time`, `created_at`, `updated_at` |
  | status | integer | 否 | 指定查询的比赛状态，0 为未发布，1 为已发布，2 为已删除 |
  | phase | integer | 否 | 指定查询的比赛阶段，0 为未开始，1 为进行中，2 为已结束 |
  | name | string | 否 | 比赛名称，模糊查询 |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据，仅在成功时返回 |
  | data.total | integer | 总数据量 |
  | data.page | integer | 当前页码 |
  | data.page_size | integer | 当前页最大数据量 |
  | data.list | array | 比赛列表 |
  | data.list[i] | object | 比赛列表中的第 i 个比赛 |
  | data.list[i].id | integer | 比赛 ID |
  | data.list[i].name | string | 比赛名称 |
  | data.list[i].status | integer | 比赛状态, 0: 未发布, 1: 已发布, 2: 已删除 |
  | data.list[i].start_time | string | 比赛开始时间，RFC3339 格式 |
  | data.list[i].end_time | string | 比赛结束时间，RFC3339 格式 |
  | data.list[i].creator_id | integer | 比赛创建者 ID |
  | data.list[i].updater_id | integer | 比赛更新者 ID |
  | data.list[i].created_at | string | 比赛创建时间，RFC3339 格式 |
  | data.list[i].updated_at | string | 比赛更新时间，RFC3339 格式 |

### 获取比赛列表 - 普通用户用

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UserGetCompetitionList` |
  | page | integer | 是 | 指定要获取的页码，最小值为 1 |
  | page_size | integer | 是 | 指定每页返回的比赛数量，最小值为 10，最大值为 100 |
  | desc | boolean | 否 | 是否按创建时间降序排序，默认值为 `false` |
  | order_by | string | 否 | 指定排序字段，默认值为 `id`, 可选值为 `id`, `start_time`, `end_time` |
  | phase | integer | 否 | 指定查询的比赛阶段，0 为未开始，1 为进行中，2 为已结束 |
  | name | string | 否 | 比赛名称，模糊查询 |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据，仅在成功时返回 |
  | data.total | integer | 总数据量 |
  | data.page | integer | 当前页码 |
  | data.page_size | integer | 当前页最大数据量 |
  | data.list | array | 比赛列表 |
  | data.list[i] | object | 比赛列表中的第 i 个比赛 |
  | data.list[i].id | integer | 比赛 ID |
  | data.list[i].name | string | 比赛名称 |
  | data.list[i].status | integer | 比赛状态, 0: 未发布, 1: 已发布, 2: 已删除 |
  | data.list[i].start_time | string | 比赛开始时间，RFC3339 格式 |
  | data.list[i].end_time | string | 比赛结束时间，RFC3339 格式 |
  | data.list[i].creator_id | integer | 比赛创建者 ID |
  | data.list[i].updater_id | integer | 比赛更新者 ID |
  | data.list[i].created_at | string | 比赛创建时间，RFC3339 格式 |
  | data.list[i].updated_at | string | 比赛更新时间，RFC3339 格式 |

### 获取比赛题目列表 - 管理员用

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetCompetitionProblemList` |
  | competition_id | integer | 是 | 指定要获取的比赛 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | array | 返回数据，仅在成功时返回 |
  | data[i] | object | 比赛题目列表中的第 i 个题目 |
  | data[i].id | integer | 比赛题目 ID |
  | data[i].competition_id | integer | 比赛 ID |
  | data[i].problem_id | integer | 题目 ID |
  | data[i].problem_title | string | 题目标题 |
  | data[i].status | integer | 比赛题目状态, 0: 禁用, 1: 启用 |
  | data[i].created_at | string | 比赛题目创建时间，RFC3339 格式 |
  | data[i].updated_at | string | 比赛题目更新时间，RFC3339 格式 |

### 获取比赛详情 - 管理员用

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetCompetition` |
  | competition_id | integer | 是 | 指定要获取的比赛 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 返回数据，仅在成功时返回 |
  | data.id | integer | 比赛 ID |
  | data.name | string | 比赛名称 |
  | data.status | integer | 比赛状态, 0: 未发布, 1: 已发布, 2: 已删除 |
  | data.start_time | string | 比赛开始时间，RFC3339 格式 |
  | data.end_time | string | 比赛结束时间，RFC3339 格式 |
  | data.creator_id | integer | 比赛创建者 ID |
  | data.updater_id | integer | 比赛更新者 ID |
  | data.created_at | string | 比赛创建时间，RFC3339 格式 |
  | data.updated_at | string | 比赛更新时间，RFC3339 格式 |
  | data.creator_realname | string | 比赛创建者真实姓名 |
  | data.updater_realname | string | 比赛更新者真实姓名 |

### 删除用户

- **方法**：`DELETE`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | user_id | integer | 是 | 指定要删除的用户 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `DeleteUser` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 更新用户

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | user_id | integer | 是 | 指定要更新的用户 ID |
  | realname | string | 否 | 用户真实姓名 |
  | status | integer | 否 | 用户状态, 0: 正常, 1: 禁用 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UpdateUser` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 重置用户密码

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | user_id | integer | 是 | 指定要更新的用户 ID |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `ResetPassword` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 更新用户密码

- **方法**：`PUT`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | user_id | integer | 是 | 指定要更新的用户 ID |
  | old_password | string | 是 | 用户旧密码 |
  | new_password | string | 是 | 用户新密码 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UpdatePassword` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 获取参赛用户列表

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `GetCompetitionUserList` |
  | competition_id | integer | 是 | 指定要获取参赛用户列表的比赛 ID |
  | order_by | string | 否 | 排序字段, 可选值为 `id`, `username`, `realname`, 默认为 `id` |
  | desc | boolean | 否 | 是否降序排序, 默认为 `false` |
  | username | string | 否 | 筛选用户名包含指定字符串的用户, 前缀匹配查询 |
  | realname | string | 否 | 筛选用户真实姓名包含指定字符串的用户, 模糊查询 |
  | status | integer | 否 | 筛选用户状态, 0: 正常, 1: 禁用 |
  | page | integer | 是 | 分页页码, 最小值为 `1` |
  | page_size | integer | 否 | 分页每页数量, 最小值为 `10`, 最大值为 `100`, 默认为 `10` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 响应数据 |
  | data.total | integer | 符合条件的总用户数 |
  | data.page | integer | 当前分页页码 |
  | data.page_size | integer | 当前分页每页数量 |
  | data.list | array | 参赛用户列表 |
  | data.list[i] | object | 参赛用户信息 |
  | data.list[i].id | integer | 参赛 ID |
  | data.list[i].competition_id | integer | 比赛 ID |
  | data.list[i].user_id | integer | 用户 ID |
  | data.list[i].username | string | 用户名 |
  | data.list[i].realname | string | 用户真实姓名 |
  | data.list[i].status | integer | 参赛状态, 0: 正常, 1: 禁用 |

### 创建用户

- **方法**：`POST`
- **请求体**：`json` 格式
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | username | string | 是 | 用户名 |
  | realname | string | 是 | 用户真实姓名 |
  | role | integer | 是 | 用户角色, 0: 普通用户, 1: 管理员 |
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `CreateUser` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |

### 用户获取比赛题目列表

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Competition-JWT-Token | 具体值为 [开始比赛](#开始比赛) 响应头中的 `X-Competition-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UserGetCompetitionProblemList` |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | array | 比赛题目列表 |
  | data[i] | object | 比赛题目信息 |
  | data[i].competition_id | integer | 比赛 ID |
  | data[i].problem_id | integer | 题目 ID |
  | data[i].problem_title | string | 题目标题 |

### 用户获取比赛题目详情

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Competition-JWT-Token | 具体值为 [开始比赛](#开始比赛) 响应头中的 `X-Competition-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `UserGetCompetitionProblemDetail` |
  | problem_id | integer | 是 | 指定要获取详情的题目 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | object | 题目详情 |
  | data.id | integer | 题目 ID |
  | data.title | string | 题目标题 |
  | data.description | string | 题目描述 |
  | data.time_limit | integer | 题目时间限制, 单位为毫秒 |
  | data.memory_limit | integer | 题目内存限制, 单位为 MB |

### 检查用户是否通过某题目

- **方法**：`GET`
- **请求头**：
  | 键 | 值 | 是否必填 |
  | --- | --- | --- |
  | Authorization | Bearer \<token\>，其中的 \<token\> 为 [登录](#登录) 响应体中的 `X-JWT-Token` 值 | 是 |
  | X-Competition-JWT-Token | 具体值为 [开始比赛](#开始比赛) 响应头中的 `X-Competition-JWT-Token` 值 | 是 |
- **查询参数**：
  | 参数 | 类型 | 是否必填 | 描述 |
  | --- | --- | --- | --- |
  | cmd | string | 是 | 指定为 `CheckUserCompetitionProblemAccepted` |
  | problem_id | integer | 是 | 指定要检查的题目 ID |
- **响应体**：`json` 格式
  | 参数 | 类型 | 描述 |
  | --- | --- | --- |
  | code | integer | 状态码 |
  | message | string | 状态描述 |
  | data | boolean | 是否通过该题目 |
  