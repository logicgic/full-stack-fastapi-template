# FastAPI 项目 - 后端

## 环境要求

* [Docker](https://www.docker.com/)。
* [uv](https://docs.astral.sh/uv/)，用于 Python 包和环境管理。

## Docker Compose

按照 [../development.md](../development.md) 中的指南，使用 Docker Compose 启动本地开发环境。

## 常规工作流

默认情况下，依赖由 [uv](https://docs.astral.sh/uv/) 管理，请先安装 uv。

在 `./backend/` 目录中，可以通过以下命令安装全部依赖：

```console
$ uv sync
```

然后可以激活虚拟环境：

```console
$ source .venv/bin/activate
```

确保编辑器使用正确的 Python 虚拟环境，解释器路径为 `backend/.venv/bin/python`。

在 `./backend/app/models.py` 中修改或添加数据和 SQL 表对应的 SQLModel 模型，在 `./backend/app/api/` 中修改或添加 API 端点，在 `./backend/app/crud.py` 中修改或添加 CRUD（创建、读取、更新、删除）工具函数。

## VS Code

项目已经包含通过 VS Code 调试器运行后端的配置，可以使用断点、暂停执行、查看变量等功能。

项目也已经配置好，可以通过 VS Code 的 Python 测试面板运行测试。

## Docker Compose Override

开发期间，可以在 `compose.override.yml` 中修改只影响本地开发环境的 Docker Compose 设置。

这个文件中的改动只影响本地开发环境，不影响生产环境。因此，可以加入一些有助于开发流程的“临时”改动。

例如，后端代码目录会同步到 Docker 容器中，把你实时修改的代码复制到容器内目录。这样可以立即测试改动，不需要重新构建 Docker 镜像。这个方式只应该用于开发环境；生产环境应使用包含较新后端代码的 Docker 镜像。不过在开发期间，它可以让迭代非常快。

这里还覆盖了默认命令，使用 `fastapi run --reload` 代替默认的 `fastapi run`。它会启动单个服务进程（生产环境通常会启动多个），并在代码变化时重载进程。注意，如果保存的 Python 文件中存在语法错误，服务会报错退出，容器也会停止。修复错误后，可以重新运行：

```console
$ docker compose watch
```

文件中还有一个被注释掉的 `command` 覆盖项。可以取消它的注释，并注释掉默认项。它会让后端容器运行一个“什么也不做”的进程，但保持容器存活。这样你可以进入正在运行的容器并执行命令，例如启动 Python 解释器测试已安装依赖，或手动启动检测到变化后自动重载的开发服务器。

要进入容器中的 `bash` 会话，可以先启动服务栈：

```console
$ docker compose watch
```

然后在另一个终端中进入正在运行的容器：

```console
$ docker compose exec backend bash
```

你应该会看到类似输出：

```console
root@7f2607af31c3:/app#
```

这表示你已经以 `root` 用户进入容器内 `/app` 目录下的 `bash` 会话。这个目录下还有一个名为 `app` 的目录，你的代码在容器中的位置是 `/app/app`。

在那里可以使用 `fastapi run --reload` 命令运行带实时重载的调试服务器。

```console
$ fastapi run --reload app/main.py
```

实际看起来类似：

```console
root@7f2607af31c3:/app# fastapi run --reload app/main.py
```

然后按回车。该命令会启动实时重载服务器，在检测到代码变化时自动重载。

不过，如果检测到的不是正常代码变化，而是语法错误，服务会直接报错停止。由于容器仍然存活，并且你还在 Bash 会话中，修复错误后可以快速重新运行同一命令（按“上箭头”再按“Enter”）。

这也是让容器保持空运行、再在 Bash 会话中手动运行实时重载服务器的价值所在。

## 后端测试

运行后端测试：

```console
$ bash ./scripts/test.sh
```

测试使用 Pytest 运行，可以在 `./backend/tests/` 中修改或添加测试。

如果使用 GitHub Actions，测试会自动运行。

### 在已运行的服务栈中测试

如果服务栈已经启动，只想运行测试，可以使用：

```bash
docker compose exec backend bash scripts/tests-start.sh
```

`/app/scripts/tests-start.sh` 脚本会在确认其余服务栈正常运行后调用 `pytest`。如果需要向 `pytest` 传递额外参数，可以直接追加到该命令后，它们会被转发。

例如，遇到第一个错误就停止：

```bash
docker compose exec backend bash scripts/tests-start.sh -x
```

### 测试覆盖率

运行测试后会生成 `htmlcov/index.html` 文件，可以在浏览器中打开查看测试覆盖率。

## 数据库迁移

本地开发时，应用目录会作为卷挂载到容器中，因此也可以在容器内运行 `alembic` 命令。迁移代码会保存在你的应用目录中，而不是只存在于容器内，因此可以提交到 git 仓库。

每次修改模型后，都要为模型创建一个“revision”，并用这个 revision “upgrade” 数据库。这样才会更新数据库中的表，否则应用可能会报错。

* 进入后端容器的交互式会话：

```console
$ docker compose exec backend bash
```

* Alembic 已经配置好，会从 `./backend/app/models.py` 导入 SQLModel 模型。

* 修改模型后（例如新增一列），在容器内创建 revision，例如：

```console
$ alembic revision --autogenerate -m "Add column last_name to User model"
```

* 将 alembic 目录中生成的文件提交到 git 仓库。

* 创建 revision 后，在数据库中运行迁移（这一步会真正修改数据库）：

```console
$ alembic upgrade head
```

如果完全不想使用迁移，可以取消 `./backend/app/core/db.py` 中以下行的注释：

```python
SQLModel.metadata.create_all(engine)
```

并注释掉 `scripts/prestart.sh` 中包含以下内容的行：

```console
$ alembic upgrade head
```

如果不想从默认模型开始，而是想一开始就删除或修改它们，并且不保留任何历史 revision，可以删除 `./backend/app/alembic/versions/` 下的 revision 文件（`.py` Python 文件）。然后按上面的说明创建第一个迁移。

## 邮件模板

邮件模板位于 `./backend/app/email-templates/`。这里有两个目录：`build` 和 `src`。`src` 目录包含用于构建最终邮件模板的源文件，`build` 目录包含应用实际使用的最终邮件模板。

继续之前，请确保 VS Code 已安装 [MJML 扩展](https://github.com/mjmlio/vscode-mjml)。

安装 MJML 扩展后，可以在 `src` 目录中创建新的邮件模板。创建新模板并在编辑器中打开 `.mjml` 文件后，使用 `Ctrl+Shift+P` 打开命令面板，搜索 `MJML: Export to HTML`。这会把 `.mjml` 文件转换为 `.html` 文件，然后可以将其保存到 `build` 目录。
