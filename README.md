# Deploying home-assistant on openSUSE MicroOS

Get openSUSE MicroOS from <https://get.opensuse.org/microos/>

Be sure to choose Container Host as system role if using installer or pick a Container Host VM image.

Once the system is deployed, as root, do the following:

* systemctl enable podman-auto-update.timer
* mkdir /var/lib/home-assistant
* chcon -R system_u:object_r:container_file_t:s0 -P /var/lib/home-assistant
* cp home-assistant.container /etc/containers/systemd
* systemctl daemon-reload
* if running podman < 4.7.0, for initial startup, you might want to manually pull the container using podman pull ghcr.io/home-assistant/home-assistant:stable to workaround timeout
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
* add to Home Assistant configuration.yaml:

```
http:
  use_x_forwarded_for: true
  trusted_proxies:
    - 10.88.0.0/16
```

# Zigbee USB coordinator support

I use Home-Assistant own zigbee stack (ZHA).

* setsebool container_use_devices 1
* uncomment AddDevice line in home-assistant.container and adapt the path to your Zigbee USB coordinator
* in ZHA, if asked for device path, use /dev/zigbee

# Bluetooth support

If your computer supports bluetooth and wants to integrate it in HA, do the following:

* transactional-update pkg in bluez
* copy my-bluetooth-dbus-policy.* to /etc/selinux
* (if you need to recreate the policy, in toolbox -r, run the following: zypper in setroubleshoot ; ausearch -if /media/root/var/log/audit/audit.log |audit2allow -M my-bluetooth-dbus-policy ; cp my-bluetooth-dbus-policy.* /media/root/etc/selinux/
* semodule -i my-bluetooth-dbus-policy.pp
* uncomment ExecStartPre in home-assistant.container
* reboot (if you had to install bluez) or systemctl daemon-reload
* systemctl restart home-assistant

# Matter support

If you want to integrate Matter in HA, do the following:
* cp matter.container /etc/containers/systemd
* systemctl daemon-reload
* systemctl start matter
* add Matter integration in HA (use the default settings)
* 
