# 泛娱乐与知识社区

- **基础信息**
  
    **Base URL**: 
    
    开发环境: http://localhost:9090/api/v1
    
    测试环境: http://test-api.easybili.app/api/v1
    
    生产环境: http://easybili.app/api/v1
    
    **API Version**: v1
    
    **API Style**: RESTful API
    
    **Content Type**: 
    
    request content type: application/json
    
    response content type: application/json
    
    uploaded file type: multipart/form-data
    
    **Authentication**: 
    
    用户登录获得 access token, 客户端在后续的请求头中使用 `Authorization: Bearer {access_token}`, token的有效期为24小时, 过期后需要重新登录或用自动登录接口刷新
    
    **Name Style**: 路径参数使用小写+连字符 (kebab-case), 查询参数、请求体和响应体中的参数使用小驼峰 (camelCase)
    
- **一般性规则**
    - **请求格式**:
      
        GET请求: 使用查询参数
        
        POST/PUT/PATCH请求: 参数以`application/json`、`multipart/form-data`使用请求体传递
        
        DELETE请求: 使用查询参数
        
    - **响应格式**:
      
        所有响应体都类似于
        
        ```json
        {
          "code": 0,           // 业务状态码
          "message": "success", // 状态描述
          "data": {}           // 响应数据，可能是对象、数组或null
        }
        ```
        
    - **状态码**:
      
        HTTP状态码: 
        
        | 状态码 | 短语 | 描述 |
        | --- | --- | --- |
        | 200 | Success | 成功响应 |
        | 201 | Created | 成功创建资源 |
        | 400 | Bad Request | 错误的请求 |
        | 401 | Unauthorized | 未授权 |
        | 403 | Forbidden | 禁止访问 |
        | 404 | Not Found | 资源不存在 |
        | 500 | Internal Server Error | 服务器出错 |
        
        业务状态码: 
        
        | 状态码 | 描述 |
        | --- | --- |
        | 0 | 成功 |
        | 10001 | 参数错误 |
        | 10002 | 资源不存在 |
        | 20001 | 用户未登录 |
        | 20002 | 权限不足 |
        | 30001 | 服务器内部错误 |
        | 40001 | 验证码错误 |
        | 40002 | 账号或密码错误 |
        | 40003 | 邮箱已注册 |
        | 40100 | 令牌无效或过期 |
        | 50001 | 视频上传失败 |
        | 50002 | 视频审核未通过 |
    - **请求参数**:
      
        Pagination 分页
        
        | 名称 | 类型 | 描述 | 默认值 | 示例 |
        | --- | --- | --- | --- | --- |
        | pageNo | Integer | 页码,从1开始 | 1 | pageNo=2 |
        | pageSize | Integer | 每页条数 | 20 | pageSize=10 |
        
        Sorting 排序
        
        | sortField | String | 排序字段 | createTime | sortField=viewCount |
        | --- | --- | --- | --- | --- |
        | sortOrder | String | 升序降序 | desc | sortOrder=asc |
        
        Filtering 过滤
        
        | categoryId | Integer | 按分类id筛选 |  | categoryId=2 |
        | --- | --- | --- | --- | --- |
        | keyword | String | 关键词搜索 |  | keyword=教程 |
        | status | String | 资源状态筛选 |  | status=published |
        | createTimeStart | String | 创建时间起始 |  | createTimeStart=25-01-01 |
        | createTimeEnd | String | 创建时间结束 |  | createTimeEnd=25-12-31 |
        
        分页接口的响应的data字段包含一下格式 (举例)
        
        ```json
        {
          "code": 0,
          "message": "success",
          "data": {
            "total": 100,        // 总记录数
            "pages": 5,          // 总页数
            "pageNo": 1,         // 当前页码
            "pageSize": 20,      // 每页大小
            "list": []           // 数据列表
          }
        }
        ```
        

**前台功能**

