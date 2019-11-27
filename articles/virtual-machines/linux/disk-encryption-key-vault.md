---
title: 创建和配置用于 Azure 磁盘加密的 Key Vault
description: 本文介绍如何创建和配置用于 Azure 磁盘加密的 Key Vault
ms.service: virtual-machines
ms.topic: article
author: rockboyfor
ms.author: v-yeche
origin.date: 08/06/2019
ms.date: 11/11/2019
ms.custom: seodec18
ms.openlocfilehash: 551d8ac59294a5dc99e10114c3c050c12085e8e0
ms.sourcegitcommit: a89eb0007edd5b4558b98c1748b2bd67ca22f4c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73730641"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>创建和配置用于 Azure 磁盘加密的 Key Vault

Azure 磁盘加密使用 Azure Key Vault 来控制和管理磁盘加密密钥和机密。  有关 Key Vault 的详细信息，请参阅 [Azure Key Vault 入门](../../key-vault/key-vault-get-started.md)和[保护 Key Vault](../../key-vault/key-vault-secure-your-key-vault.md)。 

> [!WARNING]
> - 如果之前是使用 Azure 磁盘加密与 Azure AD 来加密 VM，则必须继续使用此选项来加密 VM。 有关详细信息，请参阅[使用 Azure AD 创建和配置用于 Azure 磁盘加密的 Key Vault（以前版本）](disk-encryption-key-vault-aad.md)。

创建和配置用于 Azure 磁盘加密的 Key Vault 需要三个步骤：

1. 创建资源组（如果需要）。
2. 创建 Key Vault。 
3. 设置 Key Vault 高级访问策略。

以下快速入门说明了这些步骤：

- [使用 Azure CLI 创建和加密 Linux VM](disk-encryption-cli-quickstart.md)
- [使用 Azure Powershell 创建和加密 Linux VM](disk-encryption-cli-quickstart.md)

还可以根据需要生成或导入密钥加密密钥 (KEK)。

> [!Note]
> 本文中的步骤在 [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)和 [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)中自动执行。

## <a name="install-tools-and-connect-to-azure"></a>安装工具并连接到 Azure

