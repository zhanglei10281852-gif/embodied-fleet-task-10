# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

恢复任务已经由 engineer-a 领取，engineer-b 使用自己的身份上报结果也被接受。请修复执行租约的所有者校验，让非领取者的上报明确失败。变更只能落在必要生产实现，测试文件保持不变，不得跳过所有权用例或削弱拒绝断言。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/embodied-fleet-task-10
- 仓库地址：https://github.com/zhanglei10281852-gif/embodied-fleet-task-10.git
- parent SHA：ffdc715be469b88ccb687d66759d539cd025d524

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/embodied-fleet-task-10.git bug-repro
cd bug-repro
git checkout --detach ffdc715be469b88ccb687d66759d539cd025d524
go test ./internal/assignment -run ^TestAssignment_ClaimWrongExecutorReject$ -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/assignment -run ^TestAssignment_ClaimWrongExecutorReject$ -count=1
--- FAIL: TestAssignment_ClaimWrongExecutorReject (0.01s)
    assignment_test.go:133: expected error reporting with wrong executor
FAIL
FAIL	github.com/zhanglei10281852-gif/embodied-fleet-go/internal/assignment	0.008s
FAIL

```

stderr：

```text
(empty)
```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/assignment -run ^TestAssignment_ClaimWrongExecutorReject$ -count=1
--- FAIL: TestAssignment_ClaimWrongExecutorReject (0.25s)
    assignment_test.go:133: expected error reporting with wrong executor
FAIL
FAIL	github.com/zhanglei10281852-gif/embodied-fleet-go/internal/assignment	0.462s
FAIL

```

stderr：

```text
(empty)
```

## 通过条件

任务由 engineer-a 领取后，engineer-b 上报执行结果必须被明确拒绝，原领取人的活动租约和所有权不能被非所有者覆盖。定向所有权测试须从修复前失败变为修复后通过，assignment 包及全量回归通过，不调整拒绝用例和错误断言。
