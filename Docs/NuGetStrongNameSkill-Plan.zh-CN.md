# NuGetStrongName Skill — 实施计划

本文为[英文权威计划](NuGetStrongNameSkill-Plan.md)的中文翻译，两份文档同步维护。

Status: pending
Slice tag: `nuget-strongname`
Delivery: 按切片完成验收及独立评审，经明确授权提交 Skill 和双语计划；等待提交保持 `in progress`。本计划不授权实施、提交或发布。

## 目标与继续方式

创建可复用的 `nuget-strongname` Skill，在任意 .NET 仓库中驱动 `NuGetStrongName.Tool` CLI。CLI 的行为及发布由 [idiotsky/NuGetStrongName](https://github.com/idiotsky/NuGetStrongName)（`Docs/NuGetStrongName-Plan.md`）负责，本计划只维护 Skill。使用共享 `continue-plan` 技能，每次继续一个切片或交付收尾；使用 `pending`、`in progress`、`done`，记录实际命令、结果、阻塞和下一步。

## 切片

| # | 结果 | Status | 依赖 |
|---|------|--------|------|
| 1 | 通用 Skill，下载固定版本工具并校验哈希，在 Windows 和 Linux 上验证 | pending | NuGetStrongName 切片 1 及其首个 GitHub Release |

### Slice 1 — 创建通用 `nuget-strongname` Skill

范围与分发：
- CLI 验收通过并已有 GitHub Release 后，使用 `skill-creator` 在本仓库创建 `nuget-strongname/`，按 `writing-for-agents` 指导编写英文说明。本仓库即已安装的 skill 根目录，无需另存源文件或同步副本。不在 Skill 中重复实现 CLI 的下载、签名或打包逻辑。
- 在 Skill 自身文件中记录固定工具版本及其 SHA-256。不向本仓库提交工具 `.nupkg`、密钥、凭据或第三方待签名包。
- 首次使用时，从固定的 `v<version>` GitHub Release 下载 `NuGetStrongName.Tool.<version>.nupkg` 至可写的按版本缓存目录，用 Skill 中记录的值校验 SHA-256（不能只用同一 Release 下载的清单校验），再从缓存的本地源以该版本安装到独立 `--tool-path` 并调用。后续运行复用已校验的缓存。不执行全局安装，不修改消费仓库，不下载其他版本。哈希不符或下载失败时直接失败并给出明确诊断，不回退到其他来源。
- Release 可公开访问时直接用 `https` 下载；需要认证时使用 `gh release download` 及用户已有的 GitHub 登录。不在 Skill 中嵌入令牌。
- 安装前检查 .NET SDK/运行时前置条件。使用平台无关说明，自动识别 Windows/Linux；不要求 PowerShell，不硬编码 Windows 专用路径。同一目录可放在任意环境实际的 skill 根目录下使用。

必须保留的行为：
- 将 Skill 定位为通用 NuGet strong-name 工作流，适用于任意 .NET 仓库，不要求特定的项目名、目录布局、目标框架或包源。
- 说明真实 CLI 调用方式、包 ID/明确版本及本地输入、依赖映射、JSON 结果和退出码、验证及失败诊断。
- 使用消费仓库有效的 NuGet 配置、源映射及认证；版本由用户明确提供，或在用户要求最新版时查询稳定版并固定。沿用项目现有包管理方式，兼容中央版本管理与项目内 PackageReference；不假设固定文件路径。
- 包命名和依赖映射优先读取项目已有约定或用户输入，`-signed` 只是输出命名示例，不作为所有项目的硬性规则。默认只检查和生成包；仅在用户请求修改项目引用时，才做最小改动并验证消费者。
- 除固定的 Release 坐标外，不硬编码 .NET 11、包列表、用户目录、仓库名或机器专用路径；通用操作不必都扩展为 CLI 的新能力，不为尚未出现的场景引入插件框架或抽象层。
- 不写死密钥文件名或输出目录。需要签名时，在目标仓库搜索可用的 strong-name 私钥，并结合求值后的项目签名配置识别应使用的密钥。不暴露密钥内容，验证其可用性；找不到时提示并请用户提供，多个候选仍有歧义时请用户选择。不生成新密钥、不静默改变身份。通过 CLI 的 `--key-file` 显式传入选定密钥。
- 输出目录从用户当前指令或仓库显式设置获取，均未提供时询问用户；始终通过 `--output` 显式传入，消费仓库的已知输出选择作为任务输入传递。已强命名直接返回的路径无需选择密钥或询问输出目录。
- 说明 `already-strong-named` 成功结果：直接使用返回的原包身份，跳过签名包生成；从自制签名版切回原版后验证消费者。
- 不自动发布包、不绕过许可检查；不声称仅凭 CLI 退出成功即完成产物验证。

验收：
- Skill 校验通过；按照 Skill 完成一个真实包的下载、签名、打包及验证，并验证失败场景的非零退出和诊断。
- 验证密钥搜索、找不到或存在歧义时的提示、显式输出选择，以及已签名时无多余询问；Skill 说明不含固定密钥或输出路径。
- 在 Windows 和 Linux 上验证首次下载及哈希校验、缓存复用不重复下载、包被篡改或哈希不符时直接失败。
- 在另一个不同目录和签名配置的最小 .NET 仓库中验证同一 Skill 可复用，且不修改无关包引用或全局配置。
- 消费仓库旧工具的退役不属于本计划。

Resume: 等待 NuGetStrongName 切片 1 验收及其首个 GitHub Release；尚未创建 Skill。

## 验证与交接记录

尚未创建 Skill，未执行下载、测试、提交或发布。下一步：CLI 首个 Release 发布后从切片 1 开始。
