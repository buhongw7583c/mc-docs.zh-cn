---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 查找有关 Azure Kubernetes 服务 (AKS) 的某些常见问题的解答。
ms.topic: conceptual
origin.date: 10/02/2019
ms.date: 03/09/2020
ms.author: v-yeche
ms.openlocfilehash: 9ef0ba33a8c86f21cddf14cac21fe4e5912ca72e
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79292489"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 区域目前提供 AKS？

有关可用区域的完整列表，请参阅 [AKS 区域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？

<!--MOONCAKE: Not support the PREVIEW feature-->

目前在 Azure 中国环境中不受支持。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>是否可以将 AKS 部署到现有虚拟网络？

可以，可使用[高级网络功能][aks-advanced-networking]将 AKS 群集部署到现有虚拟网络中。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>能否限制哪些人员可以访问 Kubernetes API 服务器？

能，可以使用 [API 服务器授权 IP 范围][api-server-authorized-ip-ranges]来限制对 Kubernetes API 服务器的访问。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>是否可以限制为只能在我的虚拟网络中访问 Kubernetes API 服务器？

目前不可以，但此功能正在规划中。 可以在 [AKS GitHub 存储库][private-clusters-github-issue]中跟踪此项计划的进度。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>能否在单个群集中使用不同 VM 大小？

能，可以通过创建[多个节点池][multi-node-pools]来在 AKS 群集中使用不同虚拟机大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 会按照夜间计划自动将安全修补程序应用于群集中的 Linux 节点。 但是，你有责任确保这些 Linux 节点根据需要进行重新启动。 可以使用多个选项来重新启动节点：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集自动升级 [cordon 和 drain 节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新修补程序版本或 Kubernetes 次要版本将新节点联机。 有关详细信息，请参阅[升级 AKS 群集][aks-upgrade]。
- 使用 [Kured](https://github.com/weaveworks/kured)：适用于 Kubernetes 的开源重新启动守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重新启动的文件是否存在。 通过将相同的[封锁和排空进程][cordon-drain]用作群集升级跨群集管理 OS 重新启动。

有关使用 kured 的详细信息，请参阅[将安全性和内核更新应用于 AKS 中的节点][node-updates-kured]。

<!--MOONCAKE: Not Available on ### Windows Server nodes-->
<!--MOONCAKE: Not Available on For Windows Server nodes (currently in preview in AKS)-->

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

AKS 在多个 Azure 基础结构资源之上构建，包括虚拟机规模集、虚拟网络和托管磁盘。 这使你能够在 AKS 提供的托管 Kubernetes 环境中利用 Azure 平台的许多核心功能。 

<!--Not Available on virtual machine scale sets-->
<!--MOONCAKE: REPLACE WITH availability set-->
<!--Not Available on For example, most Azure virtual machine types can be used directly with AKS and Azure Reservations can be used to receive discounts on those resources automatically.-->
<!--Not Available on Azure Reservations-->

为了启用此体系结构，每个 AKS 部署跨越两个资源组：

1. 创建第一个资源组。 此组仅包含 Kubernetes 服务资源。 在部署过程中，AKS 资源提供程序会自动创建第二个资源组。 例如，第二个资源组为 *MC_myResourceGroup_myAKSCluster_chinaeast2*。 有关如何指定这第二个资源组的名称，请参阅下一部分。
1. 第二个资源组（称为节点资源组）包含与该群集相关联的所有基础结构资源。  这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 默认情况下，节点资源组使用类似于 *MC_myResourceGroup_myAKSCluster_chinaeast2* 的名称。 每当删除群集，AKS 就会自动删除节点资源，因此，请只对生命周期与群集相同的资源使用 AKS。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我是否可为 AKS 节点资源组提供自己的名称？

是的。 默认情况下，AKS 将节点资源组命名为 MC_resourcegroupname_clustername_location  ，但你也可以提供自己的名称。

若要指定自己的资源组名称，请安装 [aks-preview][aks-preview-cli] Azure CLI 扩展版本 *0.3.2* 或更高版本。 使用 [az aks create][az-aks-create] 命令创建 AKS 群集时，请使用 *--node-resource-group* 参数并指定资源组的名称。 如果使用 Azure 资源管理器模板部署 AKS 群集，则可以使用 nodeResourceGroup  属性定义资源组名称。

<!--Not Avaialble on [use an Azure Resource Manager template][aks-rm-template]-->

* Azure 资源提供程序会在你自己的订阅中自动创建辅助资源组。
* 只能在创建群集时指定自定义资源组名称。

使用节点资源组时，请记住，不能：

* 指定现有的资源组作为节点资源组。
* 为节点资源组指定不同的订阅。
* 创建群集后更改节点资源组名称。
* 为节点资源组中的托管资源指定名称。
* 修改或删除节点资源组中托管资源的标记。 （请参阅下一部分的附加信息。）

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>是否可以修改节点资源组中 AKS 资源的标记和其他属性？

如果修改或删除节点资源组中 Azure 创建的标记和其他资源属性，可能会出现意外的结果，例如缩放和升级错误。 AKS 允许创建和修改自定义标记。 例如，可以创建或修改标记，以分配业务单位或成本中心。 修改 AKS 群集中节点资源组下的资源会影响服务级别目标 (SLO)。 有关详细信息，请参阅 [AKS 是否提供服务级别协议？](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支持哪些 Kubernetes 许可控制器？ 是否可以添加或删除许可控制器？

AKS 支持以下[许可控制器][admission-controllers]：

- NamespaceLifecycle 
- LimitRanger 
- ServiceAccount 
- DefaultStorageClass 
- DefaultTolerationSeconds 
- MutatingAdmissionWebhook 
- ValidatingAdmissionWebhook 
- ResourceQuota 

目前无法在 AKS 中修改许可控制器列表。

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。

AKS 目前尚未与 Azure Key Vault 本机集成。 但是，[Kubernetes 项目的 Azure Key Vault FlexVolume][keyvault-flexvolume] 实现了从 Kubernetes pod 到 Key Vault 机密的直接集成。

<!--Not Avaialble on ## Can I run Windows Server containers on AKS?-->

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

在服务级别协议 (SLA) 中，如果未满足已发布的服务级别，提供商同意向客户偿还服务费用。 由于 AKS 是免费的，不存在偿还费用，因此也就没有正式的 SLA。 不过，AKS 会设法将 Kubernetes API 服务器的可用性维持在不小于 99.5% 的水平上。

重要的是要认识到 AKS 服务可用性（指 Kubernetes 控制平面的正常运行时间）和在 Azure 虚拟机上运行的特定工作负荷的可用性之间的区别。 尽管控制平面在控制平面未就绪时可能不可用，但在 Azure VM 上运行的群集工作负荷仍可正常工作。 鉴于 Azure VM 是付费资源，它们由财务 SLA 提供支持。 请在[此处](https://www.azure.cn/support/sla/virtual-machines/)阅读有关 Azure VM SLA 的更多详细信息。

<!--Not Available on [Availability Zones][availability-zones]-->

## <a name="why-cant-i-set-maxpods-below-30"></a>为何无法将 maxPods 设置为 30 以下？

在 AKS 中，使用 Azure CLI 和 Azure 资源管理器模板创建群集时可以设置 `maxPods` 值。 但是，Kubenet 和 Azure CNI 都需要一个最小值（创建时会验证该值）： 

| 网络 | 最小值 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

由于 AKS 是托管服务，我们会将加载项和 pod 部署为群集的一部分并对其进行管理。 过去，用户定义的 `maxPods` 值可以小于运行托管 pod 所需的值（例如 30）。 AKS 现在使用以下公式计算最小 pod 数：((maxPods or (maxPods * vm_count)) > 托管的加载项 pod 最小数目。

用户无法覆盖最小 `maxPods` 验证值。

<!--Not Available on ## Can I apply Azure reservation discounts to my AKS agent nodes?-->

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>我可以在 Azure 租户之间移动/迁移群集吗？

可以使用 `az aks update-credentials` 命令在 Azure 租户之间移动 AKS 群集。 按[选择更新或创建服务主体](/aks/update-credentials)中的说明操作，然后[使用新凭据更新 AKS 群集](/aks/update-credentials#update-aks-cluster-with-new-credentials)。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>我可以在订阅之间移动/迁移群集吗？

目前不支持在订阅之间移动群集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>是否可以将 AKS 群集从当前的 Azure 订阅移到另一个订阅？ 

不支持在 Azure 订阅之间移动 AKS 群集及其关联的资源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>为何群集删除需要如此长的时间？ 

大多数群集是按用户请求删除的；某些情况下，尤其是在客户引入自己的资源组或执行跨 RG 任务的情况下，删除操作可能需要更多的时间，或者可能会失败。 如果在删除时出现问题，请仔细检查，确保没有在 RG 上进行锁定、RG 之外的任何资源均已取消与 RG 的关联，等等。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果 Pod/部署处于“NodeLost”或“未知”状态，是否仍然可以升级群集？

可以，但是 AKS 不建议这样做。 理想情况下，升级应该在群集状态已知且正常的情况下完成。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果我有一个群集的一个或多个节点处于“运行不正常”状态或关闭状态，是否可以进行升级？

否。请删除/移除任何处于故障状态的节点或因为其他原因从群集中移除的节点，然后再进行升级。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我运行了群集删除操作，但出现错误：`[Errno 11001] getaddrinfo failed` 

这种情况最可能的原因是用户有一个或多个网络安全组 (NSG) 仍在使用并与群集相关联。  请将网络安全组删除，然后再次尝试群集删除操作。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我运行了升级，但现在我的 Pod 处于崩溃循环中，且就绪情况探测失败。

请确认你的服务主体尚未过期。  请参阅：[AKS 服务主体](/aks/kubernetes-service-principal)和 [AKS 更新凭据](/aks/update-credentials)。

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>我的群集在运行，但突然不能预配 LoadBalancers，不能装载 PVC，等等。 

请确认你的服务主体尚未过期。  请参阅：[AKS 服务主体](/aks/kubernetes-service-principal)和 [AKS 更新凭据](/aks/update-credentials)。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>是否可以使用虚拟机规模集 API 手动进行缩放？

否。使用虚拟机规模集 API 进行的缩放操作不受支持。 请使用 AKS API (`az aks scale`)。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>是否可以使用虚拟机规模集手动缩放到 0 个节点？

否。使用虚拟机规模集 API 进行的缩放操作不受支持。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>是否可以停止或解除分配我的所有 VM？

虽然 AKS 的复原机制可以经受此类配置并从其恢复，但我们建议你不要这样进行配置。

## <a name="can-i-use-custom-vm-extensions"></a>是否可以使用自定义 VM 扩展？

否。AKS 是一项托管服务，不支持操作 IaaS 资源。 若要安装自定义组件等内容， 请利用 Kubernetes 的 API 和机制。 例如，利用 DaemonSets 安装所需组件。

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md

<!--MOONCAKE: CORRECT FOR URL OF aks-preview-cli-->

[aks-preview-cli]: https://docs.microsoft.com/cli/azure/ext/aks-preview/aks?view=azure-cli-latest
[az-aks-create]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create

<!--Not Avaialble on [aks-rm-template]: /templates/microsoft.containerservice/2019-06-01/managedclusters-->
<!--Not Avaialble on [aks-cluster-autoscaler]: cluster-autoscaler.md-->
<!--Not Avaialble on [nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool-->
<!--Not Avaialble on [aks-windows-cli]: windows-container-cli.md-->
<!--Not Avaialble on [aks-windows-limitations]: windows-node-limitations.md-->
<!--Not Avaialble on [reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md-->

[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!--Not Avaialble on [availability-zones]: ./availability-zones.md-->

<!-- LINKS - external -->

[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service&regions=china-non-regional,china-east,china-east-2,china-north,china-north-2
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948

<!-- Update_Description: update meta properties, wording update, update link -->