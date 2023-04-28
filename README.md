# Standard-Architecture-for-3-Tier-App-in-Azure


I have decided to create an architectural design for a 3-Tier App on the Azure platform. You might have better ideas about this architecture.

The goal is to develop a highly available, secure, scalable, and cost-optimized web application running on the cloud.

Below is the detailed design explanation. Please review it.

## Steps:

#### Establish Network Topology:
- Instead of having all our resources on one virtual network, I have chosen to use the Hub & Spoke Topology, also known as the Star topology. I will explain the reasons soon.
- I have created several virtual networks and separated them into the Development and Production environments.
- Additionally, I have created another Vnet, called Management environment, for shared services.
- Vnet peering has been established to allow communication between the Development and Production environments.


#### Reasons for using Hub and Spoke topology:
- Network Isolation: This topology allows us to establish isolation between the different tiers. For example, resources in the Development environment cannot communicate with those in the Production environment. This prevents unintended changes and issues in the Production environment.
- Separation of Concerns: By making changes in the Development environment first, we can ensure that we do not impact users accessing our production environment. Once the changes or updates are validated in the Development environment, we can then apply them to the Production environment with a good understanding of the outcome.
- Compliance: This topology also takes into consideration various compliances such as HIPAA, HITRUST, and PCI.


#### Subnets and Network Access:
- Create public and private Vnet subnets for each environment.
- Assign network security groups (NSGs) to each subnet. This allows only the incoming ports and protocols required by the application into the subnets.
- Establish connectivity from our on-premises data center to the Azure environment.
- Deploy a VPN Gateway on Azure and a local network gateway on-premises to establish an IPsec tunnel for secure communication.
- Set up an Azure Bastion as the main point of ingress to the Azure environment for the operating system layer. Extend connectivity by allowing a public IP and configuring another NSG on the NIC. Also, allow port 22 in the NSG, specifying the source IP of the Engineer/Admin, and add any additional subnets from the on-premises data center, if applicable.


#### Deployment:
- Deploy a set of Active Directory domain controllers to be used as an identity source. This will allow us to create user accounts for various engineers/admins and restrict access as necessary to various OS.
- Deploy multiple domain controllers to ensure high availability.
- Deploy Application Gateways, which are layer 7 load balancers that respond to HTTP/HTTPS requests.
- Whitelist the Application Subnet in the database to allow communication between the application instances and the database.
- Deploy a Storage Account to configure inspector and boot diagnostics on VMs across all environments. This allows us to view various logs sent to the storage account, troubleshoot issues with VMs, and generate reports.

Now, we need to secure, manage, and support our environment.

- Deploy Azure Key Vault to securely store secrets and certificates, avoiding the exposure of sensitive information in source code.
- For monitoring, deploy a Log Analytics Workspace and configure diagnostic settings for each of our Azure resources, including VMs and load balancers. Set these resources to send reports to the Log Analytics. Create metrics and log alerts for notification.
- Create an Automation Account and link it to the analytics workspace. Use the update management feature to automatically onboard all VMs for automated patching. Set up schedules to ensure that VMs are updated automatically.
- Deploy a Recovery Vault to enable backup for VMs. During the onboarding process, establish a backup policy to define how often backups should.



