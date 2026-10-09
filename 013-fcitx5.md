# Input Method

## 1. 安装

sudo apt install -y fcitx5 fcitx5-rime fcitx5-config-qt \
  librime-plugin-lua fcitx5-frontend-gtk3 fcitx5-frontend-gtk4 \
  fcitx5-frontend-qt5 fcitx5-frontend-qt6

## 2. Rime

mv ~/.local/share/fcitx5/rime ~/.local/share/fcitx5/rime.bak.$(date +%F-%H%M%S) 2>/dev/null

git clone --depth=1 https://github.com/iDvel/rime-ice.git ~/.local/share/fcitx5/rime

cat > ~/.local/share/fcitx5/rime/default.custom.yaml << 'EOF'
patch:
  "menu/page_size": 9
  schema_list:
    - schema: rime_ice
  ascii_composer:
    good_old_caps_lock: true
    switch_key:
      Shift_L: commit_code
      Shift_R: commit_code
      Control_L: noop
      Control_R: noop
      Caps_Lock: clear
      Eisu_toggle: clear
EOF

cat > ~/.local/share/fcitx5/rime/rime_ice.custom.yaml << 'EOF'
patch:
  melt_eng/initial_quality: 1.0
EOF

## 3. niri

cp ~/.config/niri/config.kdl ~/.config/niri/config.kdl.bak

# 手动编辑 config.kdl，添加 environment 块
nano ~/.config/niri/config.kdl
environment {
  XMODIFIERS "@im=fcitx"
  GTK_IM_MODULE "fcitx"
  QT_IM_MODULE "fcitx"
  SDL_IM_MODULE "fcitx"
}

cat >> ~/.config/niri/config.kdl << 'EOF'

spawn-at-startup "fcitx5" "-d"
EOF

## 4. 重启并部署

pkill fcitx5 && sleep 1 && fcitx5 -d &

# 首次部署后，触发二次部署
touch ~/.local/share/fcitx5/rime/rime_ice.schema.yaml
pkill fcitx5 && sleep 1 && fcitx5 -d &
