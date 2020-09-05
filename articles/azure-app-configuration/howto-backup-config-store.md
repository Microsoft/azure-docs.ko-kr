---
title: Azure 앱 구성 저장소에서 키-값 자동 백업
description: 앱 구성 저장소 간에 키-값의 자동 백업을 설정 하는 방법에 대해 알아봅니다.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: a3c1699dd4b7b828c7dc652f14f431878f785061
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207142"
---
# <a name="back-up-app-configuration-stores-automatically"></a>앱 구성 저장소 자동 백업

이 문서에서는 기본 Azure 앱 구성 저장소에서 보조 저장소로 키-값의 자동 백업을 설정 하는 방법에 대해 알아봅니다. 자동 백업은 앱 구성과 Azure Event Grid 통합을 사용 합니다. 

자동 백업을 설정한 후에는 앱 구성이 구성 저장소에서 키 값에 대 한 변경 내용에 대 한 Azure Event Grid에 이벤트를 게시 합니다. Event Grid은 사용자가 키-값이 생성, 업데이트 또는 삭제 될 때마다 발생 하는 이벤트를 구독할 수 있는 다양 한 Azure 서비스를 지원 합니다.

## <a name="overview"></a>개요

이 문서에서는 Azure Queue storage를 사용 하 여 Event Grid에서 이벤트를 수신 하 고 Azure Functions의 타이머 트리거를 사용 하 여 큐의 이벤트를 일괄 처리로 처리 합니다. 

이벤트에 따라 함수를 트리거하면 기본 앱 구성 저장소에서 변경 된 키의 최신 값을 페치 하 고 그에 따라 보조 저장소를 업데이트 합니다. 이 설정을 사용 하면 하나의 백업 작업에서 짧은 기간에 발생 하는 여러 변경 내용을 결합 하 여 앱 구성 저장소에 대 한 과도 한 요청을 방지할 수 있습니다.

