# bash 配置文件

### 分类
按照生效范围划分：
- 全局配置：
  + `/etc/profile`
    + `/etc/profile.d/*.sh` 
  + `/etc/bashrc`
- 个人配置：
  + `~/.bashrc`
  + `~/.bash_profile`

按照功能划分：
- `profile` 类（为交互式登录的 shell 提供配置）：
  + 全局：
    + `/etc/profile` 
    + `/etc/profile.d/*.sh`
  + 个人： `~/.bash_profile` 
  + 功能：
    + （1）用于定义环境变量
    + （2）运行命令或脚本
- `bashrc` 类（为费交互式登录的 shell 提供配置）：
  + 全局：
    + `/etc/bashrc`
  + 个人：
    + `~/.bashrc`
  + 功能：
    + （1）定义命令别名
    + （2）定义本地变量

### 交互式/非交互式登录的内容补充详情如下

 `shell`登录方式：

- 交互式登录：
  + 直接通过终端输入账号密码进行登录
  + 使用 `su -UserName` 或 `su -l UserName` 命令进行切换的用户
  + **配置文件读取顺序 :**
    + `/etcprofile` --> `/etc/profile.d/*.sh` --> `~/.bash_profile` --> `~/.bashrc` --> `/etc/bashrc`
- 非交互式登录：
  +  `su UserName`
  +  在图形界面下打开的 shell 终端
  +  执行脚本的 shell 进程
  +  **配置文件读取顺序 :**
      + `~/.bashrc` --> `/etc/bashrc` --> `/etc/profile.d/*.sh`

### 编辑配置文件定义的新配置的生效方式：
- （1）重新启动 shell 进程
- （2） 使用 `source` 或 `.` 命令进程