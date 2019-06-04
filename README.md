# Docker

> 这将节省大量的金钱和时间，带来内心的平静 --《Docker 实战》 

## 简介 

- 容器，基于 LXC
- 非虚拟化，建立在虚拟机上

### 优点

- 组织有序，不同容器依赖自己需要的软件
- 提高可移植性，在不同平台之间运行
- 保护机器，通过隔离资源

## 镜像

- 多层的，一个镜像依赖多个镜像
- 下载过一次就不再下载，缓存
- 格式

```
repo.com/username/image:tag
```

- 标签发布
  - 提供大版本标签如 1、latest 等
  - 同时提供小版本标签如 1.1 等
  - 发布一个镜像时，同时发布这两个版本号

## 容器

- 状态：运行、暂停、重新启动、退出

## 存储卷

### 绑定存储挂载卷

- 绑定到主机，-v host:container:ro，ro 只读（可选）
- 和其他应用共享文件，容易产生冲突
- 如果目录不存在，docker 就去创建
- 最好避免使用这种方式

### Docker 管理卷

- Docker 管理保存在一个目录
- docker rm -v 顺便删除卷
- -volumes-from 继承卷定义

#### 存储卷设计模式
 
##### 卷容器模式

- 容器只有数据，保存了一些数据配置
- 其他卷复制卷的定义，共享数据
- 适用于容器之间共享数据

##### 数据打包的存储卷模式

- 有二个卷容器：秘钥、配置
- 启动一个容器，复制这两个卷定义，然后拷贝到相同目录下，作为新的配置

##### 多态容器模式

- 程序需要在 /config 目录下寻找配置
- 有一个卷容器，/all 保存了所有配置，保存了一个生产配置、一个测试配置
- 创建新的卷容器， 提取出生产配置到 /config 下
- 复制卷定义即可实现 config 多态

### 备注

- 挂载点必须相同，不能重新指定 from 之前的挂载点
- 挂载点不能冲突，只会接受其中之一
- 不能重新定义挂载的权限
- 卷的用户 id 和容器内的用户 id 是共享的

## 安全

## PID 隔离性

- 隔离了 PID 空间，--pid 选项可以指定 PID 空间，比如 host

## 网络

- 四种网络类型
  - closed 不接受网络连接，拥有回环接口
  - bridge 处于网络中的容器可以互相访问
  - joined 通过连接方式关联共享网络，小心端口冲突
    - 想使用本地回环
    - 想监控网络流量
    - 想不暴露端口进行监控
  - open 全部暴露
- 跨容器网络，搞一个 DNS 服务器

### 容器 DNS

- 只在容器网络内有效
- -hostname 指定主机的名字 
- -dns 指定 dns 服务器
- -dns-search 指定查找域，默认后缀

## 端口

### -p 选项

- -p 3333 容器 3333 暴露到 3333 动态端口
- -p 3333:3333 容器 3333 端口暴露到主机 3333 端口
- -p 192.168.1.1:3333:3333 同时绑定到 192.168.1.1
- -P 暴露所有端口
- -expose 暴露除了需要暴露的其他端口

## Docker 原理

- 镜像分层，镜像分为很多层，最终镜像通过合并层的方式得到结果
- Dockerfile 一条指令，就是一层；一次 commit 就是一层'
- 每一层单独保存结果，所以后续层删除之前层的文件，体积不会减少
- 尽量在同一层操作减少镜像的体积
- 镜像最多有 44 层

## 版本控制

- 有镜像 1.9.2 1.9.1 2.1.1
- latest 指向最新稳定版本
- 1.9 指向 1.9.2 版本
- 1 指向 1.9.2 版本
- 不同特性，在 tag 上增加注释，如 -linux -windows 等

## Dockerfile

- 一个指令为一层，RUN 尽量执行多个指令减少层数（减少镜像体积）
- 用 ${} 引用 ENV 
- [RUN、ENTRYPOINT 的区别](https://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/)
- ADD 指令作为一层，无法清理，推荐使用 RUN 复制文件

### 推荐做法

- 增加启动校验脚本，校验环境变量，快速失败
- 初始化进程监控
- From 镜像某个 sha256 版本有助于保持镜像稳定
- 关闭容器的 SUID、GUID 权限保证镜像安全 

### 镜像分发

- 推送到公有或者私有仓库，简单快捷，一般推荐
- 代码直接构建部署

### Docker-Compose 

- 单机环境多容器编排

## 其他功能

### run 

- 重启策略
- cpu、内存
- 环境变量
- 入口点 

### build

- 指定 Dockerfile

### 其他

- 镜像差异
- 提交镜像
- 导入导出扁平不分层的镜像


