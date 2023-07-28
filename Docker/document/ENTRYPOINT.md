# ENTRYPOINT

ENTRYPOINT has two forms:

The exec form, which is the preferred form:

```shell
ENTRYPOINT ["executable", "param1", "param2"]
```

The shell form:

```shell
ENTRYPOINT command param1 param2
```

An ENTRYPOINT allows you to configure a container that will run as an executable.

For example, the following starts nginx with its default content, listening on port 80:

```shell
$docker run -i -t --rm -p 80:80 nginx
```

Command line arguments to docker run `<image>` will be appended after all elements in an exec form ENTRYPOINT, and will override all elements specified using CMD.
This allows arguments to be passed to the entry point, i.e., docker run `<image>` -d will pass the -d argument to the entry point.
You can override the ENTRYPOINT instruction using the docker run --entrypoint flag.

The shell form prevents any CMD or run command line arguments from being used, but has the disadvantage that your ENTRYPOINT will be started as a subcommand of /bin/sh -c, which does not pass signals.
This means that the executable will not be the container’s PID 1 - and will not receive Unix signals - so your executable will not receive a SIGTERM from docker stop `<container>`.

Only the last ENTRYPOINT instruction in the Dockerfile will have an effect.
