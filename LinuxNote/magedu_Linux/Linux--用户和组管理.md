
### 用户和组管理

​	资源分派：

​		Authentication 认证

​		Authorization   授权

​		Accouting          审计

token / identity  ( username / password )

Linux用户 ： Username/ UID

> 管理员		root , 0
>
> 普通用户		1-65535
>>
>> 系统用户 ： 1-499
>>
>>> 对守护进程获取资源进行权限分配
>>
> > 登录用户 ： 500+
>>
> > > 交互式登录

Linux组 ： Groupname / GID

> 管理员组 ： root ，0
>
>  普通组 ： 
>
> > 系统组：1-499
> >
> > 普通组 ： 500+

Linux安全上下文

> 运行中的程序 ：进程 ( process )
>
> > 以进程发起者的身份进行
> >
> > 进程锁能够访问到的所有资源的权限取决于进程的发起者的身份

Linux组的类别

> 用户基本组( 主组 )
>
> > 组名同用户名 仅包含一个用户  私有组
> 
> 用户的附加组( 额外组 )

Linux用户和组相关的配置文件

- `/etc/passwd`     用户及其属性信息（名称 / UID / 基本组ID）
- `/etc/group`       组及其属性信息 
- `/etc/shadow`     用户密码及其相关信息
- `/etc/gshadow`   组密码及其相关信息

##### `/etc/passwd`:

​	name : password : UID : GID : GECOS : directory : shell

​	用户名：密码*：UID ：GID ：用户完整信息：主目录：默认shell

Note：影子口令  这里的密码大多是密码占位符

##### `/etc/group`

​	group_name : password : GID : user_list

​	组名：组密码：GID：以当前组为附加组的用户列表（分隔符为逗号）

##### `/etc/shadow`

用户名：加密了的密码：最后一次更改密码的日期：最小密码使用期限：最大密码使用期限：密码警告时间段：密码禁用期：账户过期日期：保留字段

​		加密机制：

​			加密： 明文 ——> 密文

​			解密：密文 ——> 明文

​				md5 :  message digest,128bits

​				sha1 : secure hash algorithm,160bits

​			 雪崩效应 ： 出事条件的微小改变 将会引起结果的巨大改变

#### 用户和组相关的管理命令

##### 用户创建 ——  `useradd`

​######  useradd   [OPTIONS]  LOGIN

​		-u UID ： [UID_MIN  ,  UID_MAX] 定义在 /etc/login.defs

​		-g GID :   指明用户所属基本组  可为组名  也可以GID

​		-c “COMMENT” :    用户的注释信息

​		-d /PATH/TO/HOME_DIR  :   以指定的路径作为家目录

​		-s  SHELL 指明用户的默认shell程序   可以列表在 /etc/shell 文件中

​		-G  GROUP1 [ , GROUP2 , ...... [ GROUPN ] ]  为用户指明附加组 ；族必须事先存在

​	默认值设定：/etc/default/useradd  文件中

##### 组创建  ——  `groupadd`

##### groupadd [ OPTION ] ... group_name

​	-g GID : 指明GID [ GID_MIN , GID_MAX ]

​	-r  创建系统组

​		CentOS  6 : ID < 500

​		CentOS  7 : ID < 1000

##### 查看用户相关的 ID 信息 —— `id`

##### id [ OPTION ] . . . [USER]

-u : UID

-g : GID

-G : Groups

-n : Name

##### 切换用户或以其他用户身份执行命令 —— `su`

##### su [ options . . . ]\[ - ] [ user [ args ] ]

切换用户的方式：

​	su UserName : 非登陆式切换  不会读取目标用户的配置文件

​	su - UserName : 登录式切换 会读取用户配置文件  完全切换

换个身份执行命令

​	su [ - ]UserName -c "COMMAND"

选项

-l           “ su -l UserName ”   ==  " su - UserName "

##### 用户属性修改 —— `usermod`

usermod [ OPTION ]  LOGIN

-u UID ——新UID 

-g GID —— 新基本组

-G GID Group1 [ , Group2 , . . . [ ,GroupN  ]  ]  ——  新附加组，原来的附加组将被覆盖  

