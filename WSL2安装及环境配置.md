# <center><font face="仿宋">WSL2安装及环境配置</font>
WSL安装与配置视频教程：【哔哩哔哩】（https://b23.tv/v1AwJnA）
注意：跟随视频安装的是Ubuntu的最新版本不是Ubuntu24.04
## 一、准备
### 1.检查CPU虚拟化是否启用：  
搜索并打开任务管理器左侧选中性能即可查看CPU状态
### 2.两个Windows功能是否开启：
搜索并打开启用或关闭Windows功能将两个功能开启
- 虚拟机平台
- 适用于Linux的Windows子系统
## 二、WSL2安装（版本严格对应Ubuntu 24.04）
### 1.安装指定版本的 Ubuntu：
`wsl --install -d Ubuntu-24.04`
推荐使用`wsl --install -d Ubuntu-24.04 --web-download`可以减少因为网络问题导致下载失败，如果下载安装包过程中进度条卡住不动可以试试按下esc。
### 2.设置用户名及密码：

安装完后会自行启动等待一段时间提示设置用户名，设置你的名称回车会提示你设置密码，设置你的密码，此时键盘输入后屏幕上不会有变化，直接输入即可。

安装完成后可在顶部的下拉列表里打开。
### 3.检查Ubuntu版本：
`wsl --list --verbose`
输出结果应为 Ubuntu-24.04版本不一致需重新安装正确的版本。
## 三、WSL2配置：
### 1.更新本地包索引软件源：
`sudo apt update`
### 2.升级所有已安装的软件包到最新版本：
`sudo apt install git`
### 3.安装git
`sudo apt install git`
验证是否安装成功：`git --version`输出版本号即安装完成。
### 4.配置git
#### （1）设置用户名及邮箱（与GitHub邮箱最好一致）
- 配置用户名：
  `git config --global user.name "用户名"`
- 配置邮箱:
  `git config --global user.email "邮箱"`
#### （2）为git配置代理
 `git config --global http.proxy http://127.0.0.1:7897`冒号后输入自己的代理端口）
 `git config --global https.proxy http://127.0.0.1:7897 ` 冒号后输入自己的代理端口）
验证是否生效:
`git config --global --get http.proxy`（应该输出 http://127.0.0.1:7890根据自己实际判断）
#### （3）初始化 git 仓库并做第一次推送
+  初始化本地仓库
`git init`
+  把文件加入暂存区
`git add .`
+ 提交到本地仓库
`git commit -m "first commit"`
+ 添加远程仓库（换成你的 HTTPS 地址）
`git remote add origin https://github.com/你的用户名/my-project.git`
地址可以在GitHub网页上查看，点击指定仓库点击绿色的代码按钮，点击后可在 HTTPS 和 SSH 协议间切换，直接复制链接。（HTTPS：每次操作需验证账号密码；SSH：配置密钥后可免密操作。）
+ 把本地代码推送到 GitHub
首先，确认你当前在哪个分支`git branch`前面带 * 号的那个就是当前分支，然后执行推送，把下面的 main 换成你实际的分支名：`git push -u origin main`(如果你的分支是 master，就把 main 换成 master)
+ 最后推送到这个远程仓库`git push`出现`Everything up-to-date`意味着：你的本地仓库和远程仓库内容已经完全一致，没有任何新的提交需要上传。
### 5、配置SSH：
#### （1）检查是否已有SSH密钥
首先检查电脑上是否已经存在 SSH 密钥，避免重复生成。打开终端（Git Bash、Terminal 或 PowerShell），输入以下命令：`ls -al ~/.ssh`如果看到 id_ed25519.pub 或 id_rsa.pub 等文件：说明已有密钥，可以跳过第二步，直接使用现有密钥。如果提示 "No such file or directory" 或没有任何输出，说明没有密钥，继续执行下一步
#### （2）生成新的SSH密钥
在终端中运行以下命令。记得将 your_email@example.com 替换成你注册 GitHub 的邮箱地址：
`ssh-keygen -t ed25519 -C "your_email@example.com"`
注意：如果你的系统不支持 ed25519 算法（通常较老系统），可以使用以下命令：
`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
执行后，一路按 Enter 键即可，保存位置：提示 Enter a file in which to save the key 时，直接按 Enter 选择默认位置。设置密码：提示 Enter passphrase 时，可以直接按 Enter 留空（方便）。
#### （3）将密钥添加到 ssh-agent
+ 启动 ssh-agent：`eval "$(ssh-agent -s)"`
+ 添加私钥：`ssh-add ~/.ssh/id_ed25519`
#### （4）将公钥添加到GitHub
+ 复制公钥：
    - Windows：`clip < ~/.ssh/id_ed25519.pub`
    - Linux：`at ~/.ssh/id_ed25519.pub`然后手动复制输出的内容。
+ 登录GitHub添加密钥：
    - 登录 GitHub，点击右上角头像，选择 Settings。
    - 在左侧边栏中，点击 SSH and GPG keys。
    - 点击绿色的 New SSH key 按钮。
    - 在 "Title" 字段中，为这个密钥取一个名字。
    - 在 "Key" 字段中，粘贴你刚才复制的公钥内容。
    - 点击 Add SSH key 按钮保存。
#### （5）测试连接 
+ 在终端中执行：`ssh -T git@github.com`首次连接：会看到类似 The authenticity of host 'github.com ...' can't be established. 的提示，输入 yes 并回车即可。
+ 成功：如果看到 Hi <你的用户名>! You've successfully authenticated... 的信息，说明 SSH 密钥已完美配置成功！
+ 如果测试连接时出现 Connection timed out 或 port 22: Connection refused 错误，说明你的网络可能屏蔽了 SSH 默认的 22 端口。可以通过配置 SSH 使用 443 端口来解决。创建或编辑 ~/.ssh/config 文件，添加以下内容：
```Host github.com
HostName ssh.github.com
User git
Port 443
```
保存文件后，再次运行 ssh -T git@github.com 测试即可