---
title: "Azure Linux VM 和 Azure 存储 | Microsoft Docs"
description: "介绍 Linux 虚拟机上的 Azure 标准和高级存储，以及托管和非托管磁盘。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
ms.openlocfilehash: 934580f6fcfdbff6e61626ed685459478559717d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="azure-and-linux-vm-storage"></a>Azure 和 Linux VM 存储
Azure 存储是依赖于持续性、可用性和伸缩性来满足客户需求的现代应用程序的云存储解决方案。  除了使开发人员可以构建大型应用程序来支持新方案之外，Azure 存储还为 Azure 虚拟机提供了存储基础。

## <a name="managed-disks"></a>托管磁盘

Azure VM 现支持使用 [Azure 托管磁盘](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，不必自行创建或管理任何 [Azure 存储帐户](../../storage/common/storage-introduction.md)即可创建 VM。 指定是需要高级还是标准存储，以及磁盘应具有的容量，随之 Azure 便会创建 VM 磁盘。 使用托管磁盘的 VM 具有许多重要功能，包括：

- 自动可伸缩性支持。 Azure 创建磁盘并管理基础存储，每个订阅最多可支持 10,000 个磁盘。
- 可用性集带来更高的可靠性。 Azure 可确保 VM 磁盘在可用集内自动彼此隔离。
- 增强的访问控制。 托管磁盘公开由 [Azure 基于角色的访问控制 (RBAC)](../../active-directory/role-based-access-control-what-is.md) 所控制的各种操作。

托管磁盘与非托管磁盘的定价不同。 有关这方面的信息，请参阅[托管磁盘的定价和计费](../windows/managed-disks-overview.md#pricing-and-billing)。

可以通过 [az vm convert](/cli/azure/vm#convert)，将使用非托管磁盘的现有 VM 转换为使用托管磁盘。 有关详细信息，请参阅[如何将 Linux VM 从非托管磁盘转换为 Azure 托管磁盘](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果非托管磁盘所在的存储帐户已使用或曾使用 [Azure 存储服务加密 (SSE)](../../storage/common/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 加密，则无法将其转换为托管磁盘。 下列步骤详细说明如何转换位于（或曾位于）已加密存储帐户的非托管磁盘：

- 使用 [az storage blob copy start](/cli/azure/storage/blob/copy#start) 将虚拟硬盘 (VHD) 复制到从未启用 Azure 存储服务加密的存储帐户。
- 使用 [az vm create](/cli/azure/vm#create) 创建使用托管磁盘的 VM 并指定创建期间的 VHD 文件，或
- 使用 [az vm disk attach](/cli/azure/vm/disk#attach) 将复制的 VHD 附加到具有托管磁盘的正在运行中的 VM。


## <a name="azure-storage-standard-and-premium"></a>Azure 存储：标准和高级
Azure VM - 无论它使用托管磁盘还是非托管磁盘 - 都能以标准存储磁盘或高级存储磁盘为基础进行构建。 使用门户选择 VM 时，必须在“基本信息”屏幕上使用一个下拉列表来切换标准和高级磁盘。 切换到 SSD 时，只显示支持高级存储的 VM，所有这些 VM 由 SSD 驱动器提供支持。  切换到 HDD 时，会显示支持标准存储的 VM（这些 VM 由机械磁盘驱动器提供支持），以及由 SSD 提供支持的高级存储 VM。

从 `azure-cli` 创建 VM 时，可以在通过 `-z` 或 `--vm-size` cli 标志选择 VM 大小时选择标准或高级存储。

## <a name="creating-a-vm-with-a-managed-disk"></a>创建具有托管磁盘的 VM

下面的示例要求安装 Azure CLI 2.0（可单击[此处进行安装](/cli/azure/install-azure-cli)）。

首先，使用 [az group create](/cli/azure/group#create) 创建用于管理资源的资源组：

```azurecli
az group create --location westus --name myResourceGroup
```

现在，使用 [az vm create](/cli/azure/vm#create) 创建 VM。 指定唯一的 `--public-ip-address-dns-name` 自变量，可能会采用 `mypublicdns`。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns
```

上述示例以标准存储帐户创建了一个具有托管磁盘的 VM。 若要使用高级存储帐户，请添加 `--storage-sku Premium_LRS` 参数，如以下示例所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns \
    --storage-sku Premium_LRS
```

## <a name="standard-storage"></a>标准存储
Azure 标准存储是默认的存储类型。  标准存储具有很高的性价比，性能也很不错。  

## <a name="premium-storage"></a>高级存储
Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储存储数据的虚拟机 (VM) 磁盘。 可以将应用程序的 VM 磁盘迁移到 Azure 高级存储，以充分利用这些磁盘的速度和性能。

高级存储的特性：

* 高级存储磁盘：Azure 高级存储支持可连接到 DS、DSv2 或 GS 系列 Azure VM 的 VM 磁盘。
* 高级页 Blob：高级存储支持 Azure 页 Blob（用于保存 Azure 虚拟机 (VM) 的永久性磁盘）。
* 高级本地冗余存储：高级存储帐户仅支持使用本地冗余存储 (LRS) 作为复制选项，并在单个区域中保留三个数据副本。
* [高级存储](../windows/premium-storage.md)

## <a name="premium-storage-supported-vms"></a>支持高级存储的 VM
高级存储支持 DS 系列、DSv2 系列、GS 系列和 Fs 系列 Azure 虚拟机 (VM)。 可以在支持高级存储的 VM 上同时使用标准和高级存储磁盘。 但不能在不兼容高级存储的 VM 系列中使用高级存储磁盘。

以下是我们使用高级存储验证过的 Linux 分发版。

| 分发 | 版本 | 支持的内核 |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x、8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| CentOS |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+、7.2+ | |

## <a name="azure-file-storage"></a>Azure 文件存储
Azure 文件存储使用标准 SMB 协议在云中提供文件共享。 使用 Azure 文件，可以将依赖于文件服务器的企业应用程序迁移到 Azure。 在 Azure 中运行的应用程序可以轻松地从运行 Linux 的 Azure 虚拟机装载文件共享。 并且使用最新版本的文件存储，还可以从支持 SMB 3.0 的本地应用程序装载文件共享。  由于文件共享是 SMB 共享，因此还可以通过标准的文件系统 API 来访问它们。

文件存储基于与 Blob、表和队列存储相同的技术构建，因此文件存储能够提供 Azure 存储平台内置的现有可用性、持续性、可伸缩性和异地冗余。 有关存文件存储性能目标和限制的详细信息，请参阅“Azure 存储伸缩性和性能目标”。

* [如何将 Azure 文件存储与 Linux 配合使用](../../storage/files/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>热存储
Azure 热存储层为存储经常访问的数据进行了优化。  热存储是 Blob 存储的默认存储类型。

## <a name="cool-storage"></a>冷存储
Azure 冷存储层为存储不常访问且长期留存的数据进行了优化。 冷存储的示例用例包括备份、媒体内容、科研数据、合规性和存档数据。 一般来说，冷存储是极少访问的各种数据的极佳存储位置。

|  | 热存储层 | 冷存储层 |
|:--- |:---:|:---:|
| 可用性 |99.9% |99% |
| 可用性（RA-GRS 读取次数） |99.99% |99.9% |
| 使用费 |存储成本较高 |存储成本较低 |
| 访问权限较低 |访问权限较高 | |
| 事务成本 |事务成本 | |

## <a name="redundancy"></a>冗余
始终复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性，并且即使在遇到临时硬件故障时也符合 Azure 存储 SLA 要求。

创建存储帐户时，必须选择以下复制选项之一：

* 本地冗余存储 (LRS)
* 区域冗余存储空间 (ZRS)
* 异地冗余存储 (GRS)
* 读取访问异地冗余存储 (RA-GRS)

### <a name="locally-redundant-storage"></a>本地冗余存储
本地冗余存储 (LRS) 在创建存储帐户所在的区域中复制数据。 为最大程度地提高持久性，针对存储帐户中的数据发出的每个请求将复制三次。 这三个副本每个都驻留在不同的容错域和升级域中。  请求仅在写入所有三个副本后，才成功返回。

### <a name="zone-redundant-storage"></a>区域冗余存储
区域冗余存储 (ZRS) 在两到三个设施之间复制数据（在单个区域内或两个区域之间），提供比 LRS 更高的持久性。 如果存储帐户启用了 ZRS，即使其中一个设施出现故障，数据也能持久保存。

### <a name="geo-redundant-storage"></a>异地冗余存储
异地冗余存储 (GRS) 将数据复制到距主区域数百英里以外的辅助区域。 如果存储帐户启用了 GRS，则即使遇到区域完全停电或导致主区域不可恢复的灾难，数据也能持久保存。

### <a name="read-access-geo-redundant-storage"></a>读取访问异地冗余存储
除了 GRS 所提供的在两个区域之间进行复制外，读取访问异地冗余存储 (RA-GRS) 还提供对辅助位置中的数据的只读访问权限，从而最大限度地提高了存储帐户的可用性。 当主区域中的数据不可用时，应用程序可以从辅助区域读取数据。

若要深入了解 Azure 存储冗余，请参阅：

* [Azure 存储复制](../../storage/common/storage-redundancy.md)

## <a name="scalability"></a>可伸缩性
Azure 存储可以大规模伸缩，因此可以存储和处理数百 TB 的数据来支持科学、财务分析和媒体应用程序所需的大数据方案。 也可以存储小型商业网站所需的少量数据。 当需求降低时，只需要为当前存储的数据支付费用。 Azure 存储当前存储了数十万亿个唯一的客户对象，平均每秒处理数百万个请求。

标准存储帐户：标准存储帐户的总请求率上限为 20,000 IOPS。 在标准存储帐户中，所有虚拟机磁盘的 IOPS 总数不应超过此限制。

高级存储帐户：高级存储帐户的总吞吐量速率上限为 50 Gbps。 所有 VM 磁盘的总吞吐量不应超过此限制。

## <a name="availability"></a>可用性
我们保证至少在 99.99%（对于“冷”访问层来说为 99.9%）的时间里成功地处理请求以便读取来自读取访问异地冗余存储 (RA-GRS) 帐户的数据，只要在次要区域上重试从主要区域读取数据失败的尝试。

我们保证至少在 99.9%（对于“冷”访问层来说为 99%）的时间里成功地处理请求以便从本地冗余存储 (LRS)、区域冗余存储 (ZRS) 和异地冗余存储 (GRS) 帐户读取数据。

我们保证至少在 99.9%（对于“冷”访问层来说为 99%）的时间里成功地处理请求以便将数据写入本地冗余存储 (LRS)、区域冗余存储 (ZRS) 和异地冗余存储 (GRS) 帐户，以及读取访问异地冗余存储 (RA-GRS) 帐户。

* [Azure 存储 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>区域
Azure 已在全球 30 个区域上市，在另外 4 个区域宣布了推出计划。 地域扩张是 Azure 的首要任务，因为这能够帮助我们的客户提高绩效，并为与数据位置有关的客户需求和偏好提供支持。  最近推出 Azure 的区域是德国。

Microsoft 云德国区在欧洲 Microsoft 云服务的基础上提供了一个有特色的选项，为德国、欧盟 (EU) 和欧洲自由贸易联盟 (EFTA) 的守法合作伙伴与客户带来更多的创新与经济增长机会。

位于马格德堡和法兰克福新数据中心的客户数据受到 Data Trustee、T-Systems International、独立德国公司和德国电信分公司的监管。 托管在这些数据中心的 Microsoft 商业云服务遵守德国数据处理法规，允许客户选择处理数据的方式和位置。

* [Azure 区域地图](https://azure.microsoft.com/regions/)

## <a name="security"></a>“安全”
Azure 存储提供配套的安全性功能，这些功能相辅相成，可让开发人员共同构建安全的应用程序。 存储帐户本身可以通过基于角色的访问控制和 Azure Active Directory 来保护。 在应用程序和 Azure 之间传输数据时，可以使用客户端加密、HTTPS 或 SMB 3.0 来保护数据。 使用存储服务加密 (SSE) 写入 Azure 存储时，可将数据设置为自动加密。 可以使用 Azure 磁盘加密将虚拟机所用的 OS 和数据磁盘设置为进行加密。 可以使用共享访问签名来授予对 Azure 存储中数据对象的委派访问权限。

### <a name="management-plane-security"></a>管理平面安全性
管理平面包含用于管理存储帐户的资源。 此部分讨论 Azure 资源管理器部署模型，以及如何使用基于角色的访问控制 (RBAC) 来控制访问存储帐户。 此外，还将讨论如何管理存储帐户密钥以及重新生成此类密钥。

### <a name="data-plane-security"></a>数据平面安全性
此部分探讨如何在存储帐户（例如 Blob、文件、队列和表）中，允许使用共享访问签名和存储访问策略来访问实际的数据对象。 我们介绍服务级别 SAS 和帐户级别 SAS。 此外，还将了解如何限制访问特定的 IP 地址（或 IP 地址范围）、如何限制用于 HTTPS 的协议，以及如何吊销共享访问签名而无需等到它过期。

## <a name="encryption-in-transit"></a>传输中加密
此部分讨论如何在将数据传输到 Azure 存储或从中传出时提供保护。 我们将介绍 HTTPS 的建议用法，以及 SMB 3.0 对 Azure 文件共享使用的加密。 同时还将探讨客户端加密，它可让你在将数据传输到客户端应用程序中的存储之前加密数据，以及从存储传出后解密数据。

## <a name="encryption-at-rest"></a>静态加密
我们将讨论存储服务加密 (SSE) 以及如何对存储帐户启用它，从而使块 Blob、页 Blob 以及追加 Blob 在写入到 Azure 存储时自动进行加密。 此外，将了解如何使用 Azure 磁盘加密，并探究磁盘加密、SSE 与客户端加密之间的基本差异和用例。 我们将简要探讨美国政府针对计算机实施的FIPS 合规性。

* [Azure 存储安全指南](../../storage/common/storage-security-guide.md)

## <a name="temporary-disk"></a>临时磁盘
每个 VM 包含一个临时磁盘。 临时磁盘为应用程序和进程提供短期存储存储空间，仅用于存储页面或交换文件等数据。 在[维护事件](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime)期间或[重新部署 VM](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 时，临时磁盘上的数据可能会丢失。 在 VM 标准重启期间，临时驱动器上的数据应会保留。

在 Linux 虚拟机上，此磁盘通常为 **/dev/sdb**，并且由 Azure Linux 代理格式化和装入到 **/mnt**。 临时磁盘的大小因虚拟机的大小而异。 有关详细信息，请参阅 [Linux 虚拟机的大小](sizes.md)。

有关 Azure 如何使用临时磁盘的详细信息，请参阅 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)（了解 Microsoft Azure 虚拟机上的临时驱动器）

## <a name="cost-savings"></a>成本节约
* [存储成本](https://azure.microsoft.com/pricing/details/storage/)
* [存储成本计算器](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>存储限制
* [存储服务限制](../../azure-subscription-service-limits.md#storage-limits)
