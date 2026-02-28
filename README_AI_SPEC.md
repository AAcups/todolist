项目：Docker 化前后端分离 TodoList（CI/CD 演示项目）
一、项目目标

构建一个最小可用的企业级 Demo，用于演示：

✔ 前后端分离架构
✔ Docker 容器化部署
✔ GitHub Actions CI/CD 自动构建与部署
✔ 用户认证（JWT）
✔ 基础业务功能（Todo CRUD）

二、功能需求
👤 用户系统
必须实现

用户注册

用户登录

JWT 鉴权

登录后访问受保护接口

📝 Todo 功能

登录后用户可以：

创建 Todo

查看自己的 Todo 列表

删除 Todo

三、技术约束（必须遵守）
🔧 后端

Node.js 20+

Express

PostgreSQL

JWT 鉴权

RESTful API

Docker 化运行

🎨 前端

Vue3 + Vite

Axios 调用 API

登录页 + Todo 页面

Docker + Nginx 部署

🐳 容器化

所有服务必须 Docker 化

使用 docker-compose 编排

支持一键启动

🔄 CI/CD

GitHub Actions

自动构建 Docker 镜像

推送到 GHCR

自动部署测试环境

四、系统架构
Browser
   ↓
Frontend (Vue + Nginx Container)
   ↓
Backend API (Node.js Container)
   ↓
PostgreSQL Container
五、项目目录规范（必须遵守）
todolist-ci-demo/
├── frontend/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── src/
├── backend/
│   ├── Dockerfile
│   ├── app.js
│   └── routes/
├── docker-compose.yml
└── .github/workflows/ci.yml
六、后端 API 规范
🔐 认证接口
注册
POST /api/register

请求：

{
  "username": "test",
  "password": "123456"
}
登录
POST /api/login

响应：

{
  "token": "JWT_TOKEN"
}
📝 Todo 接口（需要 JWT）
获取列表
GET /api/todos
Authorization: Bearer <token>
创建
POST /api/todos
Authorization: Bearer <token>
删除
DELETE /api/todos/:id
Authorization: Bearer <token>
七、数据库设计
users 表
id SERIAL PRIMARY KEY
username VARCHAR UNIQUE
password VARCHAR
todos 表
id SERIAL PRIMARY KEY
user_id INT
content TEXT
created_at TIMESTAMP
八、Docker 要求
后端容器

暴露端口：3000

环境变量支持：

DB_HOST

DB_USER

DB_PASS

DB_NAME

JWT_SECRET

前端容器

使用 Nginx 提供静态文件

支持反向代理 /api 到 backend

九、docker-compose 要求

必须包含：

frontend

backend

postgres

持久化 volume

支持：

docker compose up -d

即可完整运行系统。

十、CI/CD 要求（GitHub Actions）
CI 阶段

必须实现：

✔ 构建前端
✔ 构建后端
✔ 构建 Docker 镜像

CD 阶段

当 push 到 dev 分支：

✔ 推送镜像到 GHCR
✔ SSH 部署到测试服务器

十一、环境变量规范

需要支持 .env：

POSTGRES_USER=todo
POSTGRES_PASSWORD=todo
POSTGRES_DB=todo_db
JWT_SECRET=supersecret
十二、验收标准（AI 输出必须满足）

✔ docker compose up 能启动全部服务
✔ 能注册 & 登录
✔ 登录后能创建 Todo
✔ GitHub Actions 能成功构建镜像
✔ 镜像可在服务器部署

十三、任务拆分（可分配给多个 AI）
🤖 AI-1：后端开发

负责：

Express API

JWT 鉴权

PostgreSQL 操作

Dockerfile

🤖 AI-2：前端开发

负责：

登录页

Todo 页面

Axios 封装

Dockerfile + Nginx

🤖 AI-3：DevOps

负责：

docker-compose

GitHub Actions

部署脚本

环境变量管理

十四、推荐开发顺序

1️⃣ 后端 API 完成
2️⃣ 前端页面完成
3️⃣ docker-compose 跑通
4️⃣ CI/CD 接入

十五、可选增强（未来扩展）

HTTPS + Nginx

用户密码加密（bcrypt）

RBAC 权限模型

Kubernetes 部署

安全扫描（Trivy）

十六、你作为“指挥者”的使用方式

你可以把任务拆开分别给 AI：

👉 示例指令

👉 给后端 AI：

根据 README_AI_SPEC.md，实现 backend 服务

👉 给前端 AI：

根据 API 规范，实现 Vue 前端

👉 给 DevOps AI：

编写 docker-compose 和 GitHub Actions

十七、我可以继续担任你的架构顾问

下一步我可以帮你：

✔ 审查 AI 生成的代码
✔ 优化 CI/CD 流水线
✔ 加入安全扫描（非常适合你职业方向）
✔ 模拟企业级发布流程