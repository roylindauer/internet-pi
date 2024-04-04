# My Implementation Notes


## Getting docker compose module to work

I had to set `DOCKER_HOST` env var on the host. 

**Potential Bug in docker compose module**

Using the default unix socket was failing to connect, and when it did connect, it just hangs due to it not correctly dealing with an invalid command. 

The docker compose module tries to get the docker version, and it does this by running `unix:///var/run/docker.sock version --format '{{ json . }}'` which is not supported on newer versions of docker. 

The issue is the command returned:

```
"docker version" accepts no arguments.
See 'docker version --help'.

Usage:  docker version [OPTIONS]

Show the Docker version information
```


check /Users/roylindauer/.ansible/collections/ansible_collections/community/docker/plugins/module_utils/common_cli.py

```python
# `--format json` was only added as a shorthand for `--format {{ json . }}` in Docker 23.0
dummy, self._version, dummy = self.call_cli_json('version', '--format', '{{ json . }}', check_rc=True)
```

I had to manually edit the module to remove the `--format '{{ json . }}'` from the command to get it to work. 

Im not sure if its a bug in the module or a configuration issue on my end.


## PI Hole

PIHole setup failed because I had dnsmasq running on the host and because a network was not being utilized correctly. had to add external=true to the network config in the pi hole docker compose file.

```
sudo systemctl disable systemd-resolved.service
sudo systemctl stop systemd-resolved.service
```

`sudo apt-get remove dnsmasq-base` and a reboot to remove dnsmasq from the host.

