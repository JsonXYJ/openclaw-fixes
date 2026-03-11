# OpenClaw Fixes

OpenClaw 常见问题解决方案记录 / OpenClaw fixes and workarounds

---

## 🔧 问题：飞书插件重复 ID 警告

### 问题描述

启动 OpenClaw Gateway 时出现以下警告：

```
Config warnings:
- plugins.entries.feishu: plugin feishu: duplicate plugin id detected; later plugin may be overridden
```

重启后网关可能无法正常启动。

### 根本原因

用户通过 npm 安装了 `@openclaw/feishu` 插件到用户扩展目录：
```
~/.openclaw/extensions/feishu
```

但 OpenClaw 内置版本已存在于：
```
~/.npm-global/lib/node_modules/openclaw/extensions/feishu
```

两个插件目录都有相同的 `id: "feishu"`，导致 OpenClaw 插件扫描机制检测到重复 ID 冲突。

### 解决方案

删除用户安装的版本，使用内置版本：

```bash
# 删除用户安装的 feishu 插件
rm -rf ~/.openclaw/extensions/feishu

# 重启网关
openclaw gateway restart
```

### 验证

```bash
openclaw status
```

应显示：
- 网关：运行中
- 飞书通道：✅ ON + OK
- 无重复插件警告

### 建议

- **feishu 插件已内置**，无需通过 npm 单独安装
- 如确需使用自定义版本，请修改 `package.json` 中的插件 ID 避免冲突

---

## 🔐 安全建议

### 保护配置文件权限

```bash
# 限制配置文件权限（仅所有者可读写）
chmod 600 ~/.openclaw/openclaw.json

# 限制配置目录权限（仅所有者可访问）
chmod 700 ~/.openclaw
```

### GitHub Token 配置最佳实践

将敏感 token 放在技能专用配置项，而非根目录：

```json
{
  "skills": {
    "entries": {
      "gh-issues": {
        "apiKey": "your-github-token"
      }
    }
  }
}
```

**优势：**
- 最小权限原则 — 只有对应技能能读取
- 符合 OpenClaw 规范
- 避免环境变量泄露风险

---

## 📝 其他记录

_待添加..._

---

## 📌 关于

记录 OpenClaw 使用过程中遇到的问题及解决方案，帮助其他人快速排查类似问题。

**作者:** [@JsonXYJ](https://github.com/JsonXYJ)  
**许可证:** MIT
