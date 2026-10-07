# WiFi

## 1. apt 安装 NetworkManager

```bash
sudo apt install -y network-manager
sudo sed -i 's/^managed=false/managed=true/' /etc/NetworkManager/NetworkManager.conf
sudo systemctl enable --now NetworkManager
```

### 1.1 ifupdown 抢占冲突

```bash
sudo systemctl stop ifup@wlo1.service

sudo systemctl mask 'ifup@.service'

sudo systemctl disable networking.service

sudo systemctl restart NetworkManager
```