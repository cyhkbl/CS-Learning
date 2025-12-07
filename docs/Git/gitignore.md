# gitignore

 `.gitignore` 告诉 Git：**哪些“未跟踪文件（untracked）”不要出现在 `git status`、不要被 `git add .` 加进去**。它**不会**自动删除文件，也**不会**影响已经提交进仓库的文件。

`.gitignore` 放在仓库根目录，对整个仓库生效。如果在子目录放 `.gitignore`，则**只对该目录及其子目录生效**，并会与上层规则叠加。

## 匹配规则

`.gitignore` 每行一条规则，常用语法：

### 3.1 基本匹配

* `foo`：匹配任意路径中的 `foo`（文件或目录名）
* `foo/`：只匹配目录 `foo`
* `*.log`：匹配所有 `.log` 文件
* `build` 会匹配 `build`、`a/build`、`a/b/build`

### 3.2 `/` 开头：从 `.gitignore` 所在目录开始匹配

* `/build/`：只匹配当前 `.gitignore` 同级的 `build/`，不匹配 `a/build/`

### 3.3 `**`：跨目录通配

* `**/node_modules/`：任何层级的 `node_modules`
* `logs/**/debug.log`：logs 下任意层级的 debug.log

### 3.4 `?` 和 `[]`

* `?.txt`：一个字符 + `.txt`（如 `a.txt`）
* `file[0-9].txt`：`file0.txt` 到 `file9.txt`

### 3.5 `!` 反向规则（“排除中的例外”）

* `*.log` 忽略所有 log
* `!keep.log` 把 `keep.log` 重新纳入跟踪

注意：如果父目录本身被忽略了，你要“救”子文件，需要同时把目录救回来，例如：

```gitignore
logs/
!logs/
!logs/keep.log
```

### 3.6 `#` 注释与转义

* `# comment` 是注释
* 若你要匹配以 `#` 开头的文件名，用 `\#file`

---

## 4) 优先级与“谁赢”

同一个文件可能同时被多条规则命中：

* **越靠后的规则优先**（后写的覆盖前写的）
* 更具体的规则通常更直观
* `!` 可以把忽略改回“可跟踪”

---

## 5) 最大坑：已经被提交的文件不会因为 `.gitignore` 而消失

例如你已经提交了 `.env`，后来才写进 `.gitignore`，它仍会继续被跟踪。

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

---

## 6) 实战排错与验证命令（强烈建议记住）

### 6.1 看某个文件为何被忽略（最准）

```bash
git check-ignore -v path/to/file
```

会告诉你：命中了哪个 ignore 文件的哪一行规则。

### 6.2 查看当前仓库有哪些忽略规则来源

```bash
git config --get core.excludesfile
```

### 6.3 仍然要强制加入一个被忽略的文件

```bash
git add -f path/to/file
```

---

## 7) 常用模板（Python / Windows）

### 7.1 Python 项目常见 `.gitignore`

```gitignore
# venv
.venv/
venv/

# python cache
__pycache__/
*.py[cod]

# build artifacts
build/
dist/
*.egg-info/

# test/coverage
.pytest_cache/
.coverage
htmlcov/

# IDE
.vscode/
.idea/

# OS junk
.DS_Store
Thumbs.db
```

### 7.2 只忽略根目录 `.venv`（不影响子目录同名）

```gitignore
/.venv/
```

---

## 8) 推荐工作流（让仓库干净）

* 把环境、缓存、构建产物写进 `.gitignore`
* 把依赖用 `requirements.txt` / `pyproject.toml` 管理
* 如果不小心提交了不该提交的文件：用 `git rm --cached` 纠正，再提交一次

## P.S. Git 还有两种忽略来源

* `.git/info/exclude`：只对本地仓库有效，不会提交。
* 全局忽略：`~/.config/git/ignore`（或 `core.excludesfile`），对电脑上所有仓库有效。
