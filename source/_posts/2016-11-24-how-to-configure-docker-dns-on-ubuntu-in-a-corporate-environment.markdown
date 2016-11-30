---
layout: post
title: "How to configure Docker DNS on Ubuntu in a corporate environment?"
date: 2016-11-24 22:17
comments: true
published: true
categories: [docker, ubuntu]
---

When you are in a corporate environment, network is often configured to restrict outgoing requests, such as DNS resolution requests. By default, Docker uses Google DNS (8.8.8.8 and 8.8.4.4) to resolve domain names:

```
$ docker run busybox nslookup google.com
Server:    8.8.8.8
Address 1: 8.8.8.8
nslookup: can't resolve 'google.com'
```

If you are in a corporate environment, resolution fails because you have to use your internal DNS server.

You can find many, many documentation about how to configure Docker DNS on Ubuntu (such as official [Docker doc](https://docs.docker.com/engine/installation/linux/ubuntulinux/#/configure-a-dns-server-for-use-by-docker)), but none of them answer these requirements all together:

1/ Configuration must be portable: works at home or at work or anywhere else

2/ Configuration must be written in files not provided by a deb package to avoid conflicts after package updates

## Portable configuration

Ubuntu provides Dnsmasq, a local DNS server configured to use DNS server of your network through DHCP. Docker can't use it because it doesn't allow to use a local DNS server if its IP address is a local configuration, such as `127.0.0.1`.

So we'll configure Dnsmasq to listen to another available IP address, such as the one provided by `docker0` interface, to solve this issue.

Edit the new file `/etc/NetworkManager/dnsmasq.d/docker.conf` (as sudo):

```
interface=docker0
```

Then restart NetworkManager service:

```
sudo service network-manager restart
```

## Configure Docker DNS

Extract the IP address of the `docker0` interface:

```
$ docker network inspect bridge | grep Gateway
                    "Gateway": "172.17.0.1"
```

Then edit `/etc/docker/daemon.json` (as sudo):

```
{
    "dns": ["172.17.0.1"]
}
```

Restart Docker:

```
sudo service docker restart
```

Finally, check DNS resolution works again:

```
$ docker run busybox nslookup google.com
Server:    172.17.0.1
Address 1: 172.17.0.1
Name:      google.com
Address 1: 2a00:1450:4009:811::200e lhr26s02-in-x200e.1e100.net
Address 2: 216.58.198.174 lhr25s10-in-f14.1e100.net
```