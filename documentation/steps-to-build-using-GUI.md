# **Steps to Build a Secure Google Cloud Network (GUI Method)**

This document describes how I created the secure VPC network using the **Google Cloud Console (GUI)**.

---

## **1. Create the VPC**

1. Go to **VPC Network → VPC networks**
2. Click **Create VPC**
3. Name: `acme`
4. Subnet creation mode: **Custom**
5. Add a subnet (example):

   * Region: your chosen region
   * Name: `private-subnet`
   * IP range: e.g., `10.0.0.0/24`
6. Create

---

## **2. Create the Bastion Host (NO public IP)**

1. Go to **Compute Engine → VM Instances**
2. Click **Create instance**
3. Name: `bastion`
4. Tags: `bastion`
5. Network → choose your VPC and subnet
6. External IP → **Set to “None”**
7. Create the instance
[Bastion VM](screenshots/bastion-vm.png.png)
---

## **3. Create the Juice-Shop VM**

1. Create another VM
2. Name: `juice-shop`
3. Tags: `juice-shop`
4. External IP → **Ephemeral**
5. Allow HTTP traffic: **Checked**
6. Create
![Juice-shop VM](screenshots/juice-shop-vm.png)
---

## **4. Create Firewall Rule (SSH via IAP → Bastion)**

1. Go to **VPC Network → Firewall**
2. Click **Create Firewall Rule**
3. Name: `iap-ssh-bastion`
4. Target tags: `bastion`
5. Source IP ranges: `35.235.240.0/20`
6. Protocols: **TCP 22**
7. Create

---

## **5. Create Firewall Rule (Bastion → Juice-Shop SSH)**

1. Create another firewall rule
2. Name: `bastion-to-juice-shop-ssh`
3. Source tags: `bastion`
4. Target tags: `juice-shop`
5. Protocols: **TCP 22**
6. Create

---

## **6. Create Firewall Rule (HTTP → Juice-Shop)**

1. Name: `allow-http-juice-shop`
2. Target tags: `juice-shop`
3. Source: `0.0.0.0/0`
4. Protocols: **TCP 80**
5. Create
![Firewall Bastion to Juice](screenshots/firewall-bastion-to-juice.png)
---

## **7. Connect Using IAP**

To SSH into bastion:

* Use the SSH button on the VM
* It automatically uses **IAP tunneling** (because no public IP)
![Internal SSH](screenshots/internal-ssh.png)
From bastion:

```bash
ssh <INTERNAL_IP_OF_JUICE_SHOP>
```

---

## **8. Validate**

* Juice-shop loads on port 80 from the internet
* SSH to bastion only via IAP
* SSH to juice-shop only through bastion



Want me to generate one?
