# Blender

## 1. 安装（多版本，/opt）

从阿里云镜像下载官方 tar.xz（免去 download.blender.org 直连），解压到 /opt 并统一换成稳定系列名目录（blender-X.Y，不保留补丁号目录），升级即「下载 → 删除 → 替换」。以 4.2 LTS 为例：

```bash
VER=4.2.23   # 补丁号到 https://mirrors.aliyun.com/blender/release/Blender4.2/ 列表查最大者

curl -fLO "https://mirrors.aliyun.com/blender/release/Blender${VER%.*}/blender-${VER}-linux-x64.tar.xz"
curl -s "https://mirrors.aliyun.com/blender/release/Blender${VER%.*}/blender-${VER}.sha256" | grep linux-x64 | sha256sum -c -

sudo tar -xJf "blender-${VER}-linux-x64.tar.xz" -C /opt/
sudo rm -rf /opt/blender-4.2 && sudo mv "/opt/blender-${VER}-linux-x64" /opt/blender-4.2
rm -f "blender-${VER}-linux-x64.tar.xz"
```

- 4.5 / 5.2 同理（Blender4.5/、Blender5.2/），三版本并存各约 1.2G
- sha256 文件按版本命名 blender-${VER}.sha256（非按包名），需 grep 出 linux-x64 行核对
- 阿里云慢时换清华源 mirrors.tuna.tsinghua.edu.cn/blender/release/（内容同步）

## 2. 命令行入口

```bash
sudo ln -sfn /opt/blender-4.2/blender /usr/local/bin/blender-4.2
sudo ln -sfn /opt/blender-4.5/blender /usr/local/bin/blender-4.5
sudo ln -sfn /opt/blender-5.2/blender /usr/local/bin/blender-5.2
sudo ln -sfn /opt/blender-5.2/blender /usr/local/bin/blender
```

- blender 指向最新 LTS（5.2）；指定版本用 blender-4.2 / blender-4.5 / blender-5.2

## 3. 启动器图标

用户级 .desktop，Icon 直接用安装目录内 blender.svg 绝对路径：

```bash
mkdir -p ~/.local/share/applications

cat > ~/.local/share/applications/org.blender.Blender42.desktop <<'EOF'
[Desktop Entry]
Name=Blender 4.2 LTS
Comment=Blender 4.2.23 - 3D 建模/动画/渲染
Exec=/opt/blender-4.2/blender %F
Icon=/opt/blender-4.2/blender.svg
Terminal=false
Type=Application
Categories=Graphics;3DGraphics;
MimeType=application/x-blender;
EOF
```

4.5 / 5.2 同模板，文件名 org.blender.Blender45.desktop / org.blender.Blender52.desktop，然后注册并校验：

```bash
desktop-file-validate ~/.local/share/applications/org.blender.Blender*.desktop
update-desktop-database ~/.local/share/applications
blender-4.2 --version
```

## 4. 升级

换补丁版只需重跑第 1 节三步（下载新包 → 校验 → 删旧目录并改名）；桌面项与 /usr/local/bin 软链都指向稳定名 /opt/blender-X.Y，升级后无需改任何配置。
