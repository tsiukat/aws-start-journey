# Lab Internet Protocol Troubleshooting Commands

> **Course:** AWS Cloud Practitioner (CLF-C02)  
> **Topic:** ping, traceroute, netstat, telnet, curl - Network Diagnostics  


---

## Lab Objective

Practice the core network troubleshooting commands used in real support scenarios - and understand which OSI layer each command targets and what problem it diagnoses.

---

## Commands by OSI Layer

| OSI Layer | Command | What It Tests |
|-----------|---------|---------------|
| Layer 3 - Network | `ping` | Basic IP reachability and packet loss |
| Layer 3 - Network | `traceroute` | Full path to destination, latency per hop, where loss occurs |
| Layer 4 - Transport | `netstat` | Active TCP connections and listening ports on the local host |
| Layer 4 - Transport | `telnet` | Whether a specific TCP port is open and reachable |
| Layer 7 - Application | `curl` | Full HTTP/HTTPS request - response code, headers, SSL |

---

## Commands Practiced

### `ping` - Test IP Connectivity
```bash
ping 8.8.8.8 -c 5
```
Sends 5 ICMP echo requests to Google's DNS. Confirms basic connectivity and shows round-trip time and packet loss.

---

### `traceroute` - Trace the Path
```bash
traceroute 8.8.8.8
```
Shows every hop between the EC2 instance and the destination. Three asterisks `***` = failed hop. Packet loss near the end = server/ISP issue; near the start = local/AWS issue.

---

### `netstat` - Check Listening Ports
```bash
netstat -tp      # established connections
netstat -tlp     # listening services
netstat -ntlp    # listening services, no port name resolution
```
Shows which ports are active on the local host - used to verify that a port is (or is not) listening.

---

### `telnet` - Test a Specific Port
```bash
sudo yum install telnet -y
telnet www.google.com 80
```
Confirms TCP connectivity to a specific port. `Connection refused` = something is blocking (firewall/SG). `Connection timed out` = no network route.

---

### `curl` - Test HTTP/HTTPS Response
```bash
curl -vLo /dev/null https://aws.com
```
Full verbose HTTP request - shows response code, redirects, SSL handshake. Confirms application-layer connectivity end to end.

---

## Key Takeaways

| Concept | What I Learned |
|---------|----------------|
| **Layer-specific tools** | Each command targets a different network layer - using the right tool narrows the problem quickly |
| **ping vs traceroute** | ping confirms reachability; traceroute shows where in the path the problem occurs |
| **`***` in traceroute** | Failed hop - look at the hops on either side to pinpoint the break |
| **netstat for security** | Reveals unexpected listening ports - useful for confirming that a port is blocked as intended |
| **telnet for port testing** | Faster and more precise than ping for confirming specific port access |
| **curl for app layer** | Goes beyond connectivity - tests the full HTTP stack including SSL and response codes |

---

## My Reflection

This lab was a toolkit lab - no infrastructure to build, just commands to understand. What made it valuable was the connection between each command and a real customer scenario. Instead of memorizing `traceroute` as a definition, I used it in the context of a latency complaint where the customer needed to know whether the issue was AWS or their ISP.

The OSI model framing was genuinely useful here. Knowing that `ping` and `traceroute` operate at Layer 3, while `telnet` and `netstat` are Layer 4, and `curl` is Layer 7 - gives a systematic way to narrow down a problem. Start at the bottom: can you reach the IP? Can you reach the port? Can you get a valid HTTP response? Each positive answer eliminates one layer from the investigation.

The `telnet` distinction between "connection refused" and "connection timed out" was the most practically useful detail. Those two responses point to completely different root causes - one is a firewall blocking traffic, the other is a missing route or dead host.

> 💡 **Key Insight:** Troubleshooting is not guessing - it is eliminating layers systematically. These five commands cover the most common failure points from Layer 3 to Layer 7, and knowing which one to reach for first is what separates a methodical diagnosis from random trial and error.

---

*Author: Kateryna | AWS Cloud Practitioner Exam Prep*
