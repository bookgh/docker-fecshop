# docker-fecshop

docker for fecshop

## 前言

让我们深入研究官方提供的 docker 配置，然后定制 docker 环境

> 大家在布署 fecshop 的过程中，仍然是推荐使用官方的 docker 配置，本贴仅作学习用途

## 分析

fecshop 依赖于多个基础的服务。

- nginx 作为 web 服务，提供静态资源的访问和 php 请求的转发
- php 主要的服务，处理 php 请求，且是作为 php-fpm 的模式运行
- mysql 结构化数据库，也是主要的数据库，涉及事务的表主要存 mysql
- mongodb 非结构化数据库，在 fecshop 里也是发挥着重要角色，存商品和分类数据
- redis 此服务主要是做缓存
- xunsearch 此服务中提供中文搜索


## 可能的问题

- 配置各数据库的用户名和密码等
- 自定义 php 镜像, 添加更多的 php 扩展
- 配置代码路径, 环境变量
- fecshop 那么多的站点配置

## 深入服务内部

关于服务，我们需要做几件事：选择基础镜像，设置用户名和密码，额外的参数定制，以及数据的持久化存储设置

关于基础镜像的版本号的选择，我们要明确指定主版本和副版本，bug 版本号不指定，从而让每一次构建都可以保证使用的是最新的修复稳定版本，不会引入较大的变化，前提是它们都以语义化版本来发布

> 基础镜像，有大有小，需要从网络下载，如果加速下载镜像 http://docker-cn.com/registry-mirror

关于用户名和密码的设置，可以通过环境变量指定

额外的参数配置，可通过挂载配置文件到容器内的具体路径

数据的持久化存储，可通过挂载容器的目录到宿主机

关于代码路径，通过 `.env` 文件，配置 `APP_ROOT` 路径, 然后挂载到容器里，须同时挂载到 `nginx` 服务容器和 `php` 服务容器

#### nginx

基础镜像: nginx:1.15

#### php

基础镜像: `php:7.1-fpm-stretch`

因为 fecshop 依赖于 redis, mongodb, pdo_mysql 等扩展，因为需要在基础镜像之上定制，添加需要的扩展

为了方便在容器内操作 `composer`, 因此需要把二进制包挂载到 php 容器内，当然可以 php 容器里安装最新的 composer

> 如何 composer 加速 https://laravel-china.org/composer

利用 `pecl` 安装扩展前, 先执行一下更新命令 `pecl channel-update pecl.php.net`

#### mysql

基础镜像: `mysql:5.7`

通过环境变量设置 root 密码：`MYSQL_ROOT_PASSWORD: root`

通过环境变量设置数据库: `MYSQL_DATABASE: fecshop`

配置目录: `/etc/mysql/conf.d`

数据目录: `/var/lib/mysql`

#### mongodb

基础镜像: `mongo:3.6`

通过环境变量设置用户名: `MONGO_INITDB_ROOT_USER: root`

通过环境变量设置密码: `MONGO_INITDB_ROOT_PASSWORD: root`

通过环境变量设置初始数据库: `MONGODB_INITDB_DATABASE: fecshop`

通过指定的文件设置密码: `MONGO_INITDB_ROOT_PASSWORD_FILE: /run/secrets/mongo-root` 需要把密码文件通过数据卷挂载到 `/run/secrets/mongo-root`

数据目录: `/data/db`

日志目录: `/data/logs`

#### redis

基础镜像: `redis:4.0-stretch`

密码文件: `/var/secrets/redis-password`

配置文件: `/usr/local/etc/redis/redis.conf`

数据目录: `/data`

#### xunsearch

基础镜像: `hightman/xunsearch:latest`

数据目录: `/usr/local/xunsearch/data`


## repository

https://github.com/docker-labs/docker-fecshop