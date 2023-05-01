---
title:  "dnsmasq for local dev"
date:   2023-05-01 10:56:00
categories: [development, tool]
tags: [dns, domains]
---
```shell
  echo 'address=/.test/127.0.0.1' >> $(brew --prefix)/etc/dnsmasq.conf
  sudo brew services restart dnsmasq
  bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/test'
```
----
Following the [last tip](/2023/dnsmasq-for-local-dev/), i mentioned 3 main ways to point fake domains towards 127.0.0.1:

##### SERVICES LIKE LVH.ME
```diff
+ Nothing to Configure it just ready to use
+ Tools like dig or ping will behave properly
+ As many subdomains as you want
- Requires internet
- You can only works with subdomains, but only lvh.me domain
```
##### HOSTS FILES
```diff
+ No internet needed
+ Is easy to use
+ 3rd party app make it easier
- I need to configure each domain, subdomain
- Dig or ping will not behave properly
```

##### DNSMAQ
```diff
+ Can configure a (any fake or not) tld
+ domains/subdomains under the tld will work
+ Dig or ping will behave properly
+ Configuration once done no need to do it again
- More complex configuration
```

----

## How to use DNSMasq

#### Installation
- Install DNSMasq: `brew install dnsmasq`

#### Configuration


```shell
  $ echo 'address=/.test/127.0.0.1' >> $(brew --prefix)/etc/dnsmasq.conf
  $ sudo brew services restart dnsmasq
```

With this we have already our dnsmasq system working for `.test` tld we can check it doing

```shell
  $ dig +short somefakedomain.test @127.0.0.1
  127.0.0.1
```

Now we need make our dns resolution to look for the `.test` tld in our dnsmasq. This could be done in 2 ways:
1. Add `127.0.0.1` to our dns resolvers `(/etc/resolv.conf)`. But then that will affect to the resolution of all domains not only the `.test`.
2. Or make the dns resolver aware that `.test` domains has to be resolved with dnsmasq at `127.0.0.1`

We are going to do the **second** one:

```shell
  sudo mkdir -v /etc/resolver
  sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/test'
```

Once this done, we can test it doing:

```shell
  $ ping -c 1 somedomain.test

  # PING somedomain.test (127.0.0.1): 56 data bytes
  # 64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.070 ms

  # --- somedomain.test ping statistics ---
  # 1 packets transmitted, 1 packets received, 0.0% packet loss
  # round-trip min/avg/max/stddev = 0.070/0.070/0.070/0.000 ms

  $ ping -c 1 another.fakedomain.test

  # PING another.fakedomain.test (127.0.0.1): 56 data bytes
  # 64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.058 ms

  # --- another.fakedomain.test ping statistics ---
  # 1 packets transmitted, 1 packets received, 0.0% packet loss
  # round-trip min/avg/max/stddev = 0.058/0.058/0.058/0.000 ms
```

That's it!! With this and the auto https you no longer need to worry for testing domains/subdomains
