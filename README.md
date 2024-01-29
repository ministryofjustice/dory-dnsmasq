# Dory's dnsmasq container

## What is this?

[Dory](https://github.com/FreedomBen/dory) uses this container to provide the dnsmasq
services in conjunction with an nginx-proxy.  It's a very lightweight container built
on Alpine Linux, using only the dnsmasq-dnssec package.
See the [dory](https://github.com/FreedomBen/dory) project page for more info.

## How do I use it?

### A single TLD

You can use this container in your project, or as a standalone utility if you'd like.

Here's how to fire it up.

```
docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN freedomben/dory-dnsmasq:1.0.0 [<domain>] [<ip>]
```

So if you wanted all queries against domain "docker" to be routed to IP 3.3.3.3:

```
docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN freedomben/dory-dnsmasq:1.0.0 docker 3.3.3.3
```

If you'd rather extend it to bake in your defaults, here's an example:

```
FROM freedomben/dory-dnsmasq

CMD["docker", "3.3.3.3"]
```

### Multiple TLDs

You can have more than one TLD if you prefer.  Use the container the same way as with a single TLD, but add each domain and ip as extra args.

```
docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN freedomben/dory-dnsmasq:1.0.0 [<domain1>] [<ip1>] [<domain2>] [<ip2>]
```

So if you wanted all queries against domain "docker" to be routed to IP 3.3.3.3, and dev to be routed to 4.4.4.4:

```
docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN freedomben/dory-dnsmasq:1.0.0 docker 3.3.3.3 dev 4.4.4.4
```

The domains can go to the same IP.  You just have to repeat it.  Say you wanted both to go to 3.3.3.3:

```
docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN freedomben/dory-dnsmasq:1.0.0 docker 3.3.3.3 dev 3.3.3.3
```

### Wildcard

You can have all domain names that are not answered from /etc/hosts or DHCP be resolved to a specific address by using a `#` as the domain.  So to have all requests be resolved to 4.4.4.4:

```
docker run -p 53:53/tcp -p 53:53/udp --cap-add=NET_ADMIN freedomben/dory-dnsmasq:1.0.0 '#' 4.4.4.4
```

*NOTE:  You have to put the # in quotes otherwise bash will think it's a comment character*

### Build and push images

The following commands are useful for local development.

Create the builder:

```bash
make builder
```

Build and push images:

```bash
make build push
```

Remove the builder:

```bash
make remove
```

### Releases

A GitHub workflow is used to build and push a container image to Docker Hub.
- Before release, ensure that you have found a valid version of dnsmasq-dnssec, on the [Alpine Packages page](https://pkgs.alpinelinux.org/packages?name=dnsmasq-dnssec&branch=v3.19&repo=&arch=&maintainer=).
- Ensure that this version number is assigned to `DNSMASQ_VERSION` in [.github/workflows/multi-arch-build.yml]. e.g. `DNSMASQ_VERSION=2.89-r6`.
- Create a PR into main branch.
- After an accepted PR, tag the commit with a version in the format of `v2.89.6` to build and push the image.

### Credit

- A lightweight docker image for dnsmasq [andyshinn/dnsmasq](https://hub.docker.com/r/andyshinn/dnsmasq/).
- A PR to add compatibility for Mac M-series chips [tripox/dory-dnsmasq](https://github.com/tripox/dory-dnsmasq/commit/72549c39324014c5092cb8103378d58fbf51df80).