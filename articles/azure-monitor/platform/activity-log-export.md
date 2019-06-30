---
title: Azure 활동 로그 내보내기
description: Azure 외부의 내보내기에 대 한 보관 또는 Azure Event Hubs에 대 한 저장소로 Azure 활동 로그를 내보냅니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248146"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Storage 또는 Azure Event Hubs로 Azure 활동 로그 내보내기
합니다 [Azure 활동 로그](activity-logs-overview.md) Azure 구독에서 발생 한 구독 수준 이벤트에 대 한 정보를 제공 합니다. Azure portal에서 활동 로그 보기 또는 Azure Monitor에서 수집한 기타 데이터를 사용 하 여 분석할 수 있는 Log Analytics 작업 영역에 복사 하는 것 외에도 Azure storage 계정에 활동 로그 보관 하거나 스트리밍하기 로그 프로필을 만들 수는  이벤트 허브입니다.

## <a name="archive-activity-log"></a>활동 로그 보관
저장소 계정에 활동 로그를 보관 하는 것은 감사, 정적 분석 또는 백업을 위해 (보존 정책에 대해 모든 권한)으로 90 일 보다 긴 로그 데이터를 보존 하기 원하는 경우에 유용 합니다. 90 일 동안 이벤트를 유지 해야 하거나 덜 필요가 없습니다를 설정 하려면 저장소 계정에 보관 되므로 활동 로그 이벤트는 Azure 플랫폼에서 90 일 동안 유지 됩니다.

## <a name="stream-activity-log-to-event-hub"></a>이벤트 허브의 Stream 활동 로그
[Azure Event Hubs](/azure/event-hubs/) 는 스트리밍 플랫폼 및 이벤트 수집 서비스를 받을 수 있는 데이터 및 프로세스 수백만 개의 초당 이벤트 수입니다. 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. 활동 로그 스트리밍 기능을 사용할 수 있는 두 가지 방법은 다음과 같습니다.
* **타사 로깅 및 원격 분석 시스템으로 스트리밍**: 시간이 지나면서 Azure Event Hubs 스트리밍은 활동 로그를 타사 SIEM 및 로그 분석 솔루션으로 파이핑하기 위한 메커니즘이 되고 있습니다.
* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드**: 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 활동 로그를 유연하게 수집할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

### <a name="storage-account"></a>Storage 계정
활동 로그를 보관 하려는 경우 필요가 [저장소 계정 만들기](../../storage/common/storage-quickstart-create-account.md) 아직 없는 경우. 모니터링 데이터에 대 한 액세스를 더 잘 제어할 수 있도록 저장 하는, 다른 비 모니터링 데이터가 있는 기존 저장소 계정을 사용 하지 해야 합니다. 또한 보관 하는 경우 진단 로그 및 메트릭을 저장소 계정에 있지만, 중앙 위치에서 모든 모니터링 데이터를 유지 하는 동일한 저장소 계정을 사용 하도록 선택할 수 있습니다.

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 구독과 동일한 구독을 가지고 있지 않아도 됩니다.
> [!NOTE]
>  현재는 보안 가상 네트워크 뒤에 있는 저장소 계정에 데이터를 보관할 수 없습니다.

### <a name="event-hubs"></a>Event Hubs
활동 로그 이벤트 허브로 전송 하는 경우 해야 [이벤트 허브 만들기](../../event-hubs/event-hubs-create.md) 아직 없는 경우. 이전에이 Event Hubs 네임 스페이스에 활동 로그 이벤트를 스트림 하는 경우 해당 이벤트 허브 재사용 됩니다.

공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. Event Hubs로 스트리밍하려면 관리, 보내기 및 수신 권한이 필요 합니다. 만들기 또는 Event Hubs 네임 스페이스에 대 한 구성 탭에서 Azure portal에서 Event Hubs 네임 스페이스에 대 한 공유 액세스 정책을 수정할 수 있습니다.

스트리밍을 포함할 활동 로그 로그 프로필을 업데이트 하려면 해당 이벤트 허브 권한 부여 규칙에 대 한 ListKey 권한이 있어야 합니다. 설정을 구성하는 사용자에게 구독 모두에 액세스할 수 있는 적절한 RBAC 액세스 권한이 있다면 Event Hubs 네임스페이스가 로그를 내보내는 구독과 동일한 구독에 위치하지 않아도 됩니다. 구독은 모두 동일한 AAD 테넌트에 위치합니다.

