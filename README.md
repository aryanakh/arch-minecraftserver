## A Minecraft Server in Docker optimized for Unraid

This docker allows you to run a minecraft server inside of a docker container. It is a fork of [Binhex's Minecraft Basic Server](https://github.com/binhex/arch-minecraftserver) which allows for modded servers with custom server JARs. It also optimizes the JVM memory arguments to address performace issues relating to garbage collection. The default configuration will install the latest vanilla minecraft server. It is recommended to first install this default configuration and check if it works before installing a modded server. After install start up, stop the container. Then you can import all the files from a modded server that you need. Make sure to set the SERVER_TYPE variable to modded. This will prevent the container from pulling the latest release from Mojang. For now, you must rename the server JAR to minecraft_server.jar. In later versions I will modify this to allow custom naming and more! 


**Usage**
```
docker run -d \
    -p <host port>:8222/tcp \
    -p <host port>:25565 \
    --name=<container name> \
    -v <path for config files>:/config \
    -v /etc/localtime:/etc/localtime:ro \
    -e CREATE_BACKUP_HOURS=<frequency of world backups in hours> \
    -e PURGE_BACKUP_DAYS=<specify oldest world backups to keep in days> \
    -e ENABLE_WEBUI_CONSOLE=<yes|no> \
    -e ENABLE_WEBUI_AUTH=<yes|no> \
    -e WEBUI_USER=<specify webui username> \
    -e WEBUI_PASS=<specify webui password> \
    -e WEBUI_CONSOLE_TITLE=<specify webui console title> \
    -e SERVER_TYPE=<specify if server is mojang or modded> \
    -e JAVA_MIN_HEAP_SIZE=<java min heap size in megabytes> \
    -e JAVA_MAX_HEAP_SIZE=<java max heap size in megabytes> \
    -e JAVA_MAX_THREADS=<java max number of threads> \
    -e UMASK=<umask for created files> \
    -e PUID=<uid for user> \
    -e PGID=<gid for user> \
    aryanakh/arch-minecraftserver
```

Please replace all user variables in the above command defined by <> with the correct values.

**Access Minecraft Server console**

Requires `-e ENABLE_WEBUI_CONSOLE=yes`

`http://<host ip>:8222`

**Example**
```
docker run -d \
    -p 8222:8222/tcp \
    -p 25565:25565 \
    --name=minecraftserver \
    -v /apps/docker/minecraftserver:/config \
    -v /etc/localtime:/etc/localtime:ro \
    -e SERVER_TYPE=mojang \
    -e CREATE_BACKUP_HOURS=12 \
    -e PURGE_BACKUP_DAYS=14 \
    -e ENABLE_WEBUI_CONSOLE=yes \
    -e ENABLE_WEBUI_AUTH=yes \
    -e WEBUI_USER=admin \
    -e WEBUI_PASS=minecraft \
    -e WEBUI_CONSOLE_TITLE=Minecraft Server \
    -e JAVA_MIN_HEAP_SIZE=1024M \
    -e JAVA_MAX_HEAP_SIZE=1024M \
    -e JAVA_MAX_THREADS=1 \
    -e UMASK=000 \
    -e PUID=0 \
    -e PGID=0 \
    aryanakh/arch-minecraftserver
```

**Notes**

If you do **NOT** want world backups and/or purging of backups then set the value to '0' for env vars 'CREATE_BACKUP_HOURS' and/or 'PURGE_BACKUP_DAYS'.

Env vars 'JAVA_MIN_HEAP_SIZE' value and 'JAVA_MAX_HEAP_SIZE' values must be a multiple of 1024 and greater than 2MB. Due to java's garbage collection, it is recommended to set the Min and Max heap at the same value. Having the same minimum and maximum not only optimize RAM usages but also improves garbage collection behavior.

User ID (PUID) and Group ID (PGID) can be found by issuing the following command for the user you want to run the container as:-

```
id <username>
```
