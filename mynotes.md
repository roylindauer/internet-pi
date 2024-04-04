# Getting docker compose module to work

I had to set DOCKER_HOST env var on the host. 

Using the unix socket was failing to connect, and also the command the module uses just fails

check /Users/roylindauer/.ansible/collections/ansible_collections/community/docker/plugins/module_utils/common_cli.py

```
# `--format json` was only added as a shorthand for `--format {{ json . }}` in Docker 23.0
dummy, self._version, dummy = self.call_cli_json('version', '--format', '{{ json . }}', check_rc=True)
```

`{{ json . }}` seems to not work at all with newer docker versions. 


```
"docker version" accepts no arguments.
See 'docker version --help'.

Usage:  docker version [OPTIONS]

Show the Docker version information
```

# PI Hole

Failed to setup pihole. complained about network not being created. had to add external=true to the network config in the pi hole docker compose

