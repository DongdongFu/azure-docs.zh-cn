---
title: "创建 Azure 时序见解环境 | Microsoft Docs"
description: "本教程介绍如何创建时序见解环境，将其连接到事件源，然后即可在数分钟内分析事件数据。"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2017
ms.author: omravi
ms.openlocfilehash: d8a3f79630868c83cd9fde6ea0c414e334a58e22
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>在 Azure 门户中创建新的时序见解环境

时序见解环境是一种 Azure 资源，具有入口容量和存储容量。 客户通过 Azure 门户使用所需容量对环境进行预配。

## <a name="steps-to-create-the-environment"></a>创建环境的步骤

请按以下步骤创建环境：

1.  登录到 [Azure 门户](https://portal.azure.com)。
2.  单击左上角的加号（“+”）。
3.  在搜索框中搜索“时序见解”。

  ![创建时序见解环境](media/get-started/getstarted-create-environment1.png)

4.  选择“时序见解”，并单击“创建”。

  ![创建时序见解资源组](media/get-started/getstarted-create-environment2.png)

5.  指定环境名称。 在[时序资源管理器](https://insights.timeseries.azure.com)中，该名称将代表环境。
6.  选择一个订阅。 包含一个包含事件源的订阅。 时序见解可以自动检测存在于同一订阅中的 Azure IoT 中心和事件中心资源。
7.  选择或创建资源组。 资源组是结合使用的 Azure 资源的集合。
8.  选择托管位置。 若要避免跨数据中心移动数据，请选择包含事件源的位置。
9.  选择定价层。
10. 选择容量。 可以在创建环境后更改其容量。
11. 创建环境。 也可将环境固定到仪表板，方便每次登录时的访问。

  ![创建时序见解 固定到仪表板](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>后续步骤

* 在[时序见解门户](https://insights.timeseries.azure.com)中[定义数据访问策略](time-series-insights-data-access.md)，以便访问环境
* [创建事件源](time-series-insights-add-event-source.md)
* [发送事件](time-series-insights-send-events.md)到事件源
