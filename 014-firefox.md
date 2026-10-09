# Firefox

## 1. 安装

```bash
sudo apt install firefox-esr firefox-esr-l10n-zh-cn
```

## 2. 默认浏览器

DMS 默认通过 dms-open.desktop 接管 http/https 链接（未配置时会弹出应用选择器），以下改为 firefox-esr：

```bash
# 生成 ~/.config/mimeapps.list（优先级高于 mimeinfo.cache 回退）
xdg-settings set default-web-browser firefox-esr.desktop

# 显式补齐 scheme（幂等，可重复执行）
xdg-mime default firefox-esr.desktop x-scheme-handler/http x-scheme-handler/https text/html application/xhtml+xml

# 验证
xdg-settings get default-web-browser
xdg-mime query default x-scheme-handler/http
```

niri environment 块（~/.config/niri/config.kdl）追加一行，然后重载配置：

```bash
nano ~/.config/niri/config.kdl
```

```
environment {
  ...
  BROWSER "/usr/bin/firefox-esr"
}
```

```bash
niri msg action load-config-file
```

- niri 用 `niri msg action load-config-file` 重载（`niri msg reload` 不是有效子命令）
- 顺序：必须先完成上面的 mime 设置，再设 BROWSER，否则 xdg-settings set 会报 "$BROWSER is set" 失败
- BROWSER 不要写入 ~/.config/environment.d：90-dms.conf 是 DMS 托管文件，且 environment.d 只作用于 systemd user 服务
- 实测确认：配置后 xdg-open https://example.com 直接拉起 Firefox（2026-10-09 验证）；DMS 界面内部点击链接是否仍走自身选择器需另行实测，必要时可移除 /usr/share/applications/dms-open.desktop 中的 MimeType 行