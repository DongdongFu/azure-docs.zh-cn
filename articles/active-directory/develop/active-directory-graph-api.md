---
title: "Azure Active Directory 图形 API | Microsoft 文档"
description: "有关可通过 REST API 终结点提供对 Azure AD 的编程访问权限的图形 API 的概述和快速入门指南。"
services: active-directory
documentationcenter: 
author: viv-liu
manager: mbaldwin
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 79bb96171c00fa62f24113800c3824856febe728
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory 图形 API
> [!IMPORTANT]
> 强烈建议使用 [Microsoft Graph](https://graph.microsoft.io/)（而非 Azure AD Graph API）访问 Azure Active Directory 资源。 目前，我们在集中开发 Microsoft Graph，未计划进一步改进 Azure AD Graph API。 Azure AD Graph API 仍可能适用的方案非常有限；有关详细信息，请参阅 Office 开发人员中心的 [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph)（Microsoft Graph 或 Azure AD Graph）博客文章。
> 
> 

Azure Active Directory 图形 API 通过 REST API 终结点提供对 Azure AD 的编程访问权限。 应用程序可以使用图形 API 对目录数据和对象执行创建、读取、更新和删除 (CRUD) 操作。 例如，图形 API 支持对用户对象执行以下常见操作：

* 在目录中创建新用户
* 获取用户的详细属性，如其所属的组
* 更新用户的属性（如其位置和电话号码），或者更改其密码
* 检查用户进行基于角色的访问时使用的组成员身份
* 禁用用户的帐户或完全将其删除

除了用户对象外，还可以对其他对象（如组和应用程序）执行类似操作。 若要对目录调用图形 API，应用程序必须向 Azure AD 注册并配置为允许访问目录。 这通常可以通过用户或管理员同意流来实现。

若要开始使用 Azure Active Directory 图形 API，请参阅[图形 API 快速入门指南](active-directory-graph-api-quickstart.md)，或查看[交互式图形 API 参考文档](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

## <a name="features"></a>功能
图形 API 提供以下功能：

* **REST API 终结点**：图形 API 是一种支持 REST 的服务，该服务由使用标准 HTTP 请求访问的终结点组成。 图形 API 支持对请求和响应使用 XML 或 Javascript 对象表示法 (JSON) 内容类型。 有关详细信息，请参阅 [Azure AD Graph REST API 参考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。
* **向 Azure AD 进行身份验证**：必须在请求的 Authorization 标头中追加 JSON Web 令牌 (JWT)，以便对向图形 API 发出的每个请求进行身份验证。 可通过向 Azure AD 的令牌终结点发出请求并提供有效的凭据来获取此令牌。 可以使用 OAuth 2.0 客户端凭据流或授权代码授予流来获取调用 Graph 所需的令牌。 有关详细信息，请参阅 [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)。
* **基于角色的授权 (RBAC)**：安全组用于在图形 API 中执行 RBAC。 例如，如果要确定用户是否有权访问特定资源，应用程序可以调用[检查组成员身份（可传递）](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive)操作，该操作将返回 true 或 false。
* **差异查询**：如果要查看两个时间段之间对目录所做的更改，而不对图形 API 进行频繁的查询，可以发出差异查询请求。 这种类型的请求将只返回在上一个差异查询请求与当前请求之间所做的更改。 有关详细信息，请参阅 [Azure AD 图形 API 差异查询](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)。
* **目录扩展**：如果要开发需要读取或写入目录对象的唯一属性的应用程序，可以通过图形 API 来注册并使用扩展值。 例如，如果应用程序需要每个用户的 Skype ID 属性，则可以在目录中注册新属性，即可在每个用户对象上获取该属性。 有关详细信息，请参阅 [Azure AD 图形 API 目录架构扩展](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)。
* **受权限范围保护**：AAD 图形 API 公开权限范围，启用对 AAD 数据的安全/同意访问，并支持各种客户端应用类型，包括：
  
  * 具有用户界面的应用，这类应用通过登录用户（委派）授权而获得对数据的委派访问权限
  * 使用服务/守护程序客户端（应用角色）等应用程序定义的基于角色的访问控制的应用
    
    委派和应用角色权限范围都代表图形 API 公开的特权，且客户端应用程序可通过 Azure 门户中的应用程序注册权限[功能](https://portal.azure.com)进行请求。 客户端可以验证为其授予的权限范围，方法是检查委派权限的访问令牌中收到的范围（“scp”）声明，以及应用角色权限的角色（“roles”）声明。 了解有关 [Azure AD 图形 API 权限范围](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)的详细信息。

## <a name="scenarios"></a>方案
图形 API 可实现许多应用程序方案。 以下方案最常见：

* **业务线（单租户）应用程序**：在此方案中，一个企业开发人员为一个拥有 Office 365 订阅的组织工作。 该开发人员要构建一个 Web 应用程序，该应用程序可与 Azure AD 交互以执行为用户分配许可证等任务。 此任务需要访问图形 API，因此该开发人员在 Azure AD 中注册了单租户应用程序，并为图形 API 配置读取和写入权限。 然后，该应用程序被配置为使用自己的凭据或当前登录用户的凭据来获取调用图形 API 所需的令牌。
* **“软件即服务”应用程序（多租户）：**在此方案中，独立软件供应商 (ISV) 要开发一个为使用 Azure AD 的其他组织提供用户管理功能的托管多租户 Web 应用程序。 这些功能需要访问目录对象，因此该应用程序需要调用图形 API。 开发人员在 Azure AD 中注册该应用程序，将它配置为需要对图形 API 的读取和写入权限，然后启用了外部访问，这样其他组织便可以同意在其目录中使用该应用程序。 当其他组织中的用户首次向该应用程序进行身份验证时，他们会看到一个同意对话框，该对话框包含应用程序请求的权限。  然后，授予许可将为该应用程序提供对用户目录中的图形 API 的请求权限。 有关同意框架的详细信息，请参阅[同意框架概述](active-directory-integrating-applications.md)。

## <a name="see-also"></a>另请参阅
[Azure AD 图形 API 快速入门指南](active-directory-graph-api-quickstart.md)

[AD Graph REST 文档](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory 开发人员指南](active-directory-developers-guide.md)

