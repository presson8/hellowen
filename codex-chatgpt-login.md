---
layout: default
title: Codex 切换为 ChatGPT 官方登录操作指南
description: 清理旧的第三方模型配置，恢复 Codex 的 ChatGPT 官方账号登录。
---

# Codex 切换为 ChatGPT 官方登录操作指南

> 本文记录一次从 DeepSeek API Key 登录切换回 ChatGPT 官方账号登录的排查与处理流程。
>
> 注意：Codex 的配置项、桌面端界面和可用模型会随版本变化。文中的模型名称仅作示例，请以当前客户端实际显示结果为准。

## 一、问题描述

切换登录方式后，可能出现“终端显示登录成功，但桌面端模型下拉框仍显示 DeepSeek 模型”的情况。常见原因包括：

- `config.toml` 仍指定了 DeepSeek 提供商或模型；
- 用户目录中残留了自定义模型文件 `models.json`；
- 桌面端没有完全退出，旧的界面缓存仍在使用。

官方配置参考说明：用户级配置位于 `~/.codex/config.toml`，`forced_login_method` 可设置为 `chatgpt` 或 `api`。

## 二、操作前的安全检查

### 1. 处理旧凭证

如果旧的 DeepSeek API Key 曾经出现在截图、日志、聊天记录或其他公开位置：

1. 前往 DeepSeek 开发者后台，永久删除或废弃旧 API Key；
2. 检查近期用量和账单，确认没有异常调用；
3. 不要把 API Key、访问令牌或包含凭证的配置文件提交到 GitHub。

### 2. 备份配置文件

修改前先备份：

`C:\Users\wswwwynl\.codex\config.toml`

如果 Windows 用户名或 Codex 数据目录不同，请替换为实际路径。

## 三、具体操作步骤

### 步骤 1：修改 `config.toml`

打开 `C:\Users\wswwwynl\.codex\config.toml`，删除或注释掉以下强制使用 DeepSeek 的配置：

- `model = "deepseek-v4-..."`
- `model_provider = "deepseek"`
- `preferred_auth_method = "apikey"`
- `forced_login_method = "api"`
- `model_catalog_json = "C:/Users/wswwwynl/.codex/models.json"`

然后添加：

```toml
forced_login_method = "chatgpt"
```

删除底部的自定义提供商区块：

```toml
[model_providers.deepseek]
# 以及该区块下的所有配置
```

提示：`preferred_auth_method`、`model_catalog_json` 等项目可能来自旧版本、第三方配置或个人定制。若当前版本不识别它们，请以官方配置参考为准；修改前务必保留备份。

### 步骤 2：清理自定义模型文件

进入 `C:\Users\wswwwynl\.codex\`，找到 `models.json`，将其删除或重命名为 `models.json.bak`。如果文件不存在，可以继续下一步。

### 步骤 3：使用绝对路径重新登录

打开 PowerShell，将路径替换为本机实际的 Codex 可执行文件路径：

```powershell
# 1. 登出当前账号
& "C:\Users\wswwwynl\AppData\Local\OpenAI\Codex\bin\cdef5aaf3e41ab53\codex.exe" logout

# 2. 发起 ChatGPT 官方登录
& "C:\Users\wswwwynl\AppData\Local\OpenAI\Codex\bin\cdef5aaf3e41ab53\codex.exe" login
```

浏览器会打开登录和授权页面。使用目标 ChatGPT 账号完成授权，终端出现 `Successfully logged in` 后继续。

如果当前 CLI 支持登录状态检查，也可以执行：

```powershell
& "C:\Users\wswwwynl\AppData\Local\OpenAI\Codex\bin\cdef5aaf3e41ab53\codex.exe" login status
```

### 步骤 4：彻底重启桌面端

1. 关闭 Codex 桌面端窗口；
2. 按 `Ctrl + Shift + Esc` 打开任务管理器；
3. 结束仍在运行的 Codex、Electron 或相关 Codex 进程；
4. 重新从开始菜单启动 Codex。

不要结束与其他重要应用无关的系统进程；如果进程名称不确定，先退出桌面端并重新启动应用。

## 四、验证结果

重新打开桌面端后：

1. 点击“选择模型”下拉框；
2. 确认 DeepSeek 选项已经消失；
3. 确认列表显示当前账号和当前客户端可用的 OpenAI 官方模型；
4. 如果仍有残留，进入 Settings 手动选择官方模型后重启一次应用。

模型名称和可用范围可能因账号、地区、产品版本和工作区策略不同而变化，不要把某一组模型名称当作固定验证标准。

## 五、仍未解决时的排查顺序

1. `config.toml` 中是否仍存在 `model_provider = "deepseek"`；
2. 是否仍然指定了 DeepSeek 模型；
3. `[model_providers.deepseek]` 区块是否已删除；
4. `models.json` 是否仍然存在；
5. 是否真的结束了 Codex 桌面端相关进程；
6. PowerShell 使用的是否是实际正在运行的 Codex 可执行文件；
7. 当前登录的是否为目标 ChatGPT 账号；
8. 是否有组织或设备策略限制可用的登录方式或模型。

## 六、参考资料

- [OpenAI Codex 配置参考](https://learn.chatgpt.com/docs/config-file/config-reference)
- [OpenAI Codex 官方文档](https://developers.openai.com/learn/codex)
- [morethan-log 主题仓库](https://github.com/morethanmin/morethan-log)

