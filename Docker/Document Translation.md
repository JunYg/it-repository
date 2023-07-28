# 文档翻译

## Dockerfile 命令

### [CMD](https://docs.docker.com/engine/reference/builder/#cmd)

CMD 命令有三种模式:

- CMD ["executable","param1","param2"] (exec form, 首选模式)
- CMD ["param1","param2"] (作为 ENTRYPOINT 命令默认参数)
- CMD command param1 param2 (shell 模式)

There can only be one CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.

一个 Dockerfile 中只能有一个 CMD 命令。如果你使用了多个 CMD 命令，只有最后的 CMD 命令生效。

The main purpose of a CMD is to provide defaults for an executing container. These defaults can include an executable, or they can omit the executable, in which case you must specify an ENTRYPOINT instruction as well.

CMD 的主要目的是为执行的 container 提供默认值。这些默认值可以包含可执行命令，或是可以省略可执行命令，如果忽略你必须指定一个 ENTRYPOINT 命令。

If CMD is used to provide default arguments for the ENTRYPOINT instruction, both the CMD and ENTRYPOINT instructions should be specified with the JSON array format.

如果 CMD 用来给 ENTRYPOINT 命令提供默认参数，那么 CMD 和 ENTRTYPOINT 命令都应使用 JSON 数组格式。

> **Note**
> 
> The exec form is parsed as a JSON array, which means that you must use double-quotes (“) around words not single-quotes (‘).
> 
> exec 格式会被解析为 JSON 数组，也就意味着你必须使用半角双引号（"）而不是半角单引号（'）。

Unlike the shell form, the exec form does not invoke a command shell. This means that normal shell processing does not happen. 
For example, CMD [ "echo", "$HOME" ] will not do variable substitution on $HOME. 
If you want shell processing then either use the shell form or execute a shell directly, for example: CMD [ "sh", "-c", "echo $HOME" ]. 
When using the exec form and executing a shell directly, as in the case for the shell form, it is the shell that is doing the environment variable expansion, not docker.

When used in the shell or exec formats, the CMD instruction sets the command to be executed when running the image.

If you use the shell form of the CMD, then the <command> will execute in /bin/sh -c:


FROM ubuntu
CMD echo "This is a test." | wc -
If you want to run your <command> without a shell then you must express the command as a JSON array and give the full path to the executable. This array form is the preferred format of CMD. Any additional parameters must be individually expressed as strings in the array:


FROM ubuntu
CMD ["/usr/bin/wc","--help"]
If you would like your container to run the same executable every time, then you should consider using ENTRYPOINT in combination with CMD. See ENTRYPOINT.

If the user specifies arguments to docker run then they will override the default specified in CMD.

Note

Do not confuse RUN with CMD. RUN actually runs a command and commits the result; CMD does not execute anything at build time, but specifies the intended command for the image.
