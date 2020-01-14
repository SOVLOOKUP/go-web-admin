# go-web-admin  

![版本](https://img.shields.io/badge/release-0.1.0-blue.svg)
![语言](https://img.shields.io/badge/language-golang1.12-blue.svg)
![base](https://img.shields.io/badge/env-goframe1.11.1-red.svg)


> 基于 goframe 框架,完成 go web后端 基本组件开发

## 目录

```
- app	  业务逻辑层	所有的业务逻辑存放目录。
    - api	业务接口	接收/解析用户输入参数的入口/接口层。
    - model	数据模型	数据管理层，仅用于操作管理数据，如数据库操作。
    - service	逻辑封装	业务逻辑封装层，实现特定的业务需求，可供不同的包调用。
- boot	初始化包	用于项目初始化参数设置。
- config	配置管理	所有的配置文件存放目录。
- docfile	项目文档	DOC项目文档，如: 设计文档、脚本文件等等。
- library	公共库包	公共的功能封装包，往往不包含业务需求实现。
- log           日志
- public	静态目录	仅有该目录下的文件才能对外提供静态服务访问。(本项目没用到)
- router	路由注册	用于路由统一的注册管理。
- template	模板文件	MVC模板文件存放的目录。(本项目没用到)
- test          单元测试
- go.mod	依赖管理	使用Go Module包管理的依赖描述文件。
- main.go	入口文件	程序入口文件。
```


## 实现功能
* 登录
* jwt验证
* 权限验证 
* 用户user      增删改查
* 权限组role    增删改查
* 菜单menu      增删改查



## 权限验证说明
>  利用的casbin库, 将  user  role  menu 进行自动关联

```
项目启动时,会自动加载权限. 如有更改,会删除对应的权限,重新加载.

用户关联角色  
角色关联菜单  

权限关系为:
角色(role.name,menu.path,menu.method)  
用户(user.username,role.name)

例如:
test      /api/v1/users       GET
hequan     test

当hequan  GET  /api/v1/users 地址的时候，会去检查权限，因为他属于test组，同时组有对应权限，所以本次请求会通过。

用户 admin 有所有的权限,不进行权限匹配

登录接口 /token  不进行验证
```

## 请求

> 请求和接收 都是 传递 json 格式 数据
```
例如:
访问 /token    获取token
{
	"username": "admin",
	"password": "123456"
}

访问 http://127.0.0.1:8000/api/v1/users?page=1
 
请求头设置  Authorization: Token xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

```


##  运行

* 部署mysql,创建库 go-web-admin
* 导入docfile/sql/go-web-admin.sql
* 修改配置文件 config/config.toml


```bash


go run  main.go
2020-01-09 11:15:45.119 [INFO] 更新角色权限关系 [["hequan","运维部"]]
2020-01-09 11:15:45.208 [INFO] 角色权限关系 [["hequan","运维部"]]

  SERVER  | DOMAIN  | ADDRESS | METHOD |       ROUTE       |                     HANDLER                      |    MIDDLEWARE
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | ALL    | /*                | go-web-admin/library/jwt.JWT                     | GLOBAL MIDDLEWARE
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | ALL    | /*                | go-web-admin/library/permission.CasbinMiddleware | GLOBAL MIDDLEWARE
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | ALL    | /*                | go-web-admin/router.MiddlewareCORS               | GLOBAL MIDDLEWARE
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | DELETE | /api/v1/menus/*id | go-web-admin/app/api/a_menu.(*Controller).Delete |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | GET    | /api/v1/menus/*id | go-web-admin/app/api/a_menu.(*Controller).Get    |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | POST   | /api/v1/menus/*id | go-web-admin/app/api/a_menu.(*Controller).Post   |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | PUT    | /api/v1/menus/*id | go-web-admin/app/api/a_menu.(*Controller).Put    |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | DELETE | /api/v1/roles/*id | go-web-admin/app/api/a_role.(*Controller).Delete |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | GET    | /api/v1/roles/*id | go-web-admin/app/api/a_role.(*Controller).Get    |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | POST   | /api/v1/roles/*id | go-web-admin/app/api/a_role.(*Controller).Post   |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | PUT    | /api/v1/roles/*id | go-web-admin/app/api/a_role.(*Controller).Put    |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | DELETE | /api/v1/users/*id | go-web-admin/app/api/a_user.(*Controller).Delete |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | GET    | /api/v1/users/*id | go-web-admin/app/api/a_user.(*Controller).Get    |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | POST   | /api/v1/users/*id | go-web-admin/app/api/a_user.(*Controller).Post   |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | PUT    | /api/v1/users/*id | go-web-admin/app/api/a_user.(*Controller).Put    |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | ALL    | /token            | go-web-admin/app/api/a_user.Login                |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|
  default | default | :8000   | ALL    | /userInfo         | go-web-admin/app/api/a_user.UserInfo             |
|---------|---------|---------|--------|-------------------|--------------------------------------------------|-------------------|


默认账户密码  admin  123456
```


## 所用组件
* goframe
* gorm
* casbin
* jwt-go
* mysql
* sha1


## 注释


```
200：请求成功
201：创建、修改成功
204：删除成功
400：参数错误
401：未登录
403：禁止访问
404：未找到
500：系统错误
```


## 作者
* 何全

