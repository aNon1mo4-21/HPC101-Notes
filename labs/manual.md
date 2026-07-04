# HPC101 笔记工作流

> 目标：同一个文件夹同时作为 Obsidian Vault、VS Code Workspace、Git Repository。  
> 日常流程：课程网站 → Obsidian 记笔记 → VS Code 写代码/脚本 → Git 保存版本 → GitHub 同步。

---

## 0. 当前仓库位置

你的本地仓库路径是：

```powershell
D:\ZJU\Turing\P01_Courses\2026_Spring\HPC101\HPC101_notes
```

每次开始前，先进入这个目录：

```powershell
cd D:\ZJU\Turing\P01_Courses\2026_Spring\HPC101\HPC101_notes
```

如果不确定自己在哪里：

```powershell
pwd
```

---

## 1. 只需要做一次的 Git 代理设置

你这次遇到的错误是：

```text
schannel: failed to receive handshake, SSL/TLS connection failed
```

原因是 Git 没有走你的本地代理。已经通过下面的命令解决：

```powershell
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```

以后一般不用重复设置。

检查当前 Git 代理：

```powershell
git config --global --get http.proxy
git config --global --get https.proxy
```

如果以后换了代理端口，比如不是 `7890`，就重新设置：

```powershell
git config --global http.proxy http://127.0.0.1:你的端口
git config --global https.proxy http://127.0.0.1:你的端口
```

如果以后不想让 Git 走代理：

```powershell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

---

## 2. 每次开始做笔记前

### 2.1 打开代理 / VPN

如果 GitHub 需要代理才能访问，先打开你的代理软件，并确认端口仍然是：

```text
127.0.0.1:7890
```

### 2.2 打开 PowerShell 并进入仓库

```powershell
cd D:\ZJU\Turing\P01_Courses\2026_Spring\HPC101\HPC101_notes
```

### 2.3 查看当前 Git 状态

```powershell
git status
```

如果显示：

```text
nothing to commit, working tree clean
```

说明当前没有未保存的修改。

### 2.4 从 GitHub 拉取最新版本

如果你只在这一台电脑上记笔记，这步通常不会有变化，但建议养成习惯：

```powershell
git pull
```

如果出现冲突，不要急着乱改，先看：

```powershell
git status
```

---

## 3. 打开工作环境

### 3.1 用 VS Code 打开仓库

在仓库目录下执行：

```powershell
code .
```

如果 `code .` 不能用，就手动打开 VS Code：

```text
File → Open Folder → 选择 HPC101_notes 文件夹
```

### 3.2 用 Obsidian 打开同一个文件夹

打开 Obsidian，选择这个 Vault：

```text
D:\ZJU\Turing\P01_Courses\2026_Spring\HPC101\HPC101_notes
```

注意：Obsidian 和 VS Code 必须打开同一个文件夹。

---

## 4. 开始写一节 Lecture 笔记

### 4.1 新建 Lecture 文件

假设今天学 Lec03。

可以在 Obsidian 里新建：

```text
lectures/Lec03.md
```

也可以在 PowerShell 里创建：

```powershell
ni lectures\Lec03.md
```

如果文件已经存在，不要重复创建，直接打开即可。

### 4.2 插入 Lecture 模板

在 Obsidian 中打开 `lectures/Lec03.md`，插入模板：

```text
Ctrl + P → Templates: Insert template → lecture-template
```

然后把标题改成类似：

```md
# Lec03 - 标题
```

### 4.3 记笔记时只写这些内容

不要复制课程网站全文。每节课主要记录：

```text
1. 课程链接
2. 本节核心问题
3. 关键词
4. 重要概念
5. 重要命令 / 代码
6. 我自己的理解
7. 不懂的问题
8. 可以放进 cheatsheet 的内容
```

建议边学边写，不要等全部看完再整理。

---

## 5. 开始写 Lab 笔记

### 5.1 创建 Lab 文件夹

假设今天做 Lab03。

```powershell
mkdir labs\Lab03
mkdir labs\Lab03\code
mkdir labs\Lab03\results
ni labs\Lab03\notes.md
ni labs\Lab03\submit.slurm
```

最终结构应该是：

```text
labs/
└─ Lab03/
   ├─ notes.md
   ├─ submit.slurm
   ├─ code/
   └─ results/
```

### 5.2 插入 Lab 模板

在 Obsidian 或 VS Code 中打开：

```text
labs/Lab03/notes.md
```

插入 `lab-template.md` 的内容。

### 5.3 写代码

代码放在：

```text
labs/Lab03/code/
```

例如：

```powershell
ni labs\Lab03\code\main.c
```

### 5.4 写 Slurm 脚本

Slurm 作业脚本放在：

```text
labs/Lab03/submit.slurm
```

典型内容示例：

```bash
#!/bin/bash
#SBATCH --job-name=lab03
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --time=00:10:00
#SBATCH --output=slurm-%j.out

module purge
# module load xxx

./main
```

实际参数以课程网站和学校超算要求为准。

### 5.5 记录运行命令

无论是在本地还是超算上跑，都要把命令写进 `notes.md`。

例如：

```bash
module load gcc
cd labs/Lab03/code
gcc -O2 main.c -o main
sbatch ../submit.slurm
```

### 5.6 保存结果

输出文件、csv、日志等放到：

```text
labs/Lab03/results/
```

如果是 Slurm 输出，例如：

```text
slurm-123456.out
```

建议复制重要结果到 `notes.md`，不要只依赖输出文件。

---

## 6. 遇到报错时怎么记

### 6.1 新建 Bug 笔记

如果是一个值得以后复用的错误，就在 `bugs/` 里新建文件。

例如：

```powershell
ni bugs\git-ssl-tls-handshake-failed.md
```

### 6.2 Bug 笔记内容

按这个格式写：

```md
# Bug - Git SSL/TLS handshake failed

