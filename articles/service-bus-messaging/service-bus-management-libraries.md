---
title: Azure Service Bus 관리 라이브러리 | Microsoft Docs
description: .NET에서 Service Bus 네임스페이스 및 메시징 엔터티 관리
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: d6c2cd813e96b40fc9f95785a1fd28e324a0437b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315881"
---
# <a name="service-bus-management-libraries"></a>Service Bus 관리 라이브러리

Azure Service Bus 관리 라이브러리는 Service Bus 네임스페이스 및 엔터티를 동적으로 프로비전할 수 있습니다. 이를 통해 복잡한 배포 및 메시지 시나리오가 가능하며, 어떤 엔터티를 프로비전할 것인지 프로그래밍 방식으로 결정할 수 있습니다. 이러한 라이브러리는 현재 .NET에서 사용할 수 있습니다.

## <a name="supported-functionality"></a>지원되는 기능

* 네임스페이스 만들기, 업데이트, 삭제
* 큐 만들기, 업데이트, 삭제
* 토픽 만들기, 업데이트, 삭제
* 구독 만들기, 업데이트, 삭제

## <a name="prerequisites"></a>필수 조건

Service Bus 관리 라이브러리 사용을 시작하려면 Azure AD(Azure Active Directory) 서비스로 인증해야 합니다. Azure AD를 사용하려면 Azure 리소스에 대한 액세스를 제공하는 서비스 주체로 인증해야 합니다. 서비스 주체 만들기에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.  

* [Azure Portal을 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

이러한 자습서는 관리 라이브러리를 통해 인증에 사용되는 `AppId`(클라이언트 ID), `TenantId` 및 `ClientSecret`(인증 키)를 제공합니다. 실행하려는 리소스 그룹에 대한 **소유자** 권한이 있어야 합니다.

## <a name="programming-pattern"></a>프로그래밍 패턴

Service Bus 리소스를 조작하는 패턴은 일반 프로토콜을 따릅니다.

1. **Microsoft.IdentityModel.Clients.ActiveDirectory** 라이브러리를 사용하여 Azure AD에서 토큰을 가져옵니다.
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.core.windows.net/", new ClientCredential(clientId, clientSecret));
   ```
2. `ServiceBusManagementClient` 개체를 만듭니다.

   ```csharp
   var creds = new TokenCredentials(token);
   var sbClient = new ServiceBusManagementClient(creds)
   {
       SubscriptionId = SettingsCache["SubscriptionId"]
   };
   ```
3. `CreateOrUpdate` 매개 변수를 지정된 값으로 설정합니다.

   ```csharp
   var queueParams = new QueueCreateOrUpdateParameters()
   {
       Location = SettingsCache["DataCenterLocation"],
       EnablePartitioning = true
   };
   ```
4. 호출을 실행합니다.

   ```csharp
   await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
   ```

## <a name="next-steps"></a>다음 단계

* [.NET 관리 샘플](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.Management.ServiceBus API 참조](/dotnet/api/Microsoft.Azure.Management.ServiceBus)
