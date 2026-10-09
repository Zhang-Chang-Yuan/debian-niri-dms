# obs-studio

## 1. 安装

```bash
sudo apt install obs-studio
```

- Debian trixie 源，30.2.3；连同依赖约 200 个包（libobs、浏览器源、PipeWire 封装等），一把装完无需额外配置
- 桌面项 com.obsproject.Studio.desktop，图标随包装入 hicolor（含 256x256/512x512 PNG），装完即出现在启动器
- 无头验证：obs --version

## 2. Wayland 录屏链路（关键：装对 portal 后端）

OBS 靠 PipeWire + xdg-desktop-portal 录桌面/窗口。**只装 obs-studio 时 OBS 认不出屏幕**：根因是 portal 没有导出 ScreenCast 接口——xdg-desktop-portal-gtk 的能力清单里没有 ScreenCast（/usr/share/xdg-desktop-portal/portals/gtk.portal 的 Interfaces 不含它），而后端缺失的接口 portal 根本不导出。

修复：安装 xdg-desktop-portal-gnome（它通过 org.gnome.Mutter.ScreenCast DBus API 实现 ScreenCast，而 niri 恰好实现了这套 API）：

```bash
sudo apt install xdg-desktop-portal-gnome
systemctl --user restart xdg-desktop-portal.service
```

- niri 包自带 /usr/share/xdg-desktop-portal/niri-portals.conf（`default=gnome;gtk;` + Access/Notification 走 gtk），会话为 niri 时自动优先 gnome 后端；**不需要手写配置文件**，缺的只是这个包本身
- 不要写 `default=gnome` 一刀切覆盖：AppChooser、Inhibit、Lockdown、Access 等接口 gtk 有而 gnome 没有，全钉 gnome 会让它们失去后端（DMS 的 Inhibit 等功能）

验证（无 GUI 即可确认链路就位）：

```bash
# 1. ScreenCast 接口应出现在列表里（安装前没有）
busctl --user introspect org.freedesktop.portal.Desktop /org/freedesktop/portal/desktop | grep -E 'ScreenCast|RemoteDesktop'
# 2. gnome 后端应已注册
busctl --user list | grep impl.portal
```

- OBS 添加「屏幕捕获 (PipeWire)」源时会弹出选择器（gnome 后端提供，niri 侧由 org.gnome.Mutter.ScreenCast + PipeWire 完成传输）
- 窗口捕获同理选「窗口捕获 (PipeWire)」；录制中光标可选择显示/隐藏

## 3. 说明

- OBS 是 X11/Wayland 都能跑的 Qt 程序；niri 下无需 XWayland
- 摄像头：插上即出现在「视频捕获设备」源列表；权限由 PipeWire 管（v4l2）
- 虚拟摄像头（给会议软件当摄像头用）依赖 v4l2loopback，本机未装；需要时：sudo apt install v4l2loopback-dkms，装完 modprobe v4l2loopback 并重启 OBS
