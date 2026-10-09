# Godot

## 1. 安装（多版本，/opt）

与 021-blender.md 同构：官方 release zip 只含单体二进制（无顶层目录），解压后收进 /opt 稳定系列名目录。注意命名差异：3.x 编辑器资产是 x11 命名，4.x 是 linux.x86_64 命名。以 3.6.3 为例：

```bash
VER=3.6.3
URL="https://github.com/godotengine/godot/releases/download/${VER}-stable/Godot_v${VER}-stable_x11.64.zip"

# 本机直连 github.com 被墙，失败时走 gh-proxy.com 代理。代理单连接约 60KB/s 且会降速，
# 它支持 HTTP Range 请求，大文件可分块并行下载再拼接（只拼接连续且长度完整的块）
curl -fLO "$URL" || curl -fLO "https://gh-proxy.com/$URL"

unzip -oq "Godot_v${VER}-stable_x11.64.zip" -d /tmp/godot-3
sudo mkdir -p /opt/godot-3
sudo mv "/tmp/godot-3/Godot_v${VER}-stable_x11.64" /opt/godot-3/Godot
rm -rf "Godot_v${VER}-stable_x11.64.zip" /tmp/godot-3
```

- 4.x 同理：资产名 Godot_v4.7.2-stable_linux.x86_64.zip，解压后改名 /opt/godot-4/Godot
- zip 无官方 sha256 校验文件，用 unzip -tq 做完整性检查即可
- 下载体积：3.x 约 35MB、4.x 约 78MB；解压后 3.x 约 64MB、4.x 约 140MB（单体二进制）
- 版本号到 https://github.com/godotengine/godot/releases 查最新（3.x 已收尾于 3.6.3，4.x 活跃）

## 2. 命令行入口

```bash
sudo ln -sfn /opt/godot-3/Godot /usr/local/bin/godot-3
sudo ln -sfn /opt/godot-4/Godot /usr/local/bin/godot-4
sudo ln -sfn /opt/godot-4/Godot /usr/local/bin/godot
```

- godot 指向最新的 4.x；指定版本用 godot-3 / godot-4
- 无头验证：godot-3 --version / godot-4 --version（打印版本后退出，不开编辑器；3.x 输出形如 3.6.3.stable.official.<hash>）

## 3. 启动器图标

官方 zip 不含图标，图标取自仓库 icon.svg（raw.githubusercontent.com 可达，不受 github.com 封锁影响；注意只有 3.x 分支根目录还有 icon.svg，master/4.x 已 404），装进用户级 hicolor 主题，.desktop 用主题名引用：

```bash
mkdir -p ~/.local/share/icons/hicolor/scalable/apps

curl -fsSL -o ~/.local/share/icons/hicolor/scalable/apps/godot.svg \
  "https://raw.githubusercontent.com/godotengine/godot/3.x/icon.svg"

# 新建的 hicolor 主题需要索引文件才能被 Icon=godot 解析
cat > ~/.local/share/icons/hicolor/index.theme <<'EOF'
[Icon Theme]
Name=Hicolor
Directories=scalable/apps
EOF

command -v gtk-update-icon-cache >/dev/null && gtk-update-icon-cache -q ~/.local/share/icons/hicolor

mkdir -p ~/.local/share/applications

cat > ~/.local/share/applications/org.godotengine.Godot3.desktop <<'EOF'
[Desktop Entry]
Name=Godot 3
Comment=Godot 3.6.3 - 游戏引擎
Exec=/opt/godot-3/Godot %F
Icon=godot
Terminal=false
Type=Application
Categories=Development;IDE;
MimeType=application/x-godot-project;
EOF
```

4.x 同模板（org.godotengine.Godot4.desktop，Comment 写 4.7.2），然后注册：

```bash
desktop-file-validate ~/.local/share/applications/org.godotengine.Godot*.desktop
update-desktop-database ~/.local/share/applications
systemctl --user restart dms.service
```

- 启动器（Mod+Space）应用列表在 shell 启动时构建，新 .desktop 必须重启 DMS 才显示（同 021-blender.md 的说明）

## 4. 升级

换小版本只需重跑第 1 节（下载新 zip → 覆盖 /opt/godot-X/Godot）；桌面项与 /usr/local/bin 软链都指向稳定名，无需改动。
