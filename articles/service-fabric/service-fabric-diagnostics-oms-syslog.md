---
title: Azure Service Fabric에서 Linux 클러스터 이벤트 모니터링 | Microsoft Docs
description: Syslog에서 Linux 클러스터 이벤트를 모니터링하는 방법 알아보기
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118387"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Syslog의 Service Fabric Linux 클러스터 이벤트

Service Fabric은 사용자에게 클러스터의 중요한 작업에 대해 알리기 위해 플랫폼 이벤트 세트를 공개합니다. 공개되는 이벤트의 전체 목록은 [여기](service-fabric-diagnostics-event-generation-operational.md)서 확인할 수 있습니다. 이러한 이벤트를 사용할 수 있는 다양한 방법이 있습니다. 이 문서에서는 이러한 이벤트를 Syslog에 쓰도록 Service Fabric을 구성하는 방법을 설명합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>소개

6.4 릴리스에서는 Linux 클러스터에 대한 Syslog에 Service Fabric 플랫폼 이벤트를 보내기 위해 SyslogConsumer가 도입되었습니다. 이 기능을 켜면 Log Analytics 에이전트에서 수집 및 전송할 수 있는 이벤트가 자동으로 Syslog로 흐릅니다.

각 Syslog 이벤트에는 4개 구성 요소가 있습니다.
* Facility
* ID
* Message
* 심각도

SyslogConsumer는 Facility `Local0`를 사용하여 모든 플랫폼 이벤트를 씁니다. 구성을 변경하여 유효한 시설을 업데이트할 수 있습니다. 사용되는 Identity는 `ServiceFabric`입니다. 메시지 필드에는 다양한 도구에서 쿼리하고 사용할 수 있도록 JSON으로 직렬화된 전체 이벤트가 포함됩니다. 

## <a name="enable-syslogconsumer"></a>SyslogConsumer 사용

SyslogConsumer를 사용하려면 클러스터를 업그레이드해야 합니다. `fabricSettings` 섹션을 다음 코드로 업데이트해야 합니다. 이 코드는 SyslogConsumer와 관련된 섹션만 포함하고 있습니다.

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

실행할 변경 작업은 다음과 같습니다.
1. [일반] 섹션에는 `LinuxStructuredTracesEnabled`라는 새로운 매개 변수가 있습니다. **Syslog로 전송되는 Linux 이벤트를 정형화 및 직렬화하는 데 필요합니다.**
2. [진단] 섹션에는 새 ConsumerInstance인 SyslogConsumer가 추가되었습니다. 플랫폼에 또 다른 이벤트 소비자가 생긴 것입니다. 
3. 새로운 SyslogConsumer 섹션은 `IsEnabled`를 `true`로 설정해야 합니다. 이 항목은 Local0 시설을 사용하도록 자동으로 구성됩니다. 또 다른 매개 변수를 추가하여 이 설정을 재정의할 수 있습니다.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 로그 통합
Azure Monitor 로그와 같은 모니터링 도구에서 이러한 Syslog 이벤트를 읽을 수 있습니다. 다음 [지침]\(../azure-monitor/learn/quick-create-workspace.md)을 사용하는 Azure Marketplace를 통해 Log Analytics 작업 영역을 만들 수 있습니다. 또한 이 데이터를 수집하여 작업 영역으로 전송하려면 클러스터에 Log Analytics 에이전트를 추가해야 합니다. 성능 카운터를 수집하기 위해 사용되는 에이전트와 동일한 에이전트입니다. 

1. `Advanced Settings` 블레이드로 이동합니다.

    ![작업 영역 설정](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. `Data`을 클릭합니다.
3. `Syslog`을 클릭합니다.
4. 추적할 시설로 Local0를 구성합니다. fabricSettings에서 시설을 변경한 경우 다른 시설을 추가해도 됩니다.

    ![Syslog 구성](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. 작업 영역 리소스의 쿼리 시작 메뉴에서 `Logs`를 클릭하여 쿼리 탐색기로 이동합니다.

    ![작업 영역 로그](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. `Syslog` 테이블을 쿼리하여 ProcessName으로 `ServiceFabric`을 찾을 수 있습니다. 아래 쿼리는 이벤트의 JSON을 구문 분석하고 해당 콘텐츠를 표시하는 방법의 예입니다.

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog 쿼리](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

위의 예제는 NodeDown 이벤트입니다. 이벤트 전체 목록은 [여기](service-fabric-diagnostics-event-generation-operational.md)서 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 노드에 [Log Analytics 에이전트를 배포](service-fabric-diagnostics-oms-agent.md)하여 성능 카운터를 수집하고 컨테이너에 대한 docker 통계 및 로그를 수집합니다.
* 알아보기 합니다 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) Azure Monitor 로그의 일부로 제공 하는 기능
* [뷰 디자이너를 사용 하 여 Azure Monitor 로그에서 사용자 지정 뷰 만들기](../log-analytics/log-analytics-view-designer.md)
* 하는 방법에 대 한 참조 [Syslog와 통합을 기록 하는 Azure Monitor](../log-analytics/log-analytics-data-sources-syslog.md)합니다.
