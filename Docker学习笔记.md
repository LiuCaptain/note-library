# Docker 学习笔记

> **容器**（Container）是指一种技术，Docker 只是一种容器技术的实现，所以 `Docker !== Container`

1. ##### Docker Desktop 版本与 Docker Linux 版本有什么区别？

   Docker 是 CS（Client Server）架构，Docker Desktop 版本除了安装 Client 的命令行接口 + Server 的 Engine（包含 Docker 的 daemon）以外，还会安装一些其他东西；而 Docker Server 版本只会安装 Client 的命令行接口 + Server 的 Engine（包含 Docker 的 daemon）

2. ##### 在 Linux 上安装 Docker

   - 通过 curl 命令下载安装脚本

     ```bash
     curl -fsSL get.docker.com -o get-docker.sh
     ```

     执行上面命令会下载保存一个 `get-docker.sh` 文件

   - 执行下载的 shell 脚本安装 Docker

     ```bash
     sh get-docker.sh
     ```

3. ##### 查看 Docker 版本的命令

   ```bash
   docker version
   ```

4. ##### 启动 Docker 守护进程的命令

   ```bash
   systemctl start docker
   ```

5. ##### 重新加载 Docker 服务配置

   ```bash
   systemctl daemon-reload
   ```

   具体过程:

   - 检查和解析所有 `systemd` 单元文件的更改
   - 将更改的单元文件加载到内存中
   - 不会停止或启动任何服务，仅仅是让 `systemd` 更新它的配置状态

   > `systemd` 是一个现代的初始化系统（init system）和服务管理器，专为 Linux 系统设计

6. ##### 重启 Docker 服务

   ```bash
   systemctl restart docker
   ```

   具体过程:

   - 停止 Docker 服务
   - 启动 Docker 服务
   - 加载最新的服务配置（如果先执行了 `systemctl daemon-reload`）

7. ##### 查看 Docker 状态的基本信息

   ```bash
   docker info
   ```

8. **查看 Docker 所有命令**

   - 执行下面命令会输出所有 Docker 命令；Docker 的基本命令结构是：`docker [操作对象] [操作行为]`

     ```bash
     docker
     ```

   - 执行下面命令会输出所有可对 Docker container 操作的行为

     ```bash
     docker container --help
     ```

   - 执行下面命令会输出所有可对 Docker image 操作的行为

     ```bash
     docker image --help
     ```

9. ##### 查看 Docker 容器命令

   - 查看正在运行的容器

     ```bash
     docker container ls
     
     docker container ps // 旧版本
     ```

   - 查看所有容器，包括正在运行和已经停止的容器

     ```bash
     docker container ls -a
     
     docker container ps -a  // 旧版本
     ```

10. ##### 查看 Docker 镜像命令

    ```bash
    docker image ls
    ```

11. ##### 删除 Docker 镜像命令

    ```bash
    docker image rm <IMAGE>
    ```

12. ##### Docker 镜像与 Docker 容器

    - Docker 镜像
      1. Docker 镜像是一个 `read-only` 文件
      2. Docker 镜像包含文件系统、源码、库文件、依赖、工具等一些运行 application 所需要的文件
      3. Docker 镜像可以理解成一个模板
      4. Docker 镜像具有分层的概念

    - Docker 容器

      1. Docker 容器是 Docker 镜像的实例

      2. Docker 容器实质是复制 Docker 镜像并在镜像最上层加一层 read-write 的层，称之为 **容器层**（container layer）

         ![](/Users/ethereal/Documents/PersonalProject/NoteLibrary/images/container.svg)

13. ##### 创建一个 Docker 容器

    ```bash
    docker container run <IMAGE>
    ```

14. ##### 创建一个拥有名字的 Docker 容器

    ```bash
    docker container run --name <NAME> <IMAGE>
    ```

15. ##### 创建一个具有端口映射的 Docker 容器

    ```bash
    docker container run -p <宿主机端口>:<容器内端口> <IMAGE>
    
    docker container run --publish <宿主机端口>:<容器内端口> <IMAGE>
    ```

16. ##### 修改 Docker 容器名称

    ```bash
    docker rename <CONTAINER> <NEW NAME>
    ```

17. ##### 停止一个 Docker 容器

    ```bash
    docker container stop <CONTAINER>
    ```

18. ##### 删除一个 Docker 容器

    ```bash
    docker container rm <CONTAINER>
    ```

19. ##### 强制删除一个 Docker 容器

    ```bash
    docker container rm <CONTAINER> -f
    ```

20. ##### 查询所有 Docker 容器的 id（包括正在运行和已经停止的容器）

    ```bash
    docker container ls -aq
    ```

21. ##### 批量停止 Docker 容器

    ```bash
    docker container stop <CONTAINER1> <CONTAINER2> <CONTAINER3>...
    ```

22. ##### 停止所有 Docker 容器

    ```bash
    docker container stop $(docker container ls -aq)
    ```

23. ##### 批量删除 Docker 容器

    ```bash
    docker container rm <CONTAINER1> <CONTAINER2> <CONTAINER3>...
    ```

24. ##### 删除所有 Docker 容器

    ```bash
    docker container rm $(docker container ls -aq)
    ```

25. ##### Docker 容器的 attached 和 detached 模式

    创建 Docker 容器默认是 attached 模式，attached 模式的容器是在前台运行，会把容器的输入输出结果反映到到本地的，比如访问服务会在命令行打印出访问日志；本地的输入输出也会反映到容器中去，比如输入 ctrl + c 会停止容器。但 detached 模式并不会有输入输出的反映

    - 创建一个 detached 模式的容器

      ```bash
      docker container run -d <IMAGE>
      
      docker container run --detach <IMAGE>
      ```

    - 将 detached 模式的容器切换为 attached 模式

      ```bash
      docker container attach <CONTAINER>
      ```

