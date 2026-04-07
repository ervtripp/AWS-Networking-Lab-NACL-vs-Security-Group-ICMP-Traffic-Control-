# 🔐 AWS Networking Lab: NACL vs Security Group (ICMP Traffic Control)

## 📘 Overview

In this lab, I built a segmented AWS VPC with public and private subnets to demonstrate how **Network ACLs (NACLs)** can override **Security Group rules**.

Instead of using SSH to validate connectivity, I used **ICMP (ping)** to test communication between EC2 instances. I then implemented a NACL rule to block ICMP traffic, proving that subnet-level controls take priority over instance-level permissions.

---

## 🧠 Objectives

* Validate connectivity between public and private EC2 instances using ICMP
* Demonstrate the difference between Security Groups and NACLs
* Show how NACL rules can block traffic even when Security Groups allow it
* Reinforce understanding of **stateless vs stateful filtering**

---

## 🏗️ Architecture

* Custom VPC (`10.0.0.0/16`)
* Public Subnet (`10.0.1.0/24`)
* Private Subnet (`10.0.2.0/24`)
* Internet Gateway attached to public subnet
* EC2 instances in both subnets
* Security Groups allowing ICMP traffic
* Network ACL applied to private subnet

---

## ⚙️ Step 1: Confirm Baseline Connectivity

From the public EC2 instance, I tested connectivity to the private EC2 using ICMP:

```bash
ping <PRIVATE-EC2-IP>
```

✅ Result: Successful replies confirmed that:

* Routing between subnets was functioning
* Security Groups allowed ICMP traffic
* Internal VPC communication was working

---

## ⚙️ Step 2: Analyze Existing Security Controls

At this stage:

* Security Group rules allowed ICMP traffic
* No restrictions were applied at the subnet level

This meant traffic flowed freely between instances.

---

## ⚙️ Step 3: Modify Network ACL to Block ICMP

Instead of modifying Security Groups, I updated the existing NACL associated with the private subnet.

### Inbound Rule Added:

* Rule #: 90
* Type: All ICMP - IPv4
* Source: `10.0.1.0/24` (public subnet)
* Action: DENY

### Outbound Rule Added:

* Rule #: 90
* Type: All ICMP - IPv4
* Destination: `10.0.1.0/24`
* Action: DENY

---

## 💡 Key Configuration Insight

Network ACLs are **stateless**, meaning:

* Both inbound AND outbound rules must be configured
* Traffic is evaluated in numerical order (lowest rule number first)

---

## ⚙️ Step 4: Retest Connectivity

After applying the NACL rules, I tested ICMP again:

```bash
ping <PRIVATE-EC2-IP>
```

❌ Result: Request timed out

---

## 🧠 Analysis

Despite Security Groups allowing ICMP:

* The NACL blocked traffic at the subnet level
* Traffic never reached the EC2 instance

This demonstrated **layered network security** and the precedence of subnet-level filtering.

---

## 🔍 Key Takeaways

* Security Groups are **stateful** and operate at the instance level
* Network ACLs are **stateless** and operate at the subnet level
* NACL rules can override Security Group permissions
* ICMP traffic must be explicitly allowed or denied (not tied to ports like TCP/UDP)

---

## 🚀 Skills Demonstrated

* AWS VPC design and subnet segmentation
* Traffic flow analysis and troubleshooting
* Firewall configuration (Security Groups vs NACLs)
* Protocol-level filtering (ICMP)
* Understanding of stateless vs stateful networking

---

## 📝 Resume Summary

Implemented subnet-level traffic filtering using AWS Network ACLs to block ICMP communication between EC2 instances, demonstrating layered security controls and protocol-based traffic management within a VPC.

---

## 🔥 Reflection

In this lab, I intentionally avoided SSH and focused on ICMP to better understand how different protocols interact with AWS security layers. This helped reinforce how traffic is evaluated across multiple layers and how misconfigurations can impact connectivity.

---

## 📌 Next Steps

* Allow HTTP while blocking ICMP
* Test partial access between subnets
* Introduce NAT Gateway and analyze outbound filtering
* Perform packet flow troubleshooting scenarios

---
