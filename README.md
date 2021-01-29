## Simple Dockerized Presto

This projects aims to make it easy to get started with [Presto](https://prestodb.io/). It is based on Docker and [Docker compose](https://docs.docker.com/compose/). Currently, the following features are supported:

* Single machine set-up
* [Function Namespace Manager](https://prestodb.io/docs/current/admin/function-namespace-managers.html) (for [creating functions](https://prestodb.io/docs/current/sql/create-function.html))
* More features planned (the current ones do not allow to access any data)

### Starting Presto

The following should be enough to bring up all required services:

```bash
docker-compose up
```

This brings up a MySQL server, which takes a bit to start for the first time, and which Presto depends on. If starting the services fails the first time, try interrupting them (with `CTRL+C`) and bringing them up again.

If you are behind a corporate firewall, you will have to configure Maven (which is used to build part of Presto) as follows before running above command:

```bash
export MAVEN_OPTS="-Dhttp.proxyHost=your.proxy.com -Dhttp.proxyPort=3128 -Dhttps.proxyHost=your.proxy.com -Dhttps.proxyPort=3128"
```

### Running Queries

You can use the Presto CLI included in the Docker container of this project (`docker-presto_presto` may have a different name on your machine; run `docker images` to find out):


```bash
docker run --rm -it --entrypoint /opt/presto/bin/presto docker-presto_presto --server $(hostname):8080
```

Alternatively, you can download the [Presto CLI](https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.246/presto-cli-0.246-executable.jar), rename it, make it executable, and run the following:

```bash
./presto-cli.jar --server localhost:8080
```
