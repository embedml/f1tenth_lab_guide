<style>
    code {
      white-space : pre-wrap !important;
    }
</style>

# Setting Up Your Development Environment

## Table of Contents

1. [Things You'll Need](#things-youll-need)
2. [Quick Docker Command Summary](#quick-docker-command-summary)
3. [Getting the ROS Docker VNC Image (technically optional)](#getting-the-ros-docker-vnc-image-technically-optional)
    1. [Pulling the Image](#pulling-the-image)
4. [Running the Container](#running-the-container)
5. [Entering the Container](#entering-the-container)
    1. [Desktop Interface](#desktop-interface)
    2. [Terminal Interface](#terminal-interface)
    3. [Editing with VSCode](#editing-with-vscode)

P.S.: Please use NeoVim, it's awesome and I (Arvin) built on an excellent configuration
[here](https://github.com/ArvinSKushwaha/luavim). Also, if you use emacs, you're
dead to me /j.

A lot of this guide is completed in a Docker container. I know some nerd's gonna
say something along the lines of "why would you ever use a Docker container as a
development environment," and to them I say, "leave me alone, I'm new to Docker."

Now, if you want to guide to how to use Docker, check out the next subchapter,
(after you get Docker set up, of course). It should be noted, however, that the rest
of the guide will directly use Docker commands without too much explanation about
why exactly we use those commands. Also, if this guide is not sufficient, feel free
to google, I'm by no means an expert in Dockering.

Feel free to just skip this entire page if you already has ROS Noetic.

## Things You'll Need

Sorry, I didn't warn you this page was structured like a cookbook /j.

- [ ] Pineapples
- [ ] A Docker installation ([see here](https://docs.docker.com/get-docker/))
    - Note: If you're using Windows, I highly recommend the WSL version of installation.
- [ ] A decent internet connection (the ability to download about 4GB of data.)

Before proceeding, it may be useful to check out the next subchapter.

## Quick Docker Command Summary

Note, command options marked in square brackets (`[]`) are optional. Additionally,
command options marked with angle brackets (`<>`) customizable. For example, `command
[-a] <name>` means that both `command -a hypotenuse` and `command pineapple` are valid.

| Command | Subset of Usage | Meaning | More Info |
| :---: | :---: | :---: | :---: |
| ps | `docker ps [-a]` | List all the containers | [Info](https://docs.docker.com/engine/reference/commandline/ps/) |
| cp | `docker cp <host-directory> <container-name>:<container-directory>` | Copy a directory from the host to the container with the matching name | [Info](https://docs.docker.com/engine/reference/commandline/cp/) |
| cp | `docker cp <container-name>:<container-directory> <host-directory>` | Copy a directory from the container with the matching name to the host | [Info](https://docs.docker.com/engine/reference/commandline/cp/) |

TODO: Add More Commands

## Getting the ROS Docker VNC Image (technically optional)

If you head to our GitHub project [embedml/docker-ros-desktop-vnc](https://github.com/embedml/docker-ros-desktop-vnc),
you'll find more thorough instructions.

### Pulling the Image

Let's start by grabbing our Docker image from [hub.docker.com](hub.docker.com). This
can be done with the following command:

```bash
$ docker pull arvinskushwaha/ros-noetic-desktop-vnc
```


If you get some sort of error that looks like this:
```text
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json?all=1": dial unix /var/run/docker.sock: connect: permission denied
```
Don't worry, it's just a permissions error. You'll have to rerun the command with `sudo` as follows:
```bash
$ sudo docker pull arvinskushwaha/ros-noetic-desktop-vnc
```
Same usually goes for any future errors of that form. More information on this can
be found [here](https://docs.docker.com/engine/install/linux-postinstall/). If you
must use VSCode to edit within the container, you may have to do some wizardy from
the aforementioned link to get the `sudo` prefix to not be required.

Now, when you do this command,
you'll see something that looks like the following:

![The output from the docker pull command consisting of a series of docker image hashes
followed by a progress bar representing the download and extraction progress.](./assets/docker_pull_ss.png)

## Running the Container

From our Docker image, we can build a container. This is done with the following
command (do not run the following command yet):
```bash
$ docker run --name <container-name> -d arvinskushwaha/ros-noetic-desktop-vnc
```

The above command is essentially the base command which we add modifiers to. Also,
where it says `<container-name>`, replace it with a name of your choice.

For these options, it is important to remember that they cannot be changed without
just deleting and recreating the container. For tons of information on the options,
check [here](https://docs.docker.com/engine/reference/run).

Here are what the current command-line options mean:
- `--name <container-name>`: Each container is given a unique ID, and this option
lets you customize it. It is important to note that this value must not conflict
with other containers. This will be the identifier by which you reference a
container.
- `-d`: This tells Docker to launch the container "detached". Without this option
your terminal would simply freeze, as the container's main process just sleeps
indefinitely.

Other command-line options you should know of:
- `--net=<network>`: This tells Docker to use a different network than the default
(bridge). Values are typically `host`, `bridge`, `container`, or `none`. You can
also create custom network configurations with Docker, but I am definitely not
knowledgeable enough to say anything about that. Essentially, if you set
`--net=host`, you never have to worry about container and host ports.

- `-p <host-port>:<container-port>`: Now, to worry about container and host ports.
The VNC/NoVNC servers the container runs use port 5900 and 80 respectively. This
means we can control how they are exposed on the host-side with the `-p` option,
or just use them as-is with the `--net=host` option. Additionally, if there are other
servers running inside the container, additional `-p` options may be added for
those. As an example, if I wanted to map the VNC server port to port 3000 on
`localhost`, I would add `-p 3000:5900` as an option.

- `--device=<host-path>:<container-path>[:modifier]`: This option allows you to
pick and chose which devices are passed through to the container. Since each
device is a file in linux, we must pass in the device paths. For example,
`/dev/sda`. If I wanted to map joystick 0 (`/dev/js0`) from the host to the
container, I would add `--device=/dev/js0:/dev/js0` as an option. I don't know
must about the modifiers, so I'll just link the reference
[here](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities).

- `--privileged`: This option is kind of dangerous. It, for all intents and
purposes, gives full access to your device from the container. This can be useful
to passthrough any device needed. See the reference above.

For my default setup, I'm going with the following:
```bash
$ docker --name rosenv -p 80:80 -d arvinskushwaha/ros-noetic-desktop-vnc
```

This allows me to just access the NoVNC server by typing in `localhost` in my
browser.

## Entering the Container

Now that we've made our container, it would be useful to be able to actually do
things in it. There are two main ways of interacting with the container: terminal
and desktop.

### Desktop Interface

If you would like to use the desktop interface, you must have mapped
either the VNC or NoVNC server ports such that it's accessible to the local host.

Then, for VNC, you can download some form of VNC server and connect to
`localhost:<mapped-vnc-port>`. Or, in the case of NoVNC, just open
`localhost:<mapped-novnc-port>` in the browser.

This is what it looks like:

![An empty desktop with a taskbar in an instance of Firefox](./assets/novnc_screen.png)

### Terminal Interface

If you would prefer using the terminal, execute the following command:
```bash
$ docker exec -it <container-name> bash
```

This will open a shell prompt that looks something like the following:
```text
root@67c3c27e7401:/root#
```

Now, you may enter `./login`, a nice simple login script we've written, or just do
`cd /home/ubuntu; su ubuntu -`.
```text
root@67c3c27e7401:/root# ./login
```

This shows yet another prompt:
```text
root@67c3c27e7401:/root# ./login
ubuntu@67c3c27e7401:~$
```

To exit, execute `exit` twice. The first time should exit back to `root` and the
second should exit the container.
```text
root@67c3c27e7401:/root# ./login
ubuntu@67c3c27e7401:~$ exit
exit
root@67c3c27e7401:/root# exit
exit
```

Great! Now that we've got that down, let's talk about our in-container development environment.

In the home directory (`/home/ubuntu`), you will find two files and a directory:
- `install_dev_tools`: This file is run when the image was originally built. It contains
information about the development tools installed on the container, i.e. NeoVim. This
makes it easy for those who choose to primarily use the terminal to edit their files.

- `upgrade`: This script essentially just updates Ubuntu packages. It's recommended
to execute this every once in a while and as soon as you open this container for the
first time. It may take some time to execute.

- `ros_ws`: This directory is the main ROS workspace. It is where you will do most
of your development.

### Editing with VSCode

If you want to use VSCode to do most of your development and don't want to bother with
having to manually open the Docker container and whatnot all the time, I highly suggest
adding [this VSCode extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack).
More information about remote development in VSCode may be found [here](https://code.visualstudio.com/docs/remote/containers).
