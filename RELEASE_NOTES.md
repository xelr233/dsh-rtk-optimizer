# Release Notes

每个版本的发布说明写在对应章节。发布 workflow 会优先使用本节内容作为 GitHub release notes；没有对应章节时自动从 git log 生成。

<!-- 发布新版本时，在顶部插入新章节，例如：
## v0.2.0

### Features

- ...

### Fixes

- ...

-->

## v0.1.1

### Fixes

- **hook 模式此前从未真正执行改写**：DSH 的 shell seam 是 `execute(spec)` → `handle.result()`，旧代码调用不存在的 `shell.run(spec)`，异常被 catch 吞掉后每条命令都静默退回原文（PTC 和普通模式都受影响）；现优先 `execute()`，并兼容只有 `run(spec)` 的 seam
- hook 模式执行改写命令时忽略 bash 调用自带的 `workdir`，一律在会话根目录执行（子目录仓库里的 `git status` 直接 128）；现在按工具的规则解析：绝对路径优先，相对路径拼到会话 cwd，并同步到返回值的 `cwd`
- PTC（`run_code`）模式下输出压缩不生效：压缩结果现在写回 bash 工具的 `value`（PTC 程序读的是 value，不是 content 块），大输出同样按 `maxOutputChars` 截断
- hook 引擎异常（非零退出 / 超时 / 非 JSON 输出）时回退 `rtk rewrite`，不再静默放行
- PTC 嵌套调用缺少 agent 会话头时，cwd 依次回退到 bash `workdir` 与最近一次观察到的 cwd

## v0.1.0

### Features

- RTK 命令改写建议：`bash` 调用自动探测 rtk，支持 `suggest`（结果附注改写命令）与 `rewrite`（deny + 反馈命令）双模式
- 输出压缩管线：ANSI 剥离、git status 汇总、测试输出聚合、构建错误过滤、grep 结果分组、智能截断
- `/rtk` 命令：show / verify / stats / set / reset，运行时切换模式与配置
- rtk 未安装时自动降级（原命令原样执行），探测结果缓存避免重复 probe
