<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.
Designed for tech professionals with intermediate-level skills.
<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)
  
A **Windows 10** virtual machine and a **Windows Server 2022** instance were created using _Microsoft Azure_, with _macOS_ as the host operating system.

<h2>High-Level Deployment and Configuration Steps</h2>

- Preparing AD Infrastructure in Azure
- Deploying Active Directory
- Creating Users with PowerShell
- Group Policy and Managing Accounts

<h2>Deployment and Configuration Steps</h2>

<h3> Preparing AD Infrastructure in Azure </h3>

<h4> Setup Domain Controller in Azure </h4>

- Create a **Resource Group** in Azure.
- Set up a **Virtual Network** and a **Subnet**.
- Deploy a **Windows Server 2022 VM** named `DC-1`.
- After creation, set `DC-1`’s **NIC private IP address** to **static**.

<img width="1918" height="841" alt="1" src="https://github.com/user-attachments/assets/a1179bad-dbf1-4399-bcb6-84ce39a33fff" />

- Log in to `DC-1` and **disable the Windows Firewall** (for testing connectivity).

<img width="1193" height="860" alt="2" src="https://github.com/user-attachments/assets/d7666254-60d6-4870-a804-f14013b687d1" />
<img width="1141" height="845" alt="3" src="https://github.com/user-attachments/assets/5a856011-9f4f-4135-b9a7-fc2547136c12" />
<img width="1173" height="871" alt="4" src="https://github.com/user-attachments/assets/d58a3ac1-4012-426e-8478-8ef497dd9b18" />

<h4> Setup Client-1 in Azure </h4>

- Create a **Windows 10 VM** named `Client-1`.
- Make sure it’s in the **same region and Virtual Network** as `DC-1`.
- Update `Client-1`’s **DNS settings** to use `DC-1`’s **private IP address**.

fdjsklfjds

- From the Azure Portal, **restart** `Client-1`.
- Log in to `Client-1` and **ping** `DC-1`’s private IP to verify connectivity.
- Open **PowerShell** on `Client-1` and run:

  ```powershell
  ipconfig /all

- The output for the DNS setting should show **DC-1's** private IP Adress.

<br>

<h3>Deploying Active Directory</h3>

<br>

<h3>Creating Users with PowerShell</h3>

<br>

<h3>Group Policy and Managing Accounts</h3>
