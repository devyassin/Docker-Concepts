# Data Management in Docker



## Separation of concerns


![separetion.PNG](https://eraser.imgix.net/workspaces/qxQkjd33aYZqUcTLpYiT/KUexkXbQT2QUJaJtNJkgLRDJmBP2/5fJc8O9MR727UtGdtIYuM.png?ixlib=js-3.7.0 "separetion.PNG")





|                 Volumes |              Bind mounting |
| ----- | ----- |
| <ul><li>Volumes are stored in a part of the host file system which is managed by docker</li></ul> | <ul><li>Bind mounts are stored anywhere on the host system</li></ul> |
| <ul><li>Volumes are the best way to persist data in docker</li></ul> | <ul><li>Non Docker process can modify this part of the system</li></ul> |
| <ul><li>Non docker process should not modify this part of the system</li></ul> | <ul><li>To use bind mounts , the file or directory does not need to exist on your docker host already . If it doesn't exist, it will be created on demand</li></ul> |
| <ul><li>we can link a volume to multiple containers</li></ul> |  |
| <ul><li>we can link (mount) a volume to multiple containers</li></ul> |  |
- [ ] Name a volume
```yaml
-v mariavolume:/var/lib/mysql
```
`mariavolume`: This is the name of the volume. If the volume does not exist, Docker will create it.

`/var/lib/mysql`: This is the directory inside the container where the volume will be mounted.



Imagine you have a MariaDB database running in a Docker container. The database stores all its data in the `/var/lib/mysql` directory inside the container. By using the `-v mariavolume:/var/lib/mysql` option:

- When you start the container, Docker mounts the `mariavolume`  volume to `/var/lib/mysql` .
- Any data written by MariaDB to `/var/lib/mysql`  is actually written to the `mariavolume`  on the host system.
- If you stop and remove the container, the data remains intact in the `mariavolume` .
- When you start a new container with the same volume mount, the new container can access the existing data.


**Reason**: By default, data created inside a Docker container is not persistent. If the container is stopped or removed, all the data inside it is lost.

**Benefit**: Using a volume ensures that the data stored in `/var/lib/mysql`  (which is the default data directory for MariaDB) is persisted across container restarts and even if the container is deleted.



### Basic Commands for volumes
```yaml
# Create a Volume
docker volume create VOLUME_NAME

# Inspect a Volume
docker volume inspect VOLUME_NAME

# List Volumes
docker volume ls

# Remove a Volume
docker volume rm VOLUME_NAME

# Prune Unused Volumes
docker volume prune -f
```


- [ ] Bind Mount
```yaml
docker run -v ${PWD}/data.txt:/mounted_folder my_container_image
```
`-v ${PWD}/data.txt:/mounted_folder` option in Docker is used to bind mount a file from your host system into a Docker container. Let's break down the components of this command:

### Components of the Command
- `-v` : This flag stands for "volume" and is used to mount a volume.
- `${PWD}/data.txt` : This specifies the source path on the host machine. `${PWD}`  is a shell variable that holds the present working directory (the directory you are currently in). `data.txt`  is a file in this directory.
- `/mounted_folder` : This specifies the target path inside the container where the file will be mounted.
### What This Does
- **Bind Mount**: This command creates a bind mount, which means it links a specific file or directory on the host system (`${PWD}/data.txt` ) to a file or directory in the Docker container (`/mounted_folder` ).
- **File Accessibility**: The file `data.txt`  from your current working directory on the host will be accessible inside the container at `/mounted_folder` . Any changes made to the file in either the host or the container will be reflected in both places.


### Example Scenario
**Host Setup**:

- Assume you have a file called `data.txt`  in your current working directory on your host machine.
- The file path on your host is `/path/to/your/directory/data.txt` .
**Inside the Container**:

- Within the container, you can access the contents of `data.txt`  by referring to `/mounted_folder` .
- For example, you can read the file using a command like `cat /mounted_folder`  or write to it with a text editor.
### Benefits
- **Persistence**: Changes to the file are preserved between container restarts because the file exists on the host.
- **Data Sharing**: Multiple containers can access the same file if the same bind mount is used.
- **Simplicity**: Easily share configuration files, datasets, or other resources between your host and containers.
