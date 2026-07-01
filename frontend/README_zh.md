# FastAPI 项目 - 前端

前端使用 [Vite](https://vitejs.dev/)、[React](https://reactjs.org/)、[TypeScript](https://www.typescriptlang.org/)、[TanStack Query](https://tanstack.com/query)、[TanStack Router](https://tanstack.com/router) 和 [Tailwind CSS](https://tailwindcss.com/) 构建。

## 环境要求

- [Bun](https://bun.sh/)（推荐）或 [Node.js](https://nodejs.org/)

## 快速开始

```bash
bun install
bun run dev
```

* 然后在浏览器中打开 http://localhost:5173/。

注意，这个实时开发服务器不是在 Docker 中运行的，而是用于本地开发，这也是推荐工作流。当前端开发完成后，可以构建前端 Docker 镜像并启动它，以便在类似生产环境的场景中测试。但每次改动都重新构建镜像，不如使用带实时重载的本地开发服务器高效。

查看 `package.json` 文件可以了解其他可用命令。

### 移除前端

如果你正在开发纯 API 应用，想移除前端，可以这样做：

* 删除 `./frontend` 目录。

* 在 `compose.yml` 文件中删除完整的 `frontend` 服务/部分。

* 在 `compose.override.yml` 文件中删除完整的 `frontend` 和 `playwright` 服务/部分。

完成后，应用就变成了无前端的纯 API 应用。

---

如果需要，也可以从以下位置移除 `FRONTEND` 环境变量：

* `.env`
* `./scripts/*.sh`

这只是清理工作；即使保留这些变量，也不会产生实际影响。

## 生成客户端

### 自动生成

* 激活后端虚拟环境。
* 在项目顶层目录运行脚本：

```bash
bash ./scripts/generate-client.sh
```

* 提交改动。

### 手动生成

* 启动 Docker Compose 服务栈。

* 从 `http://localhost/api/v1/openapi.json` 下载 OpenAPI JSON 文件，并将其复制为前端目录根路径下的新文件 `openapi.json`。

* 运行以下命令生成前端客户端：

```bash
bun run generate-client
```

* 提交改动。

注意，每次后端变更导致 OpenAPI schema 变化时，都应该重新执行这些步骤来更新前端客户端。

## 使用远程 API

如果想使用远程 API，可以将环境变量 `VITE_API_URL` 设置为远程 API 的 URL。例如，可以在 `frontend/.env` 文件中设置：

```env
VITE_API_URL=https://api.my-domain.example.com
```

之后运行前端时，它会使用这个 URL 作为 API 的基础 URL。

## 代码结构

前端代码结构如下：

* `frontend/src` - 主要前端代码。
* `frontend/src/assets` - 静态资源。
* `frontend/src/client` - 生成的 OpenAPI 客户端。
* `frontend/src/components` - 前端组件。
* `frontend/src/hooks` - 自定义 hooks。
* `frontend/src/routes` - 前端路由，其中包含页面。

## 使用 Playwright 进行端到端测试

前端包含基于 Playwright 的初始端到端测试。运行测试前，需要先启动 Docker Compose 服务栈。使用以下命令启动：

```bash
docker compose up -d --wait backend
```

然后可以用以下命令运行测试：

```bash
bunx playwright test
```

也可以使用 UI 模式运行测试，以便查看浏览器并与测试过程交互：

```bash
bunx playwright test --ui
```

如果要停止并移除 Docker Compose 服务栈，同时清理测试中创建的数据，使用：

```bash
docker compose down -v
```

要更新测试，请进入测试目录，按需修改现有测试文件或添加新测试。

更多关于编写和运行 Playwright 测试的信息，请参考官方 [Playwright 文档](https://playwright.dev/docs/intro)。
