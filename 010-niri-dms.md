# Niri DMS

## 1. 安装

```bash
curl -fsSL https://install.danklinux.com | sh

sudo apt install power-profiles-daemon cups-pk-helper kimageformat6-plugins

sudo systemctl enable --now power-profiles-daemon
```

## 2. 禁用启动器中的 DMS / niri / 输入法条目

只禁用不卸载，全部走配置文件。分两类：系统 .desktop 条目用用户级同名覆盖文件隐藏；DMS 内置启动器插件用 settings.json 关闭。

### 2.1 系统 .desktop 条目（NoDisplay 覆盖）

```bash
mkdir -p ~/.local/share/applications

for f in com.danklinux.dms com.danklinux.dms.notepad com.danklinux.dankcalendar dms-open org.quickshell \
         fcitx5-configtool im-config kbd-layout-viewer5 org.fcitx.Fcitx5 org.fcitx.fcitx5-migrator; do
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
- 已覆盖条目：DMS 系列（dms、notepad、dankcalendar、dms-open、quickshell）与 fcitx5 系列（Fcitx 5、Fcitx 5 配置、迁移向导、输入法 im-config、键盘布局测试器）
- 系统文件本就 NoDisplay=true 的无需处理：fcitx5-wayland-launcher、org.fcitx.fcitx5-config-qt、org.fcitx.fcitx5-qt5/6-gui-wrapper
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