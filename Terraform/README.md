Below is an example of a Terraform configuration file that creates a Virtual Network (VNet), Subnet, Virtual Machine (VM), Network Security Group (NSG), Load Balancer, and SQL Database in Azure.

```hcl
provider "azurerm" {
  features {}
}

# Define variables
variable "resource_group_name" {
  type    = string
  default = "example-rg"
}

variable "location" {
  type    = string
  default = "East US"
}

# Resource Group
resource "azurerm_resource_group" "example" {
  name     = var.resource_group_name
  location = var.location
}

# Virtual Network
resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
}

# Subnet
resource "azurerm_subnet" "example" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"]
}

# Network Security Group
resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  security_rule {
    name                       = "Allow-SSH"
    priority                  = 100
    direction                 = "Inbound"
    access                    = "Allow"
    protocol                  = "Tcp"
    source_port_range         = "*"
    destination_port_range    = "22"
    source_address_prefix     = "*"
    destination_address_prefix = "*"
  }

  security_rule {
    name                       = "Allow-HTTP"
    priority                  = 200
    direction                 = "Inbound"
    access                    = "Allow"
    protocol                  = "Tcp"
    source_port_range         = "*"
    destination_port_range    = "80"
    source_address_prefix     = "*"
    destination_address_prefix = "*"
  }
}

# Virtual Machine
resource "azurerm_linux_virtual_machine" "example" {
  name                  = "example-vm"
  resource_group_name   = azurerm_resource_group.example.name
  location              = azurerm_resource_group.example.location
  size                  = "Standard_DS1_v2"
  admin_username        = "adminuser"
  admin_password        = "P@ssword123!"
  network_interface_ids = [azurerm_network_interface.example.id]
  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
  custom_data = <<-EOT
                #!/bin/bash
                echo "Hello, World!" > /home/adminuser/hello.txt
                EOT
}

# Network Interface
resource "azurerm_network_interface" "example" {
  name                      = "example-nic"
  location                  = azurerm_resource_group.example.location
  resource_group_name       = azurerm_resource_group.example.name
  subnet_id                 = azurerm_subnet.example.id
  network_security_group_id = azurerm_network_security_group.example.id

  ip_configuration {
    name                          = "example-ip-config"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
  }
}

# Load Balancer
resource "azurerm_lb" "example" {
  name                = "example-lb"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

resource "azurerm_lb_backend_address_pool" "example" {
  name                = "example-backend-pool"
  resource_group_name = azurerm_resource_group.example.name
  loadbalancer_id     = azurerm_lb.example.id
}

resource "azurerm_lb_probe" "example" {
  name                = "example-probe"
  resource_group_name = azurerm_resource_group.example.name
  loadbalancer_id     = azurerm_lb.example.id
  protocol            = "Tcp"
  port                = 80
}

resource "azurerm_lb_rule" "example" {
  name                           = "example-lb-rule"
  resource_group_name            = azurerm_resource_group.example.name
  loadbalancer_id                = azurerm_lb.example.id
  protocol                       = "Tcp"
  frontend_port                  = 80
  backend_port                   = 80
  frontend_ip_configuration_name = "example-frontend-ip"
  backend_address_pool_id       = azurerm_lb_backend_address_pool.example.id
  probe_id                       = azurerm_lb_probe.example.id
}

# SQL Database Server
resource "azurerm_sql_server" "example" {
  name                         = "examplesqlserver"
  resource_group_name          = azurerm_resource_group.example.name
  location                     = azurerm_resource_group.example.location
  version                      = "12.0"
  administrator_login          = "sqladmin"
  administrator_login_password = "P@ssword123!"
}

# SQL Database
resource "azurerm_sql_database" "example" {
  name                = "examplesqldb"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  server_name         = azurerm_sql_server.example.name
  sku_name            = "S1"
}

```

### Explanation of the Resources:
- **azurerm_resource_group**: Creates the resource group that contains all other resources.
- **azurerm_virtual_network**: Creates a Virtual Network (VNet) with an address space of `10.0.0.0/16`.
- **azurerm_subnet**: Creates a subnet inside the Virtual Network with an address range of `10.0.1.0/24`.
- **azurerm_network_security_group**: Defines a basic NSG with inbound rules allowing SSH (port 22) and HTTP (port 80) traffic.
- **azurerm_network_interface**: Creates a network interface attached to the subnet and NSG.
- **azurerm_linux_virtual_machine**: Deploys a Linux VM with a simple custom script that writes "Hello, World!" to a text file.
- **azurerm_lb**: Creates a basic Azure Load Balancer.
- **azurerm_lb_backend_address_pool**: Creates a backend pool for the Load Balancer.
- **azurerm_lb_probe**: Creates a health probe for the Load Balancer.
- **azurerm_lb_rule**: Defines a rule for the Load Balancer that maps HTTP traffic on port 80 to the backend pool.
- **azurerm_sql_server**: Creates an Azure SQL Server with administrator login.
- **azurerm_sql_database**: Creates a database on the Azure SQL Server.

### Notes:
1. You should replace default values like `P@ssword123!` with more secure passwords.
2. The `azurerm_linux_virtual_machine` resource specifies a Linux VM, but you can adjust this for a Windows VM by using the `azurerm_windows_virtual_machine` resource instead.
3. This configuration assumes that the load balancer is intended for a basic use case with TCP traffic on port 80. If more advanced configurations are needed, you may need to modify the Load Balancer resources.

This configuration gives you a simple, end-to-end example of deploying infrastructure in Azure with Terraform. Let me know if you need further customization!

To bind the **Network Security Group (NSG)** to a **Subnet**, you need to use the `azurerm_subnet_network_security_group_association` resource in Terraform. This resource associates an NSG with a subnet.

Here’s how you can update your Terraform configuration to associate the **NSG** with the **Subnet**:

### Updated Terraform Configuration:
```hcl
# Network Security Group
resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  security_rule {
    name                       = "Allow-SSH"
    priority                  = 100
    direction                 = "Inbound"
    access                    = "Allow"
    protocol                  = "Tcp"
    source_port_range         = "*"
    destination_port_range    = "22"
    source_address_prefix     = "*"
    destination_address_prefix = "*"
  }

  security_rule {
    name                       = "Allow-HTTP"
    priority                  = 200
    direction                 = "Inbound"
    access                    = "Allow"
    protocol                  = "Tcp"
    source_port_range         = "*"
    destination_port_range    = "80"
    source_address_prefix     = "*"
    destination_address_prefix = "*"
  }
}

# Subnet
resource "azurerm_subnet" "example" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"]
}

# Associate the NSG with the Subnet
resource "azurerm_subnet_network_security_group_association" "example" {
  subnet_id                 = azurerm_subnet.example.id
  network_security_group_id = azurerm_network_security_group.example.id
}
```

### Explanation:
- The **`azurerm_subnet_network_security_group_association`** resource is used to associate the **Network Security Group (NSG)** with the **Subnet**.
  - `subnet_id` is the ID of the **azurerm_subnet** resource.
  - `network_security_group_id` is the ID of the **azurerm_network_security_group** resource.

By using this association, the **Network Security Group (NSG)** is applied to all resources within the subnet, enforcing the inbound/outbound rules defined in the NSG.

This association ensures that the rules you define in the NSG are applied to the subnet, rather than just to individual network interfaces.

Let me know if you need further clarification!
