# The Magic of Docker

If you're looking at this guide, you're probably relatively familiar with Virtual Machines (VMs). If not,
feel free to check out the [appendix](#appendix) of this page.

<font color="#ff5577">Note: This page is a ROUGH DRAFT and has been written by someone who is not an expert on
Docker. If you find any inaccuracies, please [file an issue](https://github.com/embedml/f1tenth_lab_guide/issues/new)
along with a source or citation and we'll fix it ASAP.</font>

Docker is a set of services to perform virtualization. The software you'll become most familiar with is called
Docker Engine. Unlike Virtual Machines, which virtualize all aspects of a system down to the hardware, Docker
containers build upon the Linux kernel itself. The Linux kernel provides utilities that the Docker Engine takes
advantage of to containerize and isolate the applications run in Docker containers.

For operating systems like Windows and MacOS, Docker must still run on a Linux kernel. For MacOS, this is done
through a small Linux VM, and for Windows, this is done either with Hyper-V or WSL.

## Why are we using Docker?

Unfortunately, ROS (Robot Operating System) is not particularly portable, so to make up for that, we use Docker
to sort of bridge between the OS we are currently running on and the OS that ROS is supported on (which for ROS
Noetic, is Ubuntu 20.04).

## Appendix

Welcome to the Appendix! Here we will be looking at virtual machines (and maybe more stuff) in a bit more detail.

### Virtual Machines

Virtual Machines are awesome.
