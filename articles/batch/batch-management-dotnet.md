---
title: .NET용 클라이언트 라이브러리를 사용하여 계정 리소스 관리 - Azure Batch | Microsoft Docs
description: Batch 관리 .NET 라이브러리로 Azure Batch 계정 리소스를 만들고, 삭제하며, 수정합니다.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 301a3f9a500c41cf13dfa071d3526d2128b5e131
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775137"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>.NET용 Batch 관리 클라이언트 라이브러리를 사용하여 Batch 계정 및 할당량 관리

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch 관리 .NET](batch-management-dotnet.md)
> 
> 

[Batch 관리 .NET][api_mgmt_net] 라이브러리를 사용하여 Batch 계정 만들기, 삭제, 키 관리 및 할당량 검색을 자동화하므로 Azure Batch 애플리케이션에서 유지 관리 오버헤드를 낮출 수 있습니다.

* **Batch 계정을 만들고 삭제** 합니다. 예를 들어 ISV(독립 소프트웨어 공급업체)가 대금 청구를 위해 각각 별도의 Batch 계정에 할당되는 클라이언트용 서비스를 제공하는 경우 고객 포털에 계정 만들기 및 삭제 기능을 추가할 수 있습니다.
* **계정 키를 검색하고 다시 생성** 합니다. 이렇게 하면 주기적인 롤오버 또는 계정 키 만료를 적용하는 보안 정책을 준수할 수 있습니다. 다양한 Azure 영역에 여러 Batch 계정이 있는 경우 롤오버 프로세스를 자동화하면 솔루션의 효율성이 높아집니다.
* **계정 할당량을 확인** 하고 어떤 Batch 계정에 어떤 제한이 있는지를 확인하는 데 시행 착오 추측을 배제합니다. 작업을 시작하기 전에 계정 할당량을 확인하거나 풀을 만들거나 계산 노드를 추가함으로써 이러한 계산 리소스가 만들어지는 위치 또는 시기를 능동적으로 조정할 수 있습니다. 해당 계정에 추가 리소스를 할당하기 전에 할당량 증가가 필요한 계정을 확인할 수 있습니다.
* Batch 관리 .NET, [Azure Active Directory][aad_about] 및 [Azure Resource Manager][resman_overview]를 동일한 애플리케이션에서 함께 사용하고 **다른 Azure 서비스의 기능을 결합**하여 모든 기능을 갖춘 관리 환경을 제공합니다. 이러한 기능과 해당 API를 사용하여 원활한 인증 환경, 리소스 그룹을 만들고 삭제하는 기능 및 종단 간 관리 솔루션에 대해 위에 설명된 기능을 제공할 수 있습니다.

> [!NOTE]
> 이 문서에서 Batch 계정, 키 및 할당량을 프로그래밍 방식으로 관리하는 방법에 대해 주로 설명하는 동안 [Azure Portal][azure_portal]을 사용하여 이러한 다양한 작업을 수행할 수 있습니다. 자세한 내용은 [Azure Portal에서 Azure Batch 계정 만들기](batch-account-create-portal.md) 및 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Batch 계정을 만들고 삭제
위에서 설명한 대로 Batch 관리 API의 주요 기능은 Azure 지역에서 Batch 계정을 만들고 삭제하는 것입니다. 이렇게 하려면 [BatchManagementClient.Account.CreateAsync][net_create] 및 [DeleteAsync][net_delete] 또는 해당 동기 항목을 사용합니다.

다음 코드 조각은 계정을 만들고 Batch 서비스에서 새로 만든 계정을 가져온 후 삭제합니다. 이 코드 조각과 이 문서의 다른 코드 조각에서 `batchManagementClient`는 완전히 초기화된 [BatchManagementClient][net_mgmt_client] 인스턴스입니다.

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
> 배치 관리 .NET 라이브러리 및 해당 BatchManagementClient 클래스를 사용하는 애플리케이션에는 관리할 배치 계정을 소유하고 있는 구독에 대한 **서비스 관리자** 또는 **공동 관리자** 액세스 권한이 필요합니다. 자세한 내용은 Azure Active Directory 섹션과 [AccountManagement][acct_mgmt_sample] 코드 샘플을 참조하세요.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>계정 키를 검색하고 다시 생성
[ListKeysAsync][net_list_keys]를 사용하여 구독 내 Batch 계정에서 기본 및 보조 계정 키를 가져옵니다. [RegenerateKeyAsync][net_regenerate_keys]를 사용하여 해당 키를 다시 생성할 수 있습니다.

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
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
> 관리 애플리케이션에 대한 간소화된 연결 워크플로를 만들 수 있습니다. 먼저 [ListKeysAsync][net_list_keys]를 사용하여 관리하려는 Batch 계정에 대한 계정 키를 가져옵니다. 그런 다음 [BatchClient][net_batch_client]를 초기화할 때 사용되는 배치 .NET 라이브러리의 [BatchSharedKeyCredentials][net_sharedkeycred] 클래스를 초기화할 때 이 키를 사용합니다.
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure 구독 및 Batch 계정 할당량 확인
Azure 구독 및 Batch와 같은 개별 Azure 서비스는 모두 포함되는 특정 엔터티 수를 제한하는 기본 할당량이 있습니다. Azure 구독에 대한 기본 할당량의 경우 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요. Batch 서비스의 기본 할당량의 경우 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요. Batch 관리 .NET 라이브러리를 사용하여 애플리케이션에서 이러한 할당량을 확인할 수 있습니다. 계정 또는 풀과 같은 계산 리소스 및 계산 노드를 추가하기 전에 할당 결정을 내릴 수 있습니다.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Azure 구독에서 Batch 계정 할당량 확인
지역에 Batch 계정을 만들기 전에 Azure 구독에서 해당 지역에 계정을 추가할 수 있는지 여부를 확인할 수 있습니다.