可以使用 [Azure CLI](https://docs.azure.cn/cli/?view=azure-cli-latest)、[Azure PowerShell Az 模块](https://docs.microsoft.com/powershell/azure/overview)或 [Azure 门户](https://portal.azure.cn)来完成本文中的步骤。 

虽然可以通过浏览器访问门户，但 Azure CLI 和 Azure PowerShell 需要本地安装；有关详细信息，请参阅[适用于 Linux 的 Azure 磁盘加密：安装工具](disk-encryption-linux.md#install-tools-and-connect-to-azure)。

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用 Azure CLI 或 Azure PowerShell 之前，必须先连接到 Azure 订阅。 为此，可以[使用 Azure CLI 登录](https://docs.azure.cn/cli/authenticate-azure-cli?view=azure-cli-latest)、[使用 Azure Powershell 登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出现提示时向 Azure 门户提供凭据。

```azurecli
az cloud set -n AzureChinaCloud
az login
```

```powershell
Connect-AzAccount -Environment AzureChinaCloud
```

## <a name="create-a-resource-group"></a>创建资源组

如果已有资源组，可以跳至[创建 Key Vault](#create-a-key-vault)。 

资源组是在其中部署和管理 Azure 资源的逻辑容器。 

使用 [az group create](https://docs.azure.cn/cli/group?view=azure-cli-latest#az-group-create) Azure CLI 命令、[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell 命令或从 [Azure 门户](https://portal.azure.cn)创建资源组。

### <a name="azure-cli"></a>Azure CLI

```azurecli
az group create --name "myResourceGroup" --location chinaeast
```
### <a name="azure-powershell"></a>Azure PowerShell
```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "ChinaEast"
```

## <a name="create-a-key-vault"></a>创建密钥保管库

如果已有 Key Vault，可以跳至[设置 Key Vault 高级访问策略](#set-key-vault-advanced-access-policies)。 

使用 [az keyvault create](https://docs.azure.cn/cli/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI 命令、[New-AzKeyvault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell 命令、[Azure 门户](https://portal.azure.cn)或[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)创建 Key Vault。

>[!WARNING]
> 为确保加密机密不会跨过区域边界，Azure 磁盘加密需要将 Key Vault 和 VM 共置在同一区域中。 在要加密的 VM 所在的同一区域中创建并使用 Key Vault。 

每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI 创建 Key Vault 时，请添加“--enabled-for-disk-encryption”标志。

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "chinaeast" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 Azure PowerShell 创建 Key Vault 时，请添加“-EnabledForDiskEncryption”标志。

```powershell
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "chinaeast" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager 模板

还可以使用[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)创建 Key Vault。

1. 在 Azure 快速入门模板中，单击“部署到 Azure”。 
2. 选择订阅、资源组、资源组位置、Key Vault 名称、对象 ID、法律条款和协议，然后单击“购买”。  

## <a name="set-key-vault-advanced-access-policies"></a>设置 Key Vault 高级访问策略

Azure 平台需要访问 Key Vault 中的加密密钥或机密，才能使这些密钥和机密可供 VM 用来启动和解密卷。 

如果在创建时没有为磁盘加密、部署或模板部署启用 Key Vault（如上一步所示），则必须更新其高级访问策略。  

### <a name="azure-cli"></a>Azure CLI

使用 [az keyvault update](https://docs.azure.cn/cli/keyvault?view=azure-cli-latest#az-keyvault-update) 为 Key Vault 启用磁盘加密。 

 - **为磁盘加密启用 Key Vault：** 需要使用 Enabled-for-disk-encryption。 

     ```azurecli
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **根据需要为部署启用 Key Vault：** 在资源创建操作中引用此 Key Vault（例如，创建虚拟机）时，使 Microsoft.Compute 资源提供程序能够从此 Key Vault 中检索机密。

     ```azurecli
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **根据需要为模板部署启用 Key Vault：** 允许资源管理器从保管库中检索机密。
     ```azurecli  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

### <a name="azure-powershell"></a>Azure PowerShell
 使用 Key Vault PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 为 Key Vault 启用磁盘加密。

  - **为磁盘加密启用 Key Vault：** 若要启用 Azure 磁盘加密，需要使用 EnabledForDiskEncryption。

     ```powershell 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **根据需要为部署启用 Key Vault：** 在资源创建操作中引用此 Key Vault（例如，创建虚拟机）时，使 Microsoft.Compute 资源提供程序能够从此 Key Vault 中检索机密。

     ```powershell
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **根据需要为模板部署启用 Key Vault：** 在模板部署中引用此 Key Vault 时，使 Azure 资源管理器能够从此 Key Vault 中获取机密。

     ```powershell             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure 门户

1. 选择 Key Vault，转到“访问策略”，然后选择“单击此处可显示高级访问策略”。  
2. 选中标有“启用对 Azure 磁盘加密的访问以进行卷加密”的框。 
3. 根据需要选择“启用对 Azure 虚拟机的访问以进行部署”和/或“启用对 Azure 资源管理器的访问以进行模板部署”。   
4. 单击“保存”  。

    ![Azure Key Vault 高级访问策略](./media/disk-encryption/keyvault-portal-fig4.png)

## <a name="set-up-a-key-encryption-key-kek"></a>设置密钥加密密钥 (KEK)

若要使用密钥加密密钥 (KEK) 来为加密密钥提供附加的安全层，请将 KEK 添加到 Key Vault。 指定密钥加密密钥后，Azure 磁盘加密会使用该密钥包装加密机密，然后将机密写入 Key Vault。

可以使用 Azure CLI [az keyvault key create](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 命令、Azure PowerShell [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet 或 [Azure 门户](https://portal.azure.cn/)生成新 KEK。 必须生成 RSA 密钥类型；Azure 磁盘加密尚不支持使用椭圆曲线密钥。

<!--Not Available on You can instead import a KEK from your on-premises key management HSM. For more information, see [Key Vault Documentation](../../key-vault/key-vault-hsm-protected-keys.md)-->

必须对 Key Vault KEK URL 进行版本控制。 Azure 会强制实施这项版本控制限制。 有关有效的机密和 KEK URL，请参阅以下示例：

* 有效机密 URL 的示例： *https://contosovault.vault.azure.cn/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 有效 KEK URL 的示例： *https://contosovault.vault.azure.cn/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure 磁盘加密不支持将端口号指定为 Key Vault 机密和 KEK URL 的一部分。 有关不支持和支持的 Key Vault URL 的示例，请参阅以下示例：

* 可接受的 Key Vault URL： *https://contosovault.vault.azure.cn/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 无法接受的 Key Vault URL： *https://contosovault.vault.azure.cn:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

使用 Azure CLI [az keyvault key create](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 命令生成新 KEK 并将其存储在 Key Vault 中。

```azurecli
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

<!--MOONCAKE: CHANGE RSA-HSM to RSA-->

可以改用 Azure CLI [az keyvault key import](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 命令导入私钥：

在这两种情况下，都会向 Azure CLI [az vm encryption enable](https://docs.azure.cn/cli/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key 参数提供 KEK 的名称。 

```azurecli
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

### <a name="azure-powershell"></a>Azure PowerShell 

使用 Azure PowerShell [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) cmdlet 生成新 KEK 并将其存储在 Key Vault 中。

 ```powershell
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

可以改用 Azure PowerShell [az keyvault key import](https://docs.azure.cn/cli/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 命令导入私钥。

在这两种情况下，都会向 Azure PowerShell [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId 和 -KeyEncryptionKeyUrl 参数提供 KEK Key Vault 的 ID 和 KEK 的 URL。 请注意，此示例假定使用同一 Key Vault 保存磁盘加密密钥和 KEK。

 ```powershell
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```

## <a name="next-steps"></a>后续步骤

- [Azure 磁盘加密先决条件 CLI 脚本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁盘加密先决条件 PowerShell 脚本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- 了解 [Linux VM 上的 Azure 磁盘加密方案](disk-encryption-linux.md)
- 了解如何[对 Azure 磁盘加密进行故障排除](disk-encryption-troubleshooting.md)
- 阅读 [Azure 磁盘加密示例脚本](disk-encryption-sample-scripts.md)

<!--Update_Description: new articles on disk encryption key vault -->
<!--New.date: 11/04/2019-->