![앱 구성 저장소 백업의 아키텍처를 보여 주는 다이어그램입니다.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>리소스 프로비저닝

앱 구성 저장소를 백업 하는 경우의 동기는 여러 Azure 지역에서 여러 구성 저장소를 사용 하 여 응용 프로그램의 지리적 복원 력을 높이는 것입니다. 이를 위해 기본 및 보조 저장소는 서로 다른 Azure 지역에 있어야 합니다. 이 자습서에서 만든 다른 모든 리소스는 원하는 모든 지역에서 프로 비전 할 수 있습니다. 주 지역이 다운 된 경우 주 지역에 다시 액세스할 수 있을 때까지 백업할 새 항목이 없다는 것입니다.

이 자습서에서는 지역 `centralus` 및 해당 지역의 다른 모든 리소스에 보조 저장소를 만듭니다 `westus` .

## <a name="prerequisites"></a>필수 구성 요소

- 동작합니다. [체험 계정 만들기](https://azure.microsoft.com/free/) 
- Azure 개발 워크 로드가 포함 된 [Visual Studio 2019](https://visualstudio.microsoft.com/vs) .
- [.NET Core SDK](https://dotnet.microsoft.com/download)
- 최신 버전의 Azure CLI (2.3.1 이상) 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. Azure CLI 사용 하는 경우 먼저를 사용 하 여 로그인 해야 합니다 `az login` . 필요에 따라 Azure Cloud Shell를 사용할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 `westus` 위치에 `<resource_group_name>`이라는 리소스 그룹을 만듭니다.  `<resource_group_name>`을 리소스 그룹의 고유한 이름으로 바꿉니다.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>앱 구성 저장소 만들기

다른 지역에 기본 및 보조 앱 구성 저장소를 만듭니다.
 `<primary_appconfig_name>`및를 `<secondary_appconfig_name>` 구성 저장소의 고유한 이름으로 바꿉니다. 각 매장 이름은 DNS 이름으로 사용 되므로 고유 해야 합니다.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>큐 만들기

Event Grid에서 게시 한 이벤트를 수신 하기 위한 저장소 계정 및 큐를 만듭니다.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>앱 구성 저장소 이벤트 구독

기본 앱 구성 저장소에서 다음 두 이벤트를 구독 합니다.

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

다음 명령은 큐에 전송 된 두 이벤트에 대 한 Event Grid 구독을 만듭니다. 끝점 형식은로 설정 되 `storagequeue` 고 끝점은 큐 ID로 설정 됩니다. 을 `<event_subscription_name>` 이벤트 구독에 대해 선택한 이름으로 바꿉니다.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>큐 저장소에서 이벤트를 처리 하기 위한 함수 만들기

### <a name="set-up-with-ready-to-use-functions"></a>바로 사용할 수 있는 함수로 설정

이 문서에서는 다음과 같은 속성을 포함 하는 c # 함수를 사용 합니다.
- 런타임 스택 .NET Core 3.1
- Azure Functions 런타임 버전 3(sp3)
- 10 분 마다 타이머에 의해 트리거되는 함수

데이터의 백업을 더 쉽게 시작할 수 있도록 코드를 변경 하지 않고 사용할 수 있는 [함수를 테스트 하 고 게시](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) 했습니다. 프로젝트 파일을 다운로드 하 고 [Visual Studio에서 사용자 고유의 Azure 함수 앱에 게시](/azure/azure-functions/functions-develop-vs#publish-to-azure)합니다.

> [!IMPORTANT]
> 다운로드 한 코드에서 환경 변수를 변경 하지 마세요. 다음 섹션에서 필요한 앱 설정을 만듭니다.
>

### <a name="build-your-own-function"></a>사용자 고유의 함수 빌드

이전에 제공 된 샘플 코드가 사용자의 요구 사항을 충족 하지 않는 경우 사용자 고유의 함수를 만들 수도 있습니다. 함수는 백업을 완료 하기 위해 다음 작업을 수행할 수 있어야 합니다.
- 큐의 내용을 주기적으로 읽어 Event Grid의 알림이 포함 되어 있는지 확인 합니다. 구현에 대 한 자세한 내용은 [저장소 큐 SDK](/azure/storage/queues/storage-quickstart-queues-dotnet) 를 참조 하세요.
- 큐에 [Event Grid의 이벤트 알림이](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)포함 되어 있으면 `<key, label>` 이벤트 메시지에서 모든 고유 정보를 추출 합니다. 키와 레이블 조합은 기본 저장소의 키-값 변경 내용에 대 한 고유 식별자입니다.
- 기본 저장소에서 모든 설정을 읽습니다. 보조 저장소에서 해당 이벤트가 있는 해당 이벤트를 포함 하는 설정만 업데이트 합니다. 주 저장소에는 없지만 큐에 있는 보조 저장소에서 모든 설정을 삭제 합니다. [앱 구성 SDK](https://github.com/Azure/AppConfiguration#sdks) 를 사용 하 여 프로그래밍 방식으로 구성 저장소에 액세스할 수 있습니다.
- 처리 하는 동안 예외가 발생 하지 않은 경우 큐에서 메시지를 삭제 합니다.
- 필요에 따라 오류 처리를 구현 합니다. 처리할 수 있는 몇 가지 일반적인 예외를 보려면 앞의 코드 샘플을 참조 하세요.

함수를 만드는 방법에 대해 자세히 알아보려면 [Azure에서 타이머에 의해 트리거되는 함수 만들기](/azure/azure-functions/functions-create-scheduled-function) 및 [Visual Studio를 사용 하 여 Azure Functions 개발](/azure/azure-functions/functions-develop-vs)을 참조 하세요.


> [!IMPORTANT]
> 최적의 judgement를 사용 하 여 기본 구성 저장소를 변경 하는 빈도에 따라 타이머 일정을 선택 합니다. 함수를 너무 자주 실행 하면 저장소에 대 한 제한 요청이 종료 될 수 있습니다.
>


## <a name="create-function-app-settings"></a>함수 앱 설정 만들기

제공한 함수를 사용 하는 경우 함수 앱에서 다음 앱 설정이 필요 합니다.
- `PrimaryStoreEndpoint`: 기본 앱 구성 저장소의 끝점입니다. 예제는 `https://{primary_appconfig_name}.azconfig.io`입니다.
- `SecondaryStoreEndpoint`: 보조 앱 구성 저장소의 끝점입니다. 예제는 `https://{secondary_appconfig_name}.azconfig.io`입니다.
- `StorageQueueUri`: 큐 URI. 예제는 `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`입니다.

다음 명령은 함수 앱에 필요한 앱 설정을 만듭니다. `<function_app_name>`은 함수 앱 이름으로 바꿉니다.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>함수 앱의 관리 되는 id에 대 한 액세스 권한 부여

함수 앱에 대 한 시스템 할당 관리 id를 추가 하려면 다음 명령을 사용 하거나 [Azure Portal](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) 합니다.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> 필요한 리소스 생성 및 역할 관리를 수행 하려면 계정에 `Owner` 적절 한 범위 (구독 또는 리소스 그룹)에 대 한 권한이 필요 합니다. 역할 할당에 대 한 지원이 필요한 경우 [Azure Portal를 사용 하 여 Azure 역할 할당을 추가 하거나 제거 하는 방법을](/azure/role-based-access-control/role-assignments-portal)알아봅니다.

다음 명령 또는 [Azure Portal](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) 를 사용 하 여 앱 구성 저장소에 대 한 함수 앱 액세스의 관리 id를 부여 합니다. 다음 역할을 사용 합니다.
- `App Configuration Data Reader`기본 앱 구성 저장소에서 역할을 할당 합니다.
- `App Configuration Data Owner`보조 앱 구성 저장소에서 역할을 할당 합니다.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

다음 명령 또는 [Azure Portal](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal) 를 사용 하 여 함수 앱에 대 한 관리 id에 큐에 대 한 액세스 권한을 부여 합니다. `Storage Queue Data Contributor`큐에 역할을 할당 합니다.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>App Configuration 이벤트 트리거

모든 것이 작동 하는지 테스트 하기 위해 기본 저장소에서 키-값을 만들거나 업데이트 하거나 삭제할 수 있습니다. 타이머 트리거를 Azure Functions 하 고 몇 초 후에 보조 저장소에서이 변경 내용을 자동으로 확인 해야 합니다.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

이벤트를 트리거 했습니다. 잠시 후에 Event Grid는 큐에 이벤트 알림을 보냅니다. *다음에 예약 된 함수 실행 후*보조 저장소의 구성 설정을 보고 기본 저장소에서 업데이트 된 키 값이 포함 되어 있는지 확인 합니다.

> [!NOTE]
> 예약 된 타이머 트리거를 기다리지 않고 테스트 및 문제 해결 중에 [함수를 수동으로 트리거할](/azure/azure-functions/functions-manually-run-non-http) 수 있습니다.

백업 기능이 성공적으로 실행 되었는지 확인 한 후에는 키가 보조 저장소에 표시 되는 것을 확인할 수 있습니다.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>문제 해결

보조 저장소에 새 설정이 표시 되지 않으면 다음을 수행 합니다.

- 기본 저장소에서 설정을 만든 *후* 백업 기능이 트리거 되었는지 확인 합니다.
- Event Grid에서 큐에 이벤트 알림을 전송 하지 못할 수 있습니다. 큐에 주 저장소의 이벤트 알림이 계속 포함 되어 있는지 확인 합니다. 이 경우 backup 함수를 다시 트리거합니다.
- 오류 또는 경고에 대 한 [Azure Functions 로그](/azure/azure-functions/functions-create-scheduled-function#test-the-function) 를 확인 합니다.
- [Azure Portal](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) 를 사용 하 여 Azure 함수 앱에 Azure Functions 읽으려고 하는 응용 프로그램 설정에 대 한 올바른 값이 포함 되어 있는지 확인 합니다.
- [Azure 애플리케이션 Insights](/azure/azure-functions/functions-monitoring?tabs=cmd)를 사용 하 여 Azure Functions에 대 한 모니터링 및 경고를 설정할 수도 있습니다. 


## <a name="clean-up-resources"></a>리소스 정리
이 앱 구성 및 이벤트 구독을 계속 사용 하려는 경우이 문서에서 만든 리소스를 정리 하지 마세요. 계속할 계획이 없는 경우 다음 명령을 사용 하 여이 문서에서 만든 리소스를 삭제 합니다.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

키 값의 자동 백업을 설정 하는 방법을 배웠으므로 응용 프로그램의 지역 복원 력을 높이는 방법에 대해 자세히 알아보세요.

- [복원력 및 재해 복구](concept-disaster-recovery.md)
