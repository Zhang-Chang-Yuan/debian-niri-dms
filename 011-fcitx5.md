# Input Method

## 1. 安装

```bash
sudo apt install -y fcitx5 fcitx5-rime fcitx5-config-qt \
  librime-plugin-lua fcitx5-frontend-gtk3 fcitx5-frontend-gtk4 \
  fcitx5-frontend-qt5 fcitx5-frontend-qt6
```

## 2. Rime（默认简体）

```bash
mkdir -p ~/.local/share/fcitx5/rime

cat > ~/.local/share/fcitx5/rime/default.custom.yaml << 'EOF'
patch:
  "menu/page_size": 9
  schema_list:
    - schema: luna_pinyin
EOF

cat > ~/.local/share/fcitx5/rime/luna_pinyin.custom.yaml << 'EOF'
patch:
  switches:
    - name: simplification
      reset: 1
      states: [ 漢字, 汉字 ]
EOF
```

## 3. niri

```bash
cp ~/.config/niri/config.kdl ~/.config/niri/config.kdl.bak


nano ~/.config/niri/config.kdl
environment {
  XMODIFIERS "@im=fcitx"
  GTK_IM_MODULE "fcitx"
  QT_IM_MODULE "fcitx"
  SDL_IM_MODULE "fcitx"
}
```

```bash
cat >> ~/.config/niri/config.kdl << 'EOF'

spawn-at-startup "fcitx5" "-d"
EOF
```