## 报错信息

```text
schannel: failed to receive handshake, SSL/TLS connection failed
```

## 出现场景

Windows PowerShell 中执行 `git push` 或 `git ls-remote` 时出现。

## 原因

Git 没有走本地代理，导致无法和 GitHub 完成 SSL/TLS 握手。

## 解决方法

```powershell
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```

## 验证

```powershell
git ls-remote https://github.com/aNon1mo4-21/HPC101-Notes.git
git push
```
```

---

## 7. 把高频内容整理到 Cheatsheet

课程笔记和 Lab 笔记是详细记录，Cheatsheet 只放高频内容。

例如 Slurm 常用命令写到：

```text
cheatsheets/slurm.md
```

常见内容：

```md
# Slurm Cheatsheet

## 查看自己的队列

```bash
squeue -u $USER
```

## 提交作业

```bash
sbatch submit.slurm
```

## 取消作业

```bash
scancel JOBID
```

## 查看作业详情

```bash
scontrol show job JOBID
```
```

原则：

```text
lecture/lab 里出现多次，或者以后肯定会查的，才放进 cheatsheet。
```

---

## 8. 插入图片 / 课件 / 附件

### 8.1 图片

Obsidian 的图片附件路径设置为：

```text
assets/images
```

所以截图、拖入的图片会放到：

```text
assets/images/
```

在 Markdown 中引用形式类似：

```md
![[example.png]]
```

### 8.2 PDF

课程 PDF 建议手动放到：

```text
assets/pdfs/
```

如果 PDF 很大，不一定要提交到 GitHub。可以在 `.gitignore` 中忽略：

```gitignore
assets/pdfs/*.pdf
```

---

## 9. 做完笔记后的 Git 保存流程

每次做完笔记，按照下面顺序执行。

### 9.1 查看改了什么

```powershell
git status
```

### 9.2 查看具体修改

```powershell
git diff
```

如果 `git diff` 太长，可以直接跳过，但建议至少看一眼 `git status`。

### 9.3 添加所有修改

```powershell
git add .
```

### 9.4 再检查一次

```powershell
git status
```

确认要提交的文件没问题。

### 9.5 提交

根据今天做的事写 commit message。

Lecture：

```powershell
git commit -m "add lec03 notes"
```

Lab：

```powershell
git commit -m "add lab03 notes and code"
```

Cheatsheet：

```powershell
git commit -m "update slurm cheatsheet"
```

Bug：

```powershell
git commit -m "record git proxy issue"
```

### 9.6 推送到 GitHub

第一次你已经执行过：

```powershell
git push -u origin main
```

以后只需要：

```powershell
git push
```

---

## 10. 一次完整日常流程示例

假设今天学 Lec03 并做 Lab03。

### 开始前

```powershell
cd D:\ZJU\Turing\P01_Courses\2026_Spring\HPC101\HPC101_notes
git status
git pull
code .
```

然后打开 Obsidian 的同一个 Vault。

### 新建 lecture

```powershell
ni lectures\Lec03.md
```

在 Obsidian 中插入 lecture template，然后边看课程网站边写。

### 新建 lab

```powershell
mkdir labs\Lab03
mkdir labs\Lab03\code
mkdir labs\Lab03\results
ni labs\Lab03\notes.md
ni labs\Lab03\submit.slurm
ni labs\Lab03\code\main.c
```

写代码、写脚本、跑实验，并把命令和结果记录到：

```text
labs/Lab03/notes.md
```

### 整理 cheatsheet

如果今天学到了高频命令，更新：

```text
cheatsheets/slurm.md
```

或其他 cheatsheet。

### 结束后保存

```powershell
git status
git add .
git commit -m "add lec03 and lab03 notes"
git push
```

---

## 11. 常用 Git 命令速查

查看状态：

```powershell
git status
```

查看修改：

```powershell
git diff
```

添加所有修改：

```powershell
git add .
```

提交：

```powershell
git commit -m "message"
```

推送：

```powershell
git push
```

拉取：

```powershell
git pull
```

查看远程仓库：

```powershell
git remote -v
```

测试能不能连 GitHub：

```powershell
git ls-remote https://github.com/aNon1mo4-21/HPC101-Notes.git
```

---

## 12. 推荐的 commit message 规范

```powershell
git commit -m "add lec04 notes"
git commit -m "add lab02 slurm script"
git commit -m "update mpi cheatsheet"
git commit -m "record openmp compile error"
git commit -m "fix typo in lec01"
git commit -m "add profiling results"
```

简单规则：

```text
add    新增内容
update 更新已有内容
fix    修正错误
record 记录问题或实验结果
```

---

## 13. 不建议做的事

不要把课程网站全文复制进笔记。

不要把所有实验代码都堆在一个文件夹。

不要只保存 Slurm 输出文件而不写解释。

不要做完一天笔记却不 commit。

不要在 Obsidian、VS Code、GitHub 分别维护三份内容。

---

## 14. 最小流程版

如果今天很忙，至少完成这个流程：

```powershell
cd D:\ZJU\Turing\P01_Courses\2026_Spring\HPC101\HPC101_notes
git pull
code .
```

写完笔记后：

```powershell
git status
git add .
git commit -m "update hpc notes"
git push
```

只要做到这个，你的笔记就不会丢，也不会乱。