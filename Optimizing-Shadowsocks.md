On Debian 7:

If you see a lot of `error: too many open files` in your log, you should optimize your system.

Create `/etc/sysctl.d/local.conf` with the following content:

```
fs.file-max = 51200

net.core.rmem_max = 67108864
net.core.wmem_max = 67108864
net.core.rmem_default=65536
net.core.wmem_default=65536
net.core.netdev_max_backlog = 4096
net.core.somaxconn = 4096

net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 0
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.ip_local_port_range = 10000 65000
net.ipv4.tcp_max_syn_backlog = 4096
net.ipv4.tcp_max_tw_buckets = 5000
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_rmem = 4096 87380 67108864
net.ipv4.tcp_wmem = 4096 65536 67108864
net.ipv4.tcp_mtu_probing = 1
net.ipv4.tcp_congestion_control = hybla
```

Then:

    sysctl --system

Warning: **DO NOT ENABLE `net.ipv4.tcp_tw_recycle`!!!** See [this article](http://vincent.bernat.im/en/blog/2014-tcp-time-wait-state-linux.html).

If you use [Supervisor](https://github.com/clowwindy/shadowsocks/wiki/Configure-Shadowsocks-with-Supervisor), Make sure you have the following line in `/etc/default/supervisor`. Once you added that line, restart Supervisor (service stop supervisor && service start supervisor).

```
ulimit -n 51200
```

If you use other ways to run shadowsocks in the background, make sure to add `ulimit -n 51200` in your init script.

After optimizing, Shadowsocks should be able to handle thousands of connections with about 20MB memory and < 10% CPU.

![if_eth0-day](https://cloud.githubusercontent.com/assets/1073082/3280283/2fc1c782-f454-11e3-8cec-75e1615e6ca8.png)

![fw_conntrack-day](https://cloud.githubusercontent.com/assets/1073082/3280285/57f68cb0-f454-11e3-99dc-ad0ad39dce48.png)

![cpu-day](https://cloud.githubusercontent.com/assets/1073082/3280292/c4371598-f454-11e3-9241-26b5a5f5502b.png)

Before & after:

![cc](https://cloud.githubusercontent.com/assets/1073082/3296349/10c34b04-f5d9-11e3-95fc-e38f5299c274.jpg)