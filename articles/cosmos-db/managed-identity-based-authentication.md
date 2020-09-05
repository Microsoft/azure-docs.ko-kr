---
title: 시스템 할당 관리 id를 사용 하 여 Azure Cosmos DB 데이터에 액세스 하는 방법
description: Azure Cosmos DB에서 키에 액세스 하는 Azure Active Directory (Azure AD) 시스템 할당 관리 id (관리 서비스 id)를 구성 하는 방법에 대해 알아봅니다.
author: j-patrick
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 25ec74f3638ce857e4472d73a51e45f24c4df5ec
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997730"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>시스템 할당 관리 id를 사용 하 여 Azure Cosmos DB 데이터에 액세스

이 문서에서는 [관리 되는 id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 사용 하 여 Azure Cosmos DB 키에 액세스할 수 있는 *강력 하 고 키 회전* 을 위한 솔루션을 설정 합니다. 이 문서의 예제에서는 Azure Functions를 사용 하지만 관리 되는 id를 지 원하는 모든 서비스를 사용할 수 있습니다. 

Azure Cosmos DB 키를 복사할 필요 없이 Azure Cosmos DB 데이터에 액세스할 수 있는 함수 앱을 만드는 방법을 배웁니다. 함수 앱은 1 분 마다 절전 모드를 해제 하 고 aquarium 물고기 탱크의 현재 온도를 기록 합니다. 타이머 트리거 함수 앱을 설정 하는 방법을 알아보려면 [Azure에서 타이머에 의해 트리거되는 함수 만들기](../azure-functions/functions-create-scheduled-function.md) 문서를 참조 하세요.

시나리오를 단순화 하기 위해 오래 된 온도 문서를 정리 하도록 [ttl (Time To Live](./time-to-live.md) ) 설정이 이미 구성 되어 있습니다. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>함수 앱에 시스템 할당 관리 id 할당

이 단계에서는 시스템 할당 관리 되는 id를 함수 앱에 할당 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure 함수** 창을 열고 함수 앱으로 이동 합니다. 

1. **플랫폼 기능**  >  **id** 탭을 엽니다. 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="함수 앱에 대 한 플랫폼 기능 및 Id 옵션을 보여 주는 스크린샷":::

1. **Id** 탭에서 시스템 id 상태 **를 설정 하** 고 **Status** **저장**을 선택 합니다. **Id** 창은 다음과 같이 표시 됩니다.  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="시스템 id 상태를 설정으로 보여 주는 스크린샷":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Azure Cosmos 계정에 대 한 액세스 권한 부여

이 단계에서는 함수 앱의 시스템 할당 관리 id에 역할을 할당 합니다. Azure Cosmos DB에는 관리 id에 할당할 수 있는 여러 기본 제공 역할이 있습니다. 이 솔루션에는 다음 두 가지 역할을 사용 합니다.

|기본 제공 역할  |Description  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB 계정을 관리할 수 있습니다. 읽기/쓰기 키를 검색할 수 있습니다. |
|[Cosmos DB 계정 독자 역할](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB 계정 데이터를 읽을 수 있음. 읽기 키 검색을 허용 합니다. |

> [!IMPORTANT]
> Azure Cosmos DB의 역할 기반 액세스 제어에 대 한 지원은 제어 평면 작업에만 적용 됩니다. 데이터 평면 작업은 마스터 키 또는 리소스 토큰을 통해 보안이 유지 됩니다. 자세한 내용은 [데이터에 안전 하 게 액세스](secure-access-to-data.md) 문서를 참조 하세요.

> [!TIP] 
> 역할을 할당 하는 경우 필요한 액세스만 할당 합니다. 서비스에서 데이터 읽기만 필요한 경우에는 관리 되는 id에 **Cosmos DB 계정 읽기 권한자** 역할을 할당 합니다. 최소 권한 액세스의 중요도에 대 한 자세한 내용은 [권한 있는 계정에 대 한 낮은 노출](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) 문서를 참조 하세요.

이 시나리오에서 함수 앱은 aquarium의 온도를 읽은 다음 해당 데이터를 Azure Cosmos DB의 컨테이너에 다시 씁니다. 함수 앱은 데이터를 작성 해야 하므로 **DocumentDB 계정 참가자** 역할을 할당 해야 합니다. 

### <a name="assign-the-role-using-azure-portal"></a>Azure Portal를 사용 하 여 역할 할당

1. Azure Portal에 로그인 하 여 Azure Cosmos DB 계정으로 이동 합니다. **액세스 제어 (IAM)** 창을 열고 **역할 할당** 탭을 클릭 합니다.

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="액세스 제어 창과 역할 할당 탭을 보여 주는 스크린샷":::

1. **추가** > **역할 할당 추가**를 선택합니다.

1. **역할 할당 추가** 패널이 오른쪽에 열립니다.

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="역할 할당 추가 창을 보여 주는 스크린샷":::

   * **역할**: **DocumentDB 계정 참가자** 선택
   * 다음에 대 한 **액세스 할당**: **시스템 할당 관리 id 선택** 하위 섹션에서 **함수 앱**을 선택 합니다.
   * **선택**: **관리 되는 시스템 id**를 가진 구독의 모든 함수 앱이 창에 채워집니다. 이 경우 **FishTankTemperatureService** 함수 앱을 선택 합니다. 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="예제로 채워진 역할 할당 추가 창을 보여 주는 스크린샷":::

1. 함수 앱을 선택한 후 **저장**을 선택 합니다.

### <a name="assign-the-role-using-azure-cli"></a>Azure CLI를 사용 하 여 역할 할당

Azure CLI를 사용 하 여 역할을 할당 하려면 Azure Cloud Shell를 열고 다음 명령을 실행 합니다.

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>프로그래밍 방식으로 Azure Cosmos DB 키에 액세스

이제 Azure Cosmos DB 권한에서 **DocumentDB 계정 참가자** 역할을 사용 하는 시스템 할당 관리 id가 있는 함수 앱이 있습니다. 다음 함수 앱 코드는 Azure Cosmos DB 키를 가져오고, CosmosClient 개체를 만들고, aquarium의 온도를 가져온 다음, Azure Cosmos DB에 저장 합니다.

이 샘플에서는 [목록 키 API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) 를 사용 하 여 Azure Cosmos DB 계정 키에 액세스 합니다.

> [!IMPORTANT] 
> [Cosmos DB 계정 읽기 권한자](#grant-access-to-your-azure-cosmos-account) 역할을 할당 하려면 [읽기 전용 키 목록 API](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys)를 사용 해야 합니다. 이렇게 하면 읽기 전용 키만 채워집니다.

목록 키 API는 개체를 반환 합니다 `DatabaseAccountListKeysResult` . 이 형식은 c # 라이브러리에 정의 되어 있지 않습니다. 다음 코드에서는이 클래스의 구현을 보여 줍니다.  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

또한이 예제에서는 다음과 같이 정의 된 "TemperatureRecord" 라는 간단한 문서를 사용 합니다.

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

시스템이 할당 한 관리 되는 id 토큰을 가져오려면 [Microsoft. Azure. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 라이브러리를 사용 합니다. 토큰을 가져오고 라이브러리에 대 한 자세한 정보를 확인 하는 다른 방법을 알아보려면 `Microsoft.Azure.Service.AppAuthentication` [서비스 간 인증](../key-vault/general/service-to-service-authentication.md) 문서를 참조 하세요.


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

이제 [함수 앱을 배포할](../azure-functions/functions-create-first-function-vs-code.md)준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 및 Azure Active Directory를 사용 하는 인증서 기반 인증](certificate-based-authentication.md)
* [Azure Key Vault를 사용 하 여 Azure Cosmos DB 키 보호](access-secrets-from-keyvault.md)
* [Azure Cosmos DB에 대 한 보안 기준](security-baseline.md)
