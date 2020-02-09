---
title: 리소스 변경 내용 가져오기
description: 리소스가 변경 된 시기를 찾고 변경 된 속성의 목록을 가져오고 차이를 평가 하는 방법을 이해 합니다.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873032"
---
# <a name="get-resource-changes"></a>리소스 변경 내용 가져오기

리소스는 매일의 사용, 재구성 및 재배포를 통해 변경됩니다.
변경 내용은 개별 또는 자동화 된 프로세스에서 가져올 수 있습니다. 대부분의 변경은 의도적 이지만 때로는 그렇지 않습니다. 최근 14 일간의 변경 기록으로 Azure 리소스 그래프를 통해 다음 작업을 수행할 수 있습니다.

- Azure Resource Manager 속성에서 변경 내용이 검색 된 경우 찾기
- 각 리소스 변경에 대해 속성 변경 정보를 참조 하세요.
- 검색 된 변경 전후에 리소스의 전체 비교를 확인 합니다.

변경 검색 및 세부 정보는 다음 예제 시나리오에서 유용 합니다.

- 인시던트 관리를 통해 _잠재적_ 으로 관련 된 변경 사항을 파악 합니다. 특정 시간 창에서 변경 이벤트를 쿼리하고 변경 정보를 평가 합니다.
- CMDB 라고 하는 구성 관리 데이터베이스를 최신 상태로 유지 예약 된 빈도로 모든 리소스와 전체 속성 집합을 새로 고치는 대신 변경 된 내용만 가져옵니다.
- 리소스가 준수 상태를 변경 했을 때 변경 되었을 수 있는 다른 속성을 이해 합니다. 이러한 추가 속성을 평가 하면 Azure Policy 정의를 통해 관리 해야 할 수 있는 다른 속성에 대 한 정보를 제공할 수 있습니다.

