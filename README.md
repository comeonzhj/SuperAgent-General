# SuperAgent General

基于 [DeepSeek Harness 官方 SuperAgent 原型](https://github.com/deepseek-ai/dsh-superagent-prototype) 的通用 DSH 插件。通过飞书机器人接收私聊和群聊消息，由常驻 Router 协调任务，在独立的 DSH Worker Session 中执行。

适用于调研、分析、写作、翻译、资料整理、文件操作及软件开发等工作。具体能力取决于 DSH 提供的工具、模型和用户授权。

## 相对官方原型的调整

| 项目 | 官方原型 | 当前插件 |
|---|---|---|
| 使用场景 | 单个 GitHub 仓库的维护与协作 | 默认面向通用任务；GitHub 作为可选来源 |
| 飞书接入 | 通过配置好的机器人 `lark-cli` 接入 | 填写 `APP_ID`、`APP_SECRET`，使用官方 SDK 建立长连接并调用 API |
| 初始化 | 需要配置仓库、CLI 账号和运行目录 | 自动提供默认数据目录与任务目录，模型可沿用 DSH 设置 |
| 消息过滤 | 来源群、是否被提及、发送者准入与模型评估 | 保留原型准入和评估，增加可配置的关注/忽略关键词；关键词变更纳入策略版本 |
| 默认要求 | 以代码修改、PR 和审查为中心 | 通用业务要求优先中文，GitHub 专用要求归入可选场景技能 |
| 场景定制 | 团队提示词与技能 | 复用同一机制，为翻译、项目分析、知识库维护等配置适用条件与交付要求 |
| 管理界面 | DSH 原生面板与自动轮询 | 继续复用，默认概览、执行设置前置、设置标签最后，配置说明使用 `?` 悬浮提示，介绍移至“关于” |
| 飞书资源操作 | 主要通过机器人 CLI | Worker 可通过应用身份的 API 工具处理获授权的消息、文档、知识库和表格等资源 |

## 保留的核心设计

沿用原型的 **Engine、Router/Worker 会话、Trigger/Thread、订阅直通、上下文按需读取、SQLite、Effect/Outbox 与恢复机制**。`src/core/` 保持与上游基线一致。

主面板可查看消息、判断、任务、线程与订阅，并自动更新。任务详情可以打开真实的 `worker-…` 执行会话，查看推理、工具调用和交付过程；Router 统一回复入口消息。

上游来源、基线提交和适配边界见 [UPSTREAM.md](docs/UPSTREAM.md)。

## 安装与配置

1. 从 [Releases](https://github.com/comeonzhj/SuperAgent-General/releases) 下载预编译的 `.tgz` 插件包。
2. 在 DSH 的插件页面安装压缩包，启用 `dsh-superagent-general`，打开侧边栏 **SuperAgent**。
3. 在最后的“设置”标签中连接飞书应用，选择接收的群与可交办任务的人。
4. 需要时在“更多设置”中添加团队技能，填写场景要求。

飞书应用需要启用机器人、长连接接收消息、相应消息权限并发布。文档与知识库操作还需目标资源权限。完整步骤和升级说明见 [使用指南](docs/USER-GUIDE.md)。

当前版本基于 DSH `0.2.1-alpha.2` 的接口开发，声明的宿主兼容范围见 [package.json](package.json)。Node.js 要求为 `^22.19.0` 或 `>=24`。

## 自包含与依赖

仓库包含插件源码、测试、提示词、技能、界面词条及构建配置，可从干净检出独立安装依赖、测试和构建。预编译安装包包含运行所需的 `lib/`、组合包配置和资源文件。

- **DSH 提供**：模型、Agent/Session、工作区、工具、权限和原生界面服务。
- **包管理器安装**：官方飞书 SDK、Zod、进程锁等声明在 `package.json` 中的依赖。
- **飞书应用提供**：机器人凭证及消息、文档等资源权限。

本地参考仓库和设计文章均属于开发输入，不参与安装、测试、构建和运行。GitHub 仓库与发布包只包含本项目所需文件。

## 从源码构建

```sh
git clone https://github.com/comeonzhj/SuperAgent-General.git
cd SuperAgent-General
pnpm install --frozen-lockfile
pnpm check
pnpm pack --pack-destination dist
```

`pnpm pack` 会先构建，再生成 `dist/dsh-superagent-general-<版本>.tgz`。

- [架构与开发入口](docs/ARCHITECTURE.md)
- [使用与升级说明](docs/USER-GUIDE.md)
- [相对上游的适配边界](docs/UPSTREAM.md)

## 验证与当前边界

已通过自动化测试、类型检查和构建，并验证隔离 DSH 中的安装、原生面板、自动更新和主会话入口。飞书真实消息与资源读写需要使用自己的应用权限完成联调。

从早期版本升级时会迁移接入与模型等配置；旧版任务库保留，历史任务暂未转换到当前原型领域模型。详见使用指南。

## 来源与许可

社区适配项目，采用 [MIT License](LICENSE)。保留 DeepSeek Harness Team 的上游版权与许可，感谢官方原型和设计文章的贡献。
