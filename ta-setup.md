---
name: ta-setup
description: 助教本地仓库一键部署 / 内容同步。助教第一次使用时全程引导注册 GitHub、Fork 仓库、配置本地环境、生成入组申请消息。需要同步最新内容时，助教说「同步」即可一键更新。
---

# 助教一键部署 Skill

## 触发条件

- **首次部署**：助教说"帮我安装"、"帮我设置"、"我要部署"、"第一次使用"
- **立刻同步**：助教说"同步"、"更新"、"立刻同步"

两种触发分别进入不同流程，见下方说明。

---

---

## 流程 A：立刻同步最新内容（收到管理员通知时，或想立刻更新）

### 判断是否在仓库目录中

运行：

```bash
git remote -v 2>&1
```

如果没有 `upstream`，先绑定上游：

```bash
git remote add upstream https://github.com/longmenjihua/longmen-ta-template.git
```

### 从母版同步所有内容（保留每日工作目录）

```bash
git fetch upstream main
git ls-tree -r --name-only upstream/main | grep -vE "^(每日输出|每日反馈|每日解答|每日补充|\.github|Skill|模式识别库)/" | while IFS= read -r f; do
  git checkout upstream/main -- "$f" 2>/dev/null || echo "跳过: $f"
done
git add -A
git diff --staged --quiet || git commit -m "sync: 手动同步母版最新内容"
git push
```

输出：

```
同步完成！最新内容已更新到你的本地。
你的每日输出、每日解答、每日补充、每日反馈目录保持不变。
```

---

## 流程 B：首次部署

### Step 1：欢迎与说明

输出以下欢迎语：

```
你好！我来帮你完成龙门计划的本地环境配置。

整个过程大约需要 5 分钟，我会一步步带你完成。
你只需要跟着我的指引操作，遇到问题随时告诉我。

我们开始吧！
```

---

### Step 2：收集基本信息

询问助教：

```
首先告诉我你的姓名（例如：张三）
```

等助教回复后记录姓名，继续下一步。

---

### Step 3：引导注册 GitHub 账号

输出：

```
第一步：注册 GitHub 账号

1. 打开浏览器，访问：https://github.com/signup
2. 用你的邮箱注册一个账号
3. 注册完成后，告诉我你的 GitHub 用户名（就是你设置的那个英文名）
```

等助教回复 GitHub 用户名后，记录用户名，继续下一步。

---

### Step 4：引导 Fork 仓库

输出以下内容（将 `[姓名]` 替换为助教实际姓名）：

```
第二步：Fork 仓库

1. 打开浏览器，访问：https://github.com/longmenjihua/longmen-ta-template
2. 点击右上角的「Fork」按钮
3. Repository name 改成：longmen-ta-[姓名]（例如：longmen-ta-张三）
4. 点击「Create fork」

完成后告诉我「好了」
```

等助教确认后继续。

---

### Step 5：引导 Clone 到本地

输出（将 `[GitHub用户名]` 和 `[姓名]` 替换为实际值）：

```
第三步：把仓库下载到本地

在终端（Mac）或命令提示符（Windows）中运行：

git clone https://github.com/[GitHub用户名]/longmen-ta-[姓名].git

下载完成后，进入文件夹：

cd longmen-ta-[姓名]

然后在这个文件夹里重新打开 Claude Code，再运行此 Skill 继续配置。
```

**判断**：如果助教已经在正确的仓库目录中（当前目录包含 `config.json`），跳过 clone 步骤，直接继续 Step 6。

---

### Step 6：配置姓名

使用 Edit 工具修改当前目录下的 `config.json`，将 `ta_name` 字段更新为助教提供的姓名。

确认修改后告知：

```
已将你的姓名「[姓名]」写入配置文件。
```

---

### Step 7：配置 Git 用户信息

运行命令检查 git 是否已配置：

```bash
git config user.name
```

如果没有输出，运行：

```bash
git config --global user.name "[姓名]"
git config --global user.email "[GitHub用户名]@users.noreply.github.com"
```

---

### Step 8：开启自动同步

输出（将 `[GitHub用户名]` 和 `[姓名]` 替换为实际值）：

```
第四步：开启自动同步

1. 打开浏览器，访问你的仓库：
   https://github.com/[GitHub用户名]/longmen-ta-[姓名]/actions

2. 页面上会出现一个黄色提示栏，点击「I understand my workflows, go ahead and enable them」

3. 完成后告诉我「开启了」
```

等助教确认后，输出：

```
自动同步已开启！

以后管理员每次更新内容，
你这边每天早上 8:00 会自动同步过来，完全不需要你做任何操作。
你的每日输出、解答、补充、反馈这四个工作目录永远不会被影响。
```

---

### Step 9：安装自动上传脚本

运行以下命令检测操作系统：

```bash
uname -s 2>/dev/null || echo "Windows"
```

**Mac（输出 Darwin）**：

```bash
bash install_mac.sh
```

**Windows**：

输出：

```
检测到你使用的是 Windows 系统。

请右键点击文件夹里的「install_windows.bat」，选择「以管理员身份运行」。

完成后告诉我「安装好了」。
```

等助教确认后继续。

---

### Step 10：生成入组申请消息

输出以下消息（替换实际信息），并告诉助教把这段话发给管理员：

```
配置完成！请把下面这段话复制，发给管理员：

---
【龙门计划 - 新助教入组申请】
姓名：[姓名]
GitHub 用户名：[GitHub用户名]
仓库地址：https://github.com/[GitHub用户名]/longmen-ta-[姓名]
---

发送后等管理员确认，你就正式加入了。
```

---

### Step 11：完成提示

```
你这边已经全部配置好了！

最后一步：打开 VS Code 开始使用

1. 在电脑上找到刚才下载的文件夹「longmen-ta-[姓名]」
2. 右键点击这个文件夹 → 选择「用 VS Code 打开」
   （或者打开 VS Code → 文件 → 打开文件夹 → 选择 longmen-ta-[姓名]）
3. 打开后，在 VS Code 底部的 Claude Code 对话框里直接发题目就能用了

⚠️ 重要：每次使用都必须在 VS Code 里打开这个文件夹，否则系统无法正常工作。

以后每天的使用方式：
- 打开 VS Code，打开这个文件夹
- 把学生的题目直接发给 Claude Code
- 每天 23:00 会自动上传，不需要额外操作

如需立刻同步最新内容，在 Claude Code 里说「同步」即可。

有任何问题随时告诉我！
```

---

## 异常处理

**git clone 失败**：检查网络，确认仓库地址是否正确，建议助教重新 Fork。

**install_mac.sh 报错**：运行 `xcode-select --install` 安装 Git，再重试。

**Windows 安装失败**：确认以管理员身份运行，如仍失败联系管理员。
