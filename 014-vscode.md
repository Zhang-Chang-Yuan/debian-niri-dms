# VS Code

```bash
sudo apt install -y gpg apt-transport-https desktop-file-utils xdg-utils
```

## 2. Install VS Code

```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg

sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg

rm -f packages.microsoft.gpg

sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'

sudo apt update
sudo apt install -y code
```

## 3. 默认编辑器

注意 desktop 文件名为 com.microsoft.VSCode.desktop（微软官方源），不是 code.desktop。

GUI 打开方式（覆盖 DMS notepad 对 text/plain 的接管）：

```bash
xdg-mime default com.microsoft.VSCode.desktop text/plain text/markdown

# 可选：扩展到常见代码/文本类型
xdg-mime default com.microsoft.VSCode.desktop text/html text/css text/xml \
  application/xml application/json application/javascript \
  text/x-python text/x-shellscript text/x-chdr text/x-csrc

# 验证
xdg-mime query default text/plain
```

终端 $EDITOR / $VISUAL（写入 environment.d，登录时注入 GUI 会话）：

```bash
mkdir -p ~/.config/environment.d
printf 'EDITOR=code\nVISUAL=code\n' > ~/.config/environment.d/10-editor.conf
```

- environment.d 语法为 KEY=VALUE：无 export、无引号；systemd user 启动时读取，systemctl --user daemon-reload 后当前会话即可见（2026-10-09 验证：show-environment 已含 EDITOR/VISUAL），新登录会话同样生效
- 不写入 ~/.profile：niri/DMS 图形会话不 source 它，经启动器起的 GUI 应用拿不到其中的变量
- update-alternatives 的 editor alternative 保持 nano/vi，不要指向 GUI 的 code：visudo、crontab -e、git commit 等无图形或 root 上下文会失败
- git 需要等待编辑器关闭时单独设置：git config --global core.editor "code --wait"