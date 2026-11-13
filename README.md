# Cybersecurity Lab Notes

Hands-on cybersecurity lab reflections, learning notes, and infrastructure projects.

## Goal

To reinforce cybersecurity fundamentals through practical labs and hands-on infrastructure builds, documenting how vulnerabilities occur and how detection actually works, not just how to exploit or configure things.

## Projects

- [Home SOC Lab](https://github.com/jpojeda82-lab/cybersecurity-lab-notes-projects/blob/main/home-soc-lab_2.md) I set up a Security Onion sensor and a vulnerable Metasploitable2 target on my Proxmox server. I spent most of my time working out what was going wrong. The issues were a disk controller error, Zeek crashing due to low RAM, and the sensor not being on the correct interface. The system worked perfectly at the end and I had 27 real alerts. (More projects will go here when finished. Each has their own file that can be clicked on.)
- [Vulnerability Assessment: Metasploitable2](https://github.com/jpojeda82-lab/cybersecurity-lab-notes-projects/blob/main/vuln-assessment.md) — Ran an unauthenticated Nessus scan against a Metasploitable2 target, identifying two unauthenticated root-access backdoors, a known-exploited Apache Tomcat CVE, and several other critical misconfigurations, then documented and prioritized remediation steps for each.

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
