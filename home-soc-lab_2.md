# Home SOC Lab: Security Onion + Metasploitable2 on Proxmox

## Overview

I'm a cybersecurity student at FIU, still figuring out which specialization I want to go deep on, and this project was my excuse to get hands-on with a real detection stack instead of just reading about one. I built a home Security Operations Center lab on a Dell PowerEdge R410 running Proxmox VE, with a Security Onion sensor watching traffic and a deliberately vulnerable target machine to generate stuff for it to catch.

## Architecture

- **Hypervisor:** Proxmox VE on a Dell PowerEdge R410
- **Sensor VM:** Security Onion 3.2.0 — 24GB RAM, 6 cores, 250GB disk
- **Target VM:** Metasploitable2 (intentionally vulnerable)
- **Networking:** Both VMs sit on the same Proxmox bridge (`vmbr0`) and are reachable from my laptop for testing

![Security Onion status showing all services healthy](screenshots/so-status%20showing%20all%20services%20healthy.png)

## Bugs I Hit and Fixed

Nothing about this went smoothly on the first try, which honestly was the more educational part. Here's what actually broke and how I chased each one down.

### 1. Metasploitable2 wouldn't boot after import

Right after importing the Metasploitable2 vmdk, the VM would hang during boot with an LVM failure. Took a bit to realize the problem wasn't the disk image itself, it was the bus I'd attached it on. I'd imported it as a SCSI disk, and Metasploitable2's old kernel doesn't have the right driver for that controller, so it couldn't find its own root volume. Switching the disk to IDE fixed it immediately, the kernel could see the drive again and boot normally.

### 2. Zeek kept crashing under memory pressure

Once both VMs were up, Zeek workers on the Security Onion box kept dying. I'd given the sensor VM 16GB of RAM, which sounded like plenty until I remembered it's running the full Elastic + Zeek + Suricata + Strelka stack simultaneously. The host was swapping hard trying to keep everything alive, and Zeek was the first thing to get OOM-killed. Bumping the VM up to 24GB gave everything enough headroom to run without swapping, and the crashes stopped.

### 3. Zeek was listening on the wrong interface

This one took the longest to find because everything looked healthy on the surface, so-status was green and services were running, but I wasn't seeing any alerts no matter what I threw at the target. I dug into it with `docker exec so-zeek cat /opt/zeek/etc/node.cfg` and found Zeek was configured to sniff `bond0`, which was a dead interface that wasn't actually receiving any traffic. The real monitor NIC was `ens19`. I fixed it by editing the Salt pillar config at `/opt/so/saltstack/local/pillar/minions/securityonion1_standalone.sls` and reapplying with `salt-call state.apply`. As soon as Zeek was pointed at the right interface, traffic started showing up.

### Bonus bug: no traffic to monitor at all

Even after fixing the interface, I still wasn't seeing much because Proxmox's virtual bridge switches unicast traffic directly between VMs and doesn't mirror it out to a third "monitoring" NIC by default. I had to manually set up traffic mirroring on the Proxmox host using `tc` (Linux traffic control) so the monitor interface could actually see what was happening between the two VMs. Worth noting: these `tc` rules don't survive a reboot of either VM, so they need to be reapplied any time one restarts. That's on my list to make permanent (see below).

## Results

With the pipeline finally wired up correctly, I ran Nmap scans against Metasploitable2 from an external laptop:

- `nmap -sV <target>`
- `nmap -sS -T4 -p- <target>`

![Nmap scan launched against the Metasploitable2 target](screenshots/Nmap%20scan%20results%20against%20Metasploitable2.png)

Security Onion generated 27 real alerts off these scans, including ET SCAN Nmap detection signatures and RPC portmap listing alerts. That confirmed the full detection pipeline was working end to end: packet capture → Zeek/Suricata → Elasticsearch → alerts in the web UI.

![Alerts overview in the Security Onion web UI](screenshots/Alerts%20overview%20showing%2027%20detections.png)

![Detail view of one of the Nmap detection alerts](screenshots/Detail%20view%20of%20the%20Nmap%20Scripting%20Engine%20alert.png)

![Hunt results showing the scan traffic](screenshots/Hunt%20results%20showing%20captured%20traffic%20to%20Metasploitable2.png)

## Still To Do

- Make the `tc` traffic mirroring rules persistent across VM reboots
- Add an Active Directory VM as a second target
- Explore more attack types and compare detection coverage across them

## What's Next

The next session is going to focus on adding an Active Directory VM as a second target and testing detection coverage against a wider variety of attack types, not just recon scans.
