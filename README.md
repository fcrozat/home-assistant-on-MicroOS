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
