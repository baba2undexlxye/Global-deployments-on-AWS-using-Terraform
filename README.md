# Global-deployment-using-Terraform-in-AWS


📌 Project Scenario
---

Tokyo Midtown Medical Center (TMMC) is expanding its healthcare services to support customers located throughout Japan and travelling abroad. The organization plans to introduce J-Tele-Doctor, a remote medical consultation platform for patients who:

Wish to avoid hospital visits due to potential sickness transmission

Are travelling internationally

Live abroad part-time

AWS Japan secured the contract, as part of the Solutions Architecture team we are responsible for designing the global hosting architecture.

The application is:
✔️ Available globally
✔️ Offer regional language support
✔️ Secure
✔️ Compliant with Japan’s data sovereignty requirements

Stage One focuses on global application hosting, security zones, multi-AZ compute, and syslog data transfer to Japan.

🧭 Stage One Requirements

🌍 1. Global Application Hosting Regions
---

The team deployed local application hosting in the following areas:

Location	Region
Main App (Japan)	Tokyo (ap-northeast-1)
New York	us-east-1
London	eu-west-2
Sao Paulo	sa-east-1
Australia	ap-southeast-2
Hong Kong	ap-east-1
California us-west-1
---
🏗️ 2. Local Infrastructure Requirements (Per Region)


Each region included:
---
✔️ Two Availability Zones (High Availability)
✔️ Auto Scaling Group (ASG)

Minimum configuration:

Requires 2 AZs

Minimum 1 EC2 instance deployed for testing

✔️ Security Zone for Syslog Transfer

Must demonstrate the ability to send syslog data back to Japan ONLY

✔️ Public Access Restricted

Only port 80 may be open to the public

All other ports locked down
---

🔒 Data Residency Rules

Syslog data is stored in Japan only

No personal information can be stored outside Japan

Data cannot be sent abroad via VPN

Syslog storage AZ must be private

🔧 Technical Stage Limitations

Databases will be implemented in Stage 2

SIEM/Syslog server is also Stage 2
---
🧱 Global Network Architecture (Per Region)

Each region deploys:

1 VPC

2 Availability Zones

Public Subnets (for Load Balancer / Bastion, if needed)

Private Subnets (for EC2 + Syslog Zones)

Internet Gateway

Route Tables

ASG + Launch Template
---
Below is the exact CIDR mapping provided.
---
🌏 Regional VPC + Subnet Design
🇯🇵 Tokyo – Main Region (ap-northeast-1)

VPC: tokyo
CIDR: 172.18.0.0/16

Subnets

Public

172.18.1.0/24 (AZ a)

172.18.4.0/24 (AZ d)

Private

172.18.11.0/24 (AZ a)

172.18.14.0/24 (AZ d)

🇺🇸 New York – (us-east-1)

CIDR: 172.19.0.0/16

Public

172.19.1.0/24

172.19.2.0/24

Private

172.19.11.0/24

172.19.12.0/24

🇬🇧 London – (eu-west-2)

CIDR: 172.20.0.0/16

Public

172.20.1.0/24

172.20.2.0/24

Private

172.20.11.0/24

172.20.12.0/24

🇧🇷 Sao Paulo – (sa-east-1)

CIDR: 172.21.0.0/16

Public

172.21.1.0/24

172.21.3.0/24

Private

172.21.11.0/24

172.21.13.0/24

🇦🇺 Australia – (ap-southeast-2)

CIDR: 172.22.0.0/16

Public

172.22.1.0/24

172.22.2.0/24

Private

172.22.11.0/24

172.22.12.0/24

🇭🇰 Hong Kong – (ap-east-1)

CIDR: 172.23.0.0/16

Public

172.23.1.0/24

172.23.2.0/24

Private

172.23.11.0/24

172.23.12.0/24

🇺🇸 California – (us-west-1)

CIDR: 172.24.0.0/16

Public

172.24.1.0/24

172.24.2.0/24

Private

172.24.11.0/24

172.24.12.0/24
---
🔐 Security Architecture
Public Subnets

Only allow inbound port 80 (HTTP)

Outbound restricted unless required

Private Subnets

EC2 instances within ASG

No public IPs

Default route may go through NAT Gateway (if internet required for app updates)

Syslog traffic tunneled back to Tokyo only

Syslog Transfer Zone

Dedicated private subnet in Tokyo

Receives log ingestion from all remote regions

Must not contain any personal medical information

VPN not allowed → use TLS-secured log transfer or AWS services such as: 

Kinesis

CloudWatch Logs

S3 → EventBridge → KDF
---
🚀 The following was demonstrated in this project: 

✔️ 1. EC2 Deployment in all regions
✔️ 2. ASG configured in 2 AZs
✔️ 3. Port 80 publicly accessible
✔️ 4. Syslog delivery to Tokyo demonstrated
✔️ 5. No personal data stored outside Japan
✔️ 6. Private subnet for syslog AZ
