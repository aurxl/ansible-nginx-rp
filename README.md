# Ansible-Nginx-Reverse-Proxy

[![Publish role on Ansible Galaxy](https://github.com/aurxl/ansible-nginx-rp/actions/workflows/push_to_galaxy.yml/badge.svg)](https://github.com/aurxl/ansible-nginx-rp/actions/workflows/push_to_galaxy.yml)

This role will install nginx as a reverse proxy and will let you configure it.
- path specific (`https://your.public.domain/foo` -> `http://your.local.machine:8080`)
- domain specific (`https://www.your.public.domain` -> `http://your.local.machine:8080`)

Supported linux distros are:
- RHEL based (Fedora, EL, Rocky, ...)
- (untested) Debian based (Debian, Ubuntu, ...)

#### installation
```
ansible-galaxy role install aurxl.nginx_rp
```

#### Dehydrated
No dehydrated commands will be issued using this role. This is up to the admin. I dont want to automatically ban any domain by letsencrypt.
Therefor all preparation is taken, but `sudo dehydrated --register --accept-terms` and `sudo dehydrated -c` must be executed by hand.

#### temporary self signed certificates
In order to provide a `https` connection per default, self signed certificates will be generated and used. Disable this by setting `nginx_rp.tmp_self_sign: false`.

**Note:** Nginx wont start, if there aren't any other certificates at the right location, like some from dehydrated if configured previously.

When self signed certs were generated, but dehydrated should issue certs:
1. `sudo rm -rf  /etc/dehydrated/certs/`
2. `sudo dehydrated -c && sudo systemctl restart nginx`


## Example host_var
```yaml
nginx_rp:
  name: mux.foo.fighters
  server_options: |
    

  path:
    - from: ~ /quux(.*)
      to: http://quux.local/$1
    - from: /foo
      to: http://foo.local  # FQDN or IP of host to proxy to
      options: |            # Optionally define some extra options that will be added to the location 
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;

  domain:
    - name: foo.bar
      altnames: 
        - www.foo.bar             # Define altnames, remember to make certificates for them too!
      proxy_to: http://foo2.local # FQDN or IP of host to proxy to
      options: |                  # Optionally define some extra options that will be added to the location
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
      server_options: |

  ssl:
    dehydrated: true
    cert_dir: ""              # Defaults to /etc/ssl/private and /etc/dehydrated/certs when dehydrated enabled
    domains:
      foo.bar:
        altnames: # Define altnames
          - www.foo.bar
      mux.foo.fighters:
```
