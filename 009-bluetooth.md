# Bluetooth

## 1. 安装

```bash
sudo apt install -y bluez libspa-0.2-bluetooth

sudo systemctl disable bluetooth.service
sudo systemctl start bluetooth.service
sudo systemctl stop bluetooth.service
```

## 2. 适配

```bash
sudo systemctl start bluetooth.service

bluetoothctl power on && bluetoothctl scan on
```