이 문서에서는 리소스 그래프의 SDK를 통해이 정보를 수집 하는 방법을 보여 줍니다. Azure Portal에서이 정보를 보려면 Azure Policy의 [변경 기록](../../policy/how-to/determine-non-compliance.md#change-history-preview) 또는 Azure 활동 로그 [변경 기록](../../../azure-monitor/platform/activity-log-view.md#azure-portal)을 참조 하세요.
응용 프로그램 배포에 대 한 인프라 계층의 응용 프로그램 변경에 대 한 자세한 내용은 Azure Monitor에서 [응용 프로그램 변경 분석 (미리 보기) 사용](../../../azure-monitor/app/change-analysis.md) 을 참조 하세요.

> [!NOTE]
> 리소스 그래프의 변경 정보는 리소스 관리자 속성에 대 한 것입니다. 가상 컴퓨터 내의 변경 내용 추적에 대 한 자세한 내용은 Azure Automation의 [변경 내용 추적](../../../automation/automation-change-tracking.md) 또는 [vm에 대 한 Azure Policy 게스트 구성](../../policy/concepts/guest-configuration.md)을 참조 하세요.

> [!IMPORTANT]
> Azure 리소스 그래프의 변경 기록은 공개 미리 보기로 제공 됩니다.

## <a name="find-detected-change-events-and-view-change-details"></a>검색 된 변경 이벤트를 찾고 변경 내용 보기

리소스에서 변경 된 내용을 확인 하는 첫 번째 단계는 시간 창 내에서 해당 리소스와 관련 된 변경 이벤트를 찾는 것입니다. 각 변경 이벤트에는 리소스에서 변경 된 내용에 대 한 세부 정보도 포함 됩니다. 이 단계는 **resourceChanges** REST 엔드포인트을 통해 수행 됩니다.

**ResourceChanges** 엔드포인트은 요청 본문에서 다음 매개 변수를 허용 합니다.

- **resourceId** \[필수\]: 변경 내용을 찾을 Azure 리소스입니다.
- **interval** \[required\]: **줄루어 표준 시간대 (Z)** 를 사용 하 여 변경 이벤트를 확인 하는 경우 _시작_ 및 _종료_ 날짜가 포함 된 속성입니다.
- **Fetchpropertychanges** (선택 사항): 응답 개체에 속성 변경 내용이 포함 된 경우를 설정 하는 부울 속성입니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

위의 요청 본문을 사용 하 여 **resourceChanges** 에 대 한 REST API URI는 다음과 같습니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

응답은 다음 예제와 유사 합니다.

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

**ResourceId** 에 대해 검색 된 각 변경 이벤트에는 다음과 같은 속성이 있습니다.

- **Changeid** -이 값은 해당 리소스에 대해 고유 합니다. **Changeid** 문자열은 경우에 따라 다른 속성을 포함할 수 있지만 고유 하 게 보장 됩니다.
- **beforeSnapshot** -변경이 검색 되기 전에 수행 된 리소스 스냅숏의 **snapshotId** 및 **타임 스탬프** 를 포함 합니다.
- **Aftersnapshot** -변경이 검색 된 후에 수행 된 리소스 스냅숏의 **snapshotId** 및 **타임 스탬프** 를 포함 합니다.
- **beforeSnapshot** **-전체** 변경 레코드에 대해 검색 되는 변경 내용 유형을 설명 **합니다.** 값은 _Create_, _Update_및 _Delete_입니다. **Propertychanges** 속성 **배열은가를** _업데이트할_때만 포함 됩니다.
- **Propertychanges** -이 속성 배열은 **BeforeSnapshot** 와 **aftersnapshot**간에 업데이트 된 모든 리소스 속성을 자세히 설명 합니다.
  - **propertyName** -변경 된 리소스 속성의 이름입니다.
  - **changeCategory** -변경 내용을 설명 합니다. 값은 _시스템_ 및 _사용자_입니다.
  - 이상 **-개별** 리소스 속성에 대해 검색 된 변경 형식을 설명 합니다.
    값은 _삽입_, _업데이트_, _제거_입니다.
  - **beforeValue** - **beforeSnapshot**의 리소스 속성 값입니다. 가를 _삽입_하는 경우에는 표시 **되지 않습니다.**
  - **Aftervalue** - **aftervalue**의 리소스 속성 값입니다. 가를 _제거_하는 경우에는 표시 **되지 않습니다.**

## <a name="compare-resource-changes"></a>리소스 변경 내용 비교

**ResourceChanges** 엔드포인트에서 **changeid** 를 사용 하 여 **resourceChangeDetails** REST 엔드포인트을 사용 하 여 변경 된 리소스의 이전 및 이후 스냅숏을 가져옵니다.

**ResourceChangeDetails** 엔드포인트에는 요청 본문에 두 개의 매개 변수가 필요 합니다.

- **resourceId**: 변경 내용을 비교할 Azure 리소스입니다.
- **Changeid**: **resourceChanges**에서 수집 된 **resourceId** 에 대 한 고유 변경 이벤트입니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

위의 요청 본문을 사용 하 여 **resourceChangeDetails** 에 대 한 REST API URI는 다음과 같습니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

응답은 다음 예제와 유사 합니다.

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** 및 **aftersnapshot** 은 각각 스냅숏을 만든 시간 및 속성을 제공 합니다. 이러한 스냅숏 간의 특정 지점에서 변경이 발생 했습니다. 위의 예제를 살펴보면 변경 된 속성이 **supportsHttpsTrafficOnly**된 것을 볼 수 있습니다.

결과를 비교 하려면 **resourceChanges** 의 **changes** 속성을 사용 하거나 **resourceChangeDetails** 에서 각 스냅숏의 **콘텐츠** 부분을 평가 하 여 차이를 확인 합니다. 스냅숏을 비교 하는 경우 **타임 스탬프** 는 항상 예상 대로 차이를 표시 합니다.

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 중인 언어를 참조 하세요.
- [고급 쿼리에서](../samples/advanced.md)고급 사용을 참조 하세요.
- [리소스 검색](../concepts/explore-resources.md) 방법을 자세히 알아봅니다.