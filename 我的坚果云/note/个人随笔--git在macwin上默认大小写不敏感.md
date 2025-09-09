### MAC 上如果修改文件大小写然后push是push不上去的，

```shell
# 1. 禁用大小写忽略（确保 Git 区分大小写）
git config core.ignorecase false

# 2. 强制刷新索引，让 Git 重新识别所有文件
git rm --cached -r .
git add .

# 3. 提交文件名修复
git commit -m "Fix file name case differences"

```

1. `core.ignorecase false`：告诉 Git **区分大小写**。
2. `git rm --cached -r .`：移除索引缓存，但不删除工作目录文件。
3. `git add .`：重新添加文件，Git 会正确识别文件名大小写变化。
4. `git commit`：提交更新，让远程仓库和本地同步文件名。

> 执行完这三步后，本地文件名和远程 GitHub 上的文件名就会完全一致，再次 `git status` 会显示干净。