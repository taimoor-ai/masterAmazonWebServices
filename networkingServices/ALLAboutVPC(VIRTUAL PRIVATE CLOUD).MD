# AWS VPC & VPC Peering: Deep Dive and Hands-on Guide

## 📌 Introduction to VPC

A **VPC (Virtual Private Cloud)** is a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define. It gives you complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways.

### The "Private Cloud" Analogy

Think of the AWS Cloud as a massive city. A **VPC** is your private gated house within that city. You decide who enters, who leaves, and how the rooms (subnets) inside are connected.

---

## 🏗️ Core VPC Components

| Component                        | Description                                                                                                                                  |
| :------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------- |
| **Subnet**                 | A range of IP addresses in your VPC. (Public = Internet access; Private = No direct internet access).                                        |
| **Internet Gateway (IGW)** | A gateway that allows communication between your VPC and the internet.                                                                       |
| **Route Table**            | A set of rules (routes) used to determine where network traffic from your subnet or gateway is directed.                                     |
| **NAT Gateway**            | Allows instances in a private subnet to connect to the internet but prevents the internet from initiating a connection with those instances. |
| **CIDR Block**             | **Classless Inter-Domain Routing**. It defines the IP range for your VPC (e.g., `10.0.0.0/16`).                                      |

---

## 📏 Understanding CIDR Blocks

CIDR determines how many IP addresses your network will have.

* `10.0.0.0/16`: Provides **65,536** IP addresses.
* `10.0.0.0/24`: Provides **256** IP addresses.
* `10.0.0.0/28`: Provides **16** IP addresses.

> **Note:** Overlapping CIDR ranges between VPCs will prevent VPC Peering. Always plan your IP ranges ahead of time.

---

## 🖼️ VPC Architecture Diagram

```mermaid
graph TD
    subgraph AWS_Cloud
        subgraph VPC_Test
            IGW1[Internet Gateway]
            RT1[Route Table]
            Subnet1[Public Subnet]
            EC2_A[EC2 Instance A]
          
            IGW1 --> RT1
            RT1 --> Subnet1
            Subnet1 --> EC2_A
        end
      
        subgraph VPC_Prod
            IGW2[Internet Gateway]
            RT2[Route Table]
            Subnet2[Public Subnet]
            EC2_B[EC2 Instance B]
          
            IGW2 --> RT2
            RT2 --> Subnet2
            Subnet2 --> EC2_B
        end
      
        VPC_Test -- VPC Peering Connection -- VPC_Prod
    end
```

---

## 🚀 Step-by-Step Hands-on Guide

### Phase 1: Creating a Custom VPC

1. **Navigate to VPC Dashboard:** Search for VPC in the AWS Console.
2. **Create VPC:** Select "VPC only," name it `test-vpc`, and set CIDR to `10.0.0.0/16`.
3. **Create Subnet:** Go to Subnets > Create Subnet. Select `test-vpc`, name it `test-public-subnet`, choose an Availability Zone, and set CIDR to `10.0.0.0/24`.
4. **Enable Auto-assign Public IP:** (Optional but recommended for public subnets) Edit subnet settings and enable "Auto-assign public IPv4 address."

### Phase 2: Enabling Internet Access

1. **Internet Gateway (IGW):** Create an IGW named `test-igw`. After creation, select **Actions > Attach to VPC** and pick `test-vpc`.
2. **Configure Route Table:**
   * Create a Route Table named `test-rt` for `test-vpc`.
   * **Subnet Association:** Associate it with `test-public-subnet`.
   * **Edit Routes:** Add a route with Destination `0.0.0.0/0` and Target `Internet Gateway (test-igw)`.

### Phase 3: Launching an Instance

1. Launch an EC2 instance.
2. In **Network Settings**, click Edit.
3. Change VPC to `test-vpc` and Subnet to `test-public-subnet`.
4. Launch the instance and test the connection via SSH.

---

## 🤝 VPC Peering (Connecting Two VPCs)

VPC Peering allows you to route traffic between two VPCs using private IP addresses.

### The Connection Process

1. **Create 2nd VPC:** Create `prod-vpc` with a non-overlapping CIDR (e.g., `192.168.0.0/16`).
2. **Peering Connection:**
   * Go to **Peering Connections > Create Peering Connection**.
   * Select `test-vpc` as the Requester and `prod-vpc` as the Accepter.
3. **Accept Request:** Select the peering connection > **Actions > Accept Request**.
4. **Update Route Tables (Crucial Step):**
   * In `test-rt`: Add a route to `192.168.0.0/16` via the Peering Connection.
   * In `prod-rt`: Add a route to `10.0.0.0/16` via the Peering Connection.
5. **Update Security Groups:** Ensure the Security Group of the target instance allows **ICMP - IPv4** (Ping) from the CIDR range of the other VPC.

---

## 💡 Real-World Applications

1. **Environment Isolation:** Keeping Development, Testing, and Production environments in separate VPCs for security.
2. **Shared Services:** A central "Shared Services" VPC (holding databases or logging tools) connected to multiple application VPCs via peering.
3. **Corporate Connectivity:** Connecting your on-premise data center to an AWS VPC via VPN or Direct Connect.

---

## ⚡ Quick Revision

* **VPC** = Private Network in AWS.
* **Subnet** = A slice of the VPC IP range.
* **IGW** = The "Door" to the internet.
* **Route Table** = The "Map" for traffic.
* **NAT Gateway** = One-way internet access for private instances.
* **VPC Peering** = Private bridge between two VPCs.
* **ICMP Protocol** = Must be enabled in Security Groups to allow "Ping" tests.
* **Overlapping CIDRs** = The biggest enemy of VPC Peering. Ensure ranges are unique!
