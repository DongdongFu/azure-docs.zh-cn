---
title: "Azure 活动日志概述 | Microsoft Docs"
description: "了解什么是 Azure 活动日志，以及如何通过它了解发生在 Azure 订阅中的事件。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: johnkem
ms.openlocfilehash: d36cc43889c190544b9a2735ce00e718c11fd216
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>使用 Azure 活动日志监视订阅活动
Azure 活动日志是一种方便用户深入了解 Azure 中发生的订阅级别事件的订阅日志。 这包括从 Azure 资源管理器操作数据到服务运行状况事件更新的一系列数据。 活动日志之前称为“审核日志”或“操作日志”，因为“管理”类别报告订阅的控制面事件。 通过活动日志，可确定订阅中资源上进行的任何写入操作 (PUT, POST, DELETE) 的“什么操作、谁操作和操作时间”等信息。 还可以了解该操作和其他相关属性的状态。 活动日志未包括读取 (GET) 操作或针对使用经典/“RDFE”模型的资源的操作。

![活动日志与其他类型的日志 ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

图 1：活动日志与其他类型的日志

活动日志不同于[诊断日志](monitoring-overview-of-diagnostic-logs.md)。 活动日志提供有关从外部（“控制面”）对资源所执行操作的数据。 诊断日志由资源发出，并提供有关该资源（“数据面”）的操作信息。

可以通过 Azure 门户、CLI、PowerShell cmdlet 和 Azure 监视器 REST API 从活动日志检索事件。


> [!WARNING]
> Azure 活动日志主要用于在 Azure Resource Manager 中发生的活动。 它不跟踪使用经典/RDFE 模型的资源。 某些经典资源类型在 Azure Resource Manager 中具有代理资源提供程序（例如 Microsoft.ClassicCompute）。 如果通过 Azure Resource Manager 使用这些代理资源提供程序与经典资源类型进行交互，则操作会显示在活动日志中。 如果在经典门户中或者以其他方式在 Azure Resource Manager 代理外部与经典资源类型进行交互，则操作只会记录在操作日志中。 可以在门户的一个单独部分中浏览操作日志。
>
>

请观看介绍了活动日志的以下视频。
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]
> 
>

## <a name="categories-in-the-activity-log"></a>活动日志中的类别
活动日志包含多个数据类别。 有关这些类别的架构的完整详细信息，请参阅[此文章](monitoring-activity-log-schema.md)。 其中包括：
* 管理 - 此类别包含通过资源管理器执行的所有创建、更新、删除和行动操作的记录。 此类别中的事件类型的示例包括“创建虚拟机”和“删除网络安全组”。用户或应用程序通过资源管理器所进行的每一个操作都会作为特定资源类型上的操作建模。 如果操作类型为“写入”、“删除”或“操作”，则该操作的开始、成功或失败记录都会记录在管理类别中。 管理类别还包括任何对订阅中基于角色的访问控制进行的更改。
* 服务运行状况 - 此类别包含 Azure 中发生的任何服务运行状况事件的记录。 此类别的一个事件类型示例是“美国东部的 SQL Azure 正处于故障时间”。 服务运行状况事件分 5 种：必需操作、辅助恢复、事件、维护、信息或安全性，仅当订阅中存在受事件影响的资源时，它们才出现。
* 警报 - 此类别包含所有 Azure 警报的激活记录。 可在此类别中看到的事件类型示例如“过去 5 分钟内，myVM 上的 CPU 百分比已超过 80%”。 许多 Azure 系统都具有警报概念 - 可定义某种类型的规则，并在条件匹配该规则时接收通知。 每当支持的 Azure 警报类型“激活”或满足生成通知的条件时，激活记录也会推送到此类别的活动日志中。
* 此类别包含基于订阅中定义的任何自动缩放设置的自动缩放引擎操作相关的所有事件记录。 可在此类别中看到的事件类型示例如“自动缩放扩大操作失败”。 使用自动缩放，可在支持的资源类型中，通过自动缩放设置基于日期和/或负载（指标）数据来自动增加或减少实例的数量。 满足增加或减少条件时，开始、成功或失败的事件会记录到此类别中。
* 建议 - 此类别包含特定资源类型（例如网站和 SQL Server）的建议事件。 这些事件提供有关如何更好地利用资源的建议。 拥有发出建议的资源，才会接收此类型的事件。
* 策略、安全性和资源运行状况 - 这些类别不包含任何事件；保留它们是为了将来使用。

