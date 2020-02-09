---
title: 진단 로그를 사용 하 여 Azure 데이터 탐색기 수집 작업 모니터링
description: 수집 작업을 모니터링 하기 위해 Azure 데이터 탐색기에 대한 진단 로그를 설정 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277421"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>진단 로그를 사용 하 여 Azure 데이터 탐색기 수집 작업 모니터링 (미리 보기)

Azure Data Explorer는 애플리케이션, 웹 사이트, IoT 디바이스 등으로부터 수집된 대규모 데이터 스트리밍에 대한 실시간 분석을 제공하는 속도가 빠른 완전 관리형 데이터 분석 서비스입니다. Azure Data Explorer를 사용하려면 먼저 클러스터를 만들고 이 클러스터에 데이터베이스를 하나 이상 만듭니다. 그런 다음 데이터베이스에 대한 쿼리를 실행할 수 있도록 데이터를 데이터베이스의 테이블에 수집 (로드) 합니다. [Azure Monitor 진단 로그](/azure/azure-monitor/platform/diagnostic-logs-overview) 는 Azure 리소스의 작동에 대한 데이터를 제공 합니다. Azure 데이터 탐색기는 수집 성공 및 실패에 대한 정보를 얻기 위해 진단 로그를 사용 합니다. 작업 로그를 Azure Storage, 이벤트 허브 또는 Log Analytics로 내보내 수집 상태를 모니터링할 수 있습니다. 추가 분석을 위해 Azure Storage 및 Azure Event Hub의 로그를 Azure 데이터 탐색기 클러스터의 테이블로 라우팅할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 없는 경우 [무료 azure 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-portal.md)를 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대한 진단 로그 설정

진단 로그를 사용 하 여 다음 로그 데이터의 컬렉션을 구성할 수 있습니다.
* 수집 작업 성공: 이러한 로그에 수집 작업을 성공적으로 완료 하는 방법에 대한 정보가 있습니다.
* 실패 한 수집 작업: 이러한 로그는 오류 정보를 포함 하 여 실패 한 수집 작업에 대한 자세한 정보를 포함 합니다. 

그런 다음 데이터는 사용자의 사양에 따라 저장소 계정에 보관, 이벤트 허브로 스트리밍 또는 Log Analytics으로 전송 됩니다.

### <a name="enable-diagnostic-logs"></a>진단 로그 활성화

진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 모니터링할 Azure 데이터 탐색기 클러스터 리소스를 선택 합니다.
1. **모니터링** 아래에서 **진단 설정**을 선택합니다.
  
    ![진단 로그 추가](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. **진단 설정 추가**를 선택 합니다.
1. **진단 설정** 창에서 다음을 수행 합니다.
 
    ![진단 설정 구성](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. 진단 설정의 **이름을** 선택 합니다.
    1. 하나 이상의 대상 (저장소 계정, 이벤트 허브 또는 Log Analytics)을 선택 합니다.
    1. 수집할 로그: `SucceededIngestion` 또는 `FailedIngestion`를 선택 합니다.
    1. 수집할 [메트릭](using-metrics.md#supported-azure-data-explorer-metrics) (선택 사항)을 선택 합니다.  
    1. **저장** 을 선택 하 여 새 진단 로그 설정 및 메트릭을 저장 합니다.
    1. Azure Portal에서 진단 로그 활성화를 요청 하는 **새 지원 요청** 을 만듭니다.

몇 분 안에 새 설정이 설정 됩니다. 그러면 로그는 구성 된 보관 대상 (저장소 계정, 이벤트 허브 또는 Log Analytics)에 표시 됩니다. 

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마

모든 [Azure Monitor 진단 로그는 일반적인 최상위 스키마를 공유](/azure/azure-monitor/platform/diagnostic-logs-schema)합니다. Azure 데이터 탐색기에는 고유한 이벤트에 대한 고유한 속성이 있습니다. 모든 로그는 JSON 형식으로 저장 됩니다.

### <a name="ingestion-logs-schema"></a>수집 로그 스키마

로그 JSON 문자열에는 다음 표에 나열 된 요소가 포함 됩니다.

|이름               |Description
|---                |---
|time               |보고서의 시간
|resourceId         |Azure Resource Manager 리소스 ID
|operationName      |작업 이름: ' MICROSOFT. KUSTO/클러스터/수집/작업 '
|operationVersion   |스키마 버전: ' 1.0 ' 
|category           |작업의 범주입니다. `SucceededIngestion` 또는 `FailedIngestion`입니다. [작업 성공](#successful-ingestion-operation-log) 또는 [실패 한 작업](#failed-ingestion-operation-log)에 대한 속성이 다릅니다.
|properties         |작업에 대한 자세한 정보입니다.

#### <a name="successful-ingestion-operation-log"></a>수집 작업 로그가 성공 했습니다.

**예:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**성공한 작업 진단 로그의 속성**

|이름               |Description
|---                |---
|succeededOn        |수집 완료 시간
|operationId        |Azure 데이터 탐색기 수집 작업 ID
|데이터베이스           |대상 데이터베이스의 이름입니다.
|테이블              |대상 테이블의 이름입니다.
|ingestionSourceId  |수집 데이터 원본의 ID
|ingestionSourcePath|수집 데이터 원본 또는 blob URI의 경로입니다.
|rootActivityId     |활동 ID

#### <a name="failed-ingestion-operation-log"></a>수집 작업 로그가 실패 했습니다.

**예:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**실패 한 작업 진단 로그의 속성**

|이름               |Description
|---                |---
|failedOn           |수집 완료 시간
|operationId        |Azure 데이터 탐색기 수집 작업 ID
|데이터베이스           |대상 데이터베이스의 이름입니다.
|테이블              |대상 테이블의 이름입니다.
|ingestionSourceId  |수집 데이터 원본의 ID
|ingestionSourcePath|수집 데이터 원본 또는 blob URI의 경로입니다.
|rootActivityId     |활동 ID
|자세히            |오류 및 오류 메시지에 대한 자세한 설명
|errorCode          |오류 코드 
|failureStatus      |`Permanent` 또는 `Transient`입니다. 일시적인 오류를 다시 시도 하면 성공할 수 있습니다.
|originatesFromUpdatePolicy|업데이트가 업데이트 정책에서 발생 하는 경우 True입니다.
|shouldRetry        |다시 시도 하는 경우 True

## <a name="next-steps"></a>다음 단계

* [자습서: Azure 데이터 탐색기에서 모니터링 데이터 수집 및 쿼리](ingest-data-no-code.md)
* [메트릭을 사용하여 클러스터 상태 모니터링](using-metrics.md)

