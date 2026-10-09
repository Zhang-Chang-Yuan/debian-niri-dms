# Niri DMS

## 1. 安装

```bash
curl -fsSL https://install.danklinux.com | sh

sudo apt install power-profiles-daemon cups-pk-helper kimageformat6-plugins

sudo systemctl enable --now power-profiles-daemon
```

## 2. 禁用启动器中的 DMS / niri 条目

只禁用不卸载，全部走配置文件。分两类：系统 .desktop 条目用用户级同名覆盖文件隐藏；DMS 内置启动器插件用 settings.json 关闭。fcitx5 输入法家族的条目清理属于该软件自己的配置，写在 013-fcitx5.md 第 6 节。

### 2.1 系统 .desktop 条目（NoDisplay 覆盖）

```bash
mkdir -p ~/.local/share/applications

for f in com.danklinux.dms com.danklinux.dms.notepad com.danklinux.dankcalendar dms-open org.quickshell; do
  src="/usr/share/applications/$f.desktop"
  dst="$HOME/.local/share/applications/$f.desktop"
  cp "$src" "$dst"
  sed -i '/^NoDisplay[[:space:]]*=/d' "$dst"
  sed -i '/^\[Desktop Entry\]/a NoDisplay=true' "$dst"
  desktop-file-validate "$dst"
done

update-desktop-database ~/.local/share/applications
```

- 用户目录同名文件优先于 /usr/share/applications，等价于从启动器禁用但文件仍在；删掉覆盖文件即恢复
- 已评估但保留：qt6ct（Qt6 设置）——fcitx5 配置 GUI（fcitx5-configtool）是 Qt6 程序，其界面样式由 qt6ct 管理，属必要入口而非冗余条目。其桌面项 Icon=preferences-desktop-theme 在全部已装主题（Adwaita / hicolor / default）中均不存在，启动器显示默认占位图标属原始状态，无需处理
- dms-open.desktop 是 DMS 的 x-scheme-handler 接管器，隐藏它不影响 xdg-open（默认浏览器由 xdg-mime 决定，见 014-firefox.md）

### 2.2 DMS 内置启动器插件（settings.json）

设置、二维码生成、取色器、便签、系统监视器等内置插件不是 .desktop，由 DMS 注册。编辑 ~/.config/DankMaterialShell/settings.json：

```json
"builtInPluginSettings": {
  "dms_settings":     { "enabled": false },
  "dms_notepad":      { "enabled": false },
  "dms_sysmon":       { "enabled": false },
  "dms_colorpicker":  { "enabled": false },
  "dms_qr_generator": { "enabled": false }
}
```

- 插件 id 全集：dms_settings、dms_notepad、dms_sysmon、dms_settings_search、dms_clipboard_search、dms_power、dms_colorpicker、dms_qr_generator，按需增删
- enabled=false 后插件从启动器过滤，IPC 触发（快捷键）同样失效

### 2.3 重启 DMS 生效

```bash
systemctl --user restart dms.service
```

启动器（Mod+Space）的应用列表在 shell 启动时构建，.desktop 与插件变更都必须重启 DMS 后才生效（同 021-blender.md 的说明）。