# Base Pack

```bash
sudo apt install curl wget unzip 
```

## 1. 已卸载的系统组件

### vim（vim-common + vim-tiny）

启动器出现 Vim 图标且无使用场景（编辑器用 nano / VSCode，见 014-vscode.md）：

```bash
sudo apt remove vim-common
```

- vim.desktop 属于 vim-common；vim-common 只被 vim-tiny 依赖，卸载时连带移除，不牵连其他包
- 副作用：vi 命令一并消失（vim.tiny 是其提供者）；editor 主选项仍为 /bin/nano
- 恢复：sudo apt install vim-tiny