26. ##### 查看 Docker 容器日志

    - 查看 Docker 容器日志

      ```bash
      docker container logs <CONTAINER>
      ```

    - 实时跟踪查看 Docker 容器日志

      ```
      docker container logs -f <CONTAINER>
      ```

27. ##### 创建一个交互式运行的 Docker 容器

    ```bash
    docker container run -it <IMAGE> <Command>
    ```

    当执行 `exit` 命令，这个交互式运行的容器会退出

28. ##### 要可交互式的进入正在运行的 Docker 容器

    ```bash
    docker container exec -it <CONTAINER> <Command>
    ```

    当执行 `exit` 命令，这个运行的容器**不会**退出

29. ##### 查询 Docker 容器启动了哪些进程

    ```bash
    docker container top <CONTAINER>
    ```

    这些进程运行在 Docker engine 的宿主机上

30. ##### 创建 Docker 容器时背后发生了什么？

    比如执行下面命令

    ```bash
    docker container run -d -p 80:80 --name customName nginx
    ```

    ![container-back](/Users/ethereal/Documents/PersonalProject/NoteLibrary/images/container-back.svg)

31. ##### 获取镜像的方式

    获取镜像常用的方式有三种，如下图

    <img src="/Users/ethereal/Documents/PersonalProject/NoteLibrary/images/getImage.png" alt="getImage" style="zoom:50%;" />

32. ##### 拉取 docker 镜像的命令

    ```bash
    docker image pull <ImageName>:<Tag>
    ```

    注意：如果没填写 `tag` 则默认拉取的是 `latest` 版本

33. ##### 显示 docker 镜像详细信息的命令

    ```bash
    docker image inspect <IMAGE>
    ```

34. ##### 导出 docker 镜像的命令

    ```bash
    docker image save <ImageName>:<Tag> -o <OutputImageFileName>
    ```

35. ##### 导入 docker 镜像的命令

    ```bash
    docker image load -i <ImageFilePath>
    ```

36. ##### 什么 Dockerfile？

    - Dockerfile 是用于构建 docker 镜像的文件
    - Dockerfile 里面包含了构建镜像所需的“指令”
    - Dockerfile 有其特定的语法规则

37. ##### 构建 docker 镜像的命令

    ```bash
    docker image build -t <ImageName>:<Tag> -f <DockerFilePath> <构建上下文目录>
    ```

    注意：

    - 如果省略 `Tag`，则默认为最新版本 `latest`

    - `-f` 后面必须跟的是一个 **Dockerfile 文件路径**，**不能是目录（比如 `.`）**
    - 如果不加 `-f`，则默认就是 `./Dockerfile`

38. ##### 根据现有 docker 镜像，生成拥有新的 ImageName 和 Tag 的镜像的命令

    ```bash
    docker image tag <OriginalImageName>:<OriginalTag> <NewImageName>:<NewTag>
    ```

39. ##### 登录 docker hub 的命令

    ```bash
    docker login
    ```

    然后在命令行中输入 Username 和 Password

40. 推送 docker 镜像到 docker hub 的命令

    ```bash
    docker image push <DockerAccount>/<ImageName>:<Tag>
    ```

41. 将 docker container 提交成为一个新的 Image 的命令

    ```bash
    docker container commit <CONTAINER> <NewImageName>:<NewTag>
    ```

42. 查看镜像层结构的命令

    ```bash
    docker image history <ImageName>
    ```

43. Dockerfile 的语法

    1. 通过 `Form` 指定基础镜像

       基础镜像选择的基本原则

       - 官方镜像优先于非官方的镜像，如果没有官方镜像，则尽量选择 Dockerfile 开源的镜像
       - 选择固定版本的 tag 的镜像，而不是每次都使用 latest 版本
       - 尽量选择体积小的镜像

    2. `RUN` 用于在 Image 里执行指令，如比安装软件，下载文件等等(`RUN` 命令中常用来连接命令的符号 “&&\”)

    3. 往镜像里面复制文件有两种命令，分别为 `COPY` 和 `ADD`

       - 复制普通文件

         `COPY` 和 `ADD` 都可以把本地的一个文件复制到镜像里，如果目标目录不存在，则会自动创建

         ```bash
         FROM python:3.9.5-alpine3.13
         COPY hello.py /app/hello.py
         ```

         上面代码，docker 会把本地的 `hello.py` 复制到 `/app` 目录下，如果 `/app` 这个目录不存在 docker 会自动创建

       - 复制压缩文件

         `ADD` 比 `COPY` 高级一点的地方是如果复制的是一个 gzip 等等格式的压缩文件，`ADD` 会自动解压文件

         ```bash
         FROM python:3.9.5-alpine3.13
         COPY hello.tar.gz /app/
         ```

         上面代码，docker 会先将 `hello.tar.gz` 解压，然后将解压后的文件复制到 `/app` 目录中

    4. `WORKDIR` 用来设置 **当前工作目录**，影响后续所有相关指令的相对路径

       在多次使用 `WORKDIR` 中，如果路径不是以 `/` 开头，则相对于上一个 `WORKDIR` 的路径，例如：

       ```bash
       WORKDIR /usr/src
       WORKDIR app
       
       #这等效于：
       WORKDIR /usr/src/app
       ```

       `WORKDIR` 指定的目录如果不存在，docker 会 **自动创建** 这个目录

    5. 

    6. 

44. 请观看 4-5

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    

    
