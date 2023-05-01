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

Following the last tip, i mentioned 3 main ways to point fake domains towards 127.0.0.1:

#### Service like lvh.me

**Pros**
- Nothing to Configure it just ready to use
- As many subdomains as you want
- Tools like dig or ping will behave properly

**Cons**
- Requires internet
- You can only works with subdomains, or wirh lvh.me domain

#### Typical Hosts file (using iHosts or not)

**Pros**
- No internet needed
- Is easy to use
- 3rd party app make it easier

**Cons**
- I need to configure each domain, subdomain that im going to make reference
- tools like dig or ping will not behave properly

#### Dnsmaq

**Pros**
- Can configure a (any) tld and all domains subdomains under the tld will work
- Can configure it to work as a normal dns system tools like dig or ping will behave properly
- Configuration once done no need to do it again

**Cons**
- Little bit more complex configuration


## DNSMasq

### Requirements
- Install DNSMasq: `brew install dnsmasq`

### Configuration of `.test` ltd


```shell
  $ echo 'address=/.test/127.0.0.1' >> $(brew --prefix)/etc/dnsmasq.conf
  $ sudo brew services restart dnsmasq
```

With this we have already our dns system working for `.test` tld we can check it doing

```shell
  $ dig +short somefakedomain.test @127.0.0.1
  127.0.0.1
```

Now we need to tell the computer to look for the `.test` tld in that dns server. We could only go and add `127.0.0.1` to our dns resolvers but then that will affect to all the request we do. To make it work only for the `.test` domains we should do this:

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
