# Traffic Control

## Limit bandwidth on interface

This does not limit upload speed.

```console
tc qdisc add dev {interface|ens5} root tbf rate {rate}Mbit latency 10ms burst 1540
```

Delete rule:

```console
tc qdisc del dev {interface|ens5} root
```
