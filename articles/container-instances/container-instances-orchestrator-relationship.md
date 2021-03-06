---
title: "Azure 容器实例和容器协调"
description: "了解 Azure 容器实例如何与容器协调器交互"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b960d56188abf102cf4a8894582203205eaa9a03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure 容器实例和容器协调器

由于规模较小并面向应用程序，容器非常适合敏捷交付环境和基于微服务的体系结构。 自动化和管理大量容器及其交互方式的任务称为*协调*。 常见的容器协调器包括 Kubernetes、DC/OS 和 Docker Swarm，[Azure 容器服务](https://docs.microsoft.com/azure/container-service/)包含所有这些容器。

Azure 容器实例提供协调平台的某些基本调度功能，但不包括这些平台提供的更高价值的服务，事实上可与这些服务互补。 本文介绍 Azure 容器实例的处理范围，以及整套容器协调器如何与它交互。

## <a name="traditional-orchestration"></a>传统的协调

协调的标准定义包括以下任务：

- **调度**：在给定容器映像和资源请求的情况下，找到适合运行容器的计算机。
- **关联/反关联**：指定一组应该彼此靠近（为提高可用性）或保持足够距离（为提高性能）运行的容器。
- **运行状况监视**：监视容器故障并自动重新调度容器。
- **故障转移**：跟踪每台计算机上运行的组件，并将容器从有故障的计算机重新调度到正常的节点。
- **缩放**：根据需要手动或自动添加或删除容器实例。
- **网络**：提供覆盖网络用于协调容器，以便跨多台主机通信。
- **服务发现**：使容器能够自动找到对方，即便它们在主机之间移动并更改了 IP 地址。
- **协调的应用程序升级**：管理容器升级，避免应用程序关闭，并在出错的情况下回滚。

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Azure 容器实例的协调：分层方法

Azure 容器实例支持分层的协调方法，提供全部所需的调度和管理功能来运行单个容器，同时允许协调器平台管理容器实例顶层的多容器任务。

由于容器实例的所有底层基础结构由 Azure 管理，因此容器协调器平台不需要考虑如何查找用于运行单个容器的适当主机。 云的弹性确保始终有一台可用的主机。 协调器可将重心放在简化多容器体系结构的开发的任务上，包括缩放和协调的升级。



## <a name="potential-scenarios"></a>可能的方案

尽管协调器与 Azure 容器实例的集成仍处于初步阶段，但我们可以预见到将来会出现一些不同的环境：

### <a name="orchestration-of-container-instances-exclusively"></a>以独占方式协调容器实例

由于 Azure 容器实例可快速启动并按秒计费，专门以容器实例为基础的环境能够以最快的速度启动，并处理较大差异的工作负荷。

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>在虚拟机中合并容器实例和容器

对于长时间运行的稳定工作负荷，协调专用虚拟机群集中的容器通常会比使用容器实例运行相同的容器更节省。 但是，容器实例提供理想的解决方案来快速扩展和缩减整体容器，以处理意外的或短期高发的使用量。 协调器不必先增加群集中的虚拟机数目，然后将更多的容器部署到这些计算机，而可以直接使用容器实例调度更多的容器，并删除不再需要的容器。

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>示例实现：适用于 Kubernetes 的 Azure 容器实例连接器

为了演示容器协调平台如何与 Azure 容器实例集成，我们已开始构建一个[适用于 Kubernetes 的示例连接器][aci-connector-k8s]。

适用于 Kubernetes 的连接器通过注册为具有无限容器的节点，并在 Azure 容器实例中以容器组的形式调度 [pod][pod-doc] 创建，来模拟 [kubelet][kubelet-doc]。

<!-- ![ACI Connector for Kubernetes][aci-connector-k8s-gif] -->

可为其他协调器构建能以类似方式与平台基元集成的连接器，以融合协调器 API 的能力以及在 Azure 容器实例中管理容器的速度优势和简便性。

> [!WARNING]
> 适用于 Kubernetes 的 ACI 连接器是试验性的，不应在生产环境中使用。

## <a name="next-steps"></a>后续步骤

使用[快速入门指南](container-instances-quickstart.md)创建第一个包含 Azure 容器实例的容器。

<!-- IMAGES -->
[aci-connector-k8s-gif]: ./media/container-instances-orchestrator-relationship/aci-connector-k8s.gif

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/azure/aci-connector-k8s
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/