<style>
    code {
      white-space : pre-wrap !important;
    }
</style>

# Lab 1

Welcome to Lab 1! Below, you will find an embedded PDF with the Lab 1 guide. Feel free to open it up
in another tab.

<iframe src="https://drive.google.com/file/d/1p-XJmLWq6MfYiHIBPVSEkmM_7eD2a-f9/preview" 
width="640px" height="480px" allow="autoplay"></iframe>

A small preface: commands to be executed in the terminal will be given as follows:
```
(<path-to-directory>) $ <command> [<arguments> ...]
```
If the `(<path-to-directory>)` segment is omitted, the command may be executed from anywhere.

## Setting up the ROS Workspace

If you are using the docker container, you will find that the `ros_ws` directory has already been created
for you. Otherwise, you will have to create it yourself. This can be done with the `mkdir` command.
Navigate to your home directory (any directory should work, but we will use the home directory throughout
this guide) and execute:
```
(~/) $ mkdir ros_ws
```
Now navigate inside the `ros_ws` folder and make a folder named `src`. By now, the folder structure
should look like:
```
ros_ws
`-- src
```
From here, we can use the `catkin_make` command-line utility. Run the following command from inside the
`ros_ws` directory:
```
(~/ros_ws/) $ catkin_make
```
From here, catkin (our build tool) will set up the rest of the folder structure:
```
ros_ws
|-- build
|   |-- CATKIN_IGNORE
|   |-- CMakeCache.txt
|   |-- CMakeFiles
|   |-- CTestConfiguration.ini
|   |-- CTestCustom.cmake
|   |-- CTestTestfile.cmake
|   |-- Makefile
|   |-- atomic_configure
|   |-- bin
|   |-- catkin
|   |-- catkin_generated
|   |-- catkin_make.cache
|   |-- cmake_install.cmake
|   |-- gtest
|   `-- test_results
|-- devel
|   |-- _setup_util.py
|   |-- cmake.lock
|   |-- env.sh
|   |-- lib
|   |-- local_setup.bash
|   |-- local_setup.sh
|   |-- local_setup.zsh
|   |-- setup.bash
|   |-- setup.sh
|   `-- setup.zsh
`-- src
    `-- CMakeLists.txt
```

Don't let that scare you off though, most of it is completely irrelevant to us! 
As you can see, under the `devel` folder, a `setup.bash` file was created. This
file essentially gives us access to a bunch of ROS utilities and sets some
environment variables that describe the location of our workspace. We will now
"source" that file as follows:
```
(~/ros_ws/) $ source devel/setup.bash
```
Assuming you are using the Docker container and the workspace named `ros_ws`, the following, less
verbose command:
```
$ rossource
```
may be used instead of the `source` command preceding it.

## Creating our Package

For lab 1, we will put our code in a package titled `lab1` (so original, I know). To create
such a package, we use the `catkin_create_package` command (a bit verbose, yes?), which has
the following syntax:
```
catkin_create_package <package-name> [<package-dependencies> ...]
```

Here are some basic dependencies you should be aware of:

- `roscpp`: If you prefer to use C++ for your ROS shenanigans, `roscpp` is a necessary
dependency.
- `rospy`: If you prefer Python to be your weapon of choice, `rospy` is essential.
- `std_msgs`: Sometimes you need to communicate with other ROS nodes (like always). If
your messages are a primitive type, like `Float32` or `Bool`, `std_msgs` provides those.
- `sensor_msgs`: If your communication involves sensor data, you will likely use `sensor_msgs`.
For example, the `LaserScan` message.

Of course, you can always add or remove dependencies later, so don't stress too much about it.
To start with, we'll use `rospy`, `roscpp`, `std_msgs`, and `sensor_msgs`. Hence, run the following
command:
```
(~/ros_ws/src/) $ catkin_create_package lab1 roscpp rospy std_msgs sensor_msgs
```
Note that this command is executed in the `~/ros_ws/src/` directory and not the `~/ros_ws/` directory.

This command generates a folder called `lab1` in the `src` directory. Let's look at what's inside:
```
lab1
|-- CMakeLists.txt
|-- include
|   `-- lab1
|-- package.xml
`-- src
```
A bit oddly, `catkin_create_package` does not create the `scripts` directory, which is typically where
the python code goes, but regardless, the majority of the structure is setup for us!

## Investigating our `package.xml`

Every package has two things in common: `package.xml`, and `CMakeLists.txt`. Let's look at `package.xml`
first. `package.xml` behaves as a sort of metadata file containing information about who is managing a
package, what dependencies a package has, and the license for the package, amongst other things. It is
important to keep this file up-to-date, as it kind of serves as a living project tracker.

Let's start by setting the description, maintainer, author, and license. You'll find that the
dependencies have been set up for us.

Starting description:
```xml
<description>The lab1 package</description>
```

Let's update this description to something a bit more useful, like this:
```xml
<description>
    A lidar processing node that outputs the closest and furthest data points, along with the range of data points.
</description>
```

Similarly, we can update the maintainer, author, and license to the following:
```xml
<maintainer email="your.email@example.com">Your Name</maintainer>
<license>MIT</license> <!-- MIT is quite nice, but be sure to research licenses! -->
<author email="your.email@example.com">Your Name</author>
```
Remember to delete or comment out the template XML if you are replacing it!
