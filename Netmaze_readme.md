NetMaze Explorer Project - Cloud to On-Premises Network Setup
Project Overview

This project simulates a hybrid network environment by connecting a cloud network (VNet1) with a simulated on-premises network (VNet2) using Azure Virtual Network Gateways and Site-to-Site VPN connections. The setup ensures secure communication between VMs across the two VNets, providing a realistic cloud-to-on-premises networking scenario.
Resources Deployed and Configurations
1. Virtual Networks (VNets) and Subnets

    VNet1 (Cloud Network)
        Address Space: 10.0.0.0/16
        Subnets:
            WebApp Subnet: 10.0.1.0/24
            Database Subnet: 10.0.2.0/24
            Admin Subnet: 10.0.3.0/24

    VNet2 (Simulated On-Premises Network)
        Address Space: 10.1.0.0/16
        Subnets:
            OnPremise Subnet: 10.1.1.0/24

2. Virtual Machines (VMs)

    VMs in VNet1:
        WebAppVM: Deployed in the WebApp Subnet.
        DatabaseVM: Deployed in the Database Subnet (Windows Server 2022).
        AdminVM: Deployed in the Admin Subnet for management purposes.

    VMs in VNet2:
        OnPremiseVM: Deployed in the OnPremise Subnet (Linux VM).

3. Network Security Groups (NSGs)

NSGs were created and associated with each subnet to control traffic flow:

    NSGs for VNet1 Subnets:
        Allow inbound and outbound traffic from VNet2:
            Source: VirtualNetwork or 10.1.0.0/16
            Destination: Subnets within VNet1.
            Protocol: Any (refined after initial testing).
            Action: Allow.

    NSGs for VNet2 Subnets:
        Allow inbound and outbound traffic from VNet1:
            Source: VirtualNetwork or 10.0.0.0/16
            Destination: Subnets within VNet2.
            Protocol: Any.
            Action: Allow.

    Specific ICMP Rules:
        Purpose: To allow ping between VMs in VNet1 and VNet2.
        Configuration: Allow ICMP protocol traffic between relevant VNets and subnets.

4. Virtual Network Gateways

    VNG1 (for VNet1)
        Region: East-US
        Gateway Type: VPN
        VPN Type: Route-based
        SKU: VpnGw1
        Public IP Address: Assigned

    VNG2 (for VNet2)
        Region: Central-US
        Gateway Type: VPN
        VPN Type: Route-based
        SKU: VpnGw1
        Public IP Address: Assigned

5. Local Network Gateways

    VNet1-LocalNetworkGateway (Representing VNet2)
        Public IP: Public IP of VNG2
        Address Space: 10.1.0.0/16

    VNet2-LocalNetworkGateway (Representing VNet1)
        Public IP: Public IP of VNG1
        Address Space: 10.0.0.0/16

6. VPN Connections

    VNet1-to-VNet2 Connection
        Type: Site-to-Site (IPSec)
        From: VNG1 to VNet2-LocalNetworkGateway
        Shared Key: A common key for both sides

    VNet2-to-VNet1 Connection
        Type: Site-to-Site (IPSec)
        From: VNG2 to VNet1-LocalNetworkGateway
        Shared Key: Same key as above

7. Azure Bastion Host

    Purpose: Provides secure RDP/SSH access to VMs without exposing them via public IPs.
    Deployment Steps:
        AzureBastionSubnet:
            Created in VNet1 with a range of 10.0.4.0/27.
        Bastion Deployment:
            Name: BastionHost
            Region: East-US
            Public IP: BastionPublicIP (Standard SKU, Static)
            Associated with AzureBastionSubnet in VNet1.

Testing and Validation

    Ping Tests: Successfully tested ping between VMs in VNet1 and VNet2 to validate connectivity.
    Service Tests: Verified SSH (Linux), RDP (Windows), and other relevant services between VMs across VNets.
    VPN Connection Status: Both connections are active and verified as Connected in the Azure portal.

Key Considerations and Best Practices

    Security: Regularly review and tighten NSG rules to allow only necessary traffic.
    Monitoring: Use Azure Network Watcher for ongoing monitoring of VPN connections and traffic flow.
    Logging: Enable NSG flow logs to track and audit traffic for security compliance.

Troubleshooting

    Firewall Settings: Adjusted Windows Firewall to allow ICMP for pinging.
    Region Alignment: Ensured all VPN configurations aligned with the correct regions for each VNet.
    NSG Adjustments: Modified NSG rules as needed to allow full connectivity between VNet1 and VNet2.
