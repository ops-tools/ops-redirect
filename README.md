# ops-redirect

> Redirect incoming traffic from one [IP:]port to another

ops-redirect – is a bash script for setting **iptables** rules to redirect traffic using [NAT](https://en.wikipedia.org/wiki/Network_address_translation) and optionally save them across reboots.

This tool could be used in scenarios like zero-downtime server deploy or controlled local port exposing to the world.

## Usage

Run `ops-redirect` without parameters or with `--help` switch to see a short usage message.

```
Redirect incoming traffic from one [IP:]port to another

Usage: ops-redirect [options] <src> <dest>

Options:
  -i, --iface name       Source interface name, any if omitted
  -p, --proto {tcp|udp}  Protocol to match, tcp if omitted
  -s, --save             Save rules to persistent config
  -h, --help             Print help and exit
```

## Examples

Redirect all traffic from port 80 to 8080.

```
$ ops-redirect 80 8080
```

Redirect only *tcp* traffic from port 443 to 4443.

```
$ ops-redirect -p tcp 443 443
```

Redirect traffic from *eth0* interface to loopback address.

```
$ ops-redirect -i eth0 80 127.0.0.1:80
```

Redirect tcp traffic from one address to another and save config.

```
$ ops-redirect -s -p tcp 172.16.0.1:80 192.168.0.1:80.
```

## Installation

Use any appropriate method for you to get [ops-redirect](ops-redirect) file on your system.

Like git clone and symlink:

```
$ git clone https://github.com/ops-tools/ops-redirect.git
$ mkdir -p ~/bin/ && ln -s $(pwd)/ops-redirect/ops-redirect ~/bin/
```

If you want to use `-s` to save your rules across reboots on Debian you need to install `iptables-persistent` package.

```
$ sudo apt install iptables-persistent
```

### System configuration

As ops-redirect is trying to invoke iptables using `sudo`, you should allow it by giving sudo access for iptables binaries for a user that intended to run the tool.

Example `/etc/sudoers` record for user *deploy*.

```
deploy ALL=(root) NOPASSWD:/sbin/iptables*
```

There are also some tweaks that should be made.

Kernel variables:

   * `ip_forward` should be enabled for using NAT (required)
   * `route_localnet` should be enabled on every interface you want to redirect to loopback (optional)

Set them as root:

```
# sysctl -w net.ipv4.ip_forward=1
# sysctl -w net.ipv4.conf.eth0.route_localnet=1
```

See kernel's [ip-sysctl.txt](https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt) for details.

## License

This software is distributed under the [MIT license](LICENSE).
