# Aurora Proxy Panel 使用说明

适用版本：`1.2.18`

本文说明两件事：

1. 面板日常怎么用。
2. 除了一键脚本以外，源码目录还能怎么安装、运行和二次开发。

当前文件位置：

- 一键脚本：`/Users/shui/Documents/New/aurora_panel_installer.sh`
- 源码目录：`/Users/shui/Documents/New/aurora_panel_src`
- 截图回归目录：`/Users/shui/Documents/New/aurora_screenshots`

## 1. 当前已部署面板

当前测试面板已经部署在：

```text
http://45.32.87.30:8080
```

管理员账号为 `admin`。密码使用你当前保存的管理员密码。

进入服务器后常用管理命令：

```bash
cd /opt/aurora-proxy-panel
sudo ./manage.sh doctor
sudo ./manage.sh status
sudo ./manage.sh logs
sudo ./manage.sh backup
sudo ./manage.sh restart
```

## 2. 面板日常使用流程

推荐按这个顺序操作：

1. 登录面板。
2. 进入「安全设置」，修改管理员密码，并启用 TOTP 双重验证。
3. 进入「服务器与节点」，添加协议服务器。
4. 等待「接入服务器」任务完成。
5. 在服务器详情页创建协议节点。
6. 进入「用户与订阅」，创建用户并勾选可用节点。
7. 在用户详情页复制订阅地址，或打开订阅中心扫码导入 Shadowrocket / Karing 等客户端。

## 3. 添加服务器

入口：

```text
服务器与节点 -> 添加服务器
```

需要填写：

- 服务器名称：自定义，例如 `us-node-01`
- IPv4 / IPv6 / 域名：可以填 IPv4、IPv6 或域名
- SSH 端口：默认 `22`
- SSH 用户：当前版本要求 `root`
- SSH 密码：仅首次接入使用

接入成功后，面板会：

- 固定首次看到的 SSH 主机指纹。
- 写入面板专用 SSH 公钥。
- 后续改用加密保存的专用私钥连接。
- 不再保存首次录入的明文 SSH 密码。
- 自动探测 IPv4 / IPv6，用于订阅地址和界面展示。

注意：云服务商安全组仍需你自行放行端口。面板只能在远端系统启用 UFW 时添加 UFW 规则。

## 4. 创建节点

入口：

```text
服务器详情 -> 创建节点
```

### Mieru

适合无需域名的高性能节点。

推荐设置：

- 传输：`TCP`
- 公网端口：`1025-65535` 中的高位端口
- 连续三个端口：建议开启

订阅说明：

- Shadowrocket / Raw 会输出 `mierus://`
- Karing / Mihomo 会包含 Mieru
- Stash / sing-box 当前会省略 Mieru，因为其公开格式支持不完整或不稳定

限制：

- 同一台服务器当前只允许一个 Mieru 节点，因为 Mita 服务端用户集合和端口绑定是全局配置。

### VLESS REALITY Vision

适合无需自有域名、伪装性较好的 Xray 节点。

推荐设置：

- 公网端口：默认 `2053`
- REALITY SNI：使用长期稳定支持 TLS 1.3 的站点，例如 `www.microsoft.com`
- Flow：面板自动使用 `xtls-rprx-vision`

注意：

- 如果这台服务器同时部署 VMess TLS，`80/443` 会被 Caddy 占用，VLESS 建议用 `2053` 等其他端口。

### VMess WebSocket TLS

适合需要 TLS + WebSocket 的传统兼容节点。

必须准备：

- 一个域名或子域名。
- A/AAAA 记录指向协议服务器。
- 云安全组放行 TCP `80/443` 和 UDP `443`。
- 服务器本机 `80/443` 没有被其他服务占用。

WebSocket 路径可以留空，面板会随机生成。

## 5. 用户与订阅

入口：

```text
用户与订阅 -> 新增用户
```

可设置：

- 显示名称
- 用户名，可留空随机
- 密码，可留空随机
- 流量额度 GB，`0` 表示不限
- 到期日期，可留空永久
- 授权节点

用户详情页可以：

