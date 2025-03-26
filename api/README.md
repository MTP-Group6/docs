# API 设计

## RESTful API

本项目采用 OpenAPI 规范定义 RESTful API，详情参见 [openapi.yaml](https://github.com/MTP-Group6/docs/blob/main/api/openai.yaml)，API 文档可在线查看：[openapi.md](https://github.com/MTP-Group6/docs/blob/main/api/openapi.md)。

## 1. 资源关系

- **User 资源**：管理用户信息，包括用户注册、登录、登出。
- **Statistics 资源**：获取用户统计数据。
- **Video 资源**：查询视频观看者信息。
- **认证相关接口 (**`**/api/v1/auth/\***`**)**：支持用户身份验证与 Token 刷新。

## 2. API 端点概览

| 端点                              | 方法   | 描述               |
| --------------------------------- | ------ | ------------------ |
| `/api/v1/auth/captcha`            | `GET`  | 获取验证码         |
| `/api/v1/auth/login`              | `POST` | 用户登录           |
| `/api/v1/auth/logout`             | `POST` | 用户登出           |
| `/api/v1/auth/refresh`            | `POST` | 刷新 Token         |
| `/api/v1/auth/register`           | `POST` | 用户注册           |
| `/api/v1/statistics/users`        | `GET`  | 获取用户统计数据   |
| `/api/v1/users/me`                | `GET`  | 获取当前用户信息   |
| `/api/v1/users/user12345`         | `GET`  | 查询指定用户信息   |
| `/api/v1/videos/vid12345/viewers` | `GET`  | 获取视频观看者信息 |

## 3. 通用模式复用

- **复用** `**components**` **定义可复用的模型和响应格式**，保持 API 结构的一致性。
- **统一错误处理 (**`**Error**` **schema)**，确保错误响应格式标准化。

## 4. 安全认证

- **采用** `**JWT Bearer Token**` **进行身份认证**。
- **认证流程**：
  1. 通过 `/api/v1/auth/captcha` 获取验证码（如需）。
  2. 使用 `/api/v1/auth/register` 进行注册。
  3. 通过 `/api/v1/auth/login` 进行登录，获取 `JWT Token`。
  4. 访问受保护资源时，在 `Authorization` 头中携带 `Bearer Token`。
  5. 通过 `/api/v1/auth/refresh` 刷新 Token，保持登录状态。

## 5. 过滤与分页

- `/api/v1/statistics/users` **支持按条件筛选** 用户统计信息。
- 列表查询接口支持 **分页参数**，提高查询效率。

## 6. 符合 REST 规范

- **正确使用 HTTP 方法**：
  - `GET` 查询资源。
  - `POST` 创建资源。
  - `PUT` 更新资源。
  - `DELETE` 删除资源。
- **资源路径设计清晰**，采用 `/api/v1/` 作为前缀，确保版本控制。
- **使用** `**JSON**` **作为数据格式**，提高兼容性和可读性。

## 7. 工具

- **Swagger UI**：生成 API 可视化文档，方便开发者查看接口详情。
- **OpenAPI Generator**：可用于生成服务端/客户端代码，提高开发效率。
- **Postman**：支持 API 调试与测试，便于接口验证。
