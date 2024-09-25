# 作业要求

!!! warning "前置要求"
    在进行本次作业前，请先阅读[清软论坛说明](../bbs.md)，**确保**对项目的基本操作和开发流程有所了解。

## 应用部署
本次作业需要修改项目清软论坛中的 `Dockerfile`、`docker-compose.yaml`、`app/settings_prod.py` 和 `nginx/app.conf` 文件来实现容器化部署。具体要求如下：

1. ssh 连接至你个人的远端服务器，安装 Docker 并保持后台运行。（你可以参考 [Docker 基础](./docker.md) 中的 Docker 安装部分）
2. 编写合适的 Dockerfile 以及 docker-compose 配置（必要时你需要调整清软论坛的代码）来实现：
    - 清软论坛以 MySQL 为数据库、通过 nginx 以 8000 端口向外提供服务；
    - 通过你的服务器 `ip:8000` 可以访问到论坛前端并正常进行各项操作；
    - 通过你的服务器 `ip:8000/api/v1` 可以直接访问后端的各项 API；
3. 其他要求
    - 你的 Python 版本需要恰好为 3.8.x，你的Nginx版本为 latest，MySQL 版本恰好为 8.1；
    - 各个 service 之间的依赖关系应设置合理（后端服务需要在 MySQL 服务初始化完成后再启动，你可以通过 `depends_on` 和 `healthcheck` 结合实现）。数据库健康检查的间隔时间应为 10 秒，超时时间为 5 秒，重试次数为 5 次；
    - 清软论坛镜像 container 名称为 `app`，nginx 镜像 container 名称为 `nginx`，MySQL 镜像的container 名称为 `mysql`；（你可以在 docker-compose 配置中指定 container name）
    - 你的数据库使用账号 root（默认值），其密码为你的学号，数据库名称为 thss，使用端口3306（默认值）。请注意 MySQL 默认镜像的时区为 UTC，字符集是 latin1，你也需要进行调整。可以通过在 docker-compose 中指定下述值实现：
        ```yaml
        environment:
        - MYSQL_ROOT_PASSWORD=<你的学号>
        - MYSQL_DATABASE=thss
        - TZ=Asia/Shanghai
        command: ['mysqld', '--character-set-server=utf8mb4', '--collation-server=utf8mb4_unicode_ci']
        ```
    - 你的前端文件应该通过 Nginx 的静态文件服务实现（在目前的项目中是通过单独的 React 服务实现的，你需要将前端通过 `npm run build` 打包后的产物，放在后端的 **`build`** 目录下，将其改为 nginx 静态文件服务实现）。你可以使用 volume 实现也可以构建⼀个基于 nginx 的镜像实现。同时你需要在 nginx 中实现反向代理，将 `/api/v1` 的请求转发到后端容器中；
    - nginx 与论坛后端处于⼀个 network，论坛后端与数据库处于⼀个 network。也即通过 nginx 所在容器无法访问数据库容器；
    - 仅 nginx 容器将端口映射给宿主机，端口号为 8000；
    - MySQL 镜像需要指定 `/home/ubuntu/mysql/` 文件夹为持久化存储 Volume，将镜像内 `/var/lib/mysql` 目录挂载到宿主机的 `/home/ubuntu/mysql/` 目录；
    - 确保后端服务已正确连接至 MySQL 数据库而非 SQLite 数据库。在 `docker-compose.yaml` 文件中，必须确保后端容器遵循[环境搭建](../bbs.md)章节中**部署**部分的指引，进行数据库迁移并启动应用。
    - 你的服务需要至少持续工作至作业截止日期后 10 天。如果无法访问，助教会与你取得联系，请保持联系方式的畅通。

!!! tip "提示"
    - 建议在**本地**完成大部分调试工作后，再进行服务器上的部署
    - 多使用 `docker logs` 和 `docker exec` 进行日志分析和容器内部调试
    - 在编写 `docker-compose.yaml` 时，可以从单个容器开始，确保其正常运行后再逐步添加其他容器

!!! Danger "注意事项"
    服务器上的所有文件的最后修改时间和服务开始运行的时间需要**保持在作业截止日期之前**，否则你的作业将会视情况扣除一定分数。
    你提交的代码将被作为**查重的依据**，请务必保证你的代码是自己独立完成的。


## 评测说明
- 作业采用脚本测试+人工测试的方式进行，完成要求即可得分，请注意一定按照要求来进行实现，脚本无法进行评测会扣除大量分数；
    - 注意：如未使用 Docker，最多只能获得 **<span style="color: red">40%</span>** 的分数。
- DDL 日期之后，按照 $0.9^{迟交天数}$ 的衰减系数计算分数，迟交时间未满一天记作一天，最多不超过一周，一周后不再接受作业。
