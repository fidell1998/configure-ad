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

<img width="643" height="604" alt="Screenshot 2025-09-22 at 2 24 11 PM" src="https://github.com/user-attachments/assets/73911e51-c19e-4cd5-b7f1-af1bd2ba9b3b" />

- From the Azure Portal, **restart** `Client-1`.
- Log in to `Client-1` and **ping** `DC-1`’s private IP to verify connectivity.
- Open **PowerShell** on `Client-1` and run:

  ```powershell
  ipconfig /all

- The output for the DNS setting should show **DC-1's** private IP Adress.

<br>

<h3>Deploying Active Directory</h3>

Make sure both `DC-1` and `Client-1` VMs are powered **on** in the **Azure Portal**.

<h4> Install Active Directory </h4>

- Log into the `DC-1` VM.
- Open **Server Manager** and install the **Active Directory Domain Services (AD DS)** role by clicking "Add roles and features".

<img width="1116" height="829" alt="1" src="https://github.com/user-attachments/assets/21af7910-afc8-4b05-9685-1b4d7e7d2707" />

- Promote the server to a Domain Controller:

<img width="1920" height="443" alt="2" src="https://github.com/user-attachments/assets/00b8940a-8e8f-4651-9f0c-9e9b405c8caa" />

  - Choose to create a **new forest** with the domain name `mydomain.com`.  
    _(This can be any name you choose — just make sure to remember it.)_

<img width="876" height="636" alt="3" src="https://github.com/user-attachments/assets/d3ea7207-52fb-4612-8c1d-a625908f1ea2" />
    
- After promotion, the server will **restart automatically**.
- Log back into `DC-1` using the domain account:

<h4>Create an Admin and Normal User Account in AD</h4>

- Open **Active Directory Users and Computers (ADUC)** on `DC-1`.
- Create two new **Organizational Units (OUs)**:
- `_EMPLOYEES`
- `_ADMINS`
- Inside `_ADMINS`, create a new user:
- **Full Name:** Jane Doe  
- **Username:** `jane_admin`  
- **Password:** (Use the same as labuser or a secure alternative)
- Add `jane_admin` to the **Domain Admins** security group.
- Log out of `labuser` and log in to `DC-1` as:
- > From this point on, use `jane_admin` as your primary admin account.

<h4>Join Client-1 to Your Domain (`mydomain.com`)</h4>

- In the **Azure Portal**, go to `Client-1`'s **network interface settings**.
- Set the **DNS server** to the **private IP address of `DC-1`**.
- Restart `Client-1` from the Azure Portal.
- Log in to `Client-1` using the **local admin account** (`labuser`).
- Join `Client-1` to the domain:
- Navigate to:  
  `System Properties > Computer Name > Change`
- Enter domain: `mydomain.com`
- Use the credentials for `jane_admin` when prompted.
- Restart the system when prompted.
- Log back into `DC-1` and open **ADUC**.
- Confirm that `Client-1` appears under the **Computers** container.
- Create a new OU named `_CLIENTS`.
- Move `Client-1` into the `_CLIENTS` OU.

<h4>Setup Remote Desktop for non-adminstrative users on Client-1</h4>

1. Log into **Client-1** using the domain account:  
   `mydomain.com\jane_admin`

2. Open **System Properties**:  
   - You can do this by right-clicking on **This PC** and selecting **Properties**, then clicking **Remote settings** on the left.

3. Navigate to the **Remote Desktop** section.

4. Enable **Remote Desktop** if it's not already enabled.

5. Click **Select Users** and add the group:  
   `Domain Users`

6. Click **OK** to apply the changes.

> You can now log into **Client-1** using any standard (non-administrative) domain user account.

> **Tip:** In a production environment, it’s best to configure Remote Desktop access through **Group Policy**. This allows you to apply the setting across multiple systems efficiently.

<br>

<h3> Creating Users with PowerShell </h3>

 Log into **DC-1** using the domain account:  
   `mydomain.com\jane_admin`

2. Open **PowerShell ISE** as an administrator:  
   - Right-click on **PowerShell ISE** and select **Run as administrator**.

3. Create a new script file:
   - In PowerShell ISE, click **File > New**.
   - Paste the contents of the provided script into the editor XXX

4. Run the script:
   - Press **F5** or click the **Run** button.
   - Observe the user accounts being created in the console output.

5. Verify the accounts in **Active Directory Users and Computers (ADUC)**:
   - Open **ADUC**.
   - Navigate to the appropriate **Organizational Unit (OU)** where the script created the accounts.

6. Test one of the new accounts:
   - Attempt to log into **Client-1** using one of the newly created user accounts.
   - **Note:** Refer to the script to find the default password set for these accounts.

<br>

<h3>Group Policy and Managing Accounts</h3>
insert text
