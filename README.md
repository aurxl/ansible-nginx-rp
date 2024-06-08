# Ansible-Nginx-Reverse-Proxy

This role will install nginx as a reverse proxy and will let you configure it.

#### Dehydrated
No dehydrated commands will be issued using this role. This is up to the admin. I dont want to automatically ban any domain by letsencrypt.
Therefor all preparation is taken, but `dehydrated --register --accept-terms` and `sudo dehydrated -c` must be executed by hand.

## Example host_var
```yaml
nginx_rp:
    name: mux.foo.de

    path:
        - from: /foo
          to: foo.local
          options: |
            lsls

    domain:
        - name: foo.bar
          proxy_to: foo2.local

    dehydrated:
        enabled: true
        domains:
            - foo.bar
            - aurxl.de
```
