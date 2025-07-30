****
**There are three parts:**
* Relay -Responsible for being a middle man between the Relay and Website so you only have to expose the Relay.
* Runner - Responsible for running the actual games.
* Website - Responsible for display the signal received from the relay and sending input back
****
**Installing dependencies**:
This documentation was built on Cachy OS which is based on arch:
```
sudo pacman -S podman git
curl -fsSL https://bun.sh/install | bash
```
****
**Relay:** 
Relay Setup is extremely easy all you have to do is run these two commands.
`mkdir relay-persist`  # Run this in your home directory 
```
podman run -d --replace --name=relay \
  -e PERSIST_DIR=/persist \
  -v ~/relay-persist:/persist \
  -e AUTO_ADD_LOCAL_IP=true \
  --network host \
  ghcr.io/datcaptainhorse/nestri-relay:latest
```
After running that run `Podman logs relay`
This will output something that looks like this in the terminal: 
```
2025/07/01 16:48:50 INFO Mesh connection addresses:
> /ip4/10.4.9.119/tcp/8088/p2p/12D3KooW...
> /ip4/10.4.9.119/tcp/8088/ws/p2p/...
> /ip4/127.0.0.1/tcp/8088/p2p/...
> /ip6/::1/tcp/8088/ws/p2p/...
```
Copy the **exact** line that:
- starts with `/ip4/`
- **is not** `127.0.0.1`
- uses `/tcp/`
- does **not** contain `/ws/`
- **does** contain `/p2p/`
Correct example:
`/ip4/10.4.9.119/tcp/8088/p2p/12D3KooWGKW62k9cPYvHiHBeMyUTPLTZaaasEqUimtskfqXhNfsK`
This is your `RELAY_URL`. Save it!
****
**Website:**
The website is even easier to setup than the Relay it is a simple bun Web server.
-  Simply git clone the Main Nestri repository: 
   `git clone https://github.com/nestrilabs/nestri.git`
- Then go into the directory:
  `cd nestri/apps/www`
- Finally run bun:
  `bun install && bun dev --host`
This will output a IP `10.4.9.xxx`. Save it!
****
**Runner:**
Last part we have the runner this is the hardest part depending on what hardware you have. It may or may not just work!

- First create a new directory in your home directory: 
```
mkdir -p ~/nestri
sudo chmod 777 ~/nestri
```
- If you have a NVIDIA GPU:
```
podman run --replace -d \
  --name=nestri \
  --network host \
  --shm-size=6g \
  --cap-add=SYS_NICE \
  --device /dev/dri/ \
  --device /dev/nvidia-uvm \
  --device /dev/nvidia-uvm-tools \
  --device /dev/nvidiactl \
  --device /dev/nvidia0 \
  --device /dev/nvidia-modeset \
  -e RELAY_URL='PASTE_RELAY_URL_HERE' \
  -e NESTRI_ROOM='your-room-name' \
  -e RESOLUTION=1920x1080 \
  -e FRAMERATE=60 \
  -e NESTRI_PARAMS='--verbose=true --dma-buf=true --audio-rate-control=cbr --video-codec=h264 --video-rate-control=cbr --video-bitrate=8000' \
  -v ~/nestri:/home/nestri \
  ghcr.io/datcaptainhorse/nestri-cachyos:latest
```
- If you have a AMD/INTEL GPU:
```
podman run --replace -d \
  --name=nestri \
  --network host \
  --shm-size=6g \
  --cap-add=SYS_NICE \
  -e RELAY_URL='PASTE_RELAY_URL_HERE' \
  -e NESTRI_ROOM='your-room-name' \
  -e RESOLUTION=1920x1080 \ 
  -e FRAMERATE=60 \
  -e NESTRI_PARAMS='--verbose=true --dma-buf=true --audio-rate-control=cbr --video-codec=h264 --video-rate-control=cbr --video-bitrate=8000' \
  -v ~/nestri:/home/nestri \
  ghcr.io/datcaptainhorse/nestri-cachyos:latest
```
- Remember to fill in your `RELAY_URL`that you obtained from the relay output.
- You also need to fill in the `NESTRI_ROOM` which is simply a way for the relay to connect the runner and the website together if the website requests the Nestri Room Name.
****
**Putting it all together:**
Now that we have all 3 parts, and hopefully they ran correctly we must compile our website connection URL. 

Modify your `RELAY_URL` specifically for the Website by adding `/ws/` in between `8088` and `p2p`:
 `/ip4/10.4.9.119/tcp/8088/ws/p2p/12D3KooWGKW62k9cPYvHiHBeMyUTPLTZaaasEqUimtskfqXhNfsK`

Final URL:
`http://{Website url}:5173/play/{roomname}?peerURL={RELAY_URL}`

Example: 
```
http://10.4.9.119:5173/play/gametest?peerURL=/ip4/10.4.9.119/tcp/8088/ws/p2p/12D3KooWGKW62k9cPYvHiHBeMyUTPLTZaaasEqUimtskfqXhNfsK
```
****
**Extra Commands:**

- Checking active Podman containers:
  `podman ps`

- Checking Podman Logs:
  `podman logs {podman container}` # It would be either `relay` or `nestri` you can also add `-f` to the end for a live feed

- Going into a Podman containers shell:
  `podman exec -it {podman container} /bin/bash` # Is useful for `nestri`

- Restart/Stop/Start Podman Container:
  `podman stop {podman container}`
  `podman start {podman container}`
  `podman restart {podman container}`
- Running containers in the background
  In the default commands I gave you for running the `relay` and `nestri` it included `-d` this will make the podman containers run in the background were you manually have to do `podman logs {podman container}` to see the logs.
****
Hopefully you will be able to get to something like this :D
