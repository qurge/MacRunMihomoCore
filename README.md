# Mac 运行 Mihome 内核

## 版本信息

- 使用设备：Apple M4 Pro
- 系统版本：macOS 15.5
- mihomo 版本：mihomo-darwin-arm64-v1.19.31.gz

## 教程说明

- 将仓库中的`app.mihomomaccore.helper.plist`放到系统级别的`/Library/LaunchDaemons`目录
- 下载`mihomo-darwin-arm64-v1.19.31.gz`解压后放到你喜欢的目录，使用`chmod`给文件可执行权限，打开可能需要去`隐私与安全性`
  里面打开

## 使用快捷指令启动服务

需要用 mac 新建快捷指令，选择`运行 Shell 脚本`，脚本填写如下内容

```shell
PLIST_PATH="/Library/LaunchDaemons/app.mihomomaccore.helper.plist"
LABEL="app.mihomomaccore.helper"

# 判断 system 域服务是否存在，存在则 bootout 停止卸载
if sudo launchctl print "system/$LABEL" 2>/dev/null; then
    echo "服务 $LABEL 已存在，执行停止卸载..."
    sudo launchctl bootout "system/$LABEL"
    echo "旧服务已清理"
else
    echo "不存在旧服务，跳过停止"
fi

# 清理二进制隔离属性，屏蔽不存在属性的报错
sudo xattr -d com.apple.quarantine /Users/kodo/.config/mihomo/mihomo-darwin-arm64 2>/dev/null
# 二进制添加执行权限
sudo chmod +x /Users/kodo/.config/mihomo/mihomo-darwin-arm64

# plist 权限修正
sudo chown root:wheel "$PLIST_PATH"
sudo chmod 644 "$PLIST_PATH"
# 清理plist隔离属性
sudo xattr -d com.apple.quarantine "$PLIST_PATH" 2>/dev/null
# 加载新服务
sudo launchctl bootstrap system "$PLIST_PATH"
```

## 使用快捷指令卸载服务

需要用 mac 新建快捷指令，选择`运行 Shell 脚本`，脚本填写如下内容

```shell
sudo launchctl bootout system/app.mihomomaccore.helper
```

## 其他

可以搭配管理面板进行管理，查看等操作

配置文件添加以下内容

```yaml
external-controller: 127.0.0.1:9090
external-ui: ui
external-ui-url: "https://github.com/Zephyruso/zashboard/releases/latest/download/dist.zip"
```

打开网址`http://127.0.0.1:9090/ui `进行查看