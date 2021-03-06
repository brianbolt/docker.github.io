---
description: Instructions for installing Docker CE on CentOS
keywords: requirements, apt, installation, centos, rpm, install, uninstall, upgrade, update
redirect_from:
- /engine/installation/centos/
title: Get Docker CE for CentOS
---

{% assign minor-version = "17.06" %}

To get started with Docker CE on CentOS, make sure you
[meet the prerequisites](#prerequisites), then
[install Docker](#install-docker).

## Prerequisites

### Docker EE customers

To install Docker Enterprise Edition (Docker EE), go to
[Get Docker EE for CentOS](/engine/installation/linux/docker-ee/centos/)
**instead of this topic**.

To learn more about Docker EE, see
[Docker Enterprise Edition](https://www.docker.com/enterprise-edition/){: target="_blank" class="_" }.

### OS requirements

To install Docker CE, you need the 64-bit version of CentOS 7.

### Uninstall old versions

Older versions of Docker were called `docker` or `docker-engine`. If these are
installed, uninstall them, along with associated dependencies.

```bash
$ sudo yum remove docker \
                  docker-common \
                  docker-selinux \
                  docker-engine
```

It's OK if `yum` reports that none of these packages are installed.

The contents of `/var/lib/docker/`, including images, containers, volumes, and
networks, are preserved. The Docker CE package is now called `docker-ce`.

## Install Docker CE

You can install Docker CE in different ways, depending on your needs:

- Most users
  [set up Docker's repositories](#install-using-the-repository) and install
  from them, for ease of installation and upgrade tasks. This is the
  recommended approach.

- Some users download the RPM package and
  [install it manually](#install-from-a-package) and manage
  upgrades completely manually. This is useful in situations such as installing
  Docker on air-gapped systems with no access to the internet.

- In testing and development environments, some users choose to use automated
  [convenience scripts](#install-using-the-convenience-script) to install Docker.

### Install using the repository

Before you install Docker CE for the first time on a new host machine, you need
to set up the Docker repository. Afterward, you can install and update Docker
from the repository.

#### Set up the repository

{% assign download-url-base = "https://download.docker.com/linux/centos" %}

1.  Install required packages. `yum-utils` provides the `yum-config-manager`
    utility, and `device-mapper-persistent-data` and `lvm2` are required by the
    `devicemapper` storage driver.

    ```bash
    $ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
    ```

2.  Use the following command to set up the **stable** repository. You always
    need the **stable** repository, even if you want to install builds from the
    **edge** or **testing** repositories as well.

    ```bash
    $ sudo yum-config-manager \
        --add-repo \
        {{ download-url-base }}/docker-ce.repo
    ```

3.  **Optional**: Enable the **edge** and **testing** repositories. These
    repositories are included in the `docker.repo` file above but are disabled
    by default. You can enable them alongside the stable repository.

    ```bash
    $ sudo yum-config-manager --enable docker-ce-edge
    ```

    ```bash
    $ sudo yum-config-manager --enable docker-ce-testing
    ```

    You can disable the **edge** or **testing** repository by running the
    `yum-config-manager` command with the `--disable` flag. To re-enable it, use
    the `--enable` flag. The following command disables the **edge** repository.

    ```bash
    $ sudo yum-config-manager --disable docker-ce-edge
    ```

    > **Note**: Starting with Docker 17.06, stable releases are also pushed to
    > the **edge** and **testing** repositories.

    [Learn about **stable** and **edge** builds](/engine/installation/).

#### Install Docker CE

1.  Update the `yum` package index.

    ```bash
    $ sudo yum makecache fast
    ```

    If this is the first time you have refreshed the package index since adding
    the Docker repositories, you will be prompted to accept the GPG key, and
    the key's fingerprint will be shown. Verify that the fingerprint is
    correct, and if so, accept the key. The fingerprint should match
    `060A 61C5 1B55 8A7F 742B  77AA C52F EB6B 621E 9F35`.

2.  Install the latest version of Docker CE, or go to the next step to install a
    specific version.

    ```bash
    $ sudo yum install docker-ce
    ```

    > **Warning**: If you have multiple Docker repositories enabled, installing
    > or updating without specifying a version in the `yum install` or
    > `yum update` command will always install the highest possible version,
    > which may not be appropriate for your stability needs.
    {:.warning-vanilla}

3.  On production systems, you should install a specific version of Docker CE
    instead of always using the latest. List the available versions. This
    example uses the `sort -r` command to sort the results by version number,
    highest to lowest, and is truncated.

    > **Note**: This `yum list` command only shows binary packages. To show
    > source packages as well, omit the `.x86_64` from the package name.

    ```bash
    $ yum list docker-ce.x86_64  --showduplicates | sort -r

    docker-ce.x86_64  {{ minor-version }}.0.el7                               docker-ce-stable  
    ```

    The contents of the list depend upon which repositories are enabled, and
    will be specific to your version of CentOS (indicated by the `.el7` suffix
    on the version, in this example). Choose a specific version to install. The
    second column is the version string. The third column is the repository
    name, which indicates which repository the package is from and by extension
    its stability level. To install a specific version, append the version
    string to the package name and separate them by a hyphen (`-`):

    ```bash
    $ sudo yum install docker-ce-<VERSION>
    ```

4.  Start Docker.

    ```bash
    $ sudo systemctl start docker
    ```

5.  Verify that `docker` is installed correctly by running the `hello-world`
    image.

    ```bash
    $ sudo docker run hello-world
    ```

    This command downloads a test image and runs it in a container. When the
    container runs, it prints an informational message and exits.

Docker CE is installed and running. You need to use `sudo` to run Docker
commands. Continue to [Linux postinstall](linux-postinstall.md) to allow
non-privileged users to run Docker commands and for other optional configuration
steps.

#### Upgrade Docker CE

To upgrade Docker CE, first run `sudo yum makecache fast`, then follow the
[installation instructions](#install-docker), choosing the new version you want
to install.

### Install from a package

If you cannot use Docker's repository to install Docker, you can download the
`.rpm` file for your release and install it manually. You will need to download
a new file each time you want to upgrade Docker.

1.  Go to
    [{{ download-url-base }}/7/x86_64/stable/Packages/]({{ download-url-base }}/7/x86_64/stable/Packages/)
    and download the `.rpm` file for the Docker version you want to install.

    > **Note**: To install an **edge**  package, change the word
    > `stable` in the > URL to `edge`.
    > [Learn about **stable** and **edge** channels](/engine/installation/).

2.  Install Docker CE, changing the path below to the path where you downloaded
    the Docker package.

    ```bash
    $ sudo yum install /path/to/package.rpm
    ```

3.  Start Docker.

    ```bash
    $ sudo systemctl start docker
    ```

4.  Verify that `docker` is installed correctly by running the `hello-world`
    image.

    ```bash
    $ sudo docker run hello-world
    ```

    This command downloads a test image and runs it in a container. When the
    container runs, it prints an informational message and exits.

Docker CE is installed and running. You need to use `sudo` to run Docker commands.
Continue to [Post-installation steps for Linux](linux-postinstall.md) to allow
non-privileged users to run Docker commands and for other optional configuration
steps.

#### Upgrade Docker CE

To upgrade Docker CE, download the newer package file and repeat the
[installation procedure](#install-from-a-package), using `yum -y upgrade`
instead of `yum -y install`, and pointing to the new file.

{% include install-script.md %}

## Uninstall Docker CE

1.  Uninstall the Docker package:

    ```bash
    $ sudo yum remove docker-ce
    ```

2.  Images, containers, volumes, or customized configuration files on your host
    are not automatically removed. To delete all images, containers, and
    volumes:

    ```bash
    $ sudo rm -rf /var/lib/docker
    ```

You must delete any edited configuration files manually.

## Next steps

- Continue to [Post-installation steps for Linux](/engine/installation/linux/linux-postinstall.md)

- Continue with the [User Guide](/engine/userguide/index.md).
