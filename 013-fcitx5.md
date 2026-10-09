# Input Method

## 1. 安装

sudo apt install -y fcitx5 fcitx5-rime fcitx5-config-qt \
  librime-plugin-lua fcitx5-frontend-gtk3 fcitx5-frontend-gtk4 \
  fcitx5-frontend-qt5 fcitx5-frontend-qt6

## 2. Rime

mv ~/.local/share/fcitx5/rime ~/.local/share/fcitx5/rime.bak.$(date +%F-%H%M%S) 2>/dev/null

git clone --depth=1 https://github.com/iDvel/rime-ice.git ~/.local/share/fcitx5/rime

cat > ~/.local/share/fcitx5/rime/default.custom.yaml << 'EOF'
patch:
  "menu/page_size": 5
  schema_list:
    - schema: rime_ice
  ascii_composer:
    good_old_caps_lock: true
    switch_key:
      Shift_L: commit_code
      Shift_R: commit_code
      Control_L: noop
      Control_R: noop
      Caps_Lock: clear
      Eisu_toggle: clear
EOF

cat > ~/.local/share/fcitx5/rime/rime_ice.custom.yaml << 'EOF'
patch:
  melt_eng/initial_quality: 1.0
EOF

## 3. niri

cp ~/.config/niri/config.kdl ~/.config/niri/config.kdl.bak

# 手动编辑 config.kdl，添加 environment 块
nano ~/.config/niri/config.kdl
environment {
  XMODIFIERS "@im=fcitx"
  GTK_IM_MODULE "fcitx"
  QT_IM_MODULE "fcitx"
  SDL_IM_MODULE "fcitx"
}

cat >> ~/.config/niri/config.kdl << 'EOF'

spawn-at-startup "fcitx5" "-d"
EOF

## 4. 重启并部署

pkill fcitx5 && sleep 1 && fcitx5 -d &

# 首次部署后，触发二次部署
touch ~/.local/share/fcitx5/rime/rime_ice.schema.yaml
pkill fcitx5 && sleep 1 && fcitx5 -d &

## 5. 精简候选面板（classicui）

默认 classicui 竖排候选词且每页偏多，写 conf 横排显示更紧凑：

```bash
mkdir -p ~/.config/fcitx5/conf

cat > ~/.config/fcitx5/conf/classicui.conf << 'EOF'
[General]
# 候选词改横排，面板更紧凑
Vertical Candidate List=False
EOF

pkill fcitx5 && sleep 1 && fcitx5 -d &
```

- 配合第 2 节 page_size=5，候选面板每页 5 个、单行横排，视觉冗余最小
- 改字体可加 Font="Noto Sans 10"；恢复竖排删掉该文件即可
- 启动器里的 DMS/niri 自身条目（设置、便签等）清理见 010-niri-dms.md 第 2 节

## 6. 启动器条目清理

fcitx5 家族在启动器（Mod+Space）会露出多个条目，只保留配置入口，其余用同名覆盖文件隐藏（不卸载，删掉覆盖文件即恢复）：

- 保留「Fcitx 5 配置」（Exec=/usr/bin/fcitx5-configtool），输入法设置的唯一入口，不要覆盖它
- org.fcitx.Fcitx5（启动输入法）：只是启动 fcitx5 守护进程的壳，niri 第 3 节已 spawn-at-startup 启动
- org.fcitx.fcitx5-migrator（迁移向导）：一次性迁移工具
- im-config（输入法）：Debian 输入法框架切换器，与本方案共存时容易误切
- kbd-layout-viewer5（键盘布局测试器）：KDE 工具，非 fcitx5 日常入口

```bash
mkdir -p ~/.local/share/applications

for f in org.fcitx.Fcitx5 org.fcitx.fcitx5-migrator im-config kbd-layout-viewer5; do
  src="/usr/share/applications/$f.desktop"
  dst="$HOME/.local/share/applications/$f.desktop"
  cp "$src" "$dst"
  sed -i '/^NoDisplay[[:space:]]*=/d' "$dst"
  sed -i '/^\[Desktop Entry\]/a NoDisplay=true' "$dst"
  desktop-file-validate "$dst"
done

update-desktop-database ~/.local/share/applications
systemctl --user restart dms.service
```

- 系统文件本就 NoDisplay=true 的无需处理：fcitx5-wayland-launcher、org.fcitx.fcitx5-config-qt、org.fcitx.fcitx5-qt5/6-gui-wrapper
- 要调输入法设置：启动器打开「Fcitx 5 配置」
