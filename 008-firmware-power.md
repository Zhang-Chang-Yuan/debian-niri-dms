# Firmware & Power

```bash
sudo apt install -y intel-microcode firmware-linux firmware-misc-nonfree \
    firmware-intel-graphics firmware-sof-signed firmware-iwlwifi firmware-realtek

sudo apt install -y thermald fwupd

sudo systemctl enable --now thermald power-profiles-daemon fwupd
```