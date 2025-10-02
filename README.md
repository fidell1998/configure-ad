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

<img width="607" height="336" alt="4" src="https://github.com/user-attachments/assets/8e2840d2-4783-43a3-bb6a-d11c38967b4d" />

- Inside `_ADMINS`, create a new user:
- **Full Name:** Jane Doe  
- **Username:** `jane_admin`  
- **Password:** (Use the same as labuser or a secure alternative)
- Add `jane_admin` to the **Domain Admins** security group.

<img width="1911" height="843" alt="5" src="https://github.com/user-attachments/assets/b0d01705-1511-4889-a1ad-4d1859d15b24" />

- Log out of `labuser` and log in to `DC-1` as:
- > From this point on, use `jane_admin` as your primary admin account.

<h4>Join Client-1 to Your Domain (`mydomain.com`)</h4>

- Log in to `Client-1` using the **local admin account** (`labuser`).
- Join `Client-1` to the domain:
- Navigate to:  
  `System Properties > Computer Name > Change`
- Enter domain: `mydomain.com`
- Use the credentials for `jane_admin` when prompted.

<img width="1327" height="1015" alt="6" src="https://github.com/user-attachments/assets/13c3246b-7ebc-40ac-b749-ab73f4b77977" />

- Restart the system when prompted.
- Log back into `DC-1` and open **ADUC**.
- Confirm that `Client-1` appears under the **Computers** container.
- Create a new OU named `_CLIENTS`.
- Move `Client-1` into the `_CLIENTS` OU.

<h4>Setup Remote Desktop for non-adminstrative users on Client-1</h4>

- **Log into Client-1 using the domain account:**  
  `mydomain.com\jane_admin`

- **Open System Properties:**  
  Right-click on **This PC** and select **Properties**, then click **Remote settings** on the left sidebar.

- **Navigate to the Remote Desktop section.**

- **Enable Remote Desktop** if it’s not already enabled.

- **Click Select Users** and add the group:  
  `Domain Users`

<img width="1286" height="997" alt="1" src="https://github.com/user-attachments/assets/b035c707-0742-4047-a637-fa065d72063f" />

- **Click OK** to apply the changes.

> You can now log into **Client-1** using any standard (non-administrative) domain user account.

> **Tip:** In a production environment, it’s best to configure Remote Desktop access through **Group Policy**. This allows you to apply the setting across multiple systems efficiently.

<br>

<h3> Creating Users with PowerShell </h3>

- **Log into DC-1 using the domain account:**  
  `mydomain.com\jane_admin`

- **Open PowerShell ISE as an administrator:**  
  Right-click on PowerShell ISE and select **Run as administrator**.


- **Create a new script file:**  
  Open PowerShell ISE, click **File > New**, and paste the following script into the editor:

  ```powershell
  # ----- Edit these Variables for your own Use Case ----- #
  $PASSWORD_FOR_USERS   = "Password1"
  $NUMBER_OF_ACCOUNTS_TO_CREATE = 100
  # ------------------------------------------------------ #

  Function generate-random-name() {
      $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
      $vowels = @('a','e','i','o','u','y')
      $nameLength = Get-Random -Minimum 3 -Maximum 7
      $count = 0
      $name = ""

      while ($count -lt $nameLength) {
          if ($($count % 2) -eq 0) {
              $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
          }
          else {
              $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
          }
          $count++
      }

      return $name
  }

  $count = 1
  while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
      $fisrtName = generate-random-name
      $lastName = generate-random-name
      $username = $fisrtName + '.' + $lastName
      $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

      Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
      
      New-AdUser -AccountPassword $password `
                 -GivenName $firstName `
                 -Surname $lastName `
                 -DisplayName $username `
                 -Name $username `
                 -EmployeeID $username `
                 -PasswordNeverExpires $true `
                 -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
                 -Enabled $true
      $count++
  }

- **Run the script:**  
  Press **F5** or click the **Run** button.  
  Observe the user accounts being created in the console output.

- **Verify the accounts in Active Directory Users and Computers (ADUC):**  
  Open **ADUC**, then navigate to the appropriate **Organizational Unit (OU)** where the script created the accounts.

- **Test one of the new accounts:**  
  Try logging into **Client-1** using one of the newly created user accounts.  
  **Note:** Refer to the script to find the default password set for these accounts.

  For example, login: mydomain.com\bep.fug | Password: Password1
  
<br>

<h3>Group Policy and Managing Accounts</h3>
insert text
