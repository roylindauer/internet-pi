# My Implementation Notes

This is all running on a 2011 Macbook Pro running Ubuntu 22.04. 

All of this configuration could be done with an ansible playbook. 

This is a list of things I had to do to get this working.

## Getting docker compose module to work

I had to set `DOCKER_HOST` env var on the host. This env var was not set by default for some reason.

**Potential Bug in docker compose v2 module**

Using the default unix socket failed to connect, and when it did connect, it just hangs due to it not correctly dealing with an invalid command. 

The docker compose module tries to get the docker version, and it does this by running `/usr/bin/docker --host unix:///var/run/docker.sock version --format '{{ json . }}'` which is not supported on newer versions of docker. 

The issue is the command returned:

```
"docker version" accepts no arguments.
See 'docker version --help'.

Usage:  docker version [OPTIONS]

Show the Docker version information
```

And the module just hangs and does not return.

check /Users/roylindauer/.ansible/collections/ansible_collections/community/docker/plugins/module_utils/common_cli.py

```python
# `--format json` was only added as a shorthand for `--format {{ json . }}` in Docker 23.0
dummy, self._version, dummy = self.call_cli_json('version', '--format', '{{ json . }}', check_rc=True)
```

I had to manually edit the module to remove the `--format '{{ json . }}'` from the command to get it to work. 

Im not sure if its a bug in the module or a configuration issue on my end.


## PI Hole

PIHole setup failed because I had dnsmasq running on the host and because a network was not being utilized correctly. had to add external=true to the network config in the pi hole docker compose file. Also, systemd-resolved was running on the host and was causing issues.

/etc/systemd/resolved.conf
DNSStubListener=no

`sudo apt-get remove dnsmasq-base` and a reboot to remove dnsmasq from the host.

