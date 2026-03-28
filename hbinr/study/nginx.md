toolName: search_codebase
            
status: success
          
query: nginx configuration and usage in docker-compose, makefile, or scripts
          
在这个项目中，**Nginx 扮演着非常核心的“反向代理（Reverse Proxy）”和“统一入口”的角色**。

### 1. Nginx 在项目里具体在做什么？
通过分析项目配置（如 `docker/nginx/nginx.conf`），Nginx 主要负责以下几件事：
* **统一访问入口**：整个项目有多个服务（前端 Next.js 运行在 3000 端口，网关 API FastAPI 运行在 8001 端口，LangGraph 运行在 2024 端口）。Nginx 监听 **2026 端口**，根据你访问的路径（URL）将请求自动分发给对应的底层服务。你只需要访问 `http://localhost:2026` 就能使用完整功能。
* **集中处理跨域（CORS）**：Nginx 会统一在响应头中添加跨域允许头（`Access-Control-Allow-Origin` 等），并处理预检请求（OPTIONS），这样前端请求后端接口时就不会报跨域错误。

---

### 2. 可以用 Docker 安装 Nginx 吗？必须本地安装吗？

**完全可以用 Docker！实际上，项目官方最推荐的就是 Docker 方式，不需要你在本地（宿主机）安装 Nginx。**

根据项目的 `CONTRIBUTING.md` 和架构设计，项目提供了两种运行模式：

#### 方案一：全 Docker 模式（强烈推荐 ⭐）
如果你不想在电脑上装 Nginx、Node.js、Python 等环境，你可以直接使用项目提供的 Docker 命令。在这个模式下，**Nginx 会作为一个 Docker 容器运行**。
你只需要执行：
```bash
make docker-init   # 首次初始化镜像和依赖
make docker-start  # 启动所有服务（包括作为容器运行的 Nginx）
```
这样你电脑上即使没有 Nginx 也能完美运行。

#### 方案二：纯本地运行模式
只有当你选择**纯本地运行**（使用 `make dev` 命令）时，它的启动脚本（`scripts/serve.sh`）才会在你的宿主机上直接调用 `nginx` 命令。这时候才**必须**要求你本地通过 Homebrew 等方式安装了 Nginx。

**总结：**
如果你打算用 Docker 运行整个项目，你**不需要**在 Mac 上额外安装 Nginx。直接使用项目的 `make docker-start` 即可，项目会自动拉取并运行官方的 `nginx:alpine` 镜像来处理请求。