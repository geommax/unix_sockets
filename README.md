# unix_sockets
SOcket CAT (socat), udp, tcp, nc, multicast

> socat (SOcket CAT) is a command-line utility that establishes bidirectional data transfer between two endpoints. It functions like netcat but is more powerful because it supports various communication protocols, including TCP, UDP, UNIX domain sockets, pipes, and serial ports.

```bash
1. More flexible than netcat (supports more protocols)
2. Works for networking and IPC (including UNIX sockets)
3. Can act as both client and server
4. Useful for debugging network services
5. Supports encryption (SSL/TLS) for secure communication
```

```bash
ip a | grep MULTICAST
```

#### Client Machine

```bash
echo "Hello Multicast" | socat - UDP4-DATAGRAM:239.1.1.1:5000,sp=12345,ttl=1

```
#### Server Machine

```bash
socat - UDP4-RECVFROM:5000,ip-add-membership=239.1.1.1:0.0.0.0
```

1. **Check Multicast Routes**
   ```bash
   ip route | grep 239.1.1.1
   ```
   If missing, add:
   ```bash
   sudo ip route add 239.1.1.1/32 dev eth0
   ```

2. **Enable Multicast on Interface**
   ```bash
   sudo ip link set eth0 multicast on
   ```

3. **Check Firewall Rules**
   ```bash
   sudo iptables -L | grep DROP
   ```
   If needed, allow UDP multicast:
   ```bash
   sudo iptables -A INPUT -p udp -d 239.1.1.1 --dport 5000 -j ACCEPT
   ```

4. **Check IGMP Memberships**
   ```bash
   ip maddr show
   ```
