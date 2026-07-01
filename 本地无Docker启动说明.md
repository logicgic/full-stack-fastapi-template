# 不使用 Docker 启动项目

本文说明如何在本机直接启动后端、前端和 PostgreSQL，不依赖 Docker。

## 1. 前置条件

本机需要先安装：

- Python 3.14
- uv
- PostgreSQL
- Bun

检查命令：

```powershell
python --version
uv --version
psql --version
bun --version
```

注意：后端 `backend/pyproject.toml` 要求 Python 版本为 `>=3.14,<4.0`。

## 2. 配置 `.env`

编辑项目根目录下的 `.env`。

本地开发常用配置：

```env
DOMAIN=localhost
FRONTEND_HOST=http://localhost:5173
ENVIRONMENT=local

PROJECT_NAME="Full Stack FastAPI Project"
STACK_NAME=full-stack-fastapi-project

BACKEND_CORS_ORIGINS="http://localhost,http://localhost:5173,https://localhost,https://localhost:5173"

SECRET_KEY=换成随机字符串
FIRST_SUPERUSER=admin@example.com
FIRST_SUPERUSER_PASSWORD=你的管理员密码

POSTGRES_SERVER=localhost
POSTGRES_PORT=5432
POSTGRES_DB=app
POSTGRES_USER=postgres
POSTGRES_PASSWORD=你的本机PostgreSQL密码

SMTP_HOST=
SMTP_USER=
SMTP_PASSWORD=
EMAILS_FROM_EMAIL=info@example.com
SMTP_TLS=True
SMTP_SSL=False
SMTP_PORT=587

SENTRY_DSN=

DOCKER_IMAGE_BACKEND=backend
DOCKER_IMAGE_FRONTEND=frontend
```

生成 `SECRET_KEY`：

```powershell
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

## 3. 创建 PostgreSQL 数据库

先确认能连接本机 PostgreSQL：

```powershell
psql -h localhost -p 5432 -U postgres -d postgres
```

如果能进入 `psql`，创建项目数据库：

```sql
CREATE DATABASE app;
```

如果 `.env` 中 `POSTGRES_USER` 不是 `postgres`，请使用你自己的数据库用户。

## 4. 初始化后端环境

进入后端目录：

```powershell
cd backend
```

安装依赖：

```powershell
uv sync
```

检查数据库连接：

```powershell
uv run python app/backend_pre_start.py
```

正常输出类似：

```text
INFO:__main__:Initializing service
INFO:__main__:Service finished initializing
```

执行数据库迁移：

```powershell
uv run alembic upgrade head
```

创建初始管理员用户：

```powershell
uv run python app/initial_data.py
```

## 5. 启动后端

在 `backend` 目录运行：

```powershell
uv run fastapi dev app/main.py
```

后端地址：

```text
http://localhost:8000
```

API 文档：

```text
http://localhost:8000/docs
```

## 6. 启动前端

新开一个终端，进入前端目录：

```powershell
cd frontend
```

安装依赖：

```powershell
bun install
```

启动前端：

```powershell
bun run dev
```

前端地址：

```text
http://localhost:5173
```

默认管理员账号来自 `.env`：

```env
FIRST_SUPERUSER=admin@example.com
FIRST_SUPERUSER_PASSWORD=你的管理员密码
```

## 7. 常见问题

### 数据库密码验证失败

错误类似：

```text
password authentication failed for user "postgres"
```

说明 `.env` 中的 `POSTGRES_USER` 或 `POSTGRES_PASSWORD` 和本机 PostgreSQL 不一致。

请先用下面命令验证：

```powershell
psql -h localhost -p 5432 -U postgres -d postgres
```

能登录后，再把同样的用户名和密码写入 `.env`。

### `alembic` 找不到配置

迁移命令必须在 `backend` 目录运行：

```powershell
cd backend
uv run alembic upgrade head
```

因为 `alembic.ini` 位于 `backend/alembic.ini`。

### 只访问 `/docs` 是否需要初始化数据库

只打开接口文档通常不需要迁移数据库。

但登录、用户、items 等接口会访问数据库，所以要完整使用系统，必须执行：

```powershell
uv run alembic upgrade head
uv run python app/initial_data.py
```

## 8. 完整命令汇总

后端：

```powershell
cd backend
uv sync
uv run python app/backend_pre_start.py
uv run alembic upgrade head
uv run python app/initial_data.py
uv run fastapi dev app/main.py
```

前端：

```powershell
cd frontend
bun install
bun run dev
```
