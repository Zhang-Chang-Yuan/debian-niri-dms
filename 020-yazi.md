# Yazi

## 1. 安装（二进制）

从 GitHub release 下载官方二进制安装到 ~/.local/bin（见 017-bin.md），无需 sudo、无需 apt 源订阅。glibc 系统选 -gnu 压缩包（musl 为静态构建）：

```bash
mkdir -p ~/.local/bin

YAZI_VER=26.9.1
curl -fsSL -o /tmp/yazi.zip "https://github.com/sxyazi/yazi/releases/download/v${YAZI_VER}/yazi-x86_64-unknown-linux-gnu.zip"
unzip -q /tmp/yazi.zip -d /tmp/yazi-x
install -m755 /tmp/yazi-x/yazi-x86_64-unknown-linux-gnu/yazi ~/.local/bin/yazi
install -m755 /tmp/yazi-x/yazi-x86_64-unknown-linux-gnu/ya ~/.local/bin/ya
rm -rf /tmp/yazi.zip /tmp/yazi-x

yazi --version
```

- 压缩包同时含 ya（命令行 companion）、completions（zsh/fish 补全），可按需拷贝
- 升级只需改 YAZI_VER 重跑

## 2. griffo apt 源（不推荐）

deb.griffo.io 的 apt 源虽提供 yazi（trixie / 26.9.1-1~trixie），但 deb 包下载需要有效订阅凭据（实测返回 HTTP 401），且需 sudo 配置 keyring 与 sources；与官方二进制版本一致，无需使用。
