# gitignore

 `.gitignore` 告诉 Git：**哪些“未跟踪文件（untracked）”不要出现在 `git status`、不要被 `git add .` 加进去**。它**不会**自动删除文件，也**不会**影响已经提交进仓库的文件。

`.gitignore` 放在仓库根目录，对整个仓库生效。如果在子目录放 `.gitignore`，则**只对该目录及其子目录生效**，并会与上层规则叠加。

## 匹配规则

`.gitignore` 每行一条规则，常用语法：

* `foo`：匹配任意路径中的 `foo`（文件或目录名）
* `foo/`：只匹配目录 `foo`
* `*.log`：匹配所有 `.log` 后缀名
* `build` 会匹配 `build`、`a/build`、`a/b/build`

- `/` 开头：从 `.gitignore` 所在目录开始匹配。例如：`/build/`：只匹配当前 `.gitignore` 同级的 `build/`，不匹配 `a/build/`

- `**`：跨目录通配。例如：`**/node_modules/`：匹配任何层级的 `node_modules`

* `?.txt`：一个字符 + `.txt`（如 `a.txt`）
* `file[0-9].txt`：`file0.txt` 到 `file9.txt`

- `!` 反向规则。例如`*.log` 忽略所有 log 后，使用`!keep.log` 把 `keep.log` 重新纳入跟踪

注意：如果父目录本身被忽略了，要“救”子文件，需要同时把目录救回来，例如：

```gitignore
logs/
!logs/
!logs/keep.log
```

* `#`注释。`# comment` 是注释
* `\`转义：若要匹配以 `#` 开头的文件名，用 `\#file`

### 顺序

* **越靠后的规则优先**

## 已经被提交的文件不会因为 `.gitignore` 而消失

例如已经提交了 `.env`，后来才写进 `.gitignore`，它仍会继续被跟踪。

解决办法（只取消跟踪，不删本地文件）：

```bash
git rm -r --cached .env
git commit -m "Stop tracking .env"
```

对目录同理：

```bash
git rm -r --cached .venv
git commit -m "Stop tracking venv"
```

## 查看文件状态

### 看某个文件为何被忽略

```bash
git check-ignore -v path/to/file
```

会告诉你：命中了哪个 ignore 文件的哪一行规则。

### 看当前仓库有哪些忽略规则来源

```bash
git config --get core.excludesfile
```

### 强制加入一个被忽略的文件

```bash
git add -f path/to/file
```

## 如何让仓库干净

* 把环境、缓存、构建产物写进 `.gitignore`
* 把依赖用 `requirements.txt` / `pyproject.toml` 管理
* 如果不小心提交了不该提交的文件：用 `git rm --cached` 纠正，再提交一次

## P.S. Git 还有两种忽略来源

* `.git/info/exclude`：只对本地仓库有效，不会提交。
* 全局忽略：`~/.config/git/ignore`（或 `core.excludesfile`），对电脑上所有仓库有效。