- **账户管理**
    - [ ]  第三方授权登录
    - [ ]  扫码登录
    - [ ]  邮箱验证码登录
    - 获取验证码
        - URL: `/api/v1/auth/captcha`
        - 方法: `GET`
        - 内容类型: `application/json`
        - 权限: 公开接口
        - 请求参数
          
          
            | 参数名 | 类型 | 描述 |
            | --- | --- | --- |
            | captchaKey | String | 验证码会话标识，后续请求需要携带此标识 |
            | captchaImage | String | Base64编码的验证码图片 |
        - 示例1-请求报文
          
            ```json
            GET /api/v1/auth/captcha HTTP/1.1
            Host: test-api.easylive.app
            ```
            
        - 示例2-响应体 200 OK
          
            ```json
            {
              "code": 0,
              "message": "success",
              "data": {
                "captchaKey": "abc123xyz789",
                "captchaImage": "data:image/png;base64,iVBORw0KGg..."
              }
            }
            ```
        
    - 注册
        - URL: `/api/v1/auth/register`
        - 方法: `POST`
        - 内容类型: `application/json`
        - 权限: 公开接口
        - 请求参数
          
          
            | 参数名 | 类型 | 必填 | 描述 |
            | --- | --- | --- | --- |
            | email | String | 是 | 用户邮箱，将用作登录账号 |
            | password | String | 是 | 用户密码，6-20位字符 |
            | nickname | String | 是 | 用户昵称，2-20位字符 |
            | captchaKey | String | 是 | 验证码会话标识，由获取验证码接口返回 |
            | captchaCode | String | 是 | 用户输入的验证码 |
        - 示例1-请求报文
          
            ```json
            POST /api/v1/auth/register HTTP/1.1
            Host: test-api.easylive.app
            Content-Type: application/json
            
            {
              "email": "user@example.com",
              "password": "yourpassword",
              "nickname": "新用户",
              "captchaKey": "abc123xyz789",
              "captchaCode": "7a3b"
            }
            ```
            
        - 示例2-响应体 201 Created
          
            ```json
            {
              "code": 0,
              "message": "注册成功",
              "data": {
                "userId": "user12345",
                "email": "user@example.com",
                "nickname": "新用户",
                "avatar": "https://easylive.app/avatars/default.png",
                "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
                "expiresIn": 86400
              }
            }
            ```
            
        - 示例3-响应体 400 Bad Request 验证码错误
          
            ```json
            {
              "code": 40001,
              "message": "验证码错误",
              "data": null
            }
            ```
            
        - 示例3-响应体 400 Bad Request 邮箱已注册
          
            ```json
            {
              "code": 40003,
              "message": "该邮箱已被注册",
              "data": null
            }
            ```
        
    - 登录
      
        URL: `/auth/login`
        
        HTTP方法: `POST`
        
        MIME: `application/json`
        
        权限: 完全开放
        
        - 请求参数
          
          
            | 参数名 | 类型 | 必填 | 描述 |
            | --- | --- | --- | --- |
            | email | String | 是 | 用户邮箱 |
            | password | String | 是 | 用户密码 |
            | captchaKey | String | 是 | 验证码会话标识 |
            | captchaCode | String | 是 | 用户输入的验证码 |
        - 示例1-请求报文
          
            ```json
            POST /api/v1/auth/login HTTP/1.1
            Host: test-api.easylive.app
            Content-Type: application/json
            {
              "email": "user@example.com",
              "password": "yourpassword",
              "checkCodeKey": "abc123",
              "checkCode": "8f7d"
            }
            ```
            
        - 示例2-响应体 200 OK
          
            ```json
            {
              "code": 0,
              "message": "登录成功",
              "data": {
                "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
                "userId": "user12345",
                "nickname": "示例用户",
                "avatar": "https://example.com/avatars/default.png",
                "expiresIn": 86400
              }
            }
            ```
            
        - 示例3-响应体 400 Bad Request 验证码错误
          
            ```json
            {
              "code": 40001,
              "message": "验证码错误",
              "data": null
            }
            ```
            
        - 示例4-响应体 400 Bad Request 账号或密码错误
          
            ```json
            {
              "code": 40002,
              "message": "账号或密码错误",
              "data": null
            }
            ```
        
    - 自动登录
        - **URL**: `/api/v1/auth/refresh`
        - **方法**: `POST`
        - **内容类型**: `application/json`
        - **权限**: 需要有效的访问令牌
        - 示例1-请求报文
          
            ```json
            POST /api/v1/auth/refresh HTTP/1.1
            Host: test-api.easylive.app
            Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
            ```
            
        - 示例2-响应体 200 OK
          
            ```json
            {
              "code": 0,
              "message": "刷新成功",
              "data": {
                "userId": "user12345",
                "email": "user@example.com",
                "nickname": "示例用户",
                "avatar": "https://easylive.app/avatars/user12345.png",
                "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
                "expiresIn": 86400
              }
            }
            ```
            
        - 示例3-响应体 401 Unauthorized
          
            ```json
            {
              "code": 40100,
              "message": "令牌已过期，请重新登录",
              "data": null
            }
            ```
        
    - 退出
        - **URL**: `/api/v1/auth/logout`
        - **方法**: `POST`
        - **内容类型**: `application/json`
        - **权限**: 需要用户登录
        - 示例1-请求报文
          
            ```json
            POST /api/v1/auth/logout HTTP/1.1
            Host: test-api.easylive.app
            Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
            ```
            
        - 示例2-响应体 200 OK
          
            ```json
            {
              "code": 0,
              "message": "已成功退出登录",
              "data": null
            }
            ```
            
    
    ---
    
    - 更新用户信息
        - 请求参数
          
          
            | 参数名 | 类型 | 必填 | 描述 |
            | --- | --- | --- | --- |
            | nickname | String | 否 | 用户昵称，2-20位字符 |
            | avatar | String | 否 | 头像文件ID，需先上传头像文件获取 |
            | bio | String | 否 | 个人简介，最多200字符 |
            | gender | String | 否 | 性别，可选值：male(男)、female(女)、secret(保密) |
        - 示例1-请求报文
          
            ```json
            PUT /api/v1/users/me HTTP/1.1
            Host: test-api.easylive.app
            Content-Type: application/json
            Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
            
            {
              "nickname": "新昵称",
              "avatar": "avatar_file_id",
              "bio": "这是我的个人简介，热爱视频创作",
              "gender": "male"
            }
            ```
            
        - 示例2-响应体 200 OK
          
            ```json
            {
              "code": 0,
              "message": "个人资料已更新",
              "data": {
                "userId": "user12345",
                "nickname": "新昵称",
                "avatar": "https://easylive.app/avatars/avatar_file_id.png",
                "bio": "这是我的个人简介，热爱视频创作",
                "gender": "male",
                "updateTime": "2023-06-20T15:30:45Z"
              }
            }
            ```
            
        - 示例3-响应体 400 Bad Request
          
            ```json
            {
              "code": 10001,
              "message": "昵称长度应为2-20位字符",
              "data": null
            }
            ```
            
        - 错误响应 - 401 Unauthorized (未登录)
          
            ```json
            {
              "code": 40100,
              "message": "请先登录",
              "data": null
            }
            ```
        
    - 获取用户信息
        - **URL**: `/api/v1/users/{userId}`
        - **方法**: `GET`
        - **内容类型**: `application/json`
        - **权限**: 公开接口（获取自己的完整信息需要登录）
        - 请求参数
          
          
            | 参数名 | 类型 | 必填 | 描述 |
            | --- | --- | --- | --- |
            | userId | String | 是 | 用户ID，使用"me"表示当前登录用户 |
        - 示例1-请求报文
          
            ```json
            GET /api/v1/users/user12345 HTTP/1.1
            Host: test-api.easylive.app
            ```
            
            ```json
            GET /api/v1/users/me HTTP/1.1
            Host: test-api.easylive.app
            Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
            ```
            
        - 示例2-响应体 200 OK 公开的信息
          
            ```json
            {
              "code": 0,
              "message": "success",
              "data": {
                "userId": "user12345",
                "nickname": "视频达人",
                "avatar": "https://easylive.app/avatars/user12345.png",
                "bio": "热爱分享优质视频内容",
                "followersCount": 1250,
                "followingCount": 86,
                "videoCount": 32,
                "createTime": "2022-06-15T10:30:00Z"
              }
            }
            ```
            
        - 示例3-响应体 200 OK 用户需要登录获取的信息
          
            ```json
            {
              "code": 0,
              "message": "success",
              "data": {
                "userId": "user12345",
                "email": "user@example.com",
                "nickname": "视频达人",
                "avatar": "https://easylive.app/avatars/user12345.png",
                "bio": "热爱分享优质视频内容",
                "followersCount": 1250,
                "followingCount": 86,
                "videoCount": 32,
                "likeCount": 156,
                "collectionCount": 48,
                "theme": "dark",
                "createTime": "2022-06-15T10:30:00Z",
                "lastLoginTime": "2023-06-20T14:22:10Z"
              }
            }
            ```
            
        - 示例4-响应体 404 Not Found
          
            ```json
            {
              "code": 10002,
              "message": "用户不存在",
              "data": null
            }
            ```
        
    - [ ]  个性化装扮 (保存主题)
