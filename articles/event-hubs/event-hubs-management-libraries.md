---
title: 관리 라이브러리 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 .NET에서 Azure Event Hubs 네임스페이스 및 엔터티를 관리하는 데 사용할 수 있는 라이브러리에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 74392fbf0b2c0b81898410af8027a4f13fc52b67
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013999"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs 관리 라이브러리

Azure Event Hubs 관리 라이브러리를 사용하여 Event Hubs 네임스페이스 및 엔터티를 동적으로 프로비전할 수 있습니다. 이 동적 특성을 통해 복잡한 배포 및 메시지 시나리오가 가능하며, 어떤 엔터티를 프로비전할 것인지 프로그래밍 방식으로 결정할 수 있습니다. 이러한 라이브러리는 현재 .NET에서 사용할 수 있습니다.

## <a name="supported-functionality"></a>지원되는 기능

* 네임스페이스 만들기, 업데이트, 삭제
* Event Hubs 만들기, 업데이트, 삭제
* 소비자 그룹 만들기, 업데이트, 삭제

## <a name="prerequisites"></a>필수 구성 요소

Event Hubs 관리 라이브러리 사용을 시작하려면 AAD(Azure Active Directory)로 인증해야 합니다. AAD를 사용하려면 Azure 리소스에 대한 액세스를 제공하는 서비스 주체로 인증해야 합니다. 서비스 주체 만들기에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.  

* [Azure Portal를 사용 하 여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체를 만듭니다.](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)

이러한 자습서는 관리 라이브러리를 통해 인증에 사용되는 `AppId`(클라이언트 ID), `TenantId` 및 `ClientSecret`(인증 키)를 제공합니다. 실행하려는 리소스 그룹에 대한 **소유자** 권한이 있어야 합니다.

## <a name="programming-pattern"></a>프로그래밍 패턴

Event Hubs 리소스를 조작하는 패턴은 일반 프로토콜을 따릅니다.

1. `Microsoft.IdentityModel.Clients.ActiveDirectory` 라이브러리를 사용하여 AAD에서 토큰을 가져옵니다.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. `EventHubManagementClient` 개체를 만듭니다.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. `CreateOrUpdate` 매개 변수를 지정된 값으로 설정합니다.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. 호출을 실행합니다.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>다음 단계
* [.NET 관리 샘플](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub 참조](/dotnet/api/Microsoft.Azure.Management.EventHub) 
