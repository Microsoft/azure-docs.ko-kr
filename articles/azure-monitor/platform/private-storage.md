---
title: 로그 수집을 위한 고객 소유 스토리지 계정
description: Azure Monitor의 Log Analytics 작업 영역에서 로그 데이터를 수집하는 데 고유한 스토리지 계정을 사용합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 58d6f98c87e37254e77bcc8dda1cdca6e608cafc
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962675"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Azure Monitor의 로그 수집을 위한 고객 소유 스토리지 계정

Azure Monitor는 [사용자 지정 로그](data-sources-custom-logs.md) 및 일부 [Azure 로그](./diagnostics-extension-logs.md)와 같은 일부 데이터 형식의 수집 프로세스에서 스토리지 계정을 사용합니다. 수집 프로세스 중에 로그는 먼저 스토리지 계정으로 전송되고 나중에 Log Analytics 또는 Application Insights로 수집됩니다. 수집하는 동안 데이터를 제어하려는 경우 서비스 관리 스토리지 대신 자체 스토리지 계정을 사용할 수 있습니다. 사용자 고유의 저장소 계정을 사용 하면 수집 하는 동안 로그의 액세스, 콘텐츠, 암호화 및 보존을 제어할 수 있습니다. 이를 BYOS(Bring Your Own Storage)라고 합니다. 

BYOS를 필요로 하는 한 가지 시나리오는 Private Link를 통한 네트워크 격리입니다. VNet을 사용하는 경우 네트워크 격리가 종종 요구되며, 공용 인터넷에 대한 액세스가 제한됩니다. 이러한 경우 로그 수집을 위해 Azure Monitor 서비스 스토리지에 액세스하는 것이 완전히 차단되거나 잘못된 방법으로 간주됩니다. 대신 VNet 내의 고객 소유 스토리지 계정을 통해 로그를 수집하거나 쉽게 액세스할 수 있어야 합니다.

또 다른 시나리오는 CMK(고객 관리 키)를 사용하여 로그를 암호화하는 것입니다. 고객은 로그를 저장하는 클러스터에서 CMK를 사용하여 기록된 데이터를 암호화할 수 있습니다. 수집 프로세스 중에 동일한 키를 사용하여 로그를 암호화할 수도 있습니다.

## <a name="data-types-supported"></a>지원되는 데이터 형식

스토리지 계정에서 수집되는 데이터 형식은 다음과 같습니다. 이러한 유형의 수집에 대한 자세한 내용은 [Azure 진단 확장에서 Azure Monitor 로그로 데이터 수집](./diagnostics-extension-logs.md)을 참조하세요.

| Type | 테이블 정보 |
|:-----|:------------------|
| IIS 로그 | Blob: wad-iis-logfiles|
|Windows 이벤트 로그 | 표: WADWindowsEventLogsTable |
| syslog | 표: LinuxsyslogVer2v0 |
| Windows ETW 로그 | 표: WADETWEventTable|
| Service Fabric | 표: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| 사용자 지정 로그 | 해당 없음 |
| Azure Security Center Watson 덤프 파일 | 해당 없음|  

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항 
스토리지 계정은 다음 요구 사항을 충족해야 합니다.

- 스토리지에 로그를 쓰는 VNet의 리소스에 액세스할 수 있습니다.
- 연결된 작업 영역과 동일한 영역에 있어야 합니다.
- Azure Monitor 액세스 허용-저장소 계정 액세스를 선택 하 여 네트워크를 선택 하는 경우이 예외를 허용 해야 합니다. *신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용*합니다.

## <a name="process-to-configure-customer-owned-storage"></a>고객 소유 스토리지를 구성하는 프로세스
자체 스토리지 계정을 수집에 사용하는 기본 프로세스는 다음과 같습니다.

1. 스토리지 계정을 만들거나 기존 스토리지 계정을 선택합니다.
2. 스토리지 계정을 Log Analytics 작업 영역에 연결합니다.
3. 로드 및 보존을 검토하여 예상대로 작동하는지 확인하여 스토리지를 관리합니다.

링크를 만들고 제거할 수 있는 유일한 방법은 REST API를 사용하는 것입니다. 각 프로세스에 필요한 특정 API 요청에 대한 세부 정보는 아래 섹션에서 제공됩니다.

## <a name="command-line-and-rest-api"></a>명령줄 및 REST API

### <a name="command-line"></a>명령줄
연결 된 저장소 계정을 만들고 관리 하려면 [az monitor log analytics 작업 영역 연결 된 저장소](/cli/azure/monitor/log-analytics/workspace/linked-storage)를 사용 합니다. 이 명령은 작업 영역에서 저장소 계정을 연결 하 고 연결을 해제 하 고 연결 된 저장소 계정을 나열할 수 있습니다.

