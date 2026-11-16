# Eta-custom

[Mangi-11/Eta](https://github.com/Mangi-11/Eta) 的个人定制仓库：**自动跟随上游最新版，保留三项定制功能——提示词与工具边界、会话列表多选、GitHub 账号连接**。其余一切以原版为准。

上游 Eta 是 Android 系统级 AI Agent（Agent Runtime、GUI Agent、终端、系统 API、Xposed 入口等），完整介绍见[原版 README](https://github.com/Mangi-11/Eta/blob/main/README.md)。

## 本仓库做了什么

基座为上游 `main` 最新提交，之上叠加三类定制：

- 从 [Oo0520/Eta-por](https://github.com/Oo0520/Eta-por) 移植「提示词与工具边界」与「GitHub 账号」两块（cherry-pick / 逐字移植，保留来源标记），并做适配使其在上游最新结构下编译运行
- 自研「会话列表多选批量删除」（上游与 por 均没有）
- 修复 sync-upstream 对 README 冲突的处理，让每日自动跟随真正无人值守

不引入 por 分支的其它定制（子智能体增强、虚拟屏小窗、流体云、语音增强等一概不包含）。

## 定制功能一览

### 提示词工作台

设置页新增「提示词工作台」入口（COUI 与 MIUIX 双风格均有），提供：

- **自定义提示词**：system 提示词 / 人格名 / 人格表达方式 / 追加指令四件套，可整体启用或停用；与角色扮演互斥处理
- **版本管理**：每次内容变更自动记版本，历史保留最近若干份，可一键回滚；仅改开关不升版本
- **预设**：内置 7 套（默认 / 极简 / 严谨 / 技术 / 编码 / 生活 / 研究），支持导入导出；schema 2 预设可携带工具集
- **请求快照**：每轮请求落一份快照（system 全文 + 工具描述全文，不含密钥与图片），保留份数 0–30 可调；两份快照可逐段对比，用于排查「实际发给模型的到底是什么」
- **当前生效提示词**：逐段展示最终 system 消息与工具清单——预览数字与实际请求共用同一构建入口，保证一致
- **完整性自检**：植入文件、注入框架（xposed / lspd / riru / zygisk）、护栏句子、越狱特征、版本一致性五项只读检查

### 工具边界

- **工具白名单**：按分类与自定义组合勾选可用工具；请求侧与预览侧共用同一目录构建，白名单外工具不进请求也不可执行
- **工具组**：可把常用勾选保存成组，按需切换
- **纯聊天档**：单模型独立设置工具档——纯聊天档不向模型声明任何工具（仍可读图），适合对话模型或省上下文
- **MCP 压缩**：MCP 工具 schema 单独压缩后注入，减少上下文占用

### 记忆注入兜底

MEMORY.md 缺少「# 核心记忆」标题时，取文件头部一段注入，不再静默返回空串；记忆注入修复与提示词功能耦合，一并保留。

### 会话列表多选

上游一次只能删一个会话，本仓库补上批量管理（COUI 与 MIUIX 双风格均有）：

- 长按任意会话 → 菜单选「多选」进入批量模式，行首出现勾选框
- 顶栏操作条：已选计数 / 全选与取消全选（跟随当前搜索过滤）/ 批量删除 / 退出
- 删除前弹统一确认框，逐条走原有删除链路（运行中的会话先停任务再删）
- 返回键退出多选，侧栏关闭自动清空选中

### GitHub 账号

设置页「MCP 服务器」之后新增「GitHub 账号」入口（移植自 Eta-por）：

- **设备码授权**：点「连接 GitHub」出 8 位码，浏览器输码即连，不用手动建 PAT
- **凭据安全**：token 经 Android Keystore AES-GCM 加密只存本机；断开即清除，GitHub 侧可随时撤销
- **自动注册 MCP**：连接后自动添加 GitHub 官方远程 MCP（api.githubcopilot.com/mcp/，Bearer），issue、PR、仓库与文件、搜索等工具即开即用
- **终端同步**：可选把同一份凭据写进 Linux 环境（.git-credentials / .gitconfig / gh hosts.yml），终端里 git clone/push 与 gh 命令直接可用

## 下载安装

- Release：[releases](https://github.com/fgvsuiye/Eta-custom/releases)，APK 为 R8 混淆 + 资源压缩的正式构建
- 需 Android 13+ ARM64 设备
- 包名与上游正式版相同（`io.github.mangi.eta`）但**签名不同**：装过官方正式版需先卸载；与官方 beta（`io.github.mangi.eta.beta`）可共存
- 使用需要自备模型 API Key（BYOK）或订阅登录，配置方法见[原版快速开始](https://github.com/Mangi-11/Eta/blob/main/README.md#快速开始)

## 自动跟随上游

`sync-upstream` 每日 UTC 03:00 自动执行：

- 上游无更新 → 静默通过
- 有更新 → 自动合并并推送，PR Check（lint + 全量单元测试 + 构建验证）把关
- 合并冲突 → 自动中止并开 `sync-conflict` issue 提醒人工处理，不会强推坏代码

想出新安装包时打一个 `v*` 标签，`Build APK` 会自动构建并以手写 Markdown 说明发布 Release。

## 从源码构建

```sh
git clone https://github.com/fgvsuiye/Eta-custom.git
cd Eta-custom
./gradlew assembleRelease
```

需要 JDK 25 与 Android SDK（详见原版文档）。仓库内置自用签名 `signing/eta-custom.jks`，未设置签名环境变量时按上游规则降级。

## 许可

继承上游 [PolyForm Noncommercial License 1.0.0](LICENSE)，版权与许可声明原样保留：

> Required Notice: Copyright © 2026 蛮吉 (Mangi-11).

遵守上游 README 的额外要求：不二次分发成品包、不贩卖、不接收费代装。本仓库 Release 仅为本人自用构建。
