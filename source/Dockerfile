# ARG FROM_IMAGE=
# FROM $FROM_IMAGE
FROM osrf/ros2:devel
# install packages
RUN apt-get update && apt-get install -q -y \
    libasio-dev \
    libtinyxml2-dev \
    wget \
    python3-rosinstall-generator \
    && rm -rf /var/lib/apt/lists/*

ARG ROS_DISTRO=eloquent
ENV ROS_DISTRO=$ROS_DISTRO
ENV ROS_VERSION=2 \
    ROS_PYTHON_VERSION=3

WORKDIR $ROS2_WS

RUN rosinstall_generator class_loader --tar --deps | vcs import src

# install dependencies
RUN apt-get update && rosdep install -y \
    --from-paths src \
    --ignore-src \
    --skip-keys "console_bridge fastcdr fastrtps libopensplice67 libopensplice69 rti-connext-dds-5.3.1 urdfdom_headers" \
    && rm -rf /var/lib/apt/lists/*

# build source
RUN colcon \
    build \
    --symlink-install \
    --mixin build-testing-on release \
    --cmake-args --no-warn-unused-cli \

ARG RUN_TESTS
ARG FAIL_ON_TEST_FAILURE
RUN if [ ! -z "$RUN_TESTS" ]; then \
        colcon test; \
        if [ ! -z "$FAIL_ON_TEST_FAILURE" ]; then \
            colcon test-result; \
        else \
            colcon test-result || true; \
        fi \
    fi
