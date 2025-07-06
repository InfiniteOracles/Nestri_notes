****
**Automatic Launching:**
Using the -e `NESTRI_LAUNCH_CMD` and `NESTRI_LAUNCH_COMPOSITOR` you can change the Compositor and default launched application. 
For example:
`-e NESTRI_LAUNCH_CMD="/home/external/startup.sh"`  # If left empty will go to default
`-e NESTRI_LAUNCH_COMPOSITOR=""` # If left empty will go to default
With the very limited application list to get a real use out of it you gotta make scripts to 
Package list:
```
### Package Installation ###
# Core system components
RUN --mount=type=cache,target=/var/cache/pacman/pkg \
    pacman -Sy --needed --noconfirm \
        vulkan-intel lib32-vulkan-intel vpl-gpu-rt \
        vulkan-radeon lib32-vulkan-radeon \
        mesa \
        steam steam-native-runtime proton-cachyos gtk3 lib32-gtk3 \
        sudo xorg-xwayland seatd libinput gamescope mangohud wlr-randr \
        libssh2 curl wget \
        pipewire pipewire-pulse pipewire-alsa wireplumber \
        noto-fonts-cjk supervisor jq chwd lshw pacman-contrib \
        openssh && \
    # GStreamer stack
    pacman -Sy --needed --noconfirm \
        gstreamer gst-plugins-base gst-plugins-good \
        gst-plugins-bad gst-plugin-pipewire \
        gst-plugin-webrtchttp gst-plugin-rswebrtc gst-plugin-rsrtp \
        gst-plugin-va gst-plugin-qsv && \
    # lib32 GStreamer stack to fix some games with videos
    pacman -Sy --needed --noconfirm \
        lib32-gstreamer lib32-gst-plugins-base lib32-gst-plugins-good && \
    # Cleanup
    paccache -rk1 && \
    rm -rf /usr/share/{info,man,doc}/*
```
These are all the packages installed by default and if you don't wanna have to make scripts or do manual launching to install other apps. This is all that will be accessible to you which is completely fine if you only use steam.
****
**Manual Launching:**
It is actual pretty simple to manually launch a app.
- First go into the nestri user after going into the bash of the podman container
  `podman exec -it nestri /bin/bash`
  `su - nestri`
  `source /etc/nestri/envs.sh`
- Then you much kill all GameScope instances and steam instances.
  `pkill -9 -f gamescope && pkill -9 -f steam
  The -9 will send a SIGKILL signal which will force kill the app.
- After that double check to make sure they still aren't running.
```
ps aux | grep -i '[g]amescope'
ps aux | grep -i '[s]team'
```
- finally then run your app by settings the Wayland display and running your app under gamescope  
```
env
ls -la /tmp/.X11-unix
export DISPLAY=:2
WAYLAND_DISPLAY=wayland-1 gamescope --backend wayland --force-grab-cursor -g -f --rt --mangoapp -W 1920 -H 1080 -r 60 -e -- steam-native -tenfoot -cef-force-gpu
```
  - Currently this command will open `steam-native` using `-tenfoot` which will open it in steam big picture.
****
