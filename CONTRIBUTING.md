# 贡献指南（Contributing Guide）

> 欢迎来到湖北工程学院开源社团的第一个协作仓库！🎉
> 这份文档是**为第一次接触 GitHub 的新人写的**，请放心，跟着一步步做就能完成你的第一次开源贡献。

如果你对 Git 已经很熟，可以直接看[四、提交规范](#四提交规范commit-message-规范)和[五、发起 Pull Request](#五发起-pull-request)。

---

## 目录

- [一、你需要准备什么](#一你需要准备什么)
- [二、认识三个关键概念](#二认识三个关键概念)
- [三、标准工作流程（保姆级）](#三标准工作流程保姆级)
- [四、提交规范（Commit Message 规范）](#四提交规范commit-message-规范)
- [五、发起 Pull Request](#五发起-pull-request)
- [六、Review 与合并](#六review-与合并)
- [七、回退与常见问题](#七回退与常见问题)

---

## 一、你需要准备什么

| 项目 | 说明 | 获取方式 |
|:---:|:---|:---|
| GitHub 账号 | 用来 fork、发 PR | github.com 注册（免费） |
| Git | 版本控制工具 | Windows 装 Git for Windows；macOS / Linux 用包管理器 |
| 一个编辑器 | 写代码用 | VS Code（推荐）等 |

检查 Git 是否装好（在终端输入）：

```bash
git --version
```

能看到版本号就说明 OK。

---

## 二、认识三个关键概念

在动手前，先记住下面这张关系图。理解了它，后面每一步都会很清晰。

```text
GitHub 上的本仓库（HBEU-OSC/hello-open-source）
        │  ① Fork（复制一份到你的账号）
        ▼
你的仓库（你的账号/hello-open-source）── 这是"你的副本"
        │  ② Clone（下载到本地电脑）
        ▼
你本地电脑（真正写代码的地方）
```

### 2.1 Fork（分叉）

把**别人的仓库**复制一份到**你自己账号**下。Fork 出来的仓库是你的，你想怎么改都行，不会影响原仓库。

### 2.2 Clone（克隆）

把你账号下那个仓库下载到本地电脑，开始写代码。

### 2.3 Pull Request（PR，合并请求）

你改完代码后，**请求原仓库接受你的修改**。原仓库维护者 review 后合并，你的贡献就正式生效了。

> 一句话记忆：**Fork 是复制到自己名下，Clone 是下载到电脑，PR 是把你改的东西送回去。**

---

## 三、标准工作流程（保姆级）

### 第 1 步：Fork 仓库

1. 打开 `https://github.com/HBEU-OSC/hello-open-source`
2. 点击右上角 **Fork** 按钮
3. 等待几秒，你会进入一个 `你的账号/hello-open-source` 的新页面

### 第 2 步：Clone 到本地

复制你自己仓库的地址（页面上绿色 **Code** 按钮 → HTTPS），然后在终端运行：

```bash
git clone https://github.com/你的账号/hello-open-source.git
cd hello-open-source
```

> ⚠️ 一定要用**你自己账号**下的仓库地址，不是 HBEU-OSC 那个。

### 第 3 步：新建分支（重要！）

**永远不要直接在 main 分支上改代码。** 养成新建分支的习惯：

```bash
# 先确保在 main 分支
git checkout main

# 拉取最新代码
git pull origin main

# 新建一个分支，名字最好能说明你要干什么
git checkout -b add-score-statistics
```

分支命名建议：`类型/说明`，例如 `feature/win-rate`、`fix/font-bug`。

### 第 4 步：做出修改

用编辑器打开项目，修改代码或新增文件。改完后用 `git status` 看看改了什么：

```bash
git status
```

### 第 5 步：提交到本地（Commit）

```bash
# 把所有改动加入暂存区
git add .

# 用规范的格式提交
git commit -m "feat: 新增胜率统计功能"
```

> 提交信息规范见下一节，务必遵守。

### 第 6 步：推送到你的 GitHub（Push）

```bash
git push origin 你的分支名
```

> 第一次推送时，Git 可能提示你登录。Windows 下通常会自动弹出浏览器完成登录。

### 第 7 步：发起 Pull Request

1. 推送成功后，GitHub 页面会出现一个 **Compare & pull request** 黄色按钮，点击它
2. 填写标题和描述（参考[五、发起 Pull Request](#五发起-pull-request)）
3. 点击 **Create pull request**

到这里，你的第一次开源贡献就发出去了！🎉

---

## 四、提交规范（Commit Message 规范）

我们使用 **Conventional Commits（约定式提交）** 规范，格式如下：

```text
<类型>: <简短描述>
```

### 常用类型

| 类型 | 含义 | 示例 |
|:---:|:---|:---|
| `feat` | 新增功能 | `feat: 支持四条蛇对战` |
| `fix` | 修复 bug | `fix: 修复中文显示为方块的问题` |
| `docs` | 文档相关 | `docs: 更新 README 参与说明` |
| `refactor` | 重构（不改功能） | `refactor: 抽取公共的碰撞检测函数` |
| `style` | 格式（不影响逻辑） | `style: 调整代码缩进` |
| `test` | 新增 / 修改测试 | `test: 补充胜负判定测试` |

### 反例（不要这样写）

| ❌ 不建议 | ✅ 建议 |
|:---|:---|
| `update` | `feat: 新增胜率统计` |
| `aaa` | `docs: 修正 README 错别字` |
| `修改了一些东西` | `fix: 修复蓝蛇撞墙后仍移动的问题` |

---

## 五、发起 Pull Request

### 标题怎么写

直接用一句话概括你做了什么，例如：

```text
feat: 新增胜率统计功能
```

### 描述里写什么

至少要包含三部分：

```text
## 做了什么
简要说明你的改动。

## 怎么测试
说明如何运行和验证（如：python snake_game.py 跑 100 局）。

## 相关 Issue
如果认领了 Issue，写上 `closes #3`（会自动关闭对应 Issue）。
```

---

## 六、Review 与合并

- 你提交 PR 后，维护者（Owner 或核心成员）会来 review。
- 可能有三种结果：
  - **approved**：通过，等待合并。
  - **changes requested**：需要修改，你直接在本地改完再 push 即可，PR 会自动更新。
  - **comment**：只是讨论，可以回复交流。
- 合并一般由维护者点击 **Merge pull request** 完成。

---

## 七、回退与常见问题

### 7.1 改坏了怎么办？回到上一个版本

```bash
# 放弃某个文件的所有改动
git checkout -- 文件名

# 放弃所有本地改动（慎用！）
git reset --hard HEAD
```

### 7.2 Clone 后运行报错 "No module named 'pygame'"

```bash
pip install -r requirements.txt
```

### 7.3 提交信息写错了，怎么改？

```bash
git commit --amend -m "feat: 新的正确描述"
```

> 注意：只在还没有 push 的时候用 `--amend`。

### 7.4 push 被拒绝（因为你的 fork 落后于原仓库）

先把自己 fork 的 main 更新到最新，再基于它开新分支：

```bash
git remote add upstream https://github.com/HBEU-OSC/hello-open-source.git
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## 最后

有问题随时在群里问，或者在 Issues 里提问。**所有大神都是从第一次 PR 开始的，放心大胆地试。**

Happy Coding! 🐍