## <a name="event-schema-per-category"></a>每个类别的事件架构
[请参阅此文章，了解每个类别的活动日志事件架构。](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>可以对活动日志执行的操作
可以对活动日志执行的部分操作如下：

![Azure 活动日志](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* 在 **Azure 门户**中查询和查看活动日志。
* [根据活动日志事件创建警报](monitoring-activity-log-alerts.md)。
* [将活动日志流式传输到**事件中心**](monitoring-stream-activity-logs-event-hubs.md)，方便第三方服务或自定义分析解决方案（例如 PowerBI）引入。
* 在 PowerBI 中使用 [**PowerBI 内容包**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)分析活动日志。
* [将活动日志保存到**存储帐户**进行存档或手动检查](monitoring-archive-activity-log.md)。 可以使用“日志配置文件”指定保留时间（天）。
* 通过 PowerShell Cmdlet、CLI 或 REST API 查询活动日志。

## <a name="query-the-activity-log-in-the-azure-portal"></a>在 Azure 门户中查询活动日志
在 Azure 门户中，可在多个位置查看活动日志：
* 活动日志边栏选项卡，可通过在左侧导航窗格中的“更多服务”下搜索活动日志进行访问。
* 监视边栏选项卡，默认情况下在左侧导航窗格中显示。 活动日志是 Azure Monitor 边栏选项卡的一部分。
* 任何资源的资源边栏选项卡，例如虚拟机的配置边栏选项卡。 活动日志是大多数这些资源边栏选项卡的一部分，单击它可自动筛选出与特定资源相关的事件。

在 Azure 门户中，可通过以下字段筛选活动日志：
* 时间跨度 - 事件的开始时间和结束时间。
* 类别 - 上述的事件类别。
* 订阅 - 一个或多个 Azure 订阅名称。
* 资源组 - 这些订阅中的一个或多个资源组。
* 资源（名称）- 特定资源的名称。
* 资源类型 - 资源的类型，例如 Microsoft.Compute/virtualmachines。
* 操作名称 - Azure 资源管理器操作的名称，例如 Microsoft.SQL/servers/Write。
* 严重性 - 事件的严重性级别（信息、警告、错误、严重）。
* 事件发起者 -“调用方”或执行操作的用户。
* 开放搜索 - 这是一个开放的文本搜索框，可在所有事件的所有字段中搜索该字符串。

定义一组筛选条件后，如果将来需要再次使用已应用的筛选条件执行相同的查询，可以将它保存为在会话中保留的查询。 还可以将查询固定到 Azure 仪表板，始终关注特定事件。

单击“应用”运行查询并显示所有匹配的事件。 单击列表中的任何事件将显示该事件的摘要以及该事件的完整原始 JSON。

若要获得更强大的功能，可以点击“日志搜索”图标，该选项在[Log Analytics Activity Log Analytics 解决方案](../log-analytics/log-analytics-activity.md)中显示活动日志数据。 活动日志边栏选项卡提供对日志的基础筛选/浏览体验，但是 Log Analytics 可以以更强大的方式对数据进行透视、查询和可视化。

## <a name="export-the-activity-log-with-a-log-profile"></a>使用日志配置文件导出活动日志
**日志配置文件**控制如何导出活动日志。 可以使用日志配置文件配置：

* 应将活动日志发送到何处：存储帐户或事件中心
* 应发送哪些事件类别（写入、删除、操作）。 *日志配置文件中“类别”的含义与活动日志事件中不同。在日志配置文件中，“类别”表示操作类型（写入、删除、操作）。在活动日志事件中，“类别”属性表示事件的来源或类型（例如，管理、服务运行状况、警报，等等）。*
* 应该导出哪些区域（位置）。 确保包含“全局”，因为活动日志中的事件多为全局事件。
* 活动日志应当在存储帐户中保留多长时间。
    - 保留期为零天表示日志将永久保留。 如果不需永久保留，则可将该值设置为 1 到 2147483647 之间的任意天数。
    - 如果设置了保留策略，但禁止将日志存储在存储帐户中（例如，如果仅选择事件中心或 OMS 选项），则保留策略无效。
    - 保留策略按天应用，因此在一天结束时 (UTC)，会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，会删除前天的日志。

可以使用与发出日志的订阅不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有合适的 RBAC 访问权限。

这些设置可以通过门户中活动日志边栏选项卡上的“导出”选项进行配置。 也可以[使用 Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell cmdlet 或 CLI 以编程方式对其进行配置。 一个订阅只能有一个日志配置文件。

### <a name="configure-log-profiles-using-the-azure-portal"></a>通过 Azure 门户配置日志配置文件
可以在 Azure 门户中使用“导出”选项将活动日志流式传输到事件中心，或者将其存储在存储帐户中。

1. 使用门户左侧的菜单导航到“活动日志”边栏选项卡。

    ![在门户中导航到“活动日志”](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 单击边栏选项卡顶部的“导出”按钮。

    ![门户中的“导出”按钮](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 在显示的边栏选项卡中，可以选择：  
  * 要导出事件的区域
  * 要保存事件的存储帐户
  * 要在存储中保留这些事件的天数。 设置为 0 天将永久保留日志。
  * 需要在其中创建事件中心，以便流式传输这些事件的服务总线命名空间。

     ![“导出活动日志”边栏选项卡](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. 单击“保存”保存这些设置。 这些设置会即时应用到订阅。

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>通过 Azure PowerShell Cmdlet 配置日志配置文件
#### <a name="get-existing-log-profile"></a>获取现有的日志配置文件
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>添加日志配置文件
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 名称 |是 |日志配置文件的名称。 |
| StorageAccountId |否 |应该将活动日志保存到其中的存储帐户的资源 ID。 |
| serviceBusRuleId |否 |服务总线命名空间（需在其中创建事件中心）的服务总线规则 ID。 是以下格式的字符串：`{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置 |是 |要为其收集活动日志事件的逗号分隔区域的列表。 |
| RetentionInDays |是 |事件的保留天数，介于 1 到 2147483647 之间。 值为零时，将无限期（永久）存储日志。 |
| Categories |否 |应收集的事件类别的逗号分隔列表。 可能值包括：Write、Delete 和 Action。 |

#### <a name="remove-a-log-profile"></a>删除日志配置文件
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>通过 Azure 跨平台 CLI 配置日志配置文件
#### <a name="get-existing-log-profile"></a>获取现有的日志配置文件
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name` 属性应为日志配置文件的名称。

#### <a name="add-a-log-profile"></a>添加日志配置文件
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 名称 |是 |日志配置文件的名称。 |
| storageId |否 |应该将活动日志保存到其中的存储帐户的资源 ID。 |
| serviceBusRuleId |否 |服务总线命名空间（需在其中创建事件中心）的服务总线规则 ID。 是以下格式的字符串：`{service bus resource ID}/authorizationrules/{key name}`。 |
| 位置 |是 |要为其收集活动日志事件的逗号分隔区域的列表。 |
| retentionInDays |是 |事件的保留天数，介于 1 到 2147483647 之间。 值为零时，将无限期（永久）存储日志。 |
| categories |否 |应收集的事件类别的逗号分隔列表。 可能值包括：Write、Delete 和 Action。 |

#### <a name="remove-a-log-profile"></a>删除日志配置文件
```
azure insights logprofile delete --name my_log_profile
```

## <a name="next-steps"></a>后续步骤
* [详细了解活动日志（以前称为审核日志）](../azure-resource-manager/resource-group-audit.md)
* [将 Azure 活动日志流式传输到事件中心](monitoring-stream-activity-logs-event-hubs.md)
