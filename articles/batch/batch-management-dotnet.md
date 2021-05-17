---
title: Batch 관리 .NET 라이브러리를 사용하여 계정 리소스 관리
description: Batch 관리 .NET 라이브러리로 Azure Batch 계정 리소스를 만들고, 삭제하며, 수정합니다.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896734"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>.NET용 Batch 관리 클라이언트 라이브러리를 사용하여 Batch 계정 및 할당량 관리

[Batch 관리 .NET](/dotnet/api/overview/azure/batch) 라이브러리를 사용하여 Batch 계정 만들기, 삭제, 키 관리 및 할당량 검색을 자동화하므로 Azure Batch 애플리케이션에서 유지 관리 오버헤드를 낮출 수 있습니다.

- **Batch 계정을 만들고 삭제** 합니다. 예를 들어 ISV(독립 소프트웨어 공급업체)가 대금 청구를 위해 각각 별도의 Batch 계정에 할당되는 클라이언트용 서비스를 제공하는 경우 고객 포털에 계정 만들기 및 삭제 기능을 추가할 수 있습니다.
- **계정 키를 검색하고 다시 생성** 합니다. 이렇게 하면 주기적인 롤오버 또는 계정 키 만료를 적용하는 보안 정책을 준수할 수 있습니다. 다양한 Azure 영역에 여러 Batch 계정이 있는 경우 롤오버 프로세스를 자동화하면 솔루션의 효율성이 높아집니다.
- **계정 할당량을 확인** 하고 어떤 Batch 계정에 어떤 제한이 있는지를 확인하는 데 시행 착오 추측을 배제합니다. 작업을 시작하기 전에 계정 할당량을 확인하거나 풀을 만들거나 컴퓨팅 노드를 추가함으로써 이러한 컴퓨팅 리소스가 만들어지는 위치 또는 시기를 능동적으로 조정할 수 있습니다. 해당 계정에 추가 리소스를 할당하기 전에 할당량 증가가 필요한 계정을 확인할 수 있습니다.
- Batch 관리 .NET, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 및 [Azure Resource Manager](../azure-resource-manager/management/overview.md)를 동일한 애플리케이션에서 함께 사용하고 **다른 Azure 서비스의 기능을 결합** 하여 모든 기능을 갖춘 관리 환경을 제공합니다. 이러한 기능과 해당 API를 사용하여 원활한 인증 환경, 리소스 그룹을 만들고 삭제하는 기능 및 엔드투엔드 관리 솔루션에 대해 위에 설명된 기능을 제공할 수 있습니다.

> [!NOTE]
> 이 문서에서는 Batch 계정, 키 및 할당량을 프로그래밍 방식으로 관리하는 방법에 대해 주로 설명하는 동안 [Azure Portal](batch-account-create-portal.md)을 사용하여 이러한 다양한 작업을 수행할 수도 있습니다.

## <a name="create-and-delete-batch-accounts"></a>Batch 계정을 만들고 삭제

Batch 관리 API의 주요 기능은 Azure 지역에서 [Batch 계정](accounts.md)을 만들고 삭제하는 것입니다. 이렇게 하려면 [BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) 및 [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync) 또는 해당 동기 항목을 사용합니다.

다음 코드 조각은 계정을 만들고 Batch 서비스에서 새로 만든 계정을 가져온 후 삭제합니다. 이 코드 조각과 이 문서의 다른 코드 조각에서 `batchManagementClient`는 완전히 초기화된 [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) 인스턴스입니다.

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> 배치 관리 .NET 라이브러리 및 해당 BatchManagementClient 클래스를 사용하는 애플리케이션에는 관리할 배치 계정을 소유하고 있는 구독에 대한 서비스 관리자 또는 공동 관리자 액세스 권한이 필요합니다. 자세한 내용은 Azure Active Directory 섹션과 [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) 코드 샘플을 참조하세요.

## <a name="retrieve-and-regenerate-account-keys"></a>계정 키를 검색하고 다시 생성

[GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)를 사용하여 구독 내 Batch 계정에서 기본 및 보조 계정 키를 가져옵니다. [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync)를 사용하여 해당 키를 다시 생성할 수 있습니다.

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> 관리 애플리케이션에 대한 간소화된 연결 워크플로를 만들 수 있습니다. 먼저 [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync)를 사용하여 관리하려는 Batch 계정에 대한 계정 키를 가져옵니다. 그런 다음, [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)를 초기화할 때 사용되는 배치 .NET 라이브러리의 [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) 클래스를 초기화할 때 이 키를 사용합니다.

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure 구독 및 Batch 계정 할당량 확인

Azure 구독 및 Batch와 같은 개별 Azure 서비스는 모두 포함되는 특정 엔터티 수를 제한하는 기본 할당량이 있습니다. Azure 구독에 대한 기본 할당량의 경우 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요. Batch 서비스의 기본 할당량의 경우 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요. Batch 관리 .NET 라이브러리를 사용하여 애플리케이션에서 이러한 할당량을 확인할 수 있습니다. 계정 또는 풀과 같은 컴퓨팅 리소스 및 컴퓨팅 노드를 추가하기 전에 할당 결정을 내릴 수 있습니다.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Azure 구독에서 Batch 계정 할당량 확인

