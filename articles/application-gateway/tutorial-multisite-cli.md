---
title: 使用 CLI 托管多个站点 - Azure 应用程序网关
description: 了解如何使用 Azure CLI 创建托管多个站点的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/30/2020
ms.author: v-junlch
ms.openlocfilehash: e2bc6d4fe24bc0d967749b93a14d462edb063b24
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "80581798"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>使用 Azure CLI 创建托管多个站点的应用程序网关

创建[应用程序网关](application-gateway-multi-site-overview.md)时可以使用 Azure CLI 配置[多个网站的托管](application-gateway-introduction.md)。 本教程中使用虚拟机规模集创建后端池。 然后，基于所拥有的域配置侦听器和规则，以确保 Web 流量可到达池中的相应服务器。 本教程假定你拥有多个域，并使用示例 `www.contoso.com` 和 `www.fabrikam.com`。

在本文中，学习如何：

> [!div class="checklist"]
> * 设置网络
> * 创建应用程序网关
> * 创建侦听器和路由规则
> * 使用后端池创建虚拟机规模集
> * 在域中创建 CNAME 记录

![多站点路由示例](./media/tutorial-multisite-cli/scenario.png)

如果没有 Azure 订阅，请在开始之前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=A261C142F)。

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/group) 创建资源组。

以下示例在“chinanorth”  位置创建名为“myResourceGroupAG”  的资源组。

```azurecli 
az group create --name myResourceGroupAG --location chinanorth
```

## <a name="create-network-resources"></a>创建网络资源 

使用 *az network vnet create* 创建名为 *myVNet* 的虚拟网络和名为 [myAGSubnet](/cli/network/vnet) 的子网。 然后，可以使用 *az network vnet subnet create* 添加后端服务器所需的名为 [myBackendSubnet](/cli/network/vnet/subnet) 的子网。 使用 *az network public-ip create* 创建名为 [myAGPublicIPAddress](https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) 的公共 IP 地址。

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location chinanorth \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

可以使用 [az network application-gateway create](/cli/network/application-gateway) 创建名为 *myAppGateway* 的应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、sku 和 HTTP 设置。 将应用程序网关分配给之前创建的 *myAGSubnet* 和 *myAGPublicIPAddress*。 

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location chinanorth \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

创建应用程序网关可能需要几分钟时间。 创建应用程序网关后，可以看到它的这些新功能：

- *appGatewayBackendPool* - 应用程序网关必须至少具有一个后端地址池。
- *appGatewayBackendHttpSettings* - 指定将端口 80 和 HTTP 协议用于通信。
- *appGatewayHttpListener* - 与 *appGatewayBackendPool* 关联的默认侦听器。
- *appGatewayFrontendIP* - 将 *myAGPublicIPAddress* 分配给 *appGatewayHttpListener*。
- *rule1* - 与 *appGatewayHttpListener* 关联的默认路由规则。

### <a name="add-the-backend-pools"></a>添加后端池

使用 *az network application-gateway address-pool create* 添加包含后端服务器所需的名为 *contosoPool* 和 [fabrikamPool](/cli/network/application-gateway) 的后端池。

```azurecli
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>添加侦听器

应用程序网关需要侦听器才能适当地将流量路由到后端池。 在本教程中，将为两个域创建两个侦听器。 在此示例中，将为域 *www.contoso.com* 和 *www.fabrikam.com* 创建侦听器。 

使用 *az network application-gateway http-listener create* 添加路由流量所需的名为 *contosoListener* 和 [fabrikamListener](/cli/network/application-gateway) 的侦听器。

```azurecli
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>添加路由规则

规则按照其创建顺序进行处理，并且使用与发送到应用程序网关的 URL 匹配的第一个规则定向流量。 例如，如果在同一端口上同时有使用基本侦听器的规则和使用多站点侦听器的规则，则使用多站点侦听器的规则必须在使用基本侦听器的规则之前列出，多站点规则才能正常运行。 

在此示例中，将创建两个新规则并删除在创建应用程序网关时创建的默认规则。 可以使用 [az network application-gateway rule create](/cli/network/application-gateway) 添加规则。

```azurecli
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>创建虚拟机规模集

在此示例中，将创建三个虚拟机规模集以支持应用程序网关中的三个后端池。 创建的规模集分别名为 *myvmss1*、*myvmss2* 和 *myvmss3*。 每个规模集包含两个在其上安装了 NGINX 的虚拟机实例。

```azurecli
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>安装 NGINX

```azurecli
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>在域中创建 CNAME 记录

使用其公共 IP 地址创建应用程序网关后，可以获取 DNS 地址并使用它在域中创建 CNAME 记录。 可以使用 [az network public-ip show](https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-show) 获取应用程序网关的 DNS 地址。 复制 DNSSettings 的 *fqdn* 值并使用它作为所创建的 CNAME 记录的值。 

```azurecli
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

不建议使用 A 记录，因为重新启动应用程序网关后 VIP 可能会变化。

## <a name="test-the-application-gateway"></a>测试应用程序网关

在浏览器的地址栏中输入域名。 例如，http\://www.contoso.com。

![在应用程序网关中测试 contoso 站点](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

将地址更改为其他域，应看到类似下例所示的内容：

![在应用程序网关中测试 fabrikam 站点](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 设置网络
> * 创建应用程序网关
> * 创建侦听器和路由规则
> * 使用后端池创建虚拟机规模集
> * 在域中创建 CNAME 记录

> [!div class="nextstepaction"]
> [详细了解应用程序网关的作用](application-gateway-introduction.md)

