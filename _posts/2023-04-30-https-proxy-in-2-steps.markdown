---
title:  "https proxy in 1 step"
date:   2023-04-30 20:50:00
categories: [development, tool]
tags: [caddy, dns, domains]
---
 ```shell
  caddy reverse-proxy --from anysubdomain.lvh.me --to :3000 --internal-certs
```
Note: this article was updated to work with the last version 2.6.4 at the time of this fix.

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

### For this you only need ~~2 steps~~

#### ~~Second~~ Only step

```shell
  caddy reverse-proxy --from anysubdomain.lvh.me --to :3000 --internal-certs
```
First time will ask the pawssword to trust the cert.

After this go to your fake domain `anysubdomain.lvh.me` and will be done!!

## Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/ff1JDa41tpc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

---

# NOTE: Using as service

If you want to use it as service you can use the Caddyfile with your normal service manager. But if you only download the binary you can do

```shell
  caddy start --config Caddyfile
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

----

# The 2 Step version is still valid

#### First step:

> This step is only needed once. Unless you dont remove the trusted CA Cert you will not have to do it again anymore.

Trusting the CA Cert:

```shell
  sudo bash -c "caddy start && caddy trust && caddy stop"
```

#### Second step

```shell
  caddy reverse-proxy --from anysubdomain.lvh.me --to :3000 --internal-certs
```

After this go to your fake domain `anysubdomain.lvh.me` and will be done!!