- **视频内容**
    - 获取用户量
        - **URL**: `/api/v1/statistics/users`
        - **方法**: `GET`
        - **内容类型**: `application/json`
        - **权限**: 公开接口
        - 示例1-请求报文
          
            ```json
            GET /api/v1/statistics/users HTTP/1.1
            Host: test-api.easylive.app
            ```
            
        - 示例2-响应体 200 OK
          
            ```json
            {
              "code": 0,
              "message": "success",
              "data": {
                "totalUsers": 25680,
                "newUsersToday": 156,
                "activeUsersToday": 3254,
                "creatorCount": 1862
              }
            }
            ```
        
    - 获取在线观看人数
        - **URL**: `/api/v1/videos/{videoId}/viewers`
        - **方法**: `GET`
        - **内容类型**: `application/json`
        - **权限**: 公开接口
        - 请求参数
          
          
            | 参数名 | 类型 | 必填 | 描述 |
            | --- | --- | --- | --- |
            | videoId | String | 是 | 视频ID |
        - 示例1-请求报文
          
            ```json
            GET /api/v1/videos/vid12345/viewers HTTP/1.1
            Host: test-api.easylive.app
            ```
            
        - 示例2-响应体 200 OK
          
            ```json
            {
              "code": 0,
              "message": "success",
              "data": {
                "onlineViewers": 1256,
                "peakViewers": 2450,
                "updateTime": "2023-06-20T16:45:30Z"
              }
            }
            ```
            
        - 示例3-响应体 400 Bad Request
          
            ```json
            {
              "code": 10002,
              "message": "视频不存在",
              "data": null
            }
            ```
        
    - 获取视频列表
    - 获取推荐视频
    - 获取热门视频
    - 获取视频详情
    - 获取视频分P
    - 获取文件.ts
    - 获取文件.m3u8
    
    ---
    
    - 文件预上传
    - 上传视频
    - 上传图片
    - 发布视频
    - 删除视频
    
    ---
    
    加载视频系列
    
    获取视频系列
    
    修改系列顺序
    
    系列详情
    
    删除系列
    
    保存系列
    
    保存系列视频
    
    删除系列视频
    
    获取所有系列列表
    
