To debug Nestri you must first find which part is having problems
****
**Relay:**
 1. Add `-e VERBOSE=true` to the podman command and use `podman logs relay` it will show more debugging information.
 2. The relay is the one part that should just work  ¯\\__(ツ)__/¯
****
**Website:**
1. Use a chromium based browser. Firefox is buggy
2. To debug the website go to your `FINAL URL` and go into the Console in inspect element if you see `Stream opened with peer. Stream is offline for room. Mediastream is null, Room is offline.` Then that means three things either that the Runner doesn't have the correct `RELAY_URL` or it didn't start correctly or your `FINAL URL'S` Game Room Name doesn't match the Runners Game Room Name. Double check your `FINAL_URL` and `RELAY_URL` and `NESTRI-ROOM` .
3. If you go into your Console and it is stuck at `Setting up libp2p` then says `signal timed out` then that means your not connecting to the Relay so double check your `FINAL_URL` and make sure your relay is even running using `podman ps` on the machine running the relay.
*****
**Runner:**
The runner is the hardest part to debug as its also the most complex part of the system.
1. Check the podman logs using `podman logs nestri` first look for at the bottom if it reached the max tries for the compositor that usually means just to restart the container and try again using `podman restart nestri` but if you scroll up for a while until you either see `Connection established with peer` or `unable to establish a connection with the peer` if it does error out then double check your `RELAY_URL` and make sure your relay is running. 
2. Still in your podman logs check if your NVIDIA/INTEL/AMD drivers are working fine look for more at the top of the logs `video encoder` and look for `No compatible encoder found` if you see this then make sure you have updated NVIDIA drivers and CUDA installed.
****
*Common bugs:*
1. If your mouse sensitivity is either really high or really low when trying to use nestri then change the `-e RESOLUTION=1920x1080 \` to the resolution of the computer that is connecting to the Website.
2. If the website completely refuses to connect to the Runner and everything looks fine no bugs that would point to being a problem, and you have a UniFI router. Then most likely its because of `unifi network isolation across lans` were if you have the 3 parts on different IPS/computers it wont allow a connection, and you will either have to turn that off in your router settings or only do localhost.
****