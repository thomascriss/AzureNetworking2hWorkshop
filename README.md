2-hour Azure Networking Workshop

Goals:

Create a hub-and-spoke network. Create VMs in each network and test connectivity.

Prerequisites:

Azure Subscription with at contributer access or higher.


Create Virtual Networks

1. Navigate to the Azure portal and sign in
2. Click on your name in the top-right and ensure you are working in the desired directory
3. In the search bar at the top, search "Virtual Network" and select it
4. Click the 'Add' button
5. Select your desired Subscription. For Resource group, click "Create new" and name it "Network-Workshop-RG"
6. For Instance details, enter Name: Hub-vNet; Region: East US 2
7. Click the IP Addresses tab. Click the garbage can to delete the existing address space of 10.0.0.0/16
8. Enter a new IPv4 Address Space: 10.101.0.0/16
9. Click on + Add subnet and enter the Subnet Name of subnet1 and a subnet address range of 10.101.1.0/24 and then click Add. 
10. Click on Review + Create, and after validation, click Create.

Repeat steps 3-10 but use the existing Resource Group and change the following values:
Virtual Network Name: Spoke-vNet
IPv4 Address Space: 10.102.0.0/16
Subnet address range: 10.102.2.0/24


Create Azure Virtual Machines via the Portal

1. In the search bar at the top, search "Virtual Machine" and select it
2. Click the 'Add' button
3. Select your desired Subscription. For Resource group, select "Network-Workshop-RG"
4. Give your Virtual machine a name such as "Hub-VM"
5. No infrastructure redundancy is required for this lab
6. For Image, select "Windows Server 2016 Datacenter"
7. For Azure Spot instance, select "No"
8. For Size, I recommend at least a D2s_v3 (2 vcpus or higher)
9. Provide a Username and Password. You will need this later.
10. For Public inbound ports, select "Allow selected ports". Select inbound ports: RDP (3389).
11. Click the Networking tab. For Virtual Network, select Hub-VNet
12. Leave the rest of the values default
13. Click on the Management tab. For Auto-shutdown, select on and choose an appropriate time in your time zone for the server to shut off. (This will save you money if you forget to turn off or remove the VM after the workshop)
14. Leave all other settings on default
15. Click the Review + create tab, and click create

Repeat steps 1-15 but change the following values:
Virtual Machine Name: Spoke-VM
Virtual Network Name: Spoke-vNet
Public inbound ports: None


Visualize Your Network Topology

1. In the search bar of the Azure Portal enter Network Watcher.
2. Under Monitoring choose Topology.
3. Under Resource Group select Network-Workshop-RG.

You should see both virtual networks and virtual machines. Notice there is no link between the Hub and Spoke networks.


Test VM Connectivity

1. In the search bar at the top, search and select Virtual Machines. Click on Hub-VM
2. Copy the private and public IP addresses of VM1 and then click on Connect and then RDP.
3. Click on Download RDP file.
4. If prompted, select Connect. Enter the user name and password you specified when creating the VM. You may need to select More choices, then Use a different account, to specify the credentials you entered when you created the VM.
5. Select OK.
6. Click No on the Networks blade.
7. From PowerShell, enter ping Spoke-VM. The ping should fail because the virtual network is isolated from other virtual networks by default. Also, there is no DNS setup. Lastly, ICMP is not allowed to pass through the Windows firewall by default.
8. To allow Hub-VM to ping other VMs in a later step, enter the following command from PowerShell which allows ICMP inbound through the Windows firewall: New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4

Repeat these steps (connect to the VM and issue the PowerShell command) for Spoke-VM.


Connect virtual networks with virtual network peering using the Azure portal

1. In the Search box at the top of the Azure portal, begin typing Hub-vNet. When vNet1 appears in the search results, select it.
2. Select Settings, Peerings, and then click + Add.
3. Enter the following information:
        Name: PeerHubtoSpoke
        Virtual network: Spoke-vNet
        Name of the peering from vNet2 to vNet1: PeerSpoketoHub
        Configure virtual network access settings: Enabled
        Configure forwarded traffic settings: Disabled
        Select OK


Test VM connectivity again

1. Obtain the public and private IP address of Spoke-VM.
2. Open your RDP session to Hub-VM.
3. From PowerShell ping Spoke-VM by Public IP address. The ping should fail because the IP address is public and must be reached from outside of Azure, and by default Network Security Groups block all incoming traffic.
4. From PowerShell ping Spoke-VM by Private IP address. The ping should succeed because the IP address is public and traffic will stay inside the Azure network and utilize the peering we configured in previous steps.


Visualize Your Network Topology

1. In the search bar of the Azure Portal enter Network Watcher.
2. Under Monitoring choose Topology.
3. Under Resource Group select Network-Workshop-RG.

You should see both virtual networks and virtual machines. Notice there now a link between the Hub and Spoke networks.
