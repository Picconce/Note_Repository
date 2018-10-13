# 第二周项目规划
## 架构设计
- 抛弃框架使用传统 HttpServlet 技术
- 数据传递使用 json
- 数据库使用 MySQL
- 前后端分离 MVC 架构
    - web 前端页面使用 bootstrap 或者 Material Design ，非 jsp
    - 后端纯 Java
- Log4j + common-loggin 日志框架
- 自定义异常
### 业务逻辑设计
#### 场景模拟
开发 百度云盘 式的 web 项目 支持小文件的在线预览（非影音文件） 满足小规模使用 不考虑高并发和大数据量
#### 基本逻辑
- 普通用户：
    - 登录 --> 查看  下载/上传/删除文件
    - 登录 --> 查看  上传/下载/删除记录
    - 登录 -->文件分享 --> 生成分享链接 --> 加密/不加密
    - 存储空间限制
- 团队：

| 任务                                    | 完整访问权限   |  编辑权限  |  评论权限  | 查看权限|
| ---------------                        | -----:        | :----:    | :-----:   | :----:|
|查看团队云端硬盘及其文件                    |	✔	         |      ✔	 |      ✔	 |      ✔|
|在团队云端硬盘中创建和上传文件，以及创建文件夹  |✔              |  	✔    |    	✘    |	    ✘|
|在团队云端硬盘中添加或移除用户                |	✔         |	    ✘     |	    ✘      |     ✘|
|将用户添加到团队云端硬盘中的特定文件           |	✔          |	✔      |	✘       |	✘  |
|从团队云端硬盘中删除文件和文件夹              |	✔          |	✘     | 	✘      |	✘  |
|从回收站恢复文件（30 天内）              |    ✔          |	✔       |   	✘     |	   ✘  |