- 查看 Mieru 用户名和密码。
- 查看 VLESS / VMess UUID。
- 复制 Shadowrocket / Raw / Karing / Mihomo / Stash / sing-box 订阅。
- 打开二维码订阅中心。
- 更换订阅令牌。
- 停用或删除用户。

订阅令牌更换后，旧订阅地址会立即失效。

## 6. 一键脚本安装

把脚本上传到 Ubuntu / Debian 控制服务器：

```bash
chmod +x aurora_panel_installer.sh
sudo bash aurora_panel_installer.sh
```

安装器会自动：

- 安装 Docker Engine 和 Docker Compose 插件。
- 安装到 `/opt/aurora-proxy-panel`。
- 创建 `.env`、`MASTER_KEY`、`SESSION_SECRET`。
- 创建管理员账号。
- 启动 FastAPI 应用容器和 Caddy 容器。
- 有域名时使用 HTTPS。
- 无域名时使用 `http://服务器IP:8080` 临时入口。

非交互安装示例：

```bash
sudo AURORA_PANEL_DOMAIN="" \
  AURORA_HTTP_PORT=8080 \
  AURORA_ADMIN_USERNAME=admin \
  AURORA_ADMIN_PASSWORD='请换成12到64位强密码' \
  bash aurora_panel_installer.sh
```

带域名安装示例：

```bash
sudo AURORA_PANEL_DOMAIN="panel.example.com" \
  AURORA_PANEL_EMAIL="you@example.com" \
  AURORA_ADMIN_USERNAME=admin \
  AURORA_ADMIN_PASSWORD='请换成12到64位强密码' \
  bash aurora_panel_installer.sh
```

带域名时，请先确保：

- 域名 A/AAAA 已解析到控制服务器。
- 云安全组放行 TCP `80/443` 和 UDP `443`。

## 7. 不用一键脚本：源码目录直接安装

这是最推荐的“源码安装”方式。它不用自解压一键脚本，而是直接使用源码里的 `install.sh`。

### 7.1 准备源码

本机源码目录是：

```bash
/Users/shui/Documents/New/aurora_panel_src
```

上传到服务器，例如：

```bash
rsync -av --delete \
  --exclude data \
  --exclude '__pycache__' \
  --exclude '*.pyc' \
  /Users/shui/Documents/New/aurora_panel_src/ \
  root@服务器IP:/root/aurora_panel_src/
```

### 7.2 在服务器运行源码安装

```bash
ssh root@服务器IP
cd /root/aurora_panel_src
sudo bash install.sh
```

非交互源码安装：

```bash
cd /root/aurora_panel_src
sudo AURORA_PANEL_DOMAIN="" \
  AURORA_HTTP_PORT=8080 \
  AURORA_ADMIN_USERNAME=admin \
  AURORA_ADMIN_PASSWORD='请换成12到64位强密码' \
  bash install.sh
```

安装完成后，实际运行目录仍然是：

```bash
/opt/aurora-proxy-panel
```

也就是说，`/root/aurora_panel_src` 只是源码输入目录，真正的面板运行目录由 `install.sh` 复制生成。

## 8. 不用一键脚本：从一键包只解出源码

一键脚本支持只解包、不安装：

```bash
mkdir -p /root/aurora-src
AURORA_EXTRACT_ONLY=/root/aurora-src bash aurora_panel_installer.sh
```

然后使用源码安装：

```bash
cd /root/aurora-src
sudo bash install.sh
```

这个方法适合你只有 `aurora_panel_installer.sh`，但想先检查源码再安装。

## 9. 不用 install.sh：手动 Docker Compose 安装

这种方式适合你想完全自己控制 Docker、Caddyfile、`.env`。

### 9.1 安装系统依赖

在 Ubuntu / Debian 上先安装 Docker Engine 和 Compose 插件。安装完成后确认：

```bash
docker --version
docker compose version
```

### 9.2 放置源码

```bash
mkdir -p /opt/aurora-proxy-panel
rsync -av --delete \
  --exclude data \
  --exclude '__pycache__' \
  --exclude '*.pyc' \
  /root/aurora_panel_src/ \
  /opt/aurora-proxy-panel/
cd /opt/aurora-proxy-panel
```

### 9.3 创建数据目录

```bash
mkdir -p data
chown -R 1000:1000 data
chmod 700 data
```

