# **Build a Secure Google Cloud Network **

This project demonstrates how to design and implement a secure Virtual Private Cloud (VPC) network on Google Cloud by configuring firewall rules, VM tags, and Identity-Aware Proxy (IAP) to strictly control access to internal resources.

The scenario focuses on securing Jeff’s site by ensuring that:

* The **bastion host has no public IP address**
* **SSH access to the bastion is only possible via IAP**
* **SSH access to the juice-shop VM is only possible through the bastion**
* **Only HTTP (TCP 80)** is publicly accessible for the juice-shop web application

---

## ** Architecture Overview**

The project deploys a custom VPC containing two virtual machines:

### **1. Bastion Host**

* No public IP
* Acts as the secured entry point for administrators
* Accessible only through SSH via Google Cloud IAP

### **2. Juice-shop VM**

* Hosts an intentionally vulnerable web application
* Publicly accessible **only on HTTP (port 80)**
* SSH restricted to internal traffic from the bastion host

---

## ** Key Security Controls Implemented**

### **1. Allow SSH to Bastion via IAP**

* **Direction:** Ingress
* **Source:** `35.235.240.0/20` *(IAP range)*
* **Target tags:** `bastion`
* **Ports:** TCP 22
  This ensures the bastion can only be accessed using IAP tunneling—not directly from the internet.

### **2. Bastion Host Without Public IP**

* VM configured with **External IP = None**
  This eliminates exposure to the open internet.

### **3. Allow SSH from Bastion to Juice-shop**

* **Direction:** Ingress
* **Source tags:** `bastion`
* **Target tags:** `juice-shop`
* **Ports:** TCP 22
  This restricts SSH access to the juice-shop VM to only the bastion host.

### **4. Allow HTTP Access to Juice-shop**

* **Direction:** Ingress
* **Source:** `0.0.0.0/0`
* **Target tags:** `juice-shop`
* **Ports:** TCP 80
  This exposes the application to the internet on HTTP only.

---

## ** Step-by-Step Implementation**

### **1. Create a Custom VPC**

* Created `private-network`
* Configured subnets manually
* Disabled auto subnet creation

### **2. Assign VM Tags**

* **Bastion VM:** `bastion`
* **Juice-shop VM:** `juice-shop`

### **3. Configure Required Firewall Rules**

Added 3 key rules:

* Allow IAP → Bastion (SSH)
* Allow Bastion → Juice-shop (SSH)
* Allow World → Juice-shop (HTTP)

### **4. Configure Bastion Host Access via IAP**

Used:

```bash
gcloud compute ssh bastion --tunnel-through-iap
```

### **5. Connect to Juice-shop Through the Bastion**

From Bastion:

```bash
ssh <INTERNAL_IP_OF_JUICE_SHOP>
```

### **6. Validate Access**

* Juice-shop reachable publicly on port 80
* Bastion reachable only through IAP
* Juice-shop SSH reachable only internally via bastion

---

## **📁 Repository Structure**

```
/
├── README.md
├── diagrams/
│   └── gcp-secure-network.png
└── scripts/
    ├── create-firewall.sh
    ├── create-vms.sh
    └── verify-access.md
```


## ** What This Project Demonstrates**

* Secure VPC network design
* Identity-Aware Proxy configuration
* SSH access control through internal routing
* Network segmentation and least-privilege access
* Real-world cloud security best practices

