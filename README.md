# Cybersecurity Lab Notes

Hands-on cybersecurity lab reflections, learning notes, and infrastructure projects.

## Goal

To reinforce cybersecurity fundamentals through practical labs and hands-on infrastructure builds, documenting how vulnerabilities occur and how detection actually works, not just how to exploit or configure things.

## Projects

- Home SOC Lab — Built a Security Onion sensor and a vulnerable Metasploitable2 target on my Proxmox server, then spent most of the time chasing down why nothing was working: a disk controller mismatch that kept the target from booting, Zeek crashing from not enough RAM, and the sensor listening on the wrong network interface. Fixed all three and confirmed it worked end to end by running Nmap scans and watching Security Onion catch 27 real alerts.

*(More projects will be added here as they're completed — each gets its own linked file.)*

## HTB Lab Notes

High-level learning notes from hands-on labs completed on Hack The Box.

**What You'll Find Here**
- High-level lab summaries
- Enumeration and analysis techniques
- Lessons learned from misconfigurations and insecure design

**What You Will NOT Find Here**
- Flags or credentials
- Step-by-step exploit instructions
- Anything that violates platform rules

## Skills Practiced

- Network and service enumeration
- Linux and Windows fundamentals
- Web application security concepts
- Privilege escalation reasoning
- Security-focused problem solving
- Home lab infrastructure (Proxmox, Security Onion, network sensor deployment)