지역에 Batch 계정을 만들기 전에 Azure 구독에서 해당 지역에 계정을 추가할 수 있는지 여부를 확인할 수 있습니다.

아래 코드 조각에서 먼저 **ListAsync** 를 사용하여 구독 내에서 모든 배치 계정의 컬렉션을 가져옵니다. 이 컬렉션을 가져온 후 대상 영역의 계정 수를 결정합니다. 그런 다음, **GetQuotasAsync** 를 사용하여 배치 계정 할당량을 가져오고 해당 지역에서 얼마나 많은 계정(있는 경우)을 만들 수 있는지 결정합니다.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

위의 코드 조각에서 `creds`는 **TokenCredentials** 의 인스턴스입니다. 이 개체를 만드는 예제를 보려면 GitHub에서 [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) 코드 샘플을 참조하세요.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Batch 계정에서 컴퓨팅 리소스 할당량 확인

Batch 솔루션에서 컴퓨팅 리소스를 늘리기 전에 할당할 리소스가 해당 계정의 할당량을 초과하지 않는지 확인할 수 있습니다. 아래 코드 조각에서는 `mybatchaccount`라는 Batch 계정에 대한 할당량 정보를 간단히 출력합니다. 하지만 애플리케이션에서 이러한 정보를 사용하여 만들려는 추가 리소스를 계정에서 처리할 수 있는지 여부를 확인할 수 있습니다.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Azure 구독 및 서비스에 기본 할당량이 있기는 하지만 [Azure Portal에서 할당량 늘리기를 요청하여](batch-quota-limit.md#increase-a-quota) 이러한 여러 제한을 늘릴 수 있습니다.

## <a name="use-azure-ad-with-batch-management-net"></a>Batch Management .NET을 통한 Azure AD 사용

Batch Management .NET 라이브러리는 Azure 리소스 공급자 클라이언트이며 [Azure Resource Manager](../azure-resource-manager/management/overview.md)와 함께 프로그래밍 방식으로 계정 리소스를 관리하는 데 사용됩니다. Azure AD는 Batch Management .NET 라이브러리를 비롯한 Azure 리소스 공급자 클라이언트 및 Azure Resource Manager를 통해 만들어지는 요청을 인증하는 데 필요합니다. Batch Management .NET을 통한 Azure AD 사용에 대한 자세한 내용은 [Azure Active Directory를 사용한 Batch 솔루션 인증](batch-aad-auth.md)을 참조하세요.

## <a name="sample-project-on-github"></a>GitHub에서 샘플 프로젝트

실제로 사용 중인 Batch 관리 .NET을 확인하려면 GitHub의 [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) 샘플 프로젝트를 참조하세요. AccountManagement 샘플 애플리케이션은 다음 작업을 보여줍니다.

1. [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md)을 사용하여 Azure AD에서 보안 토큰을 획득합니다. 사용자가 아직 로그인하지 않은 경우 Azure 자격 증명을 요구하는 메시지가 표시됩니다.
2. Azure AD에서 획득한 보안 토큰을 사용하여 [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient)를 만들고 Azure에서 해당 계정과 연결된 구독 목록을 쿼리합니다. 목록에 둘 이상의 구독이 포함되어 있는 경우 사용자가 구독을 선택할 수 있습니다.
3. 선택한 구독에 연결된 자격 증명을 가져옵니다.
4. 자격 증명을 사용하여 [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) 개체를 만듭니다.
5. [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) 개체를 사용하여 리소스 그룹을 만듭니다.
6. [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) 개체를 사용하여 여러 가지 배치 계정 작업을 수행합니다.
   - 새 리소스 그룹에 Batch 계정을 만듭니다.
   - Batch 서비스에서 새로 만든 계정을 가져옵니다.
   - 새 계정에 대한 계정 키를 인쇄합니다.
   - 계정에 대한 새 기본 키를 다시 생성합니다.
   - 계정에 대한 할당량 정보를 인쇄합니다.
   - 구독에 대한 할당량 정보를 인쇄합니다.
   - 구독 내에서 모든 계정을 인쇄합니다.
   - 새로 만든 계정을 삭제합니다.
7. 해당 리소스 그룹을 삭제합니다.

샘플 애플리케이션을 실행하려면 먼저 Azure Portal의 Azure AD 테넌트에 애플리케이션을 등록하고 Azure Resource Manager API에 권한을 부여해야 합니다. [Active Directory를 사용하여 Batch Management 솔루션 인증](batch-aad-auth-management.md)에 제공된 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Batch .NET 클라이언트 라이브러리](quick-run-dotnet.md) 또는 [Python](quick-run-python.md)을 사용하여 Batch 지원 애플리케이션 개발에 대한 기본 사항을 알아봅니다. 이러한 빠른 시작에서는 Batch 서비스를 사용하여 여러 컴퓨팅 노드에서 워크로드를 실행하는 애플리케이션 예제를 단계별로 안내하며, Azure Storage를 사용하여 워크로드 파일을 준비하고 검색하는 방법을 설명합니다.
