---
title: Azure Monitor의 데이터 수집 규칙 (미리 보기)
description: 콘텐츠 및 구조를 비롯 하 여 Azure Monitor에서의 데이터 수집 규칙 (데이터 수집 규칙) 개요와이를 만들고 사용 하는 방법을 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 177b79e0a33f4d43d07da9d0dea26df40e2ef11e
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723863"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Azure Monitor의 데이터 수집 규칙 (미리 보기)
DCR (데이터 수집 규칙)은 Azure Monitor에 들어오는 데이터를 정의 하 고 데이터를 보내거나 저장할 위치를 지정 합니다. 이 문서에서는 콘텐츠 및 구조를 포함 하는 데이터 수집 규칙의 개요와이를 만들고 사용 하는 방법을 설명 합니다.

## <a name="input-sources"></a>입력 소스
데이터 수집 규칙은 현재 다음과 같은 입력 소스를 지원 합니다.

- Azure Monitor 에이전트를 사용 하는 Azure 가상 컴퓨터. [Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)](data-collection-rule-azure-monitor-agent.md)을 참조 하세요.



## <a name="components-of-a-data-collection-rule"></a>데이터 수집 규칙의 구성 요소
데이터 수집 규칙에는 다음 구성 요소가 포함 됩니다.

| 구성 요소 | Description |
|:---|:---|
| 데이터 원본 | 자체 형식 및 해당 데이터를 노출 하는 방법을 사용 하 여 모니터링 데이터의 고유한 원본. 데이터 원본의 예로는 Windows 이벤트 로그, 성능 카운터 및 syslog가 있습니다. 각 데이터 원본은 아래에 설명 된 것 처럼 특정 데이터 원본 유형과 일치 합니다. |
| 스트림 | 한 가지 형식으로 변환 및 스키마 화 된 되는 데이터 원본 집합을 설명 하는 고유 핸들입니다. 각 데이터 원본에는 하나 이상의 스트림이 필요 하 고, 여러 데이터 원본에서 하나의 스트림을 사용할 수 있습니다. 스트림의 모든 데이터 원본은 공통 스키마를 공유 합니다. 예를 들어 동일한 Log Analytics 작업 영역에 있는 여러 테이블에 특정 데이터 원본을 보내려는 경우 여러 스트림을 사용 합니다. |
| Destinations | 데이터를 전송 해야 하는 대상 집합입니다. 예를 들면 Log Analytics 작업 영역, Azure Monitor 메트릭 및 Azure Event Hubs 있습니다. | 
| 데이터 흐름 | 대상에 보내야 하는 스트림을 정의 합니다. | 

다음 다이어그램에서는 데이터 수집 규칙 및 해당 관계의 구성 요소를 보여 줍니다.

[![DCR 다이어그램](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>데이터 원본 유형
각 데이터 원본에는 데이터 원본 유형이 있습니다. 각 형식은 각 데이터 원본에 대해 지정 해야 하는 고유한 속성 집합을 정의 합니다. 현재 사용할 수 있는 데이터 원본 유형은 다음 표에 나와 있습니다.

| 데이터 원본 유형 | Description | 
|:---|:---|
| 확장 | VM 확장 기반 데이터 원본 |
| performanceCounters | Windows 및 Linux에 대 한 성능 카운터 |
| syslog | Linux의 Syslog 이벤트 |
| windowsEventLogs | Windows 이벤트 로그 |


## <a name="limits"></a>제한
다음 표에서는 현재 각 데이터 수집 규칙에 적용 되는 제한을 나열 합니다.

| 제한 | 값 |
|:---|:---|
| 최대 데이터 원본 수 | 10 |
| 성능 카운터의 최대 카운터 지정자 수 | 100 |
| Syslog의 최대 기능 이름 수 | 20 |
| 이벤트 로그의 최대 XPath 쿼리 수 | 100 |
| 최대 데이터 흐름 수 | 10 |
| 최대 데이터 스트림 수 | 10 |
| 최대 확장 수 | 10 |
| 확장 프로그램 설정의 최대 크기 | 32 Kb |
| 최대 Log Analytics 작업 영역 수 | 10 |


## <a name="create-a-dcr"></a>DCR 만들기
현재 DCR를 만드는 데 사용할 수 있는 두 가지 방법이 있습니다.

- [Azure Portal를 사용](data-collection-rule-azure-monitor-agent.md) 하 여 데이터 수집 규칙을 만들고 하나 이상의 가상 컴퓨터와 연결 합니다.
- JSON에서 데이터 수집 규칙을 직접 편집 하 고 [REST API를 사용 하 여 제출](https://docs.microsoft.com/rest/api/monitor/datacollectionrules)합니다.

## <a name="sample-data-collection-rule"></a>샘플 데이터 수집 규칙
아래의 샘플 데이터 수집 규칙은 Azure 관리 에이전트를 사용 하는 가상 머신에 대 한 것 이며 다음과 같은 세부 정보를 포함 합니다.

- 성능 데이터
  - 15 초 마다 특정 프로세서, 메모리, 논리 디스크 및 실제 디스크 카운터를 수집 하 고 1 분 마다 업로드 합니다.
  - 30 초 마다 특정 프로세스 카운터를 수집 하 고 5 분 마다 업로드 합니다.
- Windows 이벤트
  - 1 분 마다 Windows 보안 이벤트 및 업로드를 수집 합니다.
  - Windows 응용 프로그램 및 시스템 이벤트를 수집 하 고 5 분 마다 업로드 합니다.
- syslog
  - Cron 기능에서 디버그, 중요 및 긴급 이벤트를 수집 합니다.
  - Syslog 기능에서 경고, 위험 및 긴급 이벤트를 수집 합니다.
- Destinations
  - CentralWorkspace 라는 Log Analytics 작업 영역에 모든 데이터를 보냅니다.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>다음 단계

- Azure Monitor 에이전트를 사용 하 여 가상 머신에서 [데이터 수집 규칙](data-collection-rule-azure-monitor-agent.md) 및 연결을 만듭니다.
