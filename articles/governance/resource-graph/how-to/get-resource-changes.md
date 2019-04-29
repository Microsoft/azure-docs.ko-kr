---
title: 리소스 변경 내용 가져오기
description: 리소스 변경 된 시기를 찾는 방법을 이해 하 고 변경 된 속성의 목록을 가져옵니다.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/20/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 0ae85b45dfcd80056316ed5f2099aab4057d24c8
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760819"
---
# <a name="get-resource-changes"></a>리소스 변경 내용 가져오기

리소스 가져오기 매일 사용 하 여, 재구성, 및도 재배포 과정을 통해 변경 됩니다.
개별 또는 자동화 된 프로세스에 의해 변경 가져올 수 있습니다. 기본적으로 대부분의 변경 되었지만 없는 경우가 있습니다. 변경 기록의 지난 14 일을 사용 하 여 Azure 리소스 그래프를 사용 하면 수 있습니다.

- Azure Resource Manager 속성에서 변경이 탐지된 시기를 확인합니다.
- 해당 변경 이벤트의 일부로 변경된 속성을 확인합니다.

변경 내용 검색 및 세부 정보는 다음과 같은 시나리오에 대 한 중요:

- 인시던트 관리를 이해 하는 동안 _잠재적으로_ 관련 변경 합니다. 특정 기간 동안 변경 이벤트를 쿼리하고 변경 세부 정보를 평가 합니다.
- 구성 관리 데이터베이스를 유지, CMDB, 최신 라고 합니다. 모든 리소스 및 예약된 된 빈도로 해당 전체 속성 집합을 새로 고치거 나, 대신만 변경 내용을 가져옵니다.
- 기타 속성 변경 되었거나 리소스 규정 준수 상태를 변경 하는 경우 이해 합니다. 이러한 추가 속성 평가 Azure 정책 정의 통해 관리 해야 하는 다른 속성에 대 한 정보를 제공할 수 있습니다.

이 문서에서는 리소스 그래프의 SDK 통해이 정보를 수집 하는 방법을 보여 줍니다. Azure portal에서이 정보를 보려면 Azure Policy [변경 내용](../../policy/how-to/determine-non-compliance.md#change-history-preview)합니다.

> [!NOTE]
> 리소스 관리자 속성에 대 한 리소스 그래프의 세부 정보 변경 됩니다. 가상 머신 내에서 변경 내용을 추적 하는 것에 대 한 Azure Automation의을 참조 하세요 [변경 내용 추적](../../../automation/automation-change-tracking.md) 또는 Azure Policy [Vm에 대 한 게스트 구성](../../policy/concepts/guest-configuration.md)합니다.

> [!IMPORTANT]
> Azure 리소스 그래프의 변경 기록을 공개 미리 보기로 제공 됩니다.

## <a name="find-when-changes-were-detected"></a>변경이 감지 된 시기 찾기

첫 번째 단계는 리소스에 대해 변경 내용 표시 하는 시간 범위 내에서 해당 리소스와 관련 된 변경 이벤트를 찾는 것입니다. 이 단계를 통해 수행 됩니다 합니다 **resourceChanges** REST 끝점입니다.

합니다 **resourceChanges** 끝점에 요청 본문에는 두 개의 매개 변수가 필요 합니다.

- **resourceId**: Azure 리소스에서 변경 내용을 검색입니다.
- **interval**: .S _시작_ 하 고 _끝_ 날짜를 사용 하 여 변경 이벤트를 확인 하는 경우를 **Zulu Time Zone (Z)** 합니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

위의 요청 본문에 대 한 REST API URI를 사용 하 여 **resourceChanges** 됩니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

응답이 예제와 비슷합니다.

```json
{
    "changes": [{
            "changeId": "2db0ad2d-f6f0-4f46-b529-5c4e8c494648",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

검색 된 각 변경 이벤트에 대 한 합니다 **resourceId** 에 **changeId** 해당 리소스에 고유한 합니다. 하는 동안 합니다 **changeId** 문자열 다른 속성을 포함할 경우에 따라 수, 고유 하 게 보장에 해당 합니다. 변경 레코드의 시간을 포함 하는 전과 스냅숏이 만들어진 후 합니다.
이 창의 일부 시점에서 변경 이벤트가 발생 했습니다.

## <a name="see-what-properties-changed"></a>속성 변경 내용 확인

사용 하 여는 **changeId** 에서 합니다 **resourceChanges** 끝점에는 **resourceChangeDetails** REST 끝점 변경 이벤트의 세부 정보를 가져오는 데 다음 합니다.

합니다 **resourceChangeDetails** 끝점에 요청 본문에는 두 개의 매개 변수가 필요 합니다.

- **resourceId**: Azure 리소스에서 변경 내용을 검색입니다.
- **changeId**: 에 대 한 고유 변경 이벤트를 **resourceId** 에서 수집한 **resourceChanges**합니다.

요청 본문 예제:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556"
}
```

위의 요청 본문에 대 한 REST API URI를 사용 하 여 **resourceChangeDetails** 됩니다.

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

응답이 예제와 비슷합니다.

```json
{
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556",
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

**beforeSnapshot** 하 고 **afterSnapshot** 각 스냅숏이 만들어진 시간 및 속성을 해당 시간에 제공 합니다. 이러한 스냅숏 간에 특정 시점에 변경 내용을 발생 했습니다. 위의 예제를 보면 보면 변경 된 속성 되었음을 **supportsHttpsTrafficOnly**합니다.

비교 결과 프로그래밍 방식으로 비교 하는 **콘텐츠** 차이 확인 하려면 각 스냅숏의 부분입니다. 전체 스냅숏을 비교 하는 경우는 **타임 스탬프** 항상 예상 되 고 불구 하 고 차이점을 보여 줍니다.

## <a name="next-steps"></a>다음 단계

- 사용 중인 언어 참조 [스타터 쿼리](../samples/starter.md)합니다.
- 고급 참조에서는 [고급 쿼리](../samples/advanced.md)합니다.
- 하는 방법을 알아봅니다 [리소스를 탐색](../concepts/explore-resources.md)합니다.
