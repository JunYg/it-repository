# Docker 原理

## Namespace

> 进程隔离
>
> 类型：`mnt`, `net`, `ipc`, `user`, `pid`, `uts`, `cgroup`, `time`

```shell
# 查看当前系统拥有的 namespace
$ lsns
# /proc/<pid>/ns
$ ls -al /proc/<pid>/ns
# 使用其他进程的命名空间运行程序。进入到指定 namespace 查看相关配置
$ nsenter 
# 运行一个程序，其中一些名称空间不与父级共享。在指定 namespace 下执行程序
$ unshare 
```

### 实现原理

### Shell command

- lsns

    ```shell
    Usage:
        lsns [options] [<namespace>]

    List system namespaces.

    Options:
        -J, --json             use JSON output format
        -l, --list             use list format output
        -n, --noheadings       don't print headings
        -o, --output <list>    define which output columns to use
            --output-all       output all columns
        -p, --task <pid>       print process namespaces
        -r, --raw              use the raw output format
        -u, --notruncate       don't truncate text in columns
        -W, --nowrap           don't use multi-line representation
        -t, --type <name>      namespace type (mnt, net, ipc, user, pid, uts, cgroup, time)

        -h, --help             display this help
        -V, --version          display version

    Available output columns:
            NS  namespace identifier (inode number)
          TYPE  kind of namespace
          PATH  path to the namespace
        NPROCS  number of processes in the namespace
           PID  lowest PID in the namespace
          PPID  PPID of the PID
       COMMAND  command line of the PID
           UID  UID of the PID
          USER  username of the PID
       NETNSID  namespace ID as used by network subsystem
          NSFS  nsfs mountpoint (usually used network subsystem)
           PNS  parent namespace identifier (inode number)
           ONS  owner namespace identifier (inode number)
    ```

- nsenter

    ```shell
    Usage:
        nsenter [options] [<program> [<argument>...]]

    Run a program with namespaces of other processes.

    Options:
        -a, --all              enter all namespaces
        -t, --target <pid>     target process to get namespaces from
        -m, --mount[=<file>]   enter mount namespace
        -u, --uts[=<file>]     enter UTS namespace (hostname etc)
        -i, --ipc[=<file>]     enter System V IPC namespace
        -n, --net[=<file>]     enter network namespace
        -p, --pid[=<file>]     enter pid namespace
        -C, --cgroup[=<file>]  enter cgroup namespace
        -U, --user[=<file>]    enter user namespace
        -T, --time[=<file>]    enter time namespace
        -S, --setuid <uid>     set uid in entered namespace
        -G, --setgid <gid>     set gid in entered namespace
            --preserve-credentials do not touch uids or gids
        -r, --root[=<dir>]     set the root directory
        -w, --wd[=<dir>]       set the working directory
        -F, --no-fork          do not fork before exec'ing <program>
        -Z, --follow-context   set SELinux context according to --target PID

        -h, --help             display this help
        -V, --version          display version
    ```

- unshare

    ```shell
    Usage:
    unshare [options] [<program> [<argument>...]]

    Run a program with some namespaces unshared from the parent.
    运行一个程序，其指定名称空间不与父级共享。

    Options:
        -m, --mount[=<file>]      unshare mounts namespace
        -u, --uts[=<file>]        unshare UTS namespace (hostname etc)
        -i, --ipc[=<file>]        unshare System V IPC namespace
        -n, --net[=<file>]        unshare network namespace
        -p, --pid[=<file>]        unshare pid namespace
        -U, --user[=<file>]       unshare user namespace
        -C, --cgroup[=<file>]     unshare cgroup namespace
        -T, --time[=<file>]       unshare time namespace

        -f, --fork                fork before launching <program>
        --map-user=<uid>|<name>   map current user to uid (implies --user)
        --map-group=<gid>|<name>  map current group to gid (implies --user)
        -r, --map-root-user       map current user to root (implies --user)
        -c, --map-current-user    map current user to itself (implies --user)

        --kill-child[=<signame>]  when dying, kill the forked child (implies --fork)
                                    defaults to SIGKILL
        --mount-proc[=<dir>]      mount proc filesystem first (implies --mount)
        --propagation slave|shared|private|unchanged
                                modify mount propagation in mount namespace
        --setgroups allow|deny    control the setgroups syscall in user namespaces
        --keep-caps               retain capabilities granted in user namespaces

        -R, --root=<dir>          run the command with root directory set to <dir>
        -w, --wd=<dir>            change working directory to <dir>
        -S, --setuid <uid>        set uid in entered namespace
        -G, --setgid <gid>        set gid in entered namespace
        --monotonic <offset>      set clock monotonic offset (seconds) in time namespaces
        --boottime <offset>       set clock boottime offset (seconds) in time namespaces

        -h, --help                display this help
        -V, --version             display version
    ```

## Cgroups

> 对进程进行资源控制和监控，`cpu`、`cpuacct`、`cpuset`、`devices`、`blkio`、`freezer`、`memory`、`net_cls`、`ns`、`pid`
>
> Linux 系统通过层级（Hierarchy）结构管理
>
> ![Cgroups](./control%20groups.png)

```shell
# cgroup 配置路径 /sys/fs/cgroup
```

### Linux 调度器

- CFS，Completely Fair Schedule，完全公平调度器

    > 虚拟运行时间：vruntime = 实际执行时间 * 1023 / 权重
    >
    > ![CFS 进程调度](./CFS%20Process%20Schedule.png)

- RT

### CPU 子系统

### Memory 子系统

> 压榨型资源，会触发 killer
>
> `memory.oom_control`

### Cgroups driver

- Linux：系统默认为 systemd
- Docker: Docker 默认为 cgroupfs

> k8s 默认 --cgroup-driver=systemd，如果运行的 cgroup 不一致报错

## 文件系统
