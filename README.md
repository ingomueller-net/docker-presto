## Simple Dockerized Presto

This projects aims to make it easy to get started with [Presto](https://prestodb.io/). It is based on Docker and [Docker compose](https://docs.docker.com/compose/). Currently, the following features are supported:

* Single machine set-up
* [Function Namespace Manager](https://prestodb.io/docs/current/admin/function-namespace-managers.html) (for [creating functions](https://prestodb.io/docs/current/sql/create-function.html))
* [Hive connector](https://prestodb.io/docs/current/connector/hive.html), Hive Metastore, and pseudo-replicated HDFS (i.e., a single data node without replication)

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

### Uploading Data to HDFS

The `data/` folder is mounted into the HDFS namenode container, from where you can upload it using the HDFS client in that container (`docker-presto_presto_1` may have a different name on your machine; run `docker ps` to find out):

```bash
docker exec -it docker-presto_namenode_1 hadoop fs -mkdir /dataset
docker exec -it docker-presto_namenode_1 hadoop fs -put /data/file.parquet /dataset/
docker exec -it docker-presto_namenode_1 hadoop fs -ls /dataset
```

### Running Queries

You can use the Presto CLI included in the Docker containers of this project (adapt container name if necessary):

```bash
docker exec -it docker-presto_presto_1 presto-cli --catalog hive --schema default
```

Alternatively, you can download the [Presto CLI](https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.246/presto-cli-0.246-executable.jar), rename it, make it executable, and run the following:

```bash
./presto-cli --server localhost:8080 --catalog hive --schema default
```

### Creating an External Table

Suppose you have the following file `test.json`:

```json
{"s": "hello world", "i": 42}
```

Upload it to `/test/test.csv` on HDFS as described above. Then run the following in the Presto CLI:

```SQL
CREATE TABLE test (s VARCHAR, i INTEGER) WITH (EXTERNAL_LOCATION = 'hdfs://namenode/test/', FORMAT = 'JSON');
```

### Adminstrating the MySQL Databases

In case you need to make manual changes or want to inspect the MySQL databases, you can connect to it like this:

```bash
docker exec -it docker-presto_mysql_1 mysql -ppassword
```
