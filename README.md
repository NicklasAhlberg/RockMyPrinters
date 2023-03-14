# RockMyPrinters
No admin ever: Working with printers is the best thing in the world! 

Working with printers is just something we need to deal with and moving from Hybrid Azure AD join to Azure AD join usually include a hard time for admins, in-regards to printers. #PrinterNightmare should still be considered and moving away from GPOs is key in a modern management approach.

Rock my Printers: Why

Rock my Printers will save you a lot of time when it comes to moving away from those pesky GPO's to a more modern approach. Use Intune to install both drivers and printers, remember that we can still use a print server for the queues.

High level view of what is going to happen:

Export related printer drivers from the server.

Create an installation package per printer, to be installed from Intune.

Automatically upload the installation package to Intune (Win32 app).

Logo, install/uninstallation commands, detection rule will be handled by the tool, so no need to spend time on repetitive activities.

We will now be able to install the driver and map the printer in one go.

Prerequisites

We will use @NickolajA PowerShell module called: "IntuneWin32Apps" to upload the package to Intune. Check it out over at Github: GitHub - MSEndpointMgr/IntuneWin32App: Provides a set of functions to manage all aspects of Win32 apps in Microsoft Endpoint Manager (Intune).

Open: PowerShell as an administrator

Run: Install-Module -Name IntuneWin32App -Confirm:$false -Force:$true

Make sure you are able to manually run PowerShell commands against the printer server if you want to run the tool on a remote device. Example: Get-Printer -ComputerName %PrinterServer%

You may need to change your PowerShell execution policy to allow the tool to run. 
Read more: about Execution Policies - PowerShell | Microsoft Learn

Rock my Printers: Contents

Rock my Printers use PowerShell App Deployment Toolkit (PSADT) for printer installation. We will configure a template which the tool will use to create each unique printer installation package. 

After you have downloaded the tool you will find:

RockMyPrinters.exe (this is the executable to run the tool)

Tools (this folder contains exported drivers, logos logfile etc...)

The installation package will install the printer drivers in system context and run the printer mapping in user context. PSADT has this cool function called Execute-ProcessAsUser, that will create a temporary scheduled task which in-turn will be run as the currently logged on user. 
⭐This is just one of the excellent stuff we get access to by using PSADT.

Rock my Printers: Preparation

We will start off by downloading the tool from: %URL%

Extract the zip file and you will find a Tools folder and RockMyPrinters.exe

Navigate to: Tools -> _Template 

Open: %Deploy-Application.ps1% with your favorite editor, I am using VSCode.

Scroll down to line 25 and add your Organizational name. In this demo I will use RockEnrollTech. The appVendor variable will be used for detection after the installation package has run.

Optional: Scroll down to line: 108 to check the Execute-ProcessAsUser function in action.

Rock my Printers: Supported manufacturers

All manufacturers are supported by the tool. Below logos have been uploaded and will be used as logo by the Intune Win32 app. All other manufacturers will have a generic logo (see below).
❓Want to add more manufacturers to the list? Tweet at me and I will make it happen!

Logos

Rock my Printers: Run the tool

Now that we have made sure that all prerequisites are fulfilled and taken the preparational steps, it is time to run the tool 🤩

Run: RockMyPrinters.exe either directly on the print server or from a remote device. 
It is recommended to run it from a remote device, such as Windows 11, as we usually do not want to add "unnecessary" PowerShell modules to our servers.

Click: Settings -> Add tenant ID or domain

Enter either your tenant id or domain name such as YourDomainName.com

Save and close the .txt file

Click: Settings -> Connect to tenant

Login with an account with sufficient Intune privileges
Note: The sign in prompt will only show if you are not already signed in/have a token.

Add your printer hostname to the textbox

Click: Settings

Click: Get printers

Select printers based on your need and 

Click the Rock My Printers logo to have them exported to Intune. 
Note: This will not impact the server itself. 
This will take some time depending on a lot of parameters such as file copy speed between client and server, amount of selected printers and internet speed to upload the content to Intune. Count with about 2-3 minutes per printer, this is still much fast than doing it manually, right? 

❗Time-out after 15 minutes. Please note that the token will live for 15 minutes and you will need to re-authenticate. As demonstrated below, drivers will be exported and packages created but not uploaded to Intune, if we do not have a valid token.
💡Choose not to connect to the tenant when you do not want to upload the content to Intune.

Rock my Printers: Install from Intune (company portal)

✅Now that we have uploaded our printers to Intune all we need to do is create assignments. 
I usually add the printers as available and let the users decide which printers to install. It is fully supported to create required assignments as well, this is totally up to each organization to decide upon.

User experience using available assignments.

Rock my Printers: Detection

We are using the registry to detect the printer installation.
💡Update the _template version variable (found at line 27) to create new package versions.

Rock my Printers: Theme

🤩I have heard your feedback on my sticky colors and have added the possibility to change theme, should you want to.
