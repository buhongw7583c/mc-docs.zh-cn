---
title: Azure PowerShell 脚本示例 - WordPress | Azure
description: Azure PowerShell 脚本示例 - WordPress
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
origin.date: 03/01/2017
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 1bc8227bbdb9fe5929d8bc8abdc3674b88e471ff
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675459"
---
# <a name="create-a-wordpress-vm-with-powershell"></a>使用 PowerShell 创建 WordPress VM

此脚本创建一个虚拟机，并使用 Azure 虚拟机自定义脚本扩展安装 WordPress。 运行脚本后，可在 `http://<public IP of VM>/wordpress` 访问 WordPress 配置站点。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup3"
$location = "chinanorth"
$vmName = "myVM"

# Definer user name and blank password
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleHTTP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleHTTP  -Protocol Tcp `
  -Direction Inbound -Priority 2000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleHTTP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmname -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 14.04.2-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Start a CustomScript extension to use a simple bash script to update, download and install WordPress and MySQL 
$PublicSettings = '{"fileUris":["https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/install_wordpress.sh"],"commandToExecute":"sh install_wordpress.sh"}'

Set-AzureRmVMExtension -ExtensionName "WordPress" -ResourceGroupName $resourceGroup -VMName $vmName `
  -Publisher "Microsoft.Azure.Extensions" -ExtensionType "CustomScript" -TypeHandlerVersion 2.0 `
  -SettingString $PublicSettings `
  -Location $location
```

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 创建子网配置。 在虚拟网络创建过程中会使用此配置。 |
| [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 创建虚拟网络。 |
| [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) | 创建公共 IP 地址。 |
| [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | 创建网络安全组规则配置。 创建 NSG 时会使用此配置创建 NSG 规则。 |
| [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | 创建网络安全组。 |
| [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | 获取子网信息。 创建网络接口时会使用此信息。 |
| [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) | 创建网络接口。 |
| [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) | 创建 VM 配置。 此配置包括 VM 名称、操作系统和管理凭据等信息。 在创建 VM 期间使用此配置。 |
| [New-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm) | 创建虚拟机。 |
| [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) | 将自定义脚本扩展添加到虚拟机，此扩展将调用脚本来安装 WordPress。 |
|[Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure Linux VM 文档](../linux/powershell-samples.md?toc=%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。

<!--Update_Description: update meta properties -->