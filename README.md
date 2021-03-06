## mha wiki
```
See https://github.com/yoshinorim/mha4mysql-manager/wiki for details.
```
## mha 集成proxysql
```
1. 自动切换发生时，调用master_ip_failover script删除proxysql 中的old master
2. 手动切换时，old master指定dead删除proxysql 中的old master
3. 手动切换时，old master指定alive且指定orig_master_is_new_slave，不删除proxysql 中的old master
```
## 支持架构
```
1. proxysql 与mysql实例复用ecs服务器
2. proxysql 实现proxysql cluster，即配置实时同步
3. 发生切换时只去被选为新master的节点去修改proxysql中的元数据
```
## HealthCheck.pm修改
```
1. 当ping_type为insert时，不去创建检测依赖的数据库和表结构，减少mha权限
2. 前提是在mysql提前执行以下SQL
create database infra;
use infra;
CREATE TABLE IF NOT EXISTS infra.chk_masterha (`key` tinyint NOT NULL primary key,`val` int(10) unsigned NOT NULL DEFAULT '0');
```
## mysql mha用户所需要的权限整理
```
create user mha@'mha4mysql-manager节点以及远程检测节点';
GRANT RELOAD, SUPER ON *.* TO 'mha'@'192.168.%' ;
GRANT INSERT, UPDATE ON `infra`.* TO 'mha'@'192.168.%' ;
GRANT SELECT ON `mysql`.* TO 'mha'@'192.168.%' ;
```
## app config file 增加proxy
```
[server default]
proxy_admin_user=remote_admin
proxy_admin_passwd=admin
proxy_admin_port=6032
```
## 主要修改的源码列表
```
1. HealthCheck.pm
2. MasterFailover.pm
3. MasterRotate.pm
4. Config.pm
```