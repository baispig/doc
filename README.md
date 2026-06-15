在 Ubuntu 或 Debian 服务器中，登录 SSH 后直接复制执行：
curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh" \
  -o /root/aurora_panel_installer.sh \
&& chmod +x /root/aurora_panel_installer.sh \
&& bash /root/aurora_panel_installer.sh
如果当前不是 root 用户，使用：
curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh" \
  -o aurora_panel_installer.sh \
&& chmod +x aurora_panel_installer.sh \
&& sudo bash aurora_panel_installer.sh
也可以用 wget：
wget -O aurora_panel_installer.sh \
"https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh"

chmod +x aurora_panel_installer.sh
sudo bash aurora_panel_installer.sh
一行直接拉取并执行
bash <(curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh")
不过更推荐前面的“先下载、再执行”方式，因为出错后可以检查脚本：
nano /root/aurora_panel_installer.sh
以后 GitHub 脚本更新了，重新拉取覆盖再执行即可：
curl -fsSL "https://raw.githubusercontent.com/baispig/doc/refs/heads/main/aurora_panel_installer.sh" \
  -o /root/aurora_panel_installer.sh \
&& bash /root/aurora_panel_installer.sh
