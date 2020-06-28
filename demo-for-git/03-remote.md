# git remote配置 

###  用途

git remote命令管理一组跟踪的存储库。

### 示例

1. 查看当前配置可用的远程库：
   1. `git remote`：列出每个远程库的简短名字，git clone 某个远程库后，至少存在一个名为 origin 的远程仓库，属于 git 默认使用这个名字标识克隆的原始仓库
   2. `git remote -v|--verbose`：列出详细信息，在每一个名字后面列出其远程url
2. 添加一个新的远程
   1. `git remote add [url]`
   2. `git remote add [hostname] [url]`：添加远程仓库的同时为该远程仓库设置一个主机名
3. 移除一个远程：`git remote rm [url | hostname]`
4. 主机名操作：
   1. `git remote show hostname`：查看主机名的网址
   2. `git remote rename old-hostname new-hostname`：主机名称修改