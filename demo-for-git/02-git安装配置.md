# git安装配置 

官方网站：https://git-scm.com/downloads

git提供了git config工具，专门用来配置或读取相应的工作环境变量，环境变量存放位置：

- /etc/gitconfig 文件：系统中对所有用户都普遍适用的配置。若使用 git config 时用--system 选项，读写的就是这个文件
- ~/.gitconfig 文件：用户目录下的配置文件只适用于该用户。若使用 git config 时用--global 选项，读写的就是这个文件
- 当前项目的 git 目录中的配置文件（也就是工作目录中的 .git/config 文件）：这里的配置仅仅针对当前项目有效。每一个级别的配置都会覆盖上层的相同配置，所以.git/config 里的配置会覆盖/etc/gitconfig 中的同名变量

初次安装配置：

- 用户信息：用户名和邮箱，很重要，每次git提交会引用这两条信息。

  ```
  # 用户名
  git config --global user.name "xxx"
  # 邮箱
  git config --global user.email xxx@example.com
  ```

- 查看配置信息：`git config --list/-l`

- 获得帮助信息：`git help [command]`

### git仓库

- 在工作目录创建：`git init`
- 从现有仓库clone：`git clone [url]`