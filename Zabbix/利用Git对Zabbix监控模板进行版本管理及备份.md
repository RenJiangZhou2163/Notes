# 如何利用Git对Zabbix监控模板进行版本管理及备份?

原创 Zabbix开源社区 [Zabbix开源社区](javascript:void(0);) *前天*

收录于话题#Zabbix专家专栏10个内容



**马嘉炜 | Zabbix开源社区签约专家**

- SRE运维工程师，六年Zabbix监控系统使用经验。在Zabbix架构设计及性能优化领域有丰富的经验，擅长监控模板制作及Zabbix API的二次开发。
- 官方使用手册《Zabbix标准模板指南》译者



### 背景

监控模板是Zabbix监控系统中非常重要的一个功能组件，在日常运维中，监控模板经常需要的新增或修改配置，例如新增监控项，修改触发器表达式，调整低级自动发现规则等。


然而在Zabbix监控系统中，监控模板缺少版本管理方案，想获取某个监控模板在什么时候调整了哪些内容，或者获取之前的模板配置，这些都是无法做到的。另外，Zabbix的模板配置都是存在数据库中的，如果数据库损坏，也可能会导致监控模板丢失。

### 实现原理

Zabbix提供了模板导出的功能，可以利用Zabbix API将模板导出成配置文件。再将导出的监控模板定期推送到Git仓库中，利用Git对监控模板进行版本管理及备份。

### 环境准备

##### 1、创建Git仓库

> 仓库名<repo_name>：zabbix-templates

- 由于Zabbix监控模板中可能存在用户名密码等配置，在创建GIT仓库时，推荐创建私有仓库。
- 大多数企业都有自己的私有的GitLab，可以在GitLab中创建Git仓库。
- 如果没有私有GITLAB，可以在gitee上创建私有的Git仓库（具体可参考：[在gitee上创建GIT仓库](https://gitee.com/help/articles/4120)）

##### 2、拷贝Git仓库到本地

> 可在Zabbix Server服务器上操作，或者选择一台可以同时访问Zabbix Web和Git仓库的服务器

- 设置Git的用户名<name>，邮箱<emal>配置信息

```shell
$ git config --global user.name <name>
$ git config --global user.email <email>
```

- 配置Git免密码

```shell
$ git config --global credential.helper store
```

- 克隆Git仓库到本地（第一次克隆时会提示输入用户名及密码）

> 从第一步中获取创建的Git仓库地址<url>

```shell
$ cd /opt
$ git clone <url>
```

- 测试Git提交

```shell
$ cd zabbix-templates
$ touch README.md
$ git add README.md
$ git commit -m "add README"
$ git push -u origin master
```

### 项目配置

> 环境要求：python >= 3.6

##### 1、拷贝项目代码

> 项目代码Git地址：https://gitee.com/masix/zabbix-export-templates.git

```shell
$ cd /opt
$ git clone https://gitee.com/masix/zabbix-export-templates.git
```

##### 2、修改配置文件

```shell
$ cd /opt/zabbix-export-templates
$ vim conf/config.yaml
---
# 模板文件导出路径
export_path: /opt/zabbix-templates

# zabbix配置信息
zabbix:
  url: http://127.0.0.1/zabbix
  username: Admin
  password: zabbix
```

##### 3、安装依赖包

```shell
$ cd /opt/zabbix-export-templates
$ pip3 install -r requirements
```

##### 4、执行脚本

```shell
$ cd /opt/zabbix-export-templates
$ python3 main.py
```

> 日志路径：/opt/zabbix-export-templates/logs

##### 5、检查结果

> 检查Git仓库中是否已经提交了Zabbix监控模板文件

### 定时任务

使用Cron配置定时任务，定期将Zabbix模板导出并推送到Git仓库

```shell
$ crontab -l
# 每小时执行
0 * * * * cd /opt/zabbix-export-templates && python3 main.py
```

