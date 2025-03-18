# 系统架构
```mermaid
flowchart TB
    subgraph 客户端
        A[Web端 Vue3 + Element Plus] -->|HTTP API| D
        B[移动端小程序<br>Uniapp] -->|HTTP API| D
    end
subgraph 后端服务
    D[API 网关] --> E[微服务]
    E --> E1[用户服务]
    E --> E2[视频管理]
    E --> E3[推荐系统]
    E --> E4[互动服务]
    E --> E5[消息推送]

    E1 -->|MySQL/Redis| H
    E2 -->|MySQL/Elasticsearch| H
    E3 -->|Redis/Elasticsearch| H
    E4 -->|MySQL/Redis| H
    E5 -->|MQ| I

    D --> F[认证服务<br>JWT + OAuth2]
    F -->|用户登录鉴权| E1
    D --> G[Express 辅助服务]
end

subgraph 数据库 & 缓存
    H[MySQL]
    I[Redis]
    J[Elasticsearch]
    K["消息队列 (Kafka)"]
end

subgraph 基础设施
    L[阿里云 ECS] --> M[Docker 容器]
    N[HTTPS 证书] --> D
    O[ELK 日志系统] --> D
    P[Prometheus 监控] --> D
end

style D fill:#F0F4C3
style E fill:#C8E6C9
style I fill:#BBDEFB
classDef tech fill:#fff,stroke:#666;
class A,B,D,E,F,G,H,I,J,K,L,M,N,O,P tech
```

## 技术栈选择依据：

1. **服务端**：
   - **Eureka**：提供服务发现、负载均衡、熔断、配置管理等功能，支持微服务架构的高效构建。
   - **MySQL**：关系型数据库，适用于存储结构化数据，支撑用户数据和视频管理等业务。
   - **Redis**：关系型数据库，适用于存储结构化数据，支撑用户数据和视频管理等业务。
   - **Elasticsearch**：高效的搜索引擎，提升推荐系统的数据检索和分析能力。
   - **Kafka**：消息队列，适用于视频管理、消息推送等业务的解耦。
   - **Feign**：声明式 Web 服务客户端，简化微服务之间的通信。
2. **Web端**：
   - **Vue3**：现代化框架，具备响应式系统，开发效率高，适合构建动态单页应用。
   - **Element Plus**：企业级 UI 组件库，提供丰富的组件，帮助快速开发具有一致风格的界面。
   - **Vue Router**：强大的路由控制库，支持动态路由配置和嵌套路由功能。
   - **Pinia (状态管理)**：更轻量级、模块化的状态管理工具，适用于 Vue3，提升应用的可维护性。
3. **移动端** (Uniapp)：
   - **Uniapp**：跨平台框架，能够快速适配不同平台。
   - **Vue 语法**：通过 Uniapp 使用 Vue 语法，确保开发者能够在多平台间共享业务逻辑。
   - **Vuex**：状态管理工具，管理小程序的数据流和状态，确保跨平台的一致性。
4. **基础设施**：
   - **阿里云 ECS**：稳定的云服务器，支持弹性伸缩，保证系统的高可用性和扩展性。
   - **Docker 容器**：容器化技术，支持快速构建、部署和扩展微服务架构，提升开发与运维效率。
   - **ELK 日志系统**：集成 Elasticsearch、Logstash 和 Kibana，提供高效的日志采集、存储、分析和可视化功能。
   - **Prometheus 监控**：开源的系统监控和报警工具，适合微服务架构，能实时监控服务的健康状态和性能指标。

##  关键架构设计：

1. **小程序使用流程**：

```mermaid
---
config:
  look: handDrawn
  theme: neutral
  layout: elk
---
flowchart LR
    小程序端 -->|wx.login| 认证流程

    subgraph 认证流程
        direction TB
        注册页面 <-->|页面切换| 登陆页面
    end
 	认证流程 -->|登陆成功|页面分页
 	subgraph 页面分页
        direction TB
        首页 <-->|页面切换| 频道 <-->|页面切换| 分区 <-->|页面切换| 我的
    end
    首页 --> 首页功能
    subgraph 首页功能
        direction LR
        推荐
        热门
        动漫
        知识
        影视
        直播
        新闻
    end
    我的 --> 我的功能
     subgraph 我的功能
        direction LR
        昵称 
        头像
        粉丝
        关注
        获赞
        主页
        动态
        投稿
        收藏
        历史记录
    end


```

2. **性能优化措施**

   - **CDN 加速**：静态资源上传OSS，使用CDN加速访问
   - **分页加载**：视频列表分页、懒加载
   - **差分同步**：只传输变更数据，缓存热点数据
   - **数据库优化**：MySQL分库分表、Redis缓存高频数据
