# Deploying home-assistant on openSUSE MicroOS

Get openSUSE MicroOS from <https://get.opensuse.org/microos/>

Be sure to choose Container Host as system role if using installer or pick a Container Host VM image.

Once the system is deployed, as root, do the following:

* systemctl enable podman-auto-update.timer
* mkdir /var/lib/home-assistant
* chcon -R system_u:object_r:container_file_t:s0 -P /var/lib/home-assistant
* cp home-assistant.container /etc/containers/systemd
* systemctl daemon-reload
* systemctl start home-assistant

and that's it.

Home Assistant will update itself automatically when upstream release a new container.

# Enabling Cloudflare Tunnel

* follow https://github.com/brenner-tobias/addon-cloudflared/wiki/How-tos#cloudflare including remote tunnel setup
* copy your cloudflare tunnel token to cloudflare.token file
* as root, podman secret create cloudflare-token <path>/cloudflared.token
* cp cloudflared.container /etc/containers/systemd
* systemctl daemon-reload
* systemctl start cloudflared
