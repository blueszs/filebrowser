# Installation

File Browser is a single binary and can be used as standalone executable. However, it is also available as a [Docker](https://www.docker.com) image. The installation and first time setup is quite straightforward independently of which system you use.

## Binary

The quickest and easiest way to install File Browser is to use a package manager, or our download script, which automatically fetches the latest version of File Browser for your platform.

=== "Brew"

    ```sh
    brew tap filebrowser/tap
    brew install filebrowser
    filebrowser -r /path/to/your/files
    ```

=== "Unix"

    ```sh
    curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
    filebrowser -r /path/to/your/files
    ```

=== "Windows"

    ```sh
    iwr -useb https://raw.githubusercontent.com/filebrowser/get/master/get.ps1 | iex
    filebrowser -r /path/to/your/files
    ```

File Browser is now up and running. Read some [first boot](#first-boot) for more information.

## Docker

File Browser is available as two different Docker images, which can be found on [Docker Hub](https://hub.docker.com/r/filebrowser/filebrowser).

=== "Alpine"

    The 

    ```sh
    docker run \
      -v /path/to/srv:/srv \
      -v /path/to/database:/database \
      -v /path/to/config:/config \
      -p 8080:80 \
      filebrowser/filebrowser
    ```

    The default user has PID 1000 and GID 1000. Please make sure that this user has access to the different mounted volumes. To change the user running inside the Docker image, you need to use the [`--user` flag](https://docs.docker.com/engine/containers/run/#user).

=== "s6 overlay"

    The `s6` image is based on LinuxServer and leverages the [s6-overlay](https://github.com/just-containers/s6-overlay) system for a standard, highly customizable image. It should be used as follows:

    ```shell
    docker run \
      -v /path/to/srv:/srv \
      -v /path/to/database:/database \
      -v /path/to/config:/config \
      -e PUID=$(id -u) \
      -e PGID=$(id -g) \
      -p 8080:80 \
      filebrowser/filebrowser:s6
    ```

Where:

- `/path/to/srv` contains the files root directory for File Browser
- `/path/to/config` contains a `settings.json` file
- `/path/to/database` contains a `filebrowser.db` file

Both `settings.json` and `filebrowser.db` will automatically be initialized if they don't exist.

File Browser is now up and running. Read some [first boot](#first-boot) for more information.

> [!NOTE]
>
> The Alpine Docker image has breaking changes from v2.33.0, in order to address multiple issues that have continuously affected multiple users. The changes are as follows:
>
> - **User**: File Browser no longer runs as `root`, but as user with PID 1000 and GID 1000. You can still change this by using Docker's [`--user` flag](https://www.docker.com/blog/understanding-the-docker-user-instruction/).
> - **Volumes**: the volumes with the database and configuration are now aligned with the s6-overlay images. Instead of mounting the files themselves, which leads to frequent issues, you now mount the surrounding directory.
>
> Assuming you have a `database.db`, a `.filebrowser.json` and the data in `/data`, do the following:
> 
> 1. Change the path of `database` in `.filebrowser.json` to `/database/filebrowser.db`
> 2. Rename `database.db` to `filebrowser.db`
> 3. Rename `.filebrowser.json` to `settings.json`
> 4. Put them in the same directory locally, let's say `/app/filebrowser/`
> 5. Change the permissions of both directories: `sudo chown -R 1000:1000 /app/filebrowser /data`
> 6. Mount with the flags `-v /app/filebrowser:/database -v /app/filebrowser:/config` - you can also choose to put them in separate directories, but it is not needed.


## First Boot

Your instance is now up and running. File Browser will automatically bootstrap a database, in which the configuration and the users are stored. You can find the address in which your instance is running, as well as the randomly generated password for the user `admin`, in the console logs.

> [!WARNING]
>
> The automatically generated password for the user `admin` is only displayed once. If you fail to remember it, you will need to manually delete the database and start File Browser again.

Although this is the fastest way to bootstrap an instance, we recommend you to take a look at other possible options, by checking `config init --help` and `config set --help`, to make the installation as safe and customized as it can be.

If your goal is to have a public-facing deployment, we recommend taking a look at the [deployment](deployment.md) page for more information on how you can secure your installation.
