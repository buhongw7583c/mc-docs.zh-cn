---
title: include 文件
description: include 文件
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 11/06/2019
ms.date: 12/16/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 53049aa4c7cdb9f4bf37d3f53d07f78397c130b5
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348474"
---
## <a name="sign-in-to-azure"></a>登录 Azure 

通过 https://portal.azure.cn 登录到 Azure 门户。

> [!NOTE]
> 如果在预览期已注册使用共享映像库，可能需要重新注册 `Microsoft.Compute` 提供程序。 打开本地 Azure CLI 并键入：`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>创建映像库

映像库是用于启用映像共享的主要资源。 允许用于库名称的字符为大写或小写字母、数字、点和句点。 库名称不能包含短划线。  库名称在你的订阅中必须唯一。 

以下示例在“myGalleryRG”资源组中创建名为“myGallery”的库   。

1. 在 Azure 门户的左上角选择“创建资源”。 
1. 在搜索框中使用“共享映像库”类型，在结果中选择“共享映像库”。  
1. 在“共享映像库”页中单击“创建”。  
1. 选择正确的订阅。
1. 在“资源组”中选择“新建”，键入 *myGalleryRG* 作为名称。  
1. 在“名称”中，键入 *myGallery* 作为库名称。 
1. 对“区域”保留默认值。 
1. 可以键入库的简短说明，例如“我的映像库，用于测试”。  然后单击“查看 + 创建”。 
1. 通过验证后，选择“创建”。 
1. 部署完成后，选择“转到资源”。 

## <a name="create-an-image-definition"></a>创建映像定义 

映像定义为映像创建一个逻辑分组。 它们用于管理有关映像版本的信息，这些版本是在其中创建的。 映像定义名称可能包含大写或小写字母、数字、点、短划线和句点。 若要详细了解可以为映像定义指定的值，请参阅[映像定义](/virtual-machines/windows/shared-image-galleries#image-definitions)。

在库中创建库映像定义。 在本示例中，库映像名为 *myImageDefinition*。

1. 在新映像库的页面顶部，选择“添加新的映像定义”。  
1. 对于“映像定义名称”，请键入 *myImageDefinition*。 
1. 对于“操作系统”，请根据源 VM 选择正确的选项。 
1. 对于“VM 代系”，请根据源 VM 选择适当的选项。  大多数情况下都会选择“第 1 代”。  有关详细信息，请参阅[对第 2 代 VM 的支持](/virtual-machines/windows/generation-2)。
1. 对于“操作系统状态”，请根据源 VM 选择适当的选项。  有关详细信息，请参阅[通用化和专用化](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images)。
1. 对于“发布者”，请键入 *myPublisher*。  
1. 对于“套餐”，请键入 *myOffer*。 
1. 对于“SKU”，请键入 *mySKU*。 
1. 完成后，选择“查看 + 创建”。 
1. 映像定义通过验证后，请选择“创建”。 
1. 部署完成后，选择“转到资源”。 

## <a name="create-an-image-version"></a>创建映像版本

从托管映像创建映像版本。 在此示例中，映像版本为 1.0.0，该版本被复制到中国北部和中国东部数据中心    。 选择复制的目标区域时，请记住，你还需包括源区域作为复制的目标。 

允许用于映像版本的字符为数字和句点。 数字必须在 32 位整数范围内。 格式：*MajorVersion*.*MinorVersion*.*Patch*。

创建映像版本的步骤略有不同，具体取决于源是通用化的映像，还是专用化 VM 的快照。 

### <a name="option-generalized"></a>选项：通用

1. 在映像定义的页面顶部，选择“添加版本”。 
1. 在“区域”中，选择存储托管映像的区域。  需要在映像版本基于的托管映像所在的同一区域中创建映像版本。
1. 对于“名称”，请键入 *1.0.0*。  映像版本名称应遵循“主要版本.次要版本.修补版本”格式（使用整数）。    
1. 在“源映像”中，从下拉列表中选择源托管映像。 
1. 在“从最新版本中排除”中，保留默认设置“否”。  
1. 对于“生命周期终结日期”，请从日历中选择处于未来几个月的某个日期。 
1. 在“复制”中，将“默认副本计数”保留为 1。   需要复制到源区域，因此，请将第一个副本保留为默认副本，然后选择另一个副本区域“中国东部”。 
1. 完成操作后，选择“查看 + 创建”  。 Azure 将验证配置。
1. 映像版本通过验证后，请选择“创建”。 
1. 部署完成后，选择“转到资源”。 

可能需要花费一段时间才能将映像复制到所有目标区域。

### <a name="option-specialized"></a>选项：专用

1. 在映像定义的页面顶部，选择“添加版本”。 
1. 在“区域”中，选择存储快照的区域。  需要在映像版本基于的源所在的同一区域中创建映像版本。
1. 对于“名称”，请键入 *1.0.0*。  映像版本名称应遵循“主要版本.次要版本.修补版本”格式（使用整数）。    
1. 在“OS 磁盘快照”中，从下拉列表中选择来自源 VM 的快照。  如果源 VM 有一个要包含的数据磁盘，请从下拉列表中选择正确的“LUN”编号，然后为“数据磁盘快照”选择数据磁盘的快照。   
1. 在“从最新版本中排除”中，保留默认设置“否”。  
1. 对于“生命周期终结日期”，请从日历中选择处于未来几个月的某个日期。 
1. 在“复制”中，将“默认副本计数”保留为 1。   需要复制到源区域，因此，请将第一个副本保留为默认副本，然后选择另一个副本区域“中国东部”。 
1. 完成操作后，选择“查看 + 创建”  。 Azure 将验证配置。
1. 映像版本通过验证后，请选择“创建”。 
1. 部署完成后，选择“转到资源”。 

## <a name="share-the-gallery"></a>共享库

我们建议你在映像库级别共享访问权限。 下面逐步说明如何共享刚刚创建的库。

1. 打开 [Azure 门户](https://portal.azure.cn)。
1. 在左侧菜单中，选择“资源组”。  
1. 在资源组列表中，选择“myGalleryRG”。  此时会打开资源组的边栏选项卡。
1. 在“myGalleryRG”页左侧的菜单中，选择“访问控制(IAM)”。   
1. 在“添加角色分配”下，选择“添加”。   此时会打开“添加角色分配”窗格。  
1. 在“角色”下，选择“读取者”。  
1. 在“将访问权限分配给”下，保留默认设置“Azure AD 用户、组或服务主体”。  
1. 在“选择”下，键入要邀请的人员的电子邮件地址。 
1. 如果该用户不在你的组织中，将会显示消息“将向此用户发送一封电子邮件，使其能够与 Microsoft 协作”。  选择具有该电子邮件地址的用户，然后单击“保存”。 

如果该用户不在你的组织中，他（她）会收到一封邀请加入组织的电子邮件。 该用户需要接受邀请，才能在其资源列表中看到库以及所有映像定义和版本。

<!-- Update_Description: new article about virtual machines common shared images portal -->
<!--NEW.date: 12/16/2019-->