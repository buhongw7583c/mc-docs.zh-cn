---
title: 用于文件和 ACL 的 Azure Data Lake Storage Gen2 Python SDK（预览版）
description: 在启用了分层命名空间 (HNS) 的存储帐户中使用 Python 来管理目录和文件以及目录访问控制列表 (ACL)。
author: WenJason
ms.service: storage
origin.date: 11/24/2019
ms.date: 02/10/2020
ms.author: v-jay
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 72bcaae196facdd2c1e077edb264a61b693dfc56
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77028521"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 Python 管理 Azure Data Lake Storage Gen2（预览版）中的目录、文件和 ACL

本文介绍如何使用 Python 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录、文件与权限。 

> [!IMPORTANT]
> 用于 Python 的 Azure Data Lake Storage 客户端库当前为公共预览版。

[包（Python 包索引）](https://pypi.org/project/azure-storage-file-datalake/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [提供反馈](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>必备条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 1 元人民币的试用订阅](https://www.azure.cn/zh-cn/pricing/1rmb-trial-full/?form-type=identityauth)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

使用 [pip](https://pypi.org/project/pip/) 安装适用于 Python 的 Azure Data Lake Storage 客户端库。

```
pip install azure-storage-file-datalake --pre
```

将这些 import 语句添加到代码文件的顶部。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。 若要获取一个，最简单的方法是使用帐户密钥。 

此示例使用帐户密钥创建一个 **DataLakeServiceClient** 实例，该实例表示存储帐户。 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.chinacloudapi.cn".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- 将 `storage_account_name` 占位符值替换为存储帐户的名称。

- 将 `storage_account_key` 占位符值替换为存储帐户访问密钥。

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以通过调用 **FileSystemDataLakeServiceClient.create_file_system** 方法来创建一个。

此示例创建名为 `my-file-system` 的文件系统。

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>创建目录

通过调用 **FileSystemClient.create_directory** 方法来创建目录引用。

此示例将名为 `my-directory` 的目录添加到文件系统。 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用 **DataLakeDirectoryClient.rename_directory** 方法来重命名或移动目录。 以参数形式传递所需目录的路径。 

此示例将子目录重命名为 `my-subdirectory-renamed` 的名称。

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>删除目录

通过调用 **DataLakeDirectoryClient.delete_directory** 方法来删除目录。

此示例删除名为 `my-directory` 的目录。  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>管理目录权限

通过调用 **DataLakeDirectoryClient.get_access_control** 方法获取目录的访问控制列表 (ACL)，并通过调用 **DataLakeDirectoryClient.set_access_control** 方法来设置 ACL。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](/storage/blobs/data-lake-storage-access-control)。

此示例获取并设置名为 `my-directory` 的目录的 ACL。 字符串 `rwxr-xrw-` 为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取和写入权限。

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录 

首先，通过创建 **DataLakeFileClient** 类的实例，在目标目录中创建文件引用。 通过调用 **DataLakeFileClient.append_data** 方法上传文件。 确保通过调用 **DataLakeFileClient.flush_data** 方法完成上传。

此示例将文本文件上传到名为 `my-directory` 的目录。   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>管理文件权限

通过调用 **DataLakeFileClient.get_access_control** 方法获取文件的访问控制列表 (ACL)，并通过调用 **DataLakeFileClient.set_access_control** 方法来设置 ACL。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](/storage/blobs/data-lake-storage-access-control)。

此示例获取并设置名为 `my-file.txt` 的文件的 ACL。 字符串 `rwxr-xrw-` 为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取和写入权限。

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>从目录下载 

打开用于写入的本地文件。 然后，创建一个 **DataLakeFileClient** 实例，该实例表示要下载的文件。 调用 **DataLakeFileClient.read_file**，以便从文件读取字节，然后将这些字节写入本地文件。 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>列出目录内容

通过调用 **FileSystemClient.get_paths** 方法列出目录内容，然后枚举结果。

此示例输出名为 `my-directory` 的目录中的每个子目录和文件的路径。

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>另请参阅

* [API 参考文档](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [包（Python 包索引）](https://pypi.org/project/azure-storage-file-datalake/)
* [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供反馈](https://github.com/Azure/azure-sdk-for-python/issues)
