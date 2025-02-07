The `fd=5` means that the **socat** process (PID **7399**) is using **file descriptor 5** to listen for UDP packets on port **5000**.

---

## **🔍 Full Summary**
| **FD**  | **Type** | **Purpose** |
|---------|---------|------------|
| `0u`    | `CHR`   | Standard input (keyboard) |
| `1u`    | `CHR`   | Standard output (normal output) |
| `2u`    | `CHR`   | Standard error (error messages) |
| `3w`    | `REG`   | Writing to a log file (`.ros/log/...`) |
| `4u`    | `IPv4`  | UDP socket listening on port `37758` |
| `5u`    | `IPv4`  | UDP socket listening on port `25650` |
| `6u`    | `IPv4`  | UDP socket listening on port `25651` |
| `7u`    | `IPv4`  | UDP socket bound to `192.168.1.12:40186` (sending data) |
| `8r`    | `FIFO`  | Read pipe (IPC) |
| `9w`    | `FIFO`  | Write pipe (IPC) |

---

### **How to Check File Descriptors?**
```bash
ls -l /proc/7399/fd
```

---

The number of **file descriptors (FDs)** in Linux is **not fixed** but depends on system settings and available resources.

---

## **1. File Descriptor Limits in Linux**
Linux manages FD limits in two ways:
- **Per-process limit** (each process has its own FD limit).
- **System-wide limit** (global maximum for all processes).

### **Check Per-Process FD Limit**
```bash
ulimit -n
```
- This shows the **soft limit** (default max FDs per process).  
- Example output: `1024` (common default).

To check the **hard limit** (max that can be set):
```bash
ulimit -Hn
```
- Example: `1048576` (higher, but not always changeable by a normal user).

#### **Increase Per-Process FD Limit Temporarily**
```bash
ulimit -n 65535
```
For permanent changes, add this to `/etc/security/limits.conf`:
```
yourusername soft nofile 65535
yourusername hard nofile 65535
```

---

### **2. Check System-Wide FD Limit**
```bash
cat /proc/sys/fs/file-max
```
- This shows the **maximum number of FDs** allowed system-wide.
- Example output: `9223372036854775807` (virtually unlimited on 64-bit systems).

To change it:
```bash
echo 2097152 | sudo tee /proc/sys/fs/file-max
```
For a permanent change, add this to `/etc/sysctl.conf`:
```
fs.file-max = 2097152
```

---

### **3. Check Open FDs for a Process**
```bash
ls -l /proc/7399/fd  # Replace 7399 with the process ID
```
- This lists all **currently open file descriptors** for process **7399**.

Or use:
```bash
lsof -p 7399
```
- Shows FDs along with file/socket info.
```bash

bot@bot-Vostro-14-3468:~/unix_sockets$ lsof -p 7399
COMMAND  PID USER   FD   TYPE             DEVICE SIZE/OFF    NODE NAME
socat   7399  bot  cwd    DIR                8,6     4096  524290 /home/bot
socat   7399  bot  rtd    DIR                8,6     4096       2 /
socat   7399  bot  txt    REG                8,6   392824 5256168 /usr/bin/socat
socat   7399  bot  mem    REG                8,6  2220400 5245022 /usr/lib/x86_64-linux-gnu/libc.so.6
socat   7399  bot  mem    REG                8,6   827936 5251148 /usr/lib/x86_64-linux-gnu/libkrb5.so.3.3
socat   7399  bot  mem    REG                8,6  4455728 5253401 /usr/lib/x86_64-linux-gnu/libcrypto.so.3
socat   7399  bot  mem    REG                8,6    68552 5245059 /usr/lib/x86_64-linux-gnu/libresolv.so.2
socat   7399  bot  mem    REG                8,6    22600 5270561 /usr/lib/x86_64-linux-gnu/libkeyutils.so.1.9
socat   7399  bot  mem    REG                8,6    52016 5249890 /usr/lib/x86_64-linux-gnu/libkrb5support.so.0.1
socat   7399  bot  mem    REG                8,6    18504 5246117 /usr/lib/x86_64-linux-gnu/libcom_err.so.2.1
socat   7399  bot  mem    REG                8,6   182864 5251165 /usr/lib/x86_64-linux-gnu/libk5crypto.so.3.1
socat   7399  bot  mem    REG                8,6   338648 5251150 /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2.2
socat   7399  bot  mem    REG                8,6   182912 5271071 /usr/lib/x86_64-linux-gnu/libtirpc.so.3.0.0
socat   7399  bot  mem    REG                8,6    93280 5270720 /usr/lib/x86_64-linux-gnu/libnsl.so.2.0.1
socat   7399  bot  mem    REG                8,6   667864 5253402 /usr/lib/x86_64-linux-gnu/libssl.so.3
socat   7399  bot  mem    REG                8,6    44872 5271219 /usr/lib/x86_64-linux-gnu/libwrap.so.0.7.6
socat   7399  bot  mem    REG                8,6   240936 5245010 /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
socat   7399  bot    0u   CHR              136,0      0t0       3 /dev/pts/0
socat   7399  bot    1u   CHR              136,0      0t0       3 /dev/pts/0
socat   7399  bot    2u   CHR              136,0      0t0       3 /dev/pts/0
socat   7399  bot    3u  unix 0x0000000000000000      0t0   80275 type=DGRAM
socat   7399  bot    4u  unix 0x0000000000000000      0t0   80276 type=DGRAM
socat   7399  bot    5u  IPv4              80277      0t0     UDP *:5000 
```

---

## **Summary**
- **Each process** has an FD limit (`ulimit -n`, default 1024, can be increased).
- **System-wide limit** (`/proc/sys/fs/file-max`) is usually **very high**.
- **Each FD corresponds to an open file, socket, or pipe**.
- We can check and modify limits using `ulimit` and `/etc/security/limits.conf`.

