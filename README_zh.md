# Full Stack FastAPI 模板

<a href="https://github.com/fastapi/full-stack-fastapi-template/actions?query=workflow%3A%22Test+Docker+Compose%22" target="_blank"><img src="https://github.com/fastapi/full-stack-fastapi-template/workflows/Test%20Docker%20Compose/badge.svg" alt="Test Docker Compose"></a>
<a href="https://github.com/fastapi/full-stack-fastapi-template/actions?query=workflow%3A%22Test+Backend%22" target="_blank"><img src="https://github.com/fastapi/full-stack-fastapi-template/workflows/Test%20Backend/badge.svg" alt="Test Backend"></a>
<a href="https://coverage-badge.samuelcolvin.workers.dev/redirect/fastapi/full-stack-fastapi-template" target="_blank"><img src="https://coverage-badge.samuelcolvin.workers.dev/fastapi/full-stack-fastapi-template.svg" alt="Coverage"></a>

## 技术栈与特性

- ⚡ [**FastAPI**](https://fastapi.tiangolo.com) 用于 Python 后端 API。
  - 🧰 [SQLModel](https://sqlmodel.tiangolo.com) 用于 Python SQL 数据库交互（ORM）。
  - 🔍 [Pydantic](https://docs.pydantic.dev)，由 FastAPI 使用，用于数据验证和配置管理。
  - 💾 [PostgreSQL](https://www.postgresql.org) 作为 SQL 数据库。
- 🚀 [React](https://react.dev) 用于前端。
  - 💃 使用 TypeScript、hooks、[Vite](https://vitejs.dev) 以及现代前端技术栈的其他部分。
  - 🎨 [Tailwind CSS](https://tailwindcss.com) 和 [shadcn/ui](https://ui.shadcn.com) 用于前端组件。
  - 🤖 自动生成的前端客户端。
  - 🧪 [Playwright](https://playwright.dev) 用于端到端测试。
  - 🦇 支持暗黑模式。
- 🐋 [Docker Compose](https://www.docker.com) 用于开发和生产环境。
- 🔒 默认安全密码哈希。
- 🔑 JWT（JSON Web Token）认证。
- 📫 基于邮件的密码找回。
- 📬 [Mailcatcher](https://mailcatcher.me) 用于开发阶段的本地邮件测试。
- ✅ 使用 [Pytest](https://pytest.org) 进行测试。
- 📞 [Traefik](https://traefik.io) 作为反向代理/负载均衡器。
- 🚢 使用 Docker Compose 的部署说明，包括如何设置前端 Traefik 代理以处理自动 HTTPS 证书。
- 🏭 基于 GitHub Actions 的 CI（持续集成）和 CD（持续部署）。

### 仪表盘 - 登录

[![API docs](img/login.png)](https://github.com/fastapi/full-stack-fastapi-template)

### 仪表盘 - 管理员

[![API docs](img/dashboard.png)](https://github.com/fastapi/full-stack-fastapi-template)

### 仪表盘 - 事项

[![API docs](img/dashboard-items.png)](https://github.com/fastapi/full-stack-fastapi-template)

### 仪表盘 - 暗黑模式

[![API docs](img/dashboard-dark.png)](https://github.com/fastapi/full-stack-fastapi-template)

### 交互式 API 文档

[![API docs](img/docs.png)](https://github.com/fastapi/full-stack-fastapi-template)

## 如何使用

你可以直接 **fork 或 clone** 此仓库并按原样使用。

✨ 开箱即用。 ✨

### 如何使用私有仓库

如果你想要一个私有仓库，GitHub 不允许简单地 fork，因为它不允许更改 fork 的可见性。

但你可以执行以下操作：

- 创建一个新的 GitHub 仓库，例如 `my-full-stack`。
- 手动克隆此仓库，将名称设为你想要的项目名称，例如 `my-full-stack`：

```bash
git clone git@github.com:fastapi/full-stack-fastapi-template.git my-full-stack
```

- 进入新目录：

```bash
cd my-full-stack
```

- 将新的 origin 设置为你的新仓库地址，从 GitHub 界面复制，例如：

```bash
git remote set-url origin git@github.com:octocat/my-full-stack.git
```

- 将本仓库添加为另一个 "remote"，以便后续获取更新：

```bash
git remote add upstream git@github.com:fastapi/full-stack-fastapi-template.git
```

- 将代码推送到你的新仓库：

```bash
git push -u origin master
```

### 从原始模板更新

在克隆仓库并进行更改后，你可能希望从此原始模板获取最新更改。

- 确保你已将原始仓库添加为 remote，可以通过以下命令检查：

```bash
git remote -v

origin    git@github.com:octocat/my-full-stack.git (fetch)
origin    git@github.com:octocat/my-full-stack.git (push)
upstream    git@github.com:fastapi/full-stack-fastapi-template.git (fetch)
upstream    git@github.com:fastapi/full-stack-fastapi-template.git (push)
```

- 拉取最新更改但不合并：

```bash
git pull --no-commit upstream master
```

这将从此模板下载最新更改但不提交，这样你可以在提交前检查一切是否正常。

- 如果存在冲突，在编辑器中解决它们。

- 完成后，提交更改：

```bash
git merge --continue
```

### 配置

你可以在 `.env` 文件中更新配置以自定义你的设置。

在部署之前，请确保至少更改以下值：

- `SECRET_KEY`
- `FIRST_SUPERUSER_PASSWORD`
- `POSTGRES_PASSWORD`

你可以（并且应该）将这些通过密钥管理服务设置为环境变量。

更多详情请阅读 [deployment.md](./deployment.md) 文档。

### 生成密钥

`.env` 文件中的某些环境变量默认值为 `changethis`。

你必须将它们更改为密钥，要生成密钥，可以运行以下命令：

```bash
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

复制内容并将其用作密码/密钥。然后再次运行以生成另一个安全密钥。

## 如何使用 - 使用 Copier 的替代方式

此仓库还支持使用 [Copier](https://copier.readthedocs.io) 生成新项目。

它会复制所有文件，询问你配置问题，并根据你的回答更新 `.env` 文件。

### 安装 Copier

你可以通过以下命令安装 Copier：

```bash
pip install copier
```

或者更好的是，如果你有 [`pipx`](https://pipx.pypa.io/)，可以运行：

```bash
pipx install copier
```

**注意**：如果你有 `pipx`，安装 copier 是可选的，你可以直接运行它。

### 使用 Copier 生成项目

为你的新项目目录取一个名称，你将在下面使用它。例如，`my-awesome-project`。

进入将作为项目父目录的目录，然后使用你的项目名称运行命令：

```bash
copier copy https://github.com/fastapi/full-stack-fastapi-template my-awesome-project --trust
```

如果你有 `pipx` 但没有安装 `copier`，可以直接运行：

```bash
pipx run copier copy https://github.com/fastapi/full-stack-fastapi-template my-awesome-project --trust
```

**注意**：`--trust` 选项是必需的，以便能够执行更新 `.env` 文件的[创建后脚本](https://github.com/fastapi/full-stack-fastapi-template/blob/master/.copier/update_dotenv.py)。

### 输入变量

Copier 会询问你一些数据，你可能希望在生成项目之前准备好。

但别担心，你之后可以随时在 `.env` 文件中更新这些内容。

输入变量及其默认值（部分自动生成）如下：

- `project_name`：（默认：`"FastAPI Project"`）项目名称，显示给 API 用户（在 .env 中）。
- `stack_name`：（默认：`"fastapi-project"`）用于 Docker Compose 标签和项目名称的栈名称（无空格、无句点）（在 .env 中）。
- `secret_key`：（默认：`"changethis"`）项目的密钥，用于安全，存储在 .env 中，你可以使用上述方法生成。
- `first_superuser`：（默认：`"admin@example.com"`）第一个超级用户的邮箱（在 .env 中）。
- `first_superuser_password`：（默认：`"changethis"`）第一个超级用户的密码（在 .env 中）。
- `smtp_host`：（默认：""）用于发送邮件的 SMTP 服务器主机，你之后可以在 .env 中设置。
- `smtp_user`：（默认：""）用于发送邮件的 SMTP 服务器用户，你之后可以在 .env 中设置。
- `smtp_password`：（默认：""）用于发送邮件的 SMTP 服务器密码，你之后可以在 .env 中设置。
- `emails_from_email`：（默认：`"info@example.com"`）用于发送邮件的邮箱账户，你之后可以在 .env 中设置。
- `postgres_password`：（默认：`"changethis"`）PostgreSQL 数据库的密码，存储在 .env 中，你可以使用上述方法生成。
- `sentry_dsn`：（默认：""）Sentry 的 DSN，如果你在使用它，你之后可以在 .env 中设置。

## 后端开发

后端文档：[backend/README.md](./backend/README.md)。

## 前端开发

前端文档：[frontend/README.md](./frontend/README.md)。

## 部署

部署文档：[deployment.md](./deployment.md)。

## 开发

通用开发文档：[development.md](./development.md)。

这包括使用 Docker Compose、自定义本地域名、`.env` 配置等。

## 发布说明

请查看 [release-notes.md](./release-notes.md) 文件。

## 许可证

Full Stack FastAPI 模板基于 MIT 许可证条款授权。
