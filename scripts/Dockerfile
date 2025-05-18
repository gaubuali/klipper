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
FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y sudo

# Create user
RUN useradd -ms /bin/bash klippy && adduser klippy dialout
USER klippy

#This fixes issues with the volume command setting wrong permissions
RUN mkdir /home/klippy/.config
VOLUME /home/klippy/.config

### Klipper setup ###
WORKDIR /home/klippy

COPY . klipper/
USER root
RUN echo 'klippy ALL=(ALL:ALL) NOPASSWD: ALL' > /etc/sudoers.d/klippy && \
    chown klippy:klippy -R klipper && \
    apt-get update && \
    apt-get install -y dos2unix && \
    find /home/klippy/klipper/scripts/ -type f -name "*.sh" -exec dos2unix {} \; && \
    chmod +x /home/klippy/klipper/scripts/install-ubuntu-22.04.sh

# This is to allow the install script to run without error
RUN ln -s /bin/true /bin/systemctl

USER klippy
WORKDIR /home/klippy/klipper

# Add debugging to verify file existence and permissions
RUN ls -la /home/klippy/klipper/scripts/ && \
    bash /home/klippy/klipper/scripts/install-ubuntu-22.04.sh

# Clean up install script workaround
RUN sudo rm -f /bin/systemctl

CMD ["/home/klippy/klippy-env/bin/python", "/home/klippy/klipper/klippy/klippy.py", "/home/klippy/.config/printer.cfg"]
