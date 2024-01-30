<div align="center">

<br>

<img alt="MoJ logo" src="https://moj-logos.s3.eu-west-2.amazonaws.com/moj-uk-logo.png" width="200">

<br>

# Dory's dnsmasq container

[![Standards Icon]][Standards Link]
[![License Icon]][License Link]

</div>

# 

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


<!-- MoJ Standards -->

[Standards Link]: https://operations-engineering-reports.cloud-platform.service.justice.gov.uk/public-report/dory-dnsmasq 'Repo standards badge.'

[Standards Icon]: https://img.shields.io/endpoint?labelColor=231f20&color=005ea5&style=for-the-badge&url=https%3A%2F%2Foperations-engineering-reports.cloud-platform.service.justice.gov.uk%2Fapi%2Fv1%2Fcompliant_public_repositories%2Fendpoint%2Fdory-dnsmasq&logo=data:image/png;base64,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