활동 로그에서 이벤트 허브를 Stream [로그 프로필을 만드는](#create-a-log-profile)합니다.

## <a name="create-a-log-profile"></a>로그 프로필 만들기
Azure 활동 로그는 어떻게 정의 사용 하 여 내보낸를 **로그 프로필**합니다. 각 Azure 구독 로그 프로필을 하나만 사용할 수 있습니다. 이러한 설정을 구성할 수 있습니다 합니다 **내보내기** 포털에서 활동 로그 블레이드에서 옵션입니다. [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell cmdlet 또는 CLI를 사용하여 프로그래밍 방식으로 구성할 수도 있습니다.

다음 로그 프로필을 정의합니다.

**활동 로그를 보낼 위치입니다.** 현재 사용 가능한 옵션은 저장소 계정 또는 Event Hubs입니다.

**이벤트 범주를 보내야 합니다.** 의미 *범주* 로그 프로필 및 활동 로그에서 이벤트 다릅니다. 로그 프로필에서 *범주* 작업 유형 (쓰기, 삭제, 작업)을 나타냅니다. 활동 로그 이벤트에는 *범주*"* 원본 또는 이벤트 (예를 들어, 관리, ServiceHealth, 경고) 유형의 속성을 나타냅니다.

**내보낼 하위 지역 (위치) 해야 합니다.** 활동 로그의 많은 이벤트가 전역 이벤트 이므로 모든 위치를 포함 해야 합니다.

**활동 로그는 저장소 계정에 유지 해야 하는 기간** 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 숫자일 수 있습니다.

보존 정책이 설정, 저장소 계정에 로그를 저장할 사용 하지 않도록 설정 하지만 보존 정책 아무런 효과가 없습니다. 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다. 삭제 프로세스는 자정(UTC)에 시작되지만, 스토리지 계정에서 로그가 삭제될 때까지 최대 24시간이 걸릴 수 있습니다.



> [!WARNING]
> 스토리지 계정에서 로그 데이터의 형식이 2018년 11월 1일에 JSON 줄로 변경됩니다. [새 형식을 처리하도록 도구를 업데이트하는 방법과 영향에 대한 설명은 이 아티클을 참조하세요.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Azure portal을 사용 하 여 로그 프로필 만들기

만들기 또는 편집 하는 로그 프로필을 **이벤트 허브로 내보내기** Azure portal의 옵션입니다.

1. **모니터** Azure portal 선택 메뉴 **이벤트 허브로 내보내기**합니다.

    ![포털에서 내보내기 단추](media/activity-log-export/portal-export.png)

3. 표시 되는 블레이드에서 다음을 지정 합니다.
   * 내보낼 이벤트를 사용 하 여 영역입니다. 모든 영역 이므로 대부분의 이벤트에 연결 된 지역이 없는 활동 로그는 글로벌 (비지역) 로그 이므로 키 이벤트를 놓치지 마세요 확인을 선택 해야 합니다. 
   * 저장소 계정에 작성 하려면:
       * 이벤트를 저장 하려는 저장소 계정입니다.
       * 저장소에서 이러한 이벤트를 보존할 일 수 있습니다. 0일로 설정하면 로그를 계속 유지합니다.
   * 이벤트 허브에 작성 하려면:
       * 이러한 이벤트를 스트리밍하기 위해 만들 이벤트 허브를 하려는 Service Bus Namespace입니다.

     ![활동 로그 내보내기 블레이드](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. **저장**을 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.


### <a name="configure-log-profile-using-powershell"></a>PowerShell을 사용 하 여 로그 프로필 구성

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

로그 프로필이 이미 있는 경우 먼저 기존 로그 프로필을 제거 하 고 새로 만든를 해야 합니다.

1. `Get-AzLogProfile`를 사용하여 로그 프로필이 있는지 확인합니다.  로그 프로필이 존재 하는 경우는 *이름을* 속성입니다.

1. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `Remove-AzLogProfile`을 사용합니다.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile`를 사용하여 새 로그 프로필을 만듭니다.

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 자산 | 필수 | 설명 |
    | --- | --- | --- |
    | Name |예 |로그 프로필의 이름입니다. |
    | StorageAccountId |아닙니다. |활동 로그를 저장할 저장소 계정의 리소스 ID입니다. |
    | serviceBusRuleId |아닙니다. |이벤트 허브를 만들 Service Bus 네임스페이스의 Service Bus 규칙 ID입니다. 형식 문자열입니다. `{service bus resource ID}/authorizationrules/{key name}`합니다. |
    | Location |예 |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
    | RetentionInDays |예 |1에서 2147483647 사이의 저장소 계정에서 이벤트를 보존할 일 수입니다. 0 값은 로그를 무기한 저장합니다. |
    | Category |아닙니다. |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 가능한 값은 _작성할_를 _삭제_, 및 _동작_합니다. |

### <a name="example-script"></a>예제 스크립트
다음은 저장소 계정 및 이벤트 허브 둘 다에 활동 로그를 기록 하는 로그 프로필을 만드는 샘플 PowerShell 스크립트입니다.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Azure CLI를 사용 하 여 로그 프로필 구성

로그 프로필이 이미 있으면 먼저 기존 로그 프로필을 제거한 다음, 새 로그 프로필을 생성해야 합니다.

1. `az monitor log-profiles list`를 사용하여 로그 프로필이 있는지 확인합니다.
2. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `az monitor log-profiles delete --name "<log profile name>`을 사용합니다.
3. `az monitor log-profiles create`를 사용하여 새 로그 프로필을 만듭니다.

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 자산 | 필수 | 설명 |
    | --- | --- | --- |
    | name |예 |로그 프로필의 이름입니다. |
    | storage-account-id |예 |활동 로그를 저장할 Storage 계정의 리소스 ID입니다. |
    | locations |예 |활동 로그 이벤트를 수집할 공백으로 구분된 지역 목록입니다. `az account list-locations --query [].name`을 사용하여 구독에 대한 모든 지역 목록을 볼 수 있습니다. |
    | days |예 |보존 해야 하는 이벤트에 1에서 365 사이의 일 수입니다. 0 값은 로그를 무기한(영원히) 저장합니다.  0이면 활성화된 매개 변수를 true로 설정해야 합니다. |
    |사용 | 예 |True 또는 False입니다.  보존 정책을 사용하거나 비활성화하는 데 사용합니다.  True이면 일 매개 변수 0보다 큰 값이어야 합니다.
    | categories |예 |수집해야 할 공백으로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |



## <a name="activity-log-schema"></a>활동 로그 스키마
Azure storage 또는 Event Hub에 전송 하는지 여부를 활동 로그 데이터를 쓸 JSON는 다음 형식을 사용 합니다.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
다음 표에 요소를이 json에 대 한 설명입니다.

| 요소 이름 | 설명 |
| --- | --- |
| time |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| resourceId |영향을 받는 리소스의 리소스 ID입니다. |
| operationName |작업의 이름입니다. |
| category |작업의 범주 (예: 쓰기, 읽기, 작업) |
| resultType |결과의 형식 (예: 성공, 실패, 시작) |
| resultSignature |리소스 종류에 따라 다릅니다. |
| durationMS |밀리초 단위의 작업 기간 |
| callerIpAddress |가용성을 기반으로 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 IP 주소입니다. |
| correlationId |일반적으로 문자열 형식의 GUID입니다. 동일한 uber 작업에 속하는 correlationId를 공유하는 이벤트입니다. |
| identity |권한 부여 및 클레임을 설명하는 JSON Blob입니다. |
| 권한 부여 |이벤트의 RBAC 속성 Blob입니다. 일반적으로 "action", "role" 및 "scope" 속성이 포함됩니다. |
| level |이벤트의 수준입니다. 해당 값은 _중요_, _오류_를 _경고_를 _정보_, 및 _자세한 정보_ |
| location |발생하는 위치의 지역(또는 전역)입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |

> [!NOTE]
> 속성과 이러한 속성의 사용은 리소스에 따라 달라질 수 있습니다.



## <a name="next-steps"></a>다음 단계

* [활동 로그에 대한 자세한 내용](../../azure-resource-manager/resource-group-audit.md)
* [Azure Monitor 로그로 활동 로그를 수집 합니다.](activity-log-collect.md)
