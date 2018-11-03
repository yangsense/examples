## Docker本地仓库--Registry

## 一、Docker安装Registry

使用 `docker-compose` 来安装，配置如下：

```
version: '3.1'
services:
  registry:
    image: registry
    restart: always
    container_name: registry
    ports:
      - 5000:5000
    volumes:
      - /usr/local/docker/registry/data:/var/lib/registry
```

测试:

http://ip:5000/v2/



## 二、安装Docker UI 工具

这里介绍两个 Docker Registry WebUI 工具

- [docker-registry-frontend](https://github.com/kwk/docker-registry-frontend)
- [docker-registry-web](https://hub.docker.com/r/hyper/docker-registry-web/)

两个工具的功能和界面都差不多，我们以 `docker-registry-fontend` 为例讲解

### docker-registry-frontend

我们使用 docker-compose 来安装和运行，`docker-compose.yml` 配置如下：

```
version: '3.1'
services:
  frontend:
    image: konradkleine/docker-registry-frontend:v2
    ports:
      - 8080:80
    volumes:
      - ./certs/frontend.crt:/etc/apache2/server.crt:ro
      - ./certs/frontend.key:/etc/apache2/server.key:ro
    environment:
      - ENV_DOCKER_REGISTRY_HOST=192.168.75.133
      - ENV_DOCKER_REGISTRY_PORT=5000
```

测试:

http://192.168.75.133:5000

## 三、镜像上传

需要在 `/etc/docker/daemon.json` 中增加如下内容（如果文件不存在请新建该文件）

```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ],
  "insecure-registries": [
    "ip:5000"
  ]
}
```

注意：该文件必须符合 json 规范，否则 Docker 将不能启动。

之后重新启动服务。

```
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

### 检查客户端配置是否生效

使用 `docker info` 命令手动检查，如果从配置中看到如下内容，说明配置成功（192.168.75.133 为教学案例 IP）

```
Insecure Registries:
 192.168.75.133:5000
 127.0.0.0/8
```

### 测试镜像上传

我们以`Tomcat`镜像上传功能

```
## 拉取一个镜像
docker pull tomcat

## 查看全部镜像
docker images

## 标记本地镜像并指向目标仓库（ip:port/image_name:tag，该格式为标记版本号）
docker tag tomcat 192.168.75.133:5000/tomcat

## 提交镜像到仓库
docker push 192.168.75.133:5000/tomcat
```

### 查看全部镜像

```
curl -XGET http://192.168.75.133:5000/v2/_catalog
```

### 测试拉取镜像

- 先删除镜像

```
docker rmi tomcat
docker rmi 192.168.75.133:5000/tomcat
```

- 再拉取镜像

```
docker pull 192.168.75.133:5000/tomcat
```

## 四、`SpringBoot`运行jar包

```
vim docker-compose.yml
```

```
version: "3"
services:
  llnao-demo:
    image: 192.168.1.103:5000/llnao-demo
    restart: always
    ports:
      - "8080:8080"
```