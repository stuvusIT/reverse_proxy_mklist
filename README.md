# reverse_proxy_mklist

This role generates a proper list for [our reverse proxy role](https://github.com/stuvusIT/reverse_proxy) based on all host and group vars.


## Requirements

A running webserver at all target hosts, see [reverse proxy](https://github.com/stuvusIT/reverse_proxy) for future information.

## Role Variables

### At host or group vars, where this script is applied(who runs the reverse proxy)
#### Primary
| Option         | Type            | Default | Description                                                                   | Required |
|----------------|-----------------|---------|-------------------------------------------------------------------------------|:--------:|
| ignore_domains | list of strings |         | Domains which should be ignored(no fact for the reverse proxy should be set). |     N    |
| ignore_hosts   | list of strings |         | list of ansible hosts to ignore                                               |     N    |

### At host vars, who serve some sites (target server)
#### Primary
| Option         | Type          | Default | Description                                                                                       | Required |
|----------------|---------------|---------|---------------------------------------------------------------------------------------------------|:--------:|
| served_domains | list of dicts |         | See [reverse_proxy](https://github.com/stuvusIT/reverse_proxy#served_domains) for possible values | Y        |


## Example Playbook

### Vars:
`reverse_proxy_sever01.yml`:
```yml
description: Reverse Proxy
letsencrypt_email: fuu@example.com
default_url: https://stuvus.uni-stuttgart.de
domain_prefixes:
  - www
domain_suffixes:
  - example.com
```

`web01.yml`:
```yml
description: Webserver 1 for public usage ( testing )
served_domains:
  - domains:
    - test
    - test.test
interfaces:
  - mac: AA:AA:AA:11:11:11
    ip: 192.168.0.2
```

`web02.yml`:
```yml
description: Webserver 2 for internal and public usage
served_domains:
  - domains:
    - intern
    auth: true
  - domains:
    - public
    auth: true
interfaces:
  - mac: AA:AA:AA:11:11:22
    ip: 192.168.0.3
```

### Result

The configuration files above, will be result in the following configuration for reverse_proxy_sever01:
```yml
description: Reverse Proxy
letsencrypt_email: fuu@example.com
default_url: https://stuvus.uni-stuttgart.de
domain_prefixes:
  - www
domain_suffixes:
  - example.com
proxy_domains:
  - target_description: Webserver 1 for public usage ( testing )
    target_host: web01
    target_ip: 192.168.0.2
    served_domains:
      - domains:
        - test
        - test.test
  - target_description: Webserver 2 for internal and public usage
served_domains:
  - domains:
    - intern
    auth: true
  - domains:
    - public
    auth: true
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).


## Author Information

 * [Markus Mroch (Mr-Pi)](https://github.com/Mr-Pi/) _markus.mroch@stuvus.uni-stuttgart.de_