아래 코드 조각에서 먼저 [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts]를 사용하여 구독 내에서 모든 배치 계정의 컬렉션을 가져옵니다. 이 컬렉션을 가져온 후 대상 영역의 계정 수를 결정합니다. 그런 다음 [BatchManagementClient.Subscriptions][net_mgmt_subscriptions]를 사용하여 배치 계정 할당량을 가져오고 해당 지역에서 얼마나 많은 계정(있는 경우)을 만들 수 있는지 결정합니다.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

위의 코드 조각에서 `creds`는 [TokenCloudCredentials][azure_tokencreds]의 인스턴스입니다. 이 개체를 만드는 예제를 보려면 GitHub에서 [AccountManagement][acct_mgmt_sample] 코드 샘플을 참조하세요.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Batch 계정에서 계산 리소스 할당량 확인
Batch 솔루션에서 계산 리소스를 늘리기 전에 할당할 리소스가 해당 계정의 할당량을 초과하지 않는지 확인할 수 있습니다. 아래 코드 조각에서는 `mybatchaccount`라는 Batch 계정에 대한 할당량 정보를 간단히 출력합니다. 하지만 애플리케이션에서 이러한 정보를 사용하여 만들려는 추가 리소스를 계정에서 처리할 수 있는지 여부를 확인할 수 있습니다.

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
> Azure 구독 및 서비스에 기본 할당량이 있기는 하지만 [Azure Portal][azure_portal]에서 요청을 실행하여 이러한 여러 제한을 늘릴 수 있습니다. 예를 들어 Batch 계정 할당량을 늘리는 방법에 대한 지침은 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md) 을 참조하세요.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Batch Management .NET을 통한 Azure AD 사용

Batch Management .NET 라이브러리는 Azure 리소스 공급자 클라이언트이며 [Azure Resource Manager][resman_overview]와 함께 프로그래밍 방식으로 계정 리소스를 관리하는 데 사용됩니다. Azure AD는 Batch Management .NET 라이브러리를 비롯한 Azure 리소스 공급자 클라이언트 및 [Azure Resource Manager][resman_overview]를 통해 만들어지는 요청을 인증하는 데 필요합니다. Batch Management .NET을 통한 Azure AD 사용에 대한 자세한 내용은 [Azure Active Directory를 사용한 Batch 솔루션 인증](batch-aad-auth.md)을 참조하세요. 

## <a name="sample-project-on-github"></a>GitHub에서 샘플 프로젝트

실제로 사용 중인 Batch 관리 .NET을 확인하려면 GitHub의 [AccountManagement][acct_mgmt_sample] 샘플 프로젝트를 참조하세요. AccountManagement 샘플 애플리케이션은 다음 작업을 보여줍니다.

1. [ADAL][aad_adal]을 사용하여 Azure AD에서 보안 토큰을 획득합니다. 사용자가 아직 로그인하지 않은 경우 Azure 자격 증명을 요구하는 메시지가 표시됩니다.
2. Azure AD에서 획득한 보안 토큰을 사용하여 [SubscriptionClient][resman_subclient]를 만들고 Azure에서 해당 계정과 연결된 구독 목록을 쿼리합니다. 목록에 둘 이상의 구독이 포함되어 있는 경우 사용자가 구독을 선택할 수 있습니다.
3. 선택한 구독에 연결된 자격 증명을 가져옵니다.
4. 자격 증명을 사용하여 [ResourceManagementClient][resman_client] 개체를 만듭니다.
5. [ResourceManagementClient][resman_client] 개체를 사용하여 리소스 그룹을 만듭니다.
6. [BatchManagementClient][net_mgmt_client] 개체를 사용하여 여러 가지 배치 계정 작업을 수행합니다.
   * 새 리소스 그룹에 Batch 계정을 만듭니다.
   * Batch 서비스에서 새로 만든 계정을 가져옵니다.
   * 새 계정에 대한 계정 키를 인쇄합니다.
   * 계정에 대한 새 기본 키를 다시 생성합니다.
   * 계정에 대한 할당량 정보를 인쇄합니다.
   * 구독에 대한 할당량 정보를 인쇄합니다.
   * 구독 내에서 모든 계정을 인쇄합니다.
   * 새로 만든 계정을 삭제합니다.
7. 해당 리소스 그룹을 삭제합니다.

새로 만든 Batch 계정 및 리소스 그룹을 삭제하기 전에 [Azure Portal][azure_portal]에서 볼 수 있습니다.

샘플 애플리케이션을 실행하려면 먼저 Azure Portal의 Azure AD 테넌트에 애플리케이션을 등록하고 Azure Resource Manager API에 권한을 부여해야 합니다. [Active Directory를 사용하여 Batch Management 솔루션 인증](batch-aad-auth-management.md)에 제공된 단계를 수행합니다.


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Azure Active Directory란?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Azure AD의 인증 시나리오"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Azure Active Directory와 애플리케이션 통합"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
