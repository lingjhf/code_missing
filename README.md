# git reset 使用不当导致代码丢失

多人开发协作的情况下，经常会出现代码无故丢失，而且找不到记录。

## 复现过程

1. user1 创建两个文件。
   - file1.txt
   - file2.txt
2. user1 commit 两个文件并且 push 到远程仓库。
3. user2 拉取远程仓库。
4. user2 修改文件。
   - 在 file1.txt 添加 `user2:file1 add line`
   - 在 file2.txt 添加 `user2:file1 add line`
5. user2 commit 两个文件并且 push 到远程仓库。
6. user1 修改文件。
   - 在 file1.txt 添加 `user1:file1 add line`
7. user1 拉取远程仓库，这个时候由于 user2 修改了 file1，会导致冲突。
8. user1 解决 file1 的冲突，add 到暂存区。
9. user1 把 file2 通过 reset 回退到上一个版本。
   - `git reset 版本id file2.txt`
10. user1 单独把 file1 commit 和 push 到远程仓库
11. user2 拉取远程仓库, 这个时候 user2 之前修改的 file2 的内容就丢失了。

## 安全性

### 单分支

- 安全性：危险

由于多人共用一个分支，其中一个人使用reset或者其他单独提交等操作后，会产生代码丢失问题。

### 多分支

- 安全性：危险

与单分支一样，只要其中一条分支出现 reset 丢代码后，合并到其他分支，一样会出现丢代码。

### 多分支+PR

- 安全性：低

多分支可通过 PR 检测到丢代码问题，但是没办法避免线下手动合并丢代码问题，或者多人开发合并丢代码问题。

### fork 仓库+PR

- 安全性：高

由于不会出现分支合并，只能通过 PR 提交代码，只需要在审核阶段发现代码丢失就可以阻止主仓库代码被污染。
