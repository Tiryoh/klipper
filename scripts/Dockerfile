# This is an example Dockerfile showing how it's possible to install Klipper in Docker.
# IMPORTANT: The docker build must be run from the root of the repo, either copy the
# Dockerfile to the root, or run docker build with "-f", for example:
#       docker build . -f scripts/Dockerfile -t klipper
# Note that the host still needs to run Linux to connect the printers serial port to
# the container.
# When running, the serial port of your printer should be connected, including an
# argument such as:
#       --device /dev/ttyUSB0:/dev/ttyUSB0
# It's also required that your control program (eg: OctoPrint) be included in the same
# container as Docker does not allow sharing of the virtual serial port outside the
# container.
# The config should be in a file named "printer.cfg" in a directory mounted at:
#       /home/klippy/.config/
# For more Dockerfile examples with Klipper (and Octoprint) see:
# https://github.com/sillyfrog/OctoPrint-Klipper-mjpg-Dockerfile
FROM ubuntu:20.04

RUN apt-get update && \
    apt-get install -y sudo && \
    rm -rf /var/lib/apt/lists/*

### Pre-setup ###
# Create user
RUN useradd -ms /bin/bash klippy && adduser klippy dialout
RUN echo 'klippy ALL=(ALL:ALL) NOPASSWD: ALL' > /etc/sudoers.d/klippy

USER klippy
#This fixes issues with the volume command setting wrong permissions
RUN mkdir /home/klippy/.config
VOLUME /home/klippy/.config

### Klipper setup ###
WORKDIR /home/klippy
COPY --chown=klippy:klippy . klipper/

# This is to allow the install script to run without error
RUN sudo ln -s /bin/true /bin/systemctl

RUN ./klipper/scripts/install-ubuntu.sh && \
    sudo rm -rf /var/lib/apt/lists/*

# Clean up install script workaround
RUN sudo rm -f /bin/systemctl

CMD ["/home/klippy/klippy-env/bin/python", "/home/klippy/klipper/klippy/klippy.py", "/home/klippy/.config/printer.cfg"]