​											若保留原有 需要同时使用 -a 选项 表示 append 

-s SHELL 新的默认SHELL

-c  “ COMMENT ” 新的注释信息

-d  HOME  新的家目录  原有家目录中的文件不会同时移动到新的家目录  

​		    若要移动  需要同时使用  -m   选项

-l   login_name  新的名字

-L  lock指定用户

-u  unlock指定用户

##### 给用户添加密码 ——` passwd`

passwd [ OPTIONS ] UserName  修改指定用户的密码 仅   root  用户权限

passwd  修改自己的密码

-l  锁定指定用户

-u  解锁指定用户

-n  mindays  指定最短使用期限

-x  maxdays 指定最大使用期限

-w wardays  指定提前多少天开始警告

-i   inactivedays  非活动期限

​	--stdin  从标准输入接收用户密码

​		echo "PASSWORD" | password    -- - stdin USERNAME

Note ： /dev/null     bit buckets  ”位桶“  

​	      /dev/zero     ”泡泡机“

##### 删除用户 ——  `userdel`

​	userdel [ OPTION ] . . login

​		-r 删除用户家目录

##### 组属性修改 ——  `groupmod`

​	groupmod [ OPTION ] . . . group

​		-n group_name 新组名

​		-g GID 新的GID

##### 组删除  —— `groupdel`

​	groupdel GROUP

##### 组密码 —— `gpasswd`

​	gpasswd [ OPTIOON ] GROUP

​		-a user	将 user 添加至指定组（附加组）中

​		-d user	将 user 从指定组（附加组）删除

​		-A user1,user2 . . . userN  设置具有管理员权限的用户列表

##### 临时切换基本组 —— `newgrp`

​	如果用户不属于此组  则需要组密码

##### 修改用户属性 —— `chage`

​	chage [ OPTION ] . . . LOGIN

​		-d	LAST_DAY

​		-E	,-- --expiredate	EXPRIRE_DATE

​		-I	,-- --inactive		INACTIVE

​		-m	,-- --mindays		MIN_DAYS

​		-M	,-- --maxdays		MAX_DAYS

​		-W	,-- --wardays		WARN_DAYS

##### 其他命令： `chfn` `chsh` `finger`

##### 权限管理：

​	文件的权限主要针对三类对象进行定义

​		owner : 属主

​		group : 属组

​		other : 其他

​	每个文件都针对每类访问者定义了三种权限

​		r :	Readable

​		w :	Writable

​		x:	eXcutable

​	文件：

​		r：可使用文件查看类工具获取文件内容

​		w：可修改其内容

​		x：可以把此文件提请内核启动为一个进程

​	目录：

​		r：可以使用  ls  查看此目录中文件列表

​		w：可在此目录中创建文件 可可以删除此目录中的文件

​		x：可以使用  ls -l  查看此目录中文件列表  可以  cd  进入此目录

- — — —    0 0 0    0

- — — x     0 0 1    1

- — w —    0 1 0    2

- — w x      0 1 1    3

- r — —     1 0 0    4

- r — x       1 0 1    5

- r w —     1 1 0    6

- r w x       1 1 1    7

  

##### 修改文件权限 `chmod`

​	chmod [ OPTION ]. . . OCTAL_MODE . . . FILE . . .

​		-R ：递归修改权限

chmod [ OPTION ]. . . MODE[ ,MODE ]  . . . FILE . . . 

​		MODE:

​			修改一类用户的权限  要设置无权限时等号右边可为空

​				u=

​				g=

​				o=

​				ug=

​				a=

​				u=,g=

​			修改一类用户某位或某些位权限

​				u+

​				u-

##### 修改文件的属主和属组

##### 仅 root 可用	 ` chgrp`

​	chgrp [ OPTION ] . . . GROUP FILE . . 

​	chgrp [ OPTION ] . . . --reference=RFILE FILE . . 

##### 文件或目录创建时的遮罩码 `umask`

FILE : 666-umask

​	Note：如果某类的用户的权限减得的结果中存在 x 权限 则将其权限 +1 

DIR：777-umask

umask：查看

umask # ：设定