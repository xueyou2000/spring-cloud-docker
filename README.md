# spring-cloud-docker

一个spring cloud微服务, 与nexus maven私服和nexus docker私服集成例子。
本项目在`windows10`下开发，`docker`, `nexus`服务都在`wsl2`中运行。

## 前提需求

-   `nexus` 私服服务
-   `docker` 本机或远程服务

### 环境配置

假设已经在`wsl2`中安装了`docker`, 现在来安装`nexus`私服。

1.  `docker pull sonatype/nexus3` 拉镜像
2.  `docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 -p 8084:8084 --restart=always --name docker-nexus sonatype/nexus3`
    -   8081：nexus3网页端
	-   8082：docker(hosted)私有仓库，可以pull和push
	-   8083：docker(proxy)代理远程仓库，只能pull
	-   8084：docker(group)私有仓库和代理的组，只能pull
3.  访问`http://localhost:8081`, 默认账号为 admin ,密码在服务器得文件中, 所以需要进容器里去看。
    `docker exec -it docker-nexus bash` 进入容器
    `cat /nexus-data/admin.password` 打印密码, 类似`f890b4dc-b8ea-410e-96e6-58f2b8c47419`。 
4.  登录后，首次会让你修改密码，这里我改为123456, 后续会多次用到账号密码。 然后创建3个docker仓库
    -   `proxy`类型起名`docker-aliyun`, 设置`Remote storage`为`https://4ablr64w.mirror.aliyuncs.com`, 设置http端口8083
    -	`hosted`类型起名`docker-my`, 设置http端口8082
    -	`group`类型起名`docker-public`, 设置http端口8084, 并把上2个加进来
5.  编辑 `vim /etc/docker.daemon.json`
    ```json
    {
      "registry-mirrors": ["https://4ablr64w.mirror.aliyuncs.com"],
      "insecure-registries": ["localhost:8082"],
      "hosts": ["tcp://0.0.0.0:2376", "unix:///var/run/docker.sock"]
    }
    ```
    1. 记住，registry-mirrors 不要用私服里得，否则守护进程无法起起来，这里是我得阿里云地址
    2. insecure-registries 为docker(hosted)私有仓库, 会发布到这里，并加了这一句可以解决https问题
    3. hosts 让外部(wsl2上得windows)可以远程访问
6.  重启docker, ubuntu下, `sudo server docker restart`
7.  在`windows`中, 加下环境变量, `DOCKER_HOST` 为 `172.30.52.47:2376` 这里ip为wsl2里的ip,通过`ifconfig`查看, 寻找类似`172.30.52.47`这种的ip就是了

**maven**
`windows10`中开发,`maven`也在`windows10`里，找到`maven`安装目录的配置文件 `/conf/setting.xml`, 修改加入下面片段

`server`里的`id`对应主`pom`配置里 `repository`和`snapshotRepository`的`id`
账号密码是`nexus`的账号密码

```pom
<servers>
    <server>
      <id>maven-releases</id>
      <username>admin</username>
      <password>123456</password>
    </server>
    <server>
      <id>maven-snapshots</id>
      <username>admin</username>
      <password>123456</password>
    </server>
</servers>
```

## 上手准备

**修改主pom**

-   `<nexus-address>172.30.52.47</nexus-address>` 改为`wsl2`中的地址
-   `<nexus-username>admin</nexus-username>` 改为`nexus`的账号
-   `<nexus-password>123</nexus-password>` 改为`nexus`的密码