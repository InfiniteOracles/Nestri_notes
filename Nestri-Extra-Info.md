****
1. If you port forward the relay on either a VPS or a local machine you have to port forward 8088 and 9099 on TCP and UDP.
2. [nestri docs](https://docs.nestri.io/ "https://docs.nestri.io/") is the official unfinished documentation
3. `ghcr.io/datcaptainhorse/nestri-cachyos:latest` is dathorse's branch of nestri with some bug fixes and `-e NESTRI_LAUNCH_CMD` so i recommend using that over the current build `ghcr.io/nestrilabs/nestri/runner:nightly` until they push the changes.
4. The `RELAY_URL` on the runner doesn't have /ws/ (websocket) because dathorse couldn't figure out how to get it working on the runner but it is required on the website.
5. You can manually override the startup script with `-v your-script-path:/etc/nestri/entrypoint_nestri.sh` to launch custom apps but `NESTRI_LAUNCH_CMD` is more recommended.
6. `cat /home/nestri/.steam/steam/logs/console-linux.txt` is the logs for downloading and launching steam. So if steam isn't working/launching/black screen on stream then check that.
7. Podman is like docker but just better :)
****