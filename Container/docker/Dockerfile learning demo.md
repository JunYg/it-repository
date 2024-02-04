# Dockerfile

## **Command**

### `FROM`

基础镜像

### ~~`MAINTAINER`~~

`Dockerfile`  的维护者信息，姓名、邮箱等，可以在 `docker inspect` 中查看。

```dockerfile
MAINTAINER author <email> 
```

### `LABEL`

以键值对的形式给镜像添加一些元数据（`metadata`）

```dockerfile
LABEL <key>=<value>
LABEL com.wk.sd.supply="author"
```

### `COPY`

从上下文目录中复制文件或者目录到容器里指定路径。

```dockerfile
COPY <source> <destination>
```

目标路径：容器内指定路径，如果不存在自动创建。

### `ADD`

 源路径可以是一个 `URL`，这会在构建时下载URL的内容并复制。

 源路径可以是一个 `tar` 文件(以 `.tar`、`.tar.gz`、`.tgz` 或 `.bzip2` 结尾)，这会在构建时自动解压到 <目标路径>。

#### `COPY` 与 `ADD` 比较

1. 从构建上下文复制文件时,推荐使用 `COPY`，因为它更简单，用法也更清晰。
1. 需要自动解压 `tar` 文件或下载 `URL` 内容时,需要使用 `ADD`。
1. 否则的情况下，`ADD` 和 `COPY` 的行为基本相同。
1. 如果存在同名文件，`COPY` 和 `ADD` 都会替换目标路径中的文件。
1. 目标路径不需要事先创建，如果目录不存在会自动创建。
1. 源路径支持通配符。
1. 源路径可以是绝对路径，这会从主机中复制文件而非构建上下文。

### `RUN`

用于执行后面跟着的命令。

每执行一次都会在 `docker` 新建一层 `layer`。

用 `&&` 符号连接多条命令，则只会构建**一层**。

执行时间点：`docker build`。

```dockerfile
RUN <command 1> \
    && <command 2>
```

### `CMD`

为启动容器指定需要运行的程序，程序执行结束，容器结束。

主要用途是为容器指定默认的启动命令和参数，这在构建开发环境或运行一些服务时很有用。

可以被 `docker run` 指定要运行的程序覆盖。

如果有多个 `CMD` 命令，仅最后一条生效。

执行时间点 `docker run`。

```dockerfile
CMD <shell command> <param 1> <param 2> ...
CMD ['<executable file>', '<param 1>', '<param 2>', ...]
CMD ['<param 1>', '<param 2>', ...] # 为 ENTRYPOINT 指定程序默认参数
```

### `ENTRYPOINT`

与 `CMD` 类似，但是不会被 `docker run` 命令行参数指定的指令覆盖，命令行参数会被当做参数给 `ENTRYPOINT` 指定的程序。

主要用途是将 `Dockerfile` 定制为执行某个具体的应用或服务。

如果有多条 `ENTRYPOINT`，仅最后一条生效。

```dockerfile
ENTRYPOINT <shell command> <param 1> <param 2> ...
ENTRYPOINT ['<executable file>', '<param 1>', '<param 2>', ...]
```

### `ENV`

设置构建镜像的环境变量。

在**构建阶段**和**运行阶段**同时设置环境变量。

```dockerfile
ENV <key> <value>
ENV <key 1>=<value 1> <key 2>=<value 2> ...
```

### `ARG`

**仅**在**构建阶段**设置环境变量。

只在 `docker build` 过程中生效，构建后的镜像不存在此环境变量。

可以通过 `docker build --build-arg <key>=<value>` 覆盖。

```dockerfile
ARG <key>[=<default value>]
```

`ENV` 与 `ARG` 比较：

1. `ENV` 会在构建阶段和运行阶段均设置环境变量，`ARG` 仅在构建阶段设置。
1. `ENV` 的值可以在 `Dockerfile` 中直接指定，`ARG` 的值需要在 `docker build` 时使用 `--build-arg` 来设置。
1. `ARG` 允许在构建时作为变量传递值，这在构建一些通用的 Dockerfile 时很有用。
1. 如果 `ENV` 和 `ARG` 指定了同名变量，则 `docker build` 时通过 `--build-arg` 设置的 `ARG` 值会覆盖 `ENV` 的值。
1. 构建完成后，`ARG` 所设置的变量就会消失，不会保留在最终镜像中。

### `VOLUME`

定义匿名数据卷。

在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。

在启动容器 `docker run` 的时候，我们可以通过 `-v` 参数修改挂载点。

### `EXPOSE`

声明端口。

帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。

在运行时使用随机端口映射时，也就是 `docker run -P` 时，会自动随机映射 `EXPOSE` 的端口。

### `WORKDIR`

指定工作目录。

用 `WORKDIR` 指定的工作目录，会在构建镜像的每一层中都存在。

以后各层的当前目录就被改为指定的目录，如该目录不存在，`WORKDIR` 会帮你建立目录。

`docker build` 构建镜像过程中的，每一个 `RUN` 命令都是新建的一层，只有通过 `WORKDIR` 创建的目录才会一直存在。

```dockerfile
WORKDIR <worker directory>
```

### `USER`

用于指定执行后续命令的用户和用户组，用户和用户组必须提前已经存在。

```dockerfile
USER <username>[:<usergroup>]
```

### `HEALTHCHECK`

### `ONBUILD`

## 镜像构建

`docker build`

### 上下文路径

`Dockerfile` 所在的目录及其子目录。

构建镜像时，`Docker` 会将上下文路径中的文件和目录复制到镜像中。

`Dockerfile` 中的诸如 `COPY` 和 `ADD` 之类的指令中的源文件路径都是**相对于上下文路径的**。

可以通过将文件或目录挂载为容器的卷，或将其保存在 `Dockerfile` 以外的位置,然后在 `Dockerfile` 中使用绝对路径引用，来避免将其添加到上下文路径中。这在构建包含敏感数据的镜像时很有用,可以避免该数据被包含在上下文路径中。

`Dockerfile` 的上下文路径定义了构建镜像时 `Docker` 可以访问的文件和目录。`COPY` 和 `ADD` 等指令中的源文件路径都是相对于上下文路径的，但是也可以**使用绝对路径来引用上下文路径外的文件**。这可以用于避免一些不想添加到镜像中的敏感数据被包含在上下文路径中。
