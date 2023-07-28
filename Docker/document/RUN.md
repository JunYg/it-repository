# [RUN](https://docs.docker.com/engine/reference/builder/#run)

RUN has 2 forms:

- RUN `<command>` (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
- RUN ["executable", "param1", "param2"] (exec form)

The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile.

Layering RUN instructions and generating commits conforms to the core concepts of Docker where commits are cheap and containers can be created from any point in an image’s history, much like source control.

The exec form makes it possible to avoid shell string munging, and to RUN commands using a base image that does not contain the specified shell executable.

The default shell for the shell form can be changed using the SHELL command.

In the shell form you can use a \ (backslash) to continue a single RUN instruction onto the next line. For example, consider these two lines:

```shell
RUN /bin/bash -c 'source $HOME/.bashrc && \
echo $HOME'
```

Together they are equivalent to this single line:

```shell
RUN /bin/bash -c 'source $HOME/.bashrc && echo $HOME'
```

To use a different shell, other than ‘/bin/sh’, use the exec form passing in the desired shell. For example:

```shell
RUN ["/bin/bash", "-c", "echo hello"]
```

> **Note**
>
> The exec form is parsed as a JSON array, which means that you must use double-quotes (“) around words not single-quotes (‘).

Unlike the shell form, the exec form does not invoke a command shell. This means that normal shell processing does not happen. For example, RUN [ "echo", "$HOME" ] will not do variable substitution on $HOME. If you want shell processing then either use the shell form or execute a shell directly, for example: RUN [ "sh", "-c", "echo $HOME" ]. When using the exec form and executing a shell directly, as in the case for the shell form, it is the shell that is doing the environment variable expansion, not docker.

> **Note**
>
> In the JSON form, it is necessary to escape backslashes. This is particularly relevant on Windows where the backslash is the path separator. The following line would otherwise be treated as shell form due to not being valid JSON, and fail in an unexpected way:
>
> ```shell
> RUN ["c:\windows\system32\tasklist.exe"]
> ```
>
>The correct syntax for this example is:
>
> ```shell
> RUN ["c:\\windows\\system32\\tasklist.exe"]
> ```

The cache for RUN instructions isn’t invalidated automatically during the next build. The cache for an instruction like RUN apt-get dist-upgrade -y will be reused during the next build. The cache for RUN instructions can be invalidated by using the --no-cache flag, for example docker build --no-cache.

See the Dockerfile Best Practices guide for more information.

The cache for RUN instructions can be invalidated by ADD and COPY instructions.