### 9.4 创建 `.env`

生成随机值：

```bash
SESSION_SECRET="$(openssl rand -hex 48)"
MASTER_KEY="$(python3 - <<'PY'
import os, base64
print(base64.urlsafe_b64encode(os.urandom(32)).decode())
PY
)"
```

无域名 HTTP 临时入口：

```bash
cat > .env <<ENV
PANEL_DOMAIN=
PANEL_EMAIL=
PANEL_BASE_URL=
PANEL_HTTP_PORT=8080
PANEL_HTTPS_PORT=8443
ADMIN_USERNAME=admin
ADMIN_PASSWORD=请换成12到64位强密码
SESSION_SECRET=$SESSION_SECRET
MASTER_KEY=$MASTER_KEY
DATABASE_URL=sqlite:////data/panel.db
COOKIE_SECURE=false
TZ=Asia/Shanghai
ENV
chmod 600 .env
```

有域名 HTTPS 入口：

```bash
cat > .env <<ENV
PANEL_DOMAIN=panel.example.com
PANEL_EMAIL=you@example.com
PANEL_BASE_URL=
PANEL_HTTP_PORT=80
PANEL_HTTPS_PORT=443
ADMIN_USERNAME=admin
ADMIN_PASSWORD=请换成12到64位强密码
SESSION_SECRET=$SESSION_SECRET
MASTER_KEY=$MASTER_KEY
DATABASE_URL=sqlite:////data/panel.db
COOKIE_SECURE=true
TZ=Asia/Shanghai
ENV
chmod 600 .env
```

### 9.5 创建 Caddyfile

无域名：

```bash
cat > Caddyfile <<'CADDY'
:80 {
    encode zstd gzip
    reverse_proxy app:8000
    header {
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "strict-origin-when-cross-origin"
        Permissions-Policy "camera=(), microphone=(), geolocation=()"
    }
}
CADDY
```

有域名：

```bash
cat > Caddyfile <<'CADDY'
{
    email you@example.com
}

panel.example.com {
    encode zstd gzip
    reverse_proxy app:8000
    header {
        Strict-Transport-Security "max-age=31536000; includeSubDomains"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        Referrer-Policy "strict-origin-when-cross-origin"
        Permissions-Policy "camera=(), microphone=(), geolocation=()"
    }
}
CADDY
```

### 9.6 启动

```bash
docker compose up -d --build
docker compose ps
```

自检：

```bash
./manage.sh doctor
```

没有使用 `install.sh` 时，`manage.sh` 仍然可用，但前提是目录结构、`.env`、`Caddyfile`、`data` 权限都按上面配置好。

## 10. 本地开发运行，不用 Docker

本地开发适合改 Python、模板、CSS、订阅逻辑。它不适合生产部署。

进入应用目录：

```bash
cd /Users/shui/Documents/New/aurora_panel_src/app
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

准备本地数据库目录：

```bash
mkdir -p /Users/shui/Documents/New/aurora_panel_src/data
```

设置环境变量：

```bash
export ADMIN_USERNAME=admin
export ADMIN_PASSWORD='请换成12到64位强密码'
export SESSION_SECRET="$(openssl rand -hex 48)"
export MASTER_KEY="$(python3 - <<'PY'
import os, base64
print(base64.urlsafe_b64encode(os.urandom(32)).decode())
PY
)"
export DATABASE_URL="sqlite:////Users/shui/Documents/New/aurora_panel_src/data/panel-dev.db"
export COOKIE_SECURE=false
export PANEL_BASE_URL="http://127.0.0.1:8000"
```

启动：

```bash
uvicorn main:app --host 127.0.0.1 --port 8000 --reload --no-access-log
```

打开：

```text
http://127.0.0.1:8000
```

说明：

- FastAPI、SQLAlchemy、Paramiko 等依赖都在 `app/requirements.txt`。
- Docker 部署时这些依赖会自动安装进容器。
- 本地开发时需要自己 `pip install -r requirements.txt`。

## 11. 源码结构

```text
aurora_panel_src/
├── app/
│   ├── main.py              Web 路由、页面、表单、任务入口
│   ├── deploy.py            SSH 接入、Mieru/Xray/Caddy 部署与同步
│   ├── subscriptions.py     Shadowrocket/Raw/Karing/Mihomo/Stash/sing-box/二维码
│   ├── models.py            SQLAlchemy 数据模型
│   ├── db.py                数据库连接与轻量迁移
│   ├── security.py          密码、加密、随机值、TOTP 相关
│   ├── addressing.py        IPv4/IPv6/域名展示与订阅地址处理
│   ├── templates/           Jinja2 页面模板
│   └── static/              CSS 和前端 JS
├── install.sh               源码安装/升级入口
├── manage.sh                运行管理、备份、恢复、自检
├── docker-compose.yml       app + caddy
├── Caddyfile                面板反代配置
├── VERSION
└── CHANGELOG.md
```

## 12. 升级

一键脚本升级：

```bash
sudo bash aurora_panel_installer.sh
```

源码升级：

```bash
rsync -av --delete \
  --exclude data \
  --exclude '__pycache__' \
  --exclude '*.pyc' \
  /Users/shui/Documents/New/aurora_panel_src/ \
  root@服务器IP:/root/aurora_panel_src/