### <a name="request-and-cli-values"></a>요청 및 CLI 값

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson - Azure Security Center Azure Watson 덤프 파일에 이 값을 사용합니다.
- CustomLogs – 다음 데이터 형식에 이 값을 사용합니다.
  - 사용자 지정 로그
  - IIS 로그
  - 이벤트(Windows)
  - Syslog(Linux)
  - ETW 로그
  - Service Fabric 이벤트
  - 평가 데이터  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
이 값은 다음과 같은 구조를 사용합니다.

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


## <a name="get-linked-storage-accounts"></a>연결 된 저장소 계정 가져오기

### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>모든 데이터 원본 유형에 대 한 연결 된 저장소 계정 가져오기

#### <a name="api-request"></a>API 요청

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>응답

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>특정 데이터 원본 유형에 대한 연결된 스토리지 계정 가져오기

#### <a name="api-request"></a>API 요청

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>응답 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>링크 만들기 또는 수정

스토리지 계정을 작업 영역에 연결하면 Log Analytics는 서비스에서 소유하는 스토리지 계정 대신 해당 계정을 사용합니다. 스토리지 계정 목록을 동시에 등록할 수 있으며, 여러 작업 영역에 동일한 스토리지 계정을 사용할 수 있습니다.

작업 영역에서 VNet 리소스와 VNet 외부의 리소스를 모두 처리하는 경우 인터넷에서 들어오는 트래픽을 거부하지 않아야 합니다. 스토리지에는 작업 영역과 동일한 설정이 있어야 하고 VNet 외부의 리소스에서 사용할 수 있어야 합니다. 

### <a name="api-request"></a>API 요청

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>페이로드

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>응답

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>스토리지 계정 연결 해제
수집을 위해 스토리지 계정 사용을 중지하거나 사용하는 작업 영역을 바꾸려면 작업 영역에서 스토리지의 연결을 해제해야 합니다.

작업 영역에서 모든 스토리지 계정의 연결을 해제하면 수집이 서비스 관리 스토리지 계정을 사용하려고 시도합니다. 에이전트가 인터넷에 대해 제한된 액세스 권한을 가진 VNet에서 실행되는 경우 수집이 실패할 것으로 예상됩니다. 작업 영역에는 모니터링되는 리소스에서 연결할 수 있는 연결된 스토리지 계정이 있어야 합니다.

스토리지 계정을 삭제하기 전에 해당 스토리지 계정에 포함된 모든 데이터가 작업 영역에 수집되었는지 확인해야 합니다. 예방 조치로 대체 스토리지를 연결한 후 스토리지 계정을 사용 가능한 상태로 유지합니다. 모든 콘텐츠를 수집한 후에만 삭제하면 새로 연결된 스토리지 계정에 새 데이터가 기록되는 것을 볼 수 있습니다.


### <a name="api-request"></a>API 요청
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>스토리지 계정 교체

수집에 사용되는 스토리지 계정을 바꾸려면 먼저 새 스토리지 계정에 대한 링크를 만듭니다. 로깅 에이전트는 업데이트 된 구성을 가져오고 데이터를 새 저장소로 보내기 시작 합니다.

다음으로, 에이전트가 제거된 계정에 쓰기를 중지하도록 이전 스토리지 계정의 연결을 해제합니다. 수집 프로세스는 모두 수집될 때까지 이 계정의 데이터를 계속 읽습니다. 모든 로그가 수집될 때까지 스토리지 계정을 삭제하지 마세요.

몇 분 후에 에이전트 구성이 새로 고쳐지고 새 스토리지로 전환됩니다.

## <a name="manage-storage-account"></a>스토리지 계정 관리

### <a name="load"></a>로드

스토리지 계정은 제한 요청을 시작하기 전에 특정 로드의 읽기 및 쓰기 요청을 처리할 수 있습니다. 제한은 로그를 수집하는 데 걸리는 시간에 영향을 주므로 데이터가 손실될 수 있습니다. 스토리지가 오버로드된 경우 추가 스토리지 계정을 등록하고 그 사이에 로드를 분산합니다. 

### <a name="related-charges"></a>관련 요금

스토리지 계정은 저장된 데이터의 볼륨, 스토리지 유형 및 중복 유형에 따라 청구됩니다. 자세한 내용은 [블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 및 [Table Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/tables/)을 참조하세요.

작업 영역의 등록된 스토리지 계정이 다른 지역에 있는 경우 이러한 [대역폭 가격 책정 정보](https://azure.microsoft.com/pricing/details/bandwidth/)에 따라 송신 요금이 청구됩니다.



## <a name="next-steps"></a>다음 단계

- 개인 링크를 설정 하는 방법에 대 한 자세한 내용은 [Azure 개인 링크를 사용 하 여 네트워크를 Azure Monitor에 안전 하 게 연결](private-link-security.md) 을 참조 하세요.

