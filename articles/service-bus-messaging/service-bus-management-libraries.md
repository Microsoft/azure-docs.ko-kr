---
title: Azure Service Bus 관리 라이브러리 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 관리 라이브러리를 사용 하 여 Service Bus 네임 스페이스 및 엔터티를 동적으로 프로 비전 하는 방법을 설명 합니다.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 915606bffc2037c8fcd1a7d33218143f40c78f2c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008049"
---
# <a name="service-bus-management-libraries"></a>Service Bus 관리 라이브러리

Azure Service Bus 관리 라이브러리는 Service Bus 네임스페이스 및 엔터티를 동적으로 프로비전할 수 있습니다. 이를 통해 복잡한 배포 및 메시지 시나리오가 가능하며, 어떤 엔터티를 프로비전할 것인지 프로그래밍 방식으로 결정할 수 있습니다. 이러한 라이브러리는 현재 .NET에서 사용할 수 있습니다.

## <a name="supported-functionality"></a>지원되는 기능

* 네임스페이스 만들기, 업데이트, 삭제
* 큐 만들기, 업데이트, 삭제
* 토픽 만들기, 업데이트, 삭제
* 구독 만들기, 업데이트, 삭제

## <a name="prerequisites"></a>필수 구성 요소

Service Bus 관리 라이브러리 사용을 시작하려면 Azure AD(Azure Active Directory) 서비스로 인증해야 합니다. Azure AD를 사용하려면 Azure 리소스에 대한 액세스를 제공하는 서비스 주체로 인증해야 합니다. 서비스 주체 만들기에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.  

* [Azure Portal를 사용 하 여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체를 만듭니다.](../active-directory/develop/howto-create-service-principal-portal.md)
* [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

이러한 자습서는 관리 라이브러리를 통해 인증에 사용되는 `AppId`(클라이언트 ID), `TenantId` 및 `ClientSecret`(인증 키)를 제공합니다. 실행 하려는 리소스 그룹에 대 한 [**Azure Service Bus 이상의 데이터 소유자**](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 또는 [**참가자**](../role-based-access-control/built-in-roles.md#contributor) 권한이 있어야 합니다.

## <a name="programming-pattern"></a>프로그래밍 패턴

Service Bus 리소스를 조작하는 패턴은 일반 프로토콜을 따릅니다.

1. **Microsoft.IdentityModel.Clients.ActiveDirectory** 라이브러리를 사용하여 Azure AD에서 토큰을 가져옵니다.
   ```csharp
   var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

   var result = await context.AcquireTokenAsync("https://management.azure.com/", new ClientCredential(clientId, clientSecret));
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

## <a name="complete-code-to-create-a-queue"></a>큐를 만들기 위한 전체 코드
Service Bus 큐를 만드는 전체 코드는 다음과 같습니다. 

```csharp
using System;
using System.Threading.Tasks;

using Microsoft.Azure.Management.ServiceBus;
using Microsoft.Azure.Management.ServiceBus.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;

namespace SBusADApp
{
    class Program
    {
        static void Main(string[] args)
        {
            CreateQueue().GetAwaiter().GetResult();
        }

        private static async Task CreateQueue()
        {
            try
            {
                var subscriptionID = "<SUBSCRIPTION ID>";
                var resourceGroupName = "<RESOURCE GROUP NAME>";
                var namespaceName = "<SERVICE BUS NAMESPACE NAME>";
                var queueName = "<NAME OF QUEUE YOU WANT TO CREATE>";

                var token = await GetToken();

                var creds = new TokenCredentials(token);
                var sbClient = new ServiceBusManagementClient(creds)
                {
                    SubscriptionId = subscriptionID,
                };

                var queueParams = new SBQueue();

                Console.WriteLine("Creating queue...");
                await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, queueName, queueParams);
                Console.WriteLine("Created queue successfully.");
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not create a queue...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

        private static async Task<string> GetToken()
        {
            try
            {
                var tenantId = "<AZURE AD TENANT ID>";
                var clientId = "<APPLICATION/CLIENT ID>";
                var clientSecret = "<CLIENT SECRET>";

                var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

                var result = await context.AcquireTokenAsync(
                    "https://management.azure.com/",
                    new ClientCredential(clientId, clientSecret)
                );

                // If the token isn't a valid string, throw an error.
                if (string.IsNullOrEmpty(result.AccessToken))
                {
                    throw new Exception("Token result is empty!");
                }

                return result.AccessToken;
            }
            catch (Exception e)
            {
                Console.WriteLine("Could not get a token...");
                Console.WriteLine(e.Message);
                throw e;
            }
        }

    }
}
```

> [!IMPORTANT]
> 전체 예제는 [GitHub의 .net 관리 샘플](https://github.com/Azure-Samples/service-bus-dotnet-management/)을 참조 하세요. 

## <a name="next-steps"></a>다음 단계
[Microsoft.Azure.Management.ServiceBus API 참조](/dotnet/api/Microsoft.Azure.Management.ServiceBus)