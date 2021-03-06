---
title: "创建用于 Azure 堆栈的服务主体 |Microsoft 文档"
description: "描述如何创建新的服务主体可以用于基于角色的访问控制 Azure 资源管理器中用来管理对资源的访问。"
services: azure-resource-manager
documentationcenter: na
author: heathl17
manager: byronr
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: helaw
ms.openlocfilehash: 96d5cdfc28759fd516eab5fd97c6cf444af08cf6
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="provide-applications-access-to-azure-stack"></a>提供对 Azure 堆栈的应用程序访问

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

当应用程序需要访问以部署或配置 Azure 堆栈中的资源通过 Azure 资源管理器中时，你将创建服务主体，这是你的应用程序的凭据。  然后可以将委托给该服务主体只有必要的权限。  

例如，您可能必须使用 Azure 资源管理器来清点 Azure 资源的配置管理工具。  在此方案中，你可以创建服务主体、 读取者角色授予该服务主体，并限制到只读访问配置管理工具。 

服务主体要优于运行您自己的凭据下的应用，因为：

* 可以将权限分配给服务主体，有别于你自己的帐户权限。 通常情况下，这些权限仅限于应用需执行的操作。
* 职责变化时，无需更改应用的凭据。
* 执行无人参与的脚本时，可使用证书自动进行身份验证。  

## <a name="getting-started"></a>入门

具体取决于如何部署 Azure 堆栈后，你首先创建服务主体。  本文档将指导你逐步完成创建两个服务主体[Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad)和[Active Directory 联合身份验证 Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs)。  创建服务主体后, 一套常见到 AD FS 和 Azure Active Directory 的步骤是用于[委派权限](azure-stack-create-service-principals.md#assign-role-to-service-principal)到角色。     

## <a name="create-service-principal-for-azure-ad"></a>为 Azure AD 创建服务主体

如果你部署 Azure 堆栈使用 Azure AD 作为标识存储，则可以创建像那样的 Azure 服务主体。  本部分演示如何执行通过门户步骤。  检查你具有[所需的 Azure AD 权限](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)在开始之前。

### <a name="create-service-principal"></a>创建服务主体
在本部分中，你将创建表示你的应用程序的 Azure AD 中的应用程序 （服务主体）。

1. 通过 [Azure 门户](https://portal.azure.com)登录 Azure 帐户。
2. 选择**Azure Active Directory** > **应用程序注册** > **添加**   
3. 为应用提供名称和 URL。 选择“Web 应用/API”或“本机”作为要创建的应用程序的类型。 设置这些值后，选择“创建”。

已为你的应用程序来创建服务主体。

### <a name="get-credentials"></a>获取凭据
如果以编程方式记录，你将使用 ID 为你的应用程序和身份验证密钥。 若要获取这些值，请使用以下步骤：

1. 从 Active Directory 中的“应用注册”，选择应用程序。

2. 复制“应用程序 ID”并将其存储在应用程序代码中。 [示例应用程序](#sample-applications)部分的应用程序引用此值作为客户端 ID。

     ![客户端 ID](./media/azure-stack-create-service-principal/image12.png)
3. 若要生成身份验证密钥，请选择“密钥”。

4. 提供密钥说明和密钥持续时间。 完成后，选择“保存”。

保存密钥后, 会显示密钥的值。 复制此值，因为稍后不能检索密钥。 使用应用程序 ID 进行签名的应用程序提供的密钥的值。 将密钥值存储在应用程序可检索的位置。

![保存的密钥](./media/azure-stack-create-service-principal/image15.png)


完成后，继续执行[分配你的应用程序角色](azure-stack-create-service-principals.md#assign-role-to-service-principal)。

## <a name="create-service-principal-for-ad-fs"></a>为 AD FS 创建服务主体
如果你部署使用 AD FS 的 Azure 堆栈，你可以使用 PowerShell 创建服务主体、 将访问权限，角色分配和从 PowerShell 中使用该标识登录。

从特权终结点 ERCS 虚拟机上运行脚本。


要求：
- 经过认证是必需的。

**参数**

以下信息是必需的作为输入的自动化参数：


|参数|说明|示例|
|---------|---------|---------|
|名称|SPN 帐户名称|MyAPP|
|ClientCertificates|证书对象的数组|X509 证书|
|ClientRedirectUris<br>(可选)|应用程序重定向 URI|         |

**示例**

1. 打开提升的 Windows PowerShell 会话，并运行以下命令：

   > [!NOTE]
   > 此示例创建一个自签名的证书。 在生产部署中运行这些命令时，使用 Get 证书检索你想要使用的证书的证书对象。

   ```
   $creds = Get-Credential

   $session = New-PSSession -ComputerName <IP Address of ECRS> -ConfigurationName PrivilegedEndpoint -Credential $creds

   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=testspn2" -KeySpec KeyExchange

   Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name 'MyApp' -ClientCertificates $using:cert}

   $session|remove-pssession

   ```

2. 自动化完成后，它将显示所需的详细信息，以使用该 SPN。 

   例如：

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```
### <a name="assign-a-role"></a>将角色分配
创建服务主体后，你必须[向角色分配](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>通过 PowerShell 登录
一旦你已分配的角色，你可以登录到 Azure 堆栈的服务主体使用以下命令：

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>将角色分配给服务主体
要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其包含的所有资源。

1. 在 Azure 堆栈门户中，导航到您想要分配到应用程序的作用域的级别。 例如，若要在订阅范围内分配角色，选择“订阅”。 可改为选择资源组或资源。

2. 选择特定订阅（资源组或资源），向其中分配应用程序。

     ![选择进行分配的订阅](./media/azure-stack-create-service-principal/image16.png)

3. 选择“访问控制 (IAM)”。

     ![选择访问权限](./media/azure-stack-create-service-principal/image17.png)

4. 选择“添加”。

5. 选择要分配到应用程序的角色。

6. 搜索你的应用程序，并选择它。

7. 选择“确定”完成角色分配。 该应用程序会显示在分配到该范围角色的用户列表中。

既然你已经创建服务主体，并分配的角色，你可以开始使用此应用程序中访问 Azure 堆栈的资源。  

## <a name="next-steps"></a>后续步骤

[将用户添加为 ADFS](azure-stack-add-users-adfs.md)
[管理用户权限](azure-stack-manage-permissions.md)