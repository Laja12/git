Below is an example Terraform configuration file that creates the following Azure resources:

- Virtual Network (VNet)
- Subnet
- Virtual Machine (VM)
- Network Security Group (NSG)
- Load Balancer
- SQL Database

This example assumes you have already configured the required Azure provider and authentication.

### Terraform Configuration

hcl
provider "azurerm" {
  features {}
}

# Resource Group
resource "azurerm_resource_group" "rg" {
  name     = "example-resource-group"
  location = "East US"
}

# Virtual Network
resource "azurerm_virtual_network" "vnet" {
  name                = "example-vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

# Subnet
resource "azurerm_subnet" "subnet" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

# Network Security Group (NSG)
resource "azurerm_network_security_group" "nsg" {
  name                = "example-nsg"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

# NSG Rule to allow SSH
resource "azurerm_network_security_rule" "allow_ssh" {
  name                        = "allow-ssh"
  priority                    = 100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  network_security_group_name = azurerm_network_security_group.nsg.name
}

# Virtual Machine
resource "azurerm_linux_virtual_machine" "vm" {
  name                   = "example-vm"
  resource_group_name    = azurerm_resource_group.rg.name
  location               = azurerm_resource_group.rg.location
  size                   = "Standard_B1s"
  admin_username         = "adminuser"
  admin_password         = "P@ssword1234!"  # Use a secure method to manage passwords
  network_interface_ids  = [azurerm_network_interface.vm_nic.id]
  availability_set_id    = azurerm_availability_set.example.id

  os_disk {
    name              = "example-vm-os-disk"
    caching           = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
}

# Network Interface
resource "azurerm_network_interface" "vm_nic" {
  name                = "example-vm-nic"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
  }

  network_security_group_id = azurerm_network_security_group.nsg.id
}

# Load Balancer
resource "azurerm_lb" "example" {
  name                = "example-lb"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

# Load Balancer Backend Pool
resource "azurerm_lb_backend_address_pool" "example" {
  name                = "example-backend-pool"
  resource_group_name = azurerm_resource_group.rg.name
  loadbalancer_id     = azurerm_lb.example.id
}

# Load Balancer Health Probe
resource "azurerm_lb_probe" "example" {
  name                = "example-probe"
  resource_group_name = azurerm_resource_group.rg.name
  loadbalancer_id     = azurerm_lb.example.id
  protocol            = "Tcp"
  port                = 80
}

# Load Balancer Load Balancing Rule
resource "azurerm_lb_rule" "example" {
  name                           = "example-lb-rule"
  resource_group_name            = azurerm_resource_group.rg.name
  loadbalancer_id                = azurerm_lb.example.id
  frontend_ip_configuration_name = "example-frontend-ip"
  backend_address_pool_id       = azurerm_lb_backend_address_pool.example.id
  probe_id                       = azurerm_lb_probe.example.id
  protocol                       = "Tcp"
  frontend_port                  = 80
  backend_port                   = 80
}

# SQL Server
resource "azurerm_sql_server" "example" {
  name                         = "example-sql-server"
  resource_group_name          = azurerm_resource_group.rg.name
  location                     = azurerm_resource_group.rg.location
  version                      = "12.0"
  administrator_login          = "sqladmin"
  administrator_login_password = "P@ssword1234!"  # Use a secure method to manage passwords
}

# SQL Database
resource "azurerm_sql_database" "example" {
  name                = "example-sql-db"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  server_name         = azurerm_sql_server.example.name
  sku_name            = "S1"
}

output "vm_public_ip" {
  value = azurerm_network_interface.vm_nic.private_ip_address
}


### Key Points:
- *Resource Group*: A resource group is created for organizing the resources.
- *VNet & Subnet*: A virtual network and subnet are defined with an address space of 10.0.0.0/16 and a subnet of 10.0.1.0/24.
- *NSG & NSG Rules*: A network security group (NSG) is created with a rule to allow SSH (port 22) access.
- *VM*: A basic Linux virtual machine is created, connected to the subnet and protected by the NSG.
- *Load Balancer*: A load balancer is created with a backend pool and health probe.
- *SQL Database*: A SQL Server instance and a database are set up, along with basic authentication.

### Notes:
1. *Password Management*: Ensure that sensitive values such as admin passwords are managed securely, for example using Azure Key Vault or environment variables.
2. *Azure Regions*: The region East US is used, but you should modify it as per your requirements.
3. *SQL Database SKU*: The database SKU (S1) is chosen for simplicity, but this can be changed to suit your needs.
4. *SSH Key*: You might want to use SSH keys instead of password-based authentication for the VM for better security.

### Next Steps:
1. Save this file with a .tf extension.
2. Run terraform init to initialize the Terraform working directory.
3. Run terraform plan to review the execution plan.
4. Run terraform apply to create the resources.

To bind the *Network Security Group (NSG)* to the *Subnet*, you need to use the azurerm_subnet_network_security_group_association resource in Terraform. This resource associates an NSG with a subnet.

Here's how you can modify the existing Terraform configuration to associate the NSG with the subnet:

### Modified Terraform Configuration

hcl
# Network Security Group (NSG)
resource "azurerm_network_security_group" "nsg" {
  name                = "example-nsg"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

# NSG Rule to allow SSH
resource "azurerm_network_security_rule" "allow_ssh" {
  name                        = "allow-ssh"
  priority                    = 100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  network_security_group_name = azurerm_network_security_group.nsg.name
}

# Subnet
resource "azurerm_subnet" "subnet" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

# NSG association with the subnet
resource "azurerm_subnet_network_security_group_association" "subnet_nsg_association" {
  subnet_id                 = azurerm_subnet.subnet.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}


### Explanation:

1. *azurerm_subnet_network_security_group_association*: This resource binds the NSG to the subnet. The subnet_id refers to the subnet that you want to associate the NSG with, and the network_security_group_id refers to the NSG to bind.
   
   In this case, the subnet created in the azurerm_subnet.subnet resource is being associated with the azurerm_network_security_group.nsg NSG.

### Steps:
1. Ensure that you add this azurerm_subnet_network_security_group_association resource after defining both the NSG and the subnet.
2. After updating your .tf file, run terraform apply to apply the changes.

### Result:
After the Terraform plan is applied, the *Network Security Group (NSG)* will be effectively *bound* to the *subnet*, and any traffic through the subnet will be filtered according to the rules specified in the NSG.
