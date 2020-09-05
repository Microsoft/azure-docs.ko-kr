---
title: REST API를 사용하여 Azure 서비스 리소스 사용 검토
description: Azure REST API를 사용하여 Azure 서비스 리소스 사용을 검토하는 방법을 알아봅니다. 비용 관리 보고서를 만들고 특정 리소스 종류로 필터링합니다.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a7b06ff41e537513558e7f4dc8e1732966299b01
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684731"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>REST API를 사용하여 Azure 리소스 사용 검토

Azure Cost Management API를 통해 Azure 리소스의 소비를 검토하고 관리할 수 있습니다.

이 아티클에서는 시간당 사용량 정보를 사용하여 쉼표로 구분된 값 문서를 생성하는 일별 보고서를 만드는 방법 및 Azure 리소스 그룹의 가상 머신, 데이터베이스 및 태그된 리소스 사용을 쿼리할 수 있도록 필터를 사용하여 보고서를 사용자 지정하는 방법을 알아봅니다.

>[!NOTE]
> Cost Management API는 현재 프라이빗 미리 보기 상태입니다.

## <a name="create-a-basic-cost-management-report"></a>기본 비용 관리 보고서 만들기

Cost Management API에서 `reports` 작업을 사용하여 비용 보고를 생성하는 방법 및 보고서를 게시할 위치를 정의합니다.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` 매개 변수는 필수 항목이며 API 토큰에서 제공된 자격 증명을 사용하여 읽을 수 있는 구독 ID를 포함해야 합니다. 

`{reportName}` 매개 변수는 보고서 이름을 지정합니다. 보고서 이름 목록을 가져오려면 Reports_List 작업을 사용하여 목록을 가져오면 됩니다(`/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`). [GitHub](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json)에서 예제 출력을 봅니다.

다음과 같은 헤더가 필요합니다.

|요청 헤더|Description|  
|--------------------|-----------------|  
|*Content-Type:*| 필수 사항입니다. `application/json`로 설정합니다. |  
|*권한 부여*| 필수 사항입니다. 유효한 `Bearer` 토큰으로 설정합니다. |

HTTP 요청 본문에서 보고서의 매개 변수를 구성합니다. 아래 예제에서는 보고서가 활성화되면 매일 생성하도록 설정합니다. 해당 보고서는 Azure Storage Blob 컨테이너에 기록된 CSV 파일이며 리소스 그룹 `westus`의 모든 리소스에 대한 시간당 비용 정보를 포함합니다.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

이

## <a name="filtering-reports"></a>보고서 필터링

보고서를 만들 때 요청 본문의 `filter` 및 `dimensions` 섹션을 통해 특정 리소스 형식에 대한 비용에 집중할 수 있습니다. 이전 요청 본문에서는 지역의 모든 리소스별로 필터링하는 방법을 보여줍니다.

### <a name="get-all-compute-usage"></a>모든 컴퓨팅 사용량 가져오기

`ResourceType` 차원을 사용하여 모든 지역의 구독에서 Azure 가상 머신 비용을 보고합니다.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines",
                "Microsoft.Compute/virtualMachines"
        ]
    }
}
```

### <a name="get-all-database-usage"></a>모든 데이터베이스 사용 가져오기

`ResourceType` 차원을 사용하여 모든 지역의 구독에서 Azure SQL Database 비용을 보고합니다.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ]
    }
}
```

### <a name="report-on-specific-instances"></a>특정 인스턴스에 대한 보고

`Resource` 차원을 사용하면 특정 리소스에 대한 비용을 보고할 수 있습니다.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>시간 범위 변경

현재 주와 현재 월 기본 제공 옵션 이외의 기간을 설정하려면 `timeframe` 정의를 `Custom`으로 설정합니다.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>다음 단계
- [Azure REST API 시작하기](https://docs.microsoft.com/rest/api/azure/)   
