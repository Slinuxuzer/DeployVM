# Deploy-VM

The purpose of this project is to simplify the deployment of virtual machines on a vSphere platform by leveraging VMware vSphere customization SPECs, vSphere templates and a user supplied .csv file to modify the various input parameters as needed for each VM deployment.

# Technical Requirements

1. **vSphere 7.0u2**
2. **VMware PowerCLI 12.2.0 build 17538434**
3. **PowerShell Core Version 7.1.4**

# Script Assumptions

1. **vCenter customization SPEC exists for cloning**
2. **vCenter Template exists for cloning**
3. **vSphere Cluster Exists for deployment(s)**

# Script Logic Overview

 
1.	Import user supplied .CSV file into memory
2.	Connect to a vCenter server
3.	Clone a customization SPEC from vCenter into memory
4.	Modify cloned customization SPEC in memory with user inputs
5.	Deploy a VM from user designated template with the settings saved in the cloned customization SPEC
6.	Repeats the process for each line in the CSV
7.	Cleans up and exits
    - The .CSV varies slightly for Windows Vs. Linux
    - The Windows version of the .CSV uses four DNS fields  
    - (IPv4Dns, IPv4Dns2, IPv6Dns1, IPv6Dns2)
    - The Linux version of the .CSV uses three DNS fields
    - (Dns1, Dns2, Dns3)

The script will make a determination for each VM IPv4 and IPv6 settings.
-   If IPv4DHCP is set to 1 then IPv4Address, IPv4SubnetMask and IPv4DefaultGateway should be unset (When setting static IPv4 then IPv4DHCP should be set to 0)
-   IPv4DHCP CAN be set to 1 and IPv4Dns1, IPv4Dns2 can be unset or set to static address'
-   If IPv6DHCP is set to 1 then IPv6Address, IPv6SubnetMask and IPv6DefaultGateway should be unset (When setting static IPv6 then IPv6DHCP should be set to 0)
-   IPv6DHCP CAN be set to 1 and IPv6Dns1, IPv6Dns2 can be unset or set to static address'


# Scripts in collection

This project has two scripts included one for Windows and one for Linux deployments. It was necessary to have two different scripts due to several key differences in the way VMware vSphere formats customization SPECs for Windows and Linux

-   `Deploy-LinuxVM.ps1`
-   `Deploy-WindowsVM.ps1`

# Customization Overview

With the combination of VM Templates, Customization SPECs and user input this project allows a variety of parameters to be modified at the time of VM deployment. The following three sections (Customization SPEC, Template, and User Supplied CSV) each have a list of bullets that detail what items should be set in each specified construct. 

# Customization SPEC

Configuration Items that should be controlled through the persistent customization SPEC stored in vCenter
 
  - Computer Name (Typically set to match the vSphere VM Name)
  - Windows License key (Windows Customization SPEC)
  - Administrator Password
  - Time Zone
  - Commands to Run once at time of deployment
  - Windows Workgroup / Domain Settings

# Template

Configuration Items that should be controlled through the VM template stored in vCenter / vSphere datastore

  - vCPU / vMemory settings
  - VMDK / Virtual Hard Drive
  - VM hardware settings

# User Supplied CSV
  -   Hosting vCenter Server
  -   Hosting vSphere Cluster
  -   vSphere network port-group
  -   vSphere datastore
  -   VM Template
  -   Customization SPEC
  -   Network Settings
      -   IPv4 DHCP       (Accepts 0 OR 1)
      -   IPv4 Address   
      -   IPv4 Subnet Mask
      -   IPv4 Gateway
      -   IPv4 DNS 1
      -   IPv4 DNS 2
      -   IPv6 DHCP       (Accepts 0 or 1)
      -   IPv6 DNS1
      -   IPv6 DNS2

# Script Execution

Each script takes two input parameters:

* `-VcenterServer` - This designates the vCenter server to connect to and takes a standard text string that works best when using the vCenter server fully qualified domain name.
* `-CSV` - This designates the csv input file that the script uses to supply user input for deployments.