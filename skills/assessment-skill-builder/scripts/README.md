# Scripts

本目录放可选的确定性辅助脚本，例如：

- 跨平台 JSON schema validator
- topic_id 重复检查脚本
- metadata / registry 一致性检查脚本
- examples 最小覆盖检查脚本

原则：

- 只有在需要稳定重复执行时才加入。
- 脚本必须有最小测试或可运行样例。
- 不把平台专用流程写死在脚本里。
- 不引入非必要的外部依赖。
