# Aurora Panel 安装教程

## Ubuntu / Debian 服务器安装

登录 SSH 后，直接复制并执行以下命令。

### 使用 Root 用户安装

```bash
curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh" \
  -o /root/aurora_panel_installer.sh \
&& chmod +x /root/aurora_panel_installer.sh \
&& bash /root/aurora_panel_installer.sh
```

### 使用普通用户安装

如果当前登录的不是 `root` 用户，请使用：

```bash
curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh" \
  -o aurora_panel_installer.sh \
&& chmod +x aurora_panel_installer.sh \
&& sudo bash aurora_panel_installer.sh
```

## 使用 wget 下载并安装

也可以使用 `wget` 下载脚本：

```bash
wget -O aurora_panel_installer.sh \
  "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh"
```

赋予脚本执行权限：

```bash
chmod +x aurora_panel_installer.sh
```

运行安装脚本：

```bash
sudo bash aurora_panel_installer.sh
```

## 一行命令直接执行

```bash
bash <(curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh")
```

> 更推荐使用前面的“先下载、再执行”方式。这样安装出错时，可以检查脚本内容。

例如：

```bash
nano /root/aurora_panel_installer.sh
```

## 更新并重新执行脚本

以后 GitHub 上的安装脚本更新后，可以重新下载并覆盖原文件，然后再次执行：

```bash
curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh" \
  -o /root/aurora_panel_installer.sh \
&& bash /root/aurora_panel_installer.sh
```
