# obs-studio

## 1. 安装

```bash
sudo apt install obs-studio
```

- Debian trixie 源，30.2.3；连同依赖约 200 个包（libobs、浏览器源、PipeWire 封装等），一把装完无需额外配置
- 桌面项 com.obsproject.Studio.desktop，图标随包装入 hicolor（含 256x256/512x512 PNG），装完即出现在启动器
- 无头验证：obs --version

## 2. Wayland 录屏链路（本机可用）

录桌面/窗口走 PipeWire，链路已就位：

```bash
busctl --user list | grep -E 'Mutter.ScreenCast|portal.Desktop'
```

- niri 自己注册 org.gnome.Mutter.ScreenCast（busctl 可见，screencast 由 PipeWire 完成，占 pid 为 niri）
- xdg-desktop-portal + xdg-desktop-portal-gtk 承接转接；OBS 添加「屏幕捕获 (PipeWire)」源时弹出的选择器即来自该链路
- 窗口捕获同理选「窗口捕获 (PipeWire)」；录制中光标可选择显示/隐藏

## 3. 说明

- OBS 是 X11/Wayland 都能跑的 Qt 程序；niri 下无需 XWayland
- 摄像头：插上即出现在「视频捕获设备」源列表；权限由 PipeWire 管（v4l2）
- 虚拟摄像头（给会议软件当摄像头用）依赖 v4l2loopback，本机未装；需要时：sudo apt install v4l2loopback-dkms，装完 modprobe v4l2loopback 并重启 OBS
