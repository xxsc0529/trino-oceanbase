## Usage

### Building Plugin

Build requirements

* Java 17.0.4+, 64-bit

Run the following command from the project root directory:

    ./mvnw clean package -DskipTests

There plugin files should be under the `target` directory.

### Running with Docker

Firstly, start a trino Docker container.

```shell
docker run --name trino -d trinodb/trino:433
```

Create `log.properties`.

```text
io.trino=DEBUG
```

Create `oceanbase.properties`.

```properties
connector.name=oceanbase
connection-url=jdbc:oceanbase://localhost:2883/${ENV:USER}
connection-user=${ENV:USERNAME}
connection-password=${ENV:PASSWORD}
oceanbase.compatible-mode=oracle
oceanbase.auto-reconnect=true
oceanbase.remarks-reporting.enabled=true
decimal-mapping=ALLOW_OVERFLOW
decimal-rounding-mode=HALF_UP
```

Patch plugin and config files to container and restart container.

```shell
# add plugin files
docker cp trino-oceanbase-433 trino:/data/trino/plugin/oceanbase

# add log config file
docker cp log.properties trino:/etc/trino

# add catalog config file
docker cp oceanbase.properties trino:/etc/trino/catalog

# add timezone files
docker cp /usr/share/zoneinfo trino:/usr/share/zoneinfo
docker cp /usr/share/zoneinfo/Asia/Shanghai trino:/etc/localtime

# restart container
docker restart trino
```

Then you can execute query with the cli.

```shell
docker exec -it trino trino
```

```sql
SHOW CATALOGS;
```
