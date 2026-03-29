# 🔐 Lab 1: Brute Force Attack Detection & Prevention (SSH)

---

## 📌 Objective

Simulate a brute force attack on an SSH server and detect it using logs, then automatically block the attacker using Fail2Ban.

---

## 🧰 Tools Used

* Kali Linux (Attacker)
* Hydra (Brute force tool)
* Ubuntu Server (Target)
* OpenSSH (Service)
* Fail2Ban (Defense)
* tcpdump (Packet capture)

---

## 🏗️ Lab Setup

* Attacker Machine: Kali Linux
* Target Machine: Ubuntu Server
* Service: SSH (Port 22)
* Network: NAT (same network for both VMs)

---

## ⚙️ Step 1: Verify Connectivity

From Kali:

```bash
ping 192.168.176.131
```

✔ Confirms attacker can reach target

---

## 🔐 Step 2: Test SSH Access

```bash
ssh labuser@192.168.176.131
```

✔ Confirms SSH is working before attack

---

## ⚔️ Step 3: Launch Brute Force Attack

```bash
hydra -l labuser -P /usr/share/wordlists/rockyou.txt ssh://192.168.176.131
```
-l → single username
-P → password list

👉 Meaning:
“Try many passwords for this user”

✔ Attempts multiple passwords on SSH

---

## 🔍 Step 4: Monitor Logs (Detection)

On Ubuntu:

```bash
sudo tail -f /var/log/auth.log
```
tail → shows last lines of a file
-f = follow (live updates)

👉 Meaning:
“Keep showing new logs as they come”

✔ Shows real-time failed login attempts

---

## 📄 Step 5: Extract Evidence

```bash
sudo grep "Failed password" /var/log/auth.log > brute_force_logs.txt
```
grep = search text

👉 Meaning:
“Show only lines that contain ‘Failed password’”

✔ Saves only relevant attack logs

---

## 📡 Step 6: Capture Network Traffic

```bash
sudo tcpdump -i any port 22 -w ssh_attack.pcap
```
-i any → capture on all interfaces
port 22 → only SSH traffic
-w → write to file

👉 Meaning:
“Capture SSH traffic and save it”

✔ Captures SSH traffic

Stop capture:

```bash
CTRL + C
```

---

## 🛡️ Step 7: Install Fail2Ban

```bash
sudo apt update
sudo apt install fail2ban -y
```

---

## ⚙️ Step 8: Configure Protection

Edit config:

```bash
sudo nano /etc/fail2ban/jail.local
```

Add:

```
[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 5
bantime = 600
findtime = 600
```

Restart service:

```bash
sudo systemctl restart fail2ban
```
👉 Controls services

start
stop
restart

---

## 🚨 Step 9: Verify Blocking

```bash
sudo fail2ban-client status sshd
```

✔ Shows banned IP

---

## ❌ Attack Result

On Kali:

```
Connection refused
```

✔ Confirms attacker is blocked

---

## 🧠 Key Learnings

* Brute force attacks generate repeated login failures
* Logs are critical for detection
* Packet capture helps deep analysis
* Fail2Ban automates defense

---
### Q1: What is a brute force attack?

A method of trying multiple password combinations to gain access.

---

### Q2: How did you detect the attack?

By monitoring repeated failed login attempts in `/var/log/auth.log`.

---

### Q3: What does Fail2Ban do?

It monitors logs and blocks IPs after multiple failed attempts.

---

### Q4: Difference between logs and packet capture?

Logs show system events, while packet capture shows network traffic.

---

## 🚀 Conclusion

This lab demonstrates a complete security workflow:
Attack → Detection → Analysis → Prevention

---

