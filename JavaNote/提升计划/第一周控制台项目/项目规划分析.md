# 提升计划第一周 -- version 1.0
## 项目规划分析
### 顶层架构
- 使用基本流程控制完成业务逻辑
- 使用集合框架和基本数据结构完成数据的存储处理
- 纯 OOP 设计
### 项目定义&模块划分
- 主要逻辑
    - 存储管理
        - 物资按照　user-package ( Key-ValueList )　存储，特殊物品单个存放
        - 个人物品使用 list 存储
    - 权限管理
        - 一级管理员
            - 全权操作
        - 二级管理员
            - 特殊物品无权限，可修改普通用户空间
        - 普通用户
            - 只能管理个人物品
    - 日志模块
        - 普通用户只能查看自己物品的进出，可选择删除
        - 二级管理员可以查到所有用户的记录，包括已删除记录，也包括自己，
        - 一级管理员全权操作 —— 任何CRUD 
### 项目结构分层
#### Model层：
- Bean
    - user
        - 提供两种构造方法，创建两种用户，内置一级管理员
            - 自生成用户ID
            - 用户生成即创建存储空间 —— 空间不固定，到达上限后需联系管理员添加
        - 内置 log 存储 ，ArrayList 类型  命名为 UserLog
    - package
        - 提供构造方法 —— 应该是 Object 的
        - 生成存储对象唯一 UID 构造时调用
#### Service 层：
- ServiceDao 提供服务接口
    - PermissionSerciceDao 权限服务接口
    - DataServiceDao 基础服务接口
    - UserCRUDServiceDao —— 提供用户基本服务接口
    - LogService —— 提供日志服务接口
- ServiceImpl 提供具体服务内容
    - PermissionSerciceDao 权限服务接口
        - PermissionSerciceDaoImpl —— 提供权限服务
            - RootPermissionSerciceDaoImpl —— 继承关系
            - ManagerPermissionServiceDaoIMpl —— 二级管理员权限
            - UserPermissionServiceDaoImpl —— 用户权限
    - DataServiceDao 基础服务接口
        - DataServiceDaoImpl —— 提供基础服务
            - 创建用户存储空间
            - 存储空间检测
            - 存储空间修改
            - 存储空间余量查询
    - UserCRUDServiceDao —— 提供用户基本服务
        - UserCRUDServiceDaoImpl —— 基础服务具体实现
            - 查询当前存储空间余量
            - 查看当前存储内容 —— 展示 UID & 具体内容
            - 删除存储内容 —— 提供存储 UID
            - 添加内容 —— 返回存储 UID 并提示（ 成功与否 <====> 检测存储空间 ）
    - LogService —— 提供日志服务接口（有没有 jar 包提供？）
        - LogServiceImpl —— 日志服务接口实现
            - add —— 记录每次操作
            - select —— 查询每次操作
#### Controller 层：
接受 View 层 的调用，再调用对应的 Service
- ModelController
    - UserController —— 用户操作控制 
    - ManagerComtroller —— 二级管理员操作控制
- ServiceController
    - PermissionController —— 权限操作控制
    - LogServiceController —— 日志控制
    - DataServiceController —— 存储空间控制
#### View 层：
- LogView —— 打印登录
- MainView —— 打印欢迎、提示权限内的操作