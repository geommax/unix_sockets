To monitor **multicast traffic** and see what packets are being exchanged between two computers, follow these steps:

---

## **1. Verify Multicast Subscription**
### **Check Multicast Groups on Each Machine**
Run:
```bash
ip maddr show
```
- This shows if your network interface has **joined the multicast group**.
- If your multicast IP (e.g., `239.1.1.1`) **is missing**, manually join it:
  ```bash
  sudo ip maddr add 239.1.1.1 dev wlp2s0  # Change interface if needed
  ```

---

## **2. Monitor Multicast Packets**
### **Using `tcpdump`**
To see if multicast packets are being sent/received:
```bash
sudo tcpdump -i wlp2s0 -n host 239.1.1.1
```
- Replace `wlp2s0` with your **actual interface name** (`ip link show` to check).
- Replace `239.1.1.1` with your **multicast IP**.

#### **To capture UDP multicast packets on port `5000`:**
```bash
sudo tcpdump -i wlp2s0 udp port 5000
```

#### **To save packets for analysis:**
```bash
sudo tcpdump -i wlp2s0 udp port 5000 -w multicast.pcap
```
- Open `multicast.pcap` in **Wireshark**:
  ```bash
  wireshark multicast.pcap
  ```

---

## **3. Check Active Multicast Traffic**
### **Using `netstat` or `ss`**
To see if a process is listening on a multicast address:
```bash
netstat -g
```
Or, using `ss`:
```bash
ss -g
```
This will list all **multicast groups** your system has joined.

---

## **4. Monitor Multicast with `iftop`**
To see **live multicast traffic usage**:
```bash
sudo iftop -i wlp2s0 -n
```
- Shows real-time **bandwidth usage** for multicast.

---

## **5. Debug with `mcasttool` (Optional)**
If you need to **test** sending/receiving multicast packets:
### **Install `mtools` on both computers:**
```bash
sudo apt install mtools
```
### **Sender Side:**
```bash
mcast send 239.1.1.1 5000
```
### **Receiver Side:**
```bash
mcast receive 239.1.1.1 5000
```
- If the receiver gets messages, **multicast is working**.

---

## **6. Troubleshooting if Multicast Isn’t Working**
### **1️⃣ Check Network Interface Supports Multicast**
Run:
```bash
ip link show wlp2s0
```
- Look for **`MULTICAST`** in the output.

### **2️⃣ Enable Multicast if Disabled**
```bash
sudo ip link set wlp2s0 multicast on
```

### **3️⃣ Ensure IGMP Snooping Isn’t Blocking Traffic**
- If using a **router or switch**, check if **IGMP snooping** is enabled (some routers block multicast).

### **4️⃣ Allow Multicast in Firewall**
If using **UFW**:
```bash
sudo ufw allow proto udp to 239.1.1.1 port 5000
```
For **iptables**:
```bash
sudo iptables -A INPUT -d 239.1.1.1 -j ACCEPT
```

---

## **7. Summary**
| Task | Command |
|------|---------|
| **Check multicast groups** | `ip maddr show` |
| **Monitor packets** | `tcpdump -i wlp2s0 udp port 5000` |
| **List active multicast listeners** | `netstat -g` or `ss -g` |
| **View real-time traffic** | `iftop -i wlp2s0 -n` |
| **Send test packets** | `mcast send 239.1.1.1 5000` |
| **Receive test packets** | `mcast receive 239.1.1.1 5000` |
| **Enable multicast if disabled** | `sudo ip link set wlp2s0 multicast on` |
| **Allow multicast in firewall** | `sudo iptables -A INPUT -d 239.1.1.1 -j ACCEPT` |

Try these and let me know what happens! 🚀