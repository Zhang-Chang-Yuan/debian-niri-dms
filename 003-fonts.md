# Fonts

## 1. 安装

```bash
sudo apt install fonts-jetbrains-mono fonts-noto-mono fonts-noto-cjk fonts-noto-color-emoji
```

## 2.

```bash
sudo dpkg-reconfigure locales
```

## 3. 

```bash
sudo tee /etc/fonts/local.conf >/dev/null <<'EOF'
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <alias binding="strong">
    <family>monospace</family>
    <prefer>
      <family>JetBrains Mono</family>
      <family>Noto Mono</family>
      <family>Noto Sans Mono CJK SC</family>
    </prefer>
  </alias>
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Noto Sans CJK SC</family>
    </prefer>
  </alias>
</fontconfig>
EOF

fc-cache -fv
```