# git branch 

git branch：用于列出，创建或删除分支。

git branch携带不同参数的效果：

- `--list` 或无参： 列出现有的本地分支，当前分支用`*`突出显示
- `--list <pattern>`：模式匹配
- `-r`：列出远程跟踪分支
- `-a`：显示本地和远程分支

本地分支示例：

1. 创建分支：

   - `git branch [branch name]`
   - `git branch [branch name] [tag name]`：在tag name标签处创建新分支
   - `git checkout -b [branch name]`：创建并切换到新分支

2. 切换分支：

   - `git chechout [branch name]`：切换分支

3. 修改分支名

   - `git branch -m [old branch name] [new branch name]`

4. 删除分支

   - `git branch -d [branch name]`

5. 查看分支

   - `git branch`
   - `git branch --list`

6. 合并分支

   如分支 branch1、branch2，切换到需要合并后保留的分支 branch1，然后使用 `git merge branch2`

远程分支操作：

1. 远程库迁移，修改本地的远程库地址连接到新的远程仓库地址：

   - `git remote set-url hostname url`：修改对应远程库的url

2. 远程库分支操作：

   关联远程分支：

   - `git push --set-upstream origin <本地分支名>`：将本地分支与远程同名分支相关联

   `git pull` 操作：

   - `git pull origin <远程仓库名>`：克隆远程项目的时候，本地分支会自动与远程仓库建立追踪关系，可以使用默认的origin来替代远程仓库名
   - `git pull origin <远程分支名>:<本地分支名>`：将远程指定分支 拉取到 本地指定分支上
   - `git pull origin <远程分支名>`：将远程指定分支 拉取到 本地当前分支上
   - `git pull`：将与本地当前分支同名的远程分支 拉取到 本地当前分支上，需先关联远程分支

   `git push`操作：

   - `git push origin <本地分支名>:<远程分支名>`：将本地当前分支 推送到 远程指定分支上（注意：pull是远程在前本地在后，push相反）
   - `git push origin <本地分支名>`：将本地当前分支 推送到 与本地当前分支同名的远程分支上
   - `git push`：将本地当前分支 推送到 与本地当前分支同名的远程分支上

   `git branch`：

   - `git branch -vv`：查看本地分支与远程分支的跟踪关系
   - `git branch --set-upstream-to=<远程主机>/<远程分支> <本地分支>`：本地分支追踪远程分支