ssh root@服务器IP
cd /root/aurora_panel_src
sudo bash install.sh
```

检测到已有 `.env` 时，安装器默认保留：

- 数据库
- 管理员账号和密码
- `MASTER_KEY`
- `SESSION_SECRET`
- Caddy 数据
- 面板已有配置

升级前会自动生成一致性备份：

```text
/root/aurora-panel-backups/pre-upgrade-*.tar.gz
```

## 13. 备份与恢复

创建备份：

```bash
cd /opt/aurora-proxy-panel
sudo ./manage.sh backup
```

恢复备份：

```bash
cd /opt/aurora-proxy-panel
sudo ./manage.sh restore /root/aurora-panel-backups/备份文件.tar.gz
```

恢复前脚本会再创建一份 `pre-restore-*` 安全备份。

远端协议服务器每次同步前也会备份配置：

```text
/etc/aurora-proxy/backups/
```

## 14. 常见问题

### 面板打不开

检查：

```bash
cd /opt/aurora-proxy-panel
sudo ./manage.sh doctor
sudo ./manage.sh logs
```

同时确认云安全组放行：

- 无域名临时入口：TCP `8080`
- 域名 HTTPS：TCP `80/443`、UDP `443`

### 添加服务器失败

检查：

- SSH 用户必须是 `root`。
- SSH 密码正确。
- 云安全组放行 SSH 端口。
- 服务器系统是 Ubuntu / Debian。
- 同一台服务器不要用 IPv4 和 IPv6 重复接入。

### 部署节点失败

先进入任务详情看实时日志。

常见原因：

- 端口被占用。
- 云安全组没放行端口。
- VMess 域名没有正确解析到协议服务器。
- VLESS REALITY SNI 不稳定或不可访问。
- 远端 apt/dpkg 正在被系统更新占用；`1.2.18` 已加入等待逻辑，偶发时可重新同步。

### 删除用户后客户端还能用

删除用户会创建同步任务。等任务成功后，远端配置才会完全移除该用户。

查看：

```text
部署任务 -> 删除用户：xxx
```

### 为什么宿主机不用手动安装 FastAPI/SQLAlchemy

生产部署使用 Docker。`app/Dockerfile` 会自动执行：

```bash
pip install -r requirements.txt
```

因此 FastAPI、SQLAlchemy、Paramiko、Cryptography 等依赖都安装在容器里，不要求宿主机 Python 环境安装这些包。

只有本地开发、不用 Docker 跑 `uvicorn` 时，才需要手动：

```bash
pip install -r app/requirements.txt
```

## 15. 安全提醒

- 生产使用建议给面板配置域名和 HTTPS，不要长期使用 HTTP 临时入口。
- `.env` 里的 `MASTER_KEY` 丢失后，已保存的 SSH 私钥、Mieru 密码、REALITY 私钥、TOTP 密钥都无法解密。
- 不要把已有重要 Xray/Caddy/Mita 配置的服务器直接纳管，面板会接管相关主配置。
- 删除服务器只会从面板移除记录，不会自动卸载远端服务。
- 请只用于你有权管理的服务器和合法网络用途。
