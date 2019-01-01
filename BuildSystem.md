
# ROS2 Build System Tutorial

## Installation

Follow the instructions for a Bionic-Crystal install at [https://index.ros.org/doc/ros2/Linux-Install-Debians](https://index.ros.org/doc/ros2/Linux-Install-Debians).

Some other packages that are needed/useful for development:

```
sudo apt install python3-vcstool python3-colcon-* python3-rosdep
```

## Terminology

* **ament**: build system tool that builds a distributed set of packages, but defers the actual build to whatever underlying build system each package prefers (cmake, setuptools, bazel, ...)
* **colcon**: the reference ament command-line tool
* **vcs**: utility for fetching and managing multiple repositories at once
* **rosdep**: checks and fetches dependencies (system and ros) for a specified package

## Sources

Download the sources:

```
mkdir -p ~/foo/src
cd ~/foo/src

# vcs is a convenient tool for managing multiple repositories at once
# This fetches sophus (pure cmake), ecl_tools, ecl_lite, ecl_core (ament cmake)
wget https://raw.githubusercontent.com/stonier/repos_index/devel/crystal/ecl.repos
vcs import < ecl.repos

# Add py_trees (a pure python package)
git clone https://github.com/stonier/py_trees

vcs status
```

While both sophus and py_trees are pure in the sense that they can be just as easily
built outside the ament/colcon build system with regular cmake/setuptools build commands,
they do contain a package.xml which enable them to participate in the dependency
management framework. 

Packages can still be built without a package.xml, but in these cases
dependency management has to be handled carefully with extra effort elsewhere.

TODO: underlays and overlays (workspaces on top of workspaces)

## Dependencies

Fetch dependencies (ecl doesn't have any, py_trees will look for a few, e.g. python3-pydot):

```
cd ~/foo
export ROSDISTRO_INDEX_URL=https://raw.githubusercontent.com/ros2/rosdistro/ros2/index.yaml
rosdep update
rosdep install --from-paths src --ignore-src
```

TODO: remove ecl_tools and fetch dependencies again (it will fetch the binaries)

## Build

```
cd ~/foo
source /opt/ros/crystal/setup.bash
# use --merge-install if you want to make a tarball/binary of the install space
COLCON_ARGS="--symlink-install --event-handlers console_direct+"
colcon build ${COLCON_ARGS}
```

Lots more you can do with colcon, like passing cmake args or a cmake cache to the build,
printing the packages in build order or as a graph, running lint checkers, building a single package, ... Some examples [here](https://gist.github.com/stonier/5cb09ba059c79fbf77e772881b3e9d42). You can also
intuit some idea of the breadth of functionality available via `apt-cache search colcon*`.

## Tests

Run any c++ gtest/ctest tests, python unit/nose tests:

```
cd ~/foo
colcon tests
colcon test-result --all
```

## Executables

Source the install environment:

```
cd ~/foo
source install/setup.bash
```

Run a few things. Everything is installed and works as a traditional linux filesystem
would work except for binaries hidden in the individual `lib/<package_name>` folders.
These have the ros2 tool to help find/run them.

```
# Global Binaries
ecl_bench_flops
py-trees-demo-tree-stewardship

# Private demos
ros2 run ecl_sigslots demo_sigslots
```

## Launchables

TODO
