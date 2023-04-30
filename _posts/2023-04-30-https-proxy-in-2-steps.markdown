---
title:  "https proxy in 2 steps"
date:   2023-04-30 20:50:00
categories: [development, tool]
tags: [caddy, dns, domains]
---
 ```shell
 caddy reverse-proxy -from anysubdomain.lvh.me -to :3000 -internal-certs
```
When doing local development, testing https, subdomains, custom domains, is a real pain in the ass. This is because means: generating certs, configuring different cases, webserver....

A simple way to do this and also works properly in the browser is using [caddy](https://caddyserver.com/).

Im not going to extend with explanations on caddy, but summarizing caddy will:
- Install a CA cert and make our browsers to trust it
- And generate certificates for the requested domains.


### Requirements
- [Install caddy](https://caddyserver.com/docs/install#install) the easiest way to [download the binary](https://caddyserver.com/docs/install#static-binaries)
- Have a domain/subdomain that points to 127.0.0.1/0.0.0.0/localhost etc.

To make a domain point to localhost we can use
- hosts file (in mac iHost can be used for this)
- dnsmasq (next blog entry will be about this)
- or you can use any service like [lvh.me](lvh.me) any subdomain will point towards 127.0.0.1

### For this you only need 2 steps

#### First step

```shell
  caddy reverse-proxy -from anysubdomain.lvh.me -to :3000 -internal-certs
```

After this going to https://anysubdomain.lvh.me our response will be
![image](https://user-images.githubusercontent.com/230512/235200147-3692e0b9-263d-42f1-9cd0-94997c116599.png)

Time for:

#### Second step:

> This step is only needed once. Unless you dont remove the trusted CA Cert you will not have to do it again anymore.

Trusting the CA Cert:

```shell
  sudo caddy trust
```

After this reload the browser and will be done!!!

---

# NOTE: Using as service

If you want to use it as service you can use the Caddyfile with your normal service manager. But if you only download the binary you can do

```shell
  caddy start -config Caddyfile
```

```config
https:// {
  log
  tls internal {
	  on_demand
  }
  reverse_proxy :3000
}
```


This will load caddy in bg and can be stopped with caddy stop.