- **创作者中心**
    - 创作中心视频列表
    - 创作中心视频数量
    - 创作中心获取视频详情
    - 创作中心保存设置
    - 创作中心删除视频
- **互动**
    - 发布评论
    - 获取评论列表
    - 评论置顶
    - 取消评论置顶
    - 删除评论
    
    ---
    
    - 发布弹幕
    - 获取弹幕列表
    
    ---
    
    用户行为 (点赞、收藏和投币)
    
- **订阅**
    - 关注用户
    - 取消关注
    - 关注列表
    - 粉丝列表
- **个人中心**
  
    收藏列表
    
    ---
    
    - 加载播放历史
    - 删除历史记录
    - 清除全部历史
- **消息通知**
    - 未读消息数
    - 用户消息列表
    - 删除消息
    - 分组消息数
    - 标记消息已读
- **分类**
    - 获取所有分类
    - 获取系统设置

**后台功能**

- **账户管理**
    - 管理员验证码
    - 管理员登录
    - 管理员退出登录
- **分类管理**
    - 获取分类
    - 保存分类
    - 删除分类
    - 分类排序
- **内容管理**
    - 视频列表
    - 审核视频
    - 删除视频
    - 推荐视频
- **互动管理**
    - 弹幕列表
    - 删除弹幕
    - 获取评论
    - 删除评论
- **用户管理**
    - 用户列表
    - 修改用户状态
- **系统设置**
    - 获取系统设置
    - 保存系统设置
- **数据统计**
    - 统计总数
    - 按周统计
- **资源管理**
    - 上传文件
    - 获取文件
    - 获取文件资源
    - 获取TS文件