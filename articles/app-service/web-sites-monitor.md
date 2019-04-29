---
title: 앱 모니터링 - Azure App Service | Microsoft Docs
description: Azure Portal을 사용하여 Azure App Service에서 앱을 모니터링하는 방법을 알아보세요.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a5d4d13d8e60cd7f273363a9bc385098e15cbb71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832600"
---
# <a name="monitor-apps-in-azure-app-service"></a>Azure App Service에서 앱 모니터링
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)는 [Azure Portal](https://portal.azure.com)의 웹앱, 모바일 백 엔드 및 API 앱에 대해 기본 제공 모니터링 기능을 제공합니다.

Azure Portal에서 앱의 *할당량* 및 *메트릭*을 검토하고, App Service 계획을 검토하고, 이러한 메트릭을 기준으로 *경고* 및 *크기 조정*을 자동으로 설정합니다.

## <a name="understand-quotas"></a>할당량 이해

App Service를 호스트로 사용하는 앱에는 사용 가능한 리소스에 대한 특정 한도가 적용됩니다. 이러한 한도는 해당 앱과 연결된 App Service 계획으로 정의됩니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

앱이 *무료* 또는 *공유* 계획에 호스트되는 경우 앱에서 사용할 수 있는 리소스에 대한 한도는 할당량으로 정의됩니다.

앱이 *기본*, *표준* 또는 *프리미엄* 계획에 호스팅되는 경우 사용할 수 있는 리소스에 대한 한도는 *크기*(작음, 중간, 큼) 및 App Service 계획의 *인스턴스 수*(1, 2, 3 등)로 설정됩니다.

무료 또는 공유 앱의 할당량은 다음과 같습니다.

| 할당량 | 설명 |
| --- | --- |
| **CPU(짧음)** | 5분 간격으로 이 앱에 허용되는 CPU의 양입니다. 이 할당량은 5분마다 재설정됩니다. |
| **CPU(일)** | 하루에 이 앱에 허용되는 총 CPU 양입니다. 이 할당량은 자정 UTC에 24시간마다 재설정됩니다. |
| **메모리** | 이 앱에 허용되는 총 메모리 양입니다. |
| **대역폭** | 하루에 이 앱에 허용되는 나가는 총 대역폭 양입니다. 이 할당량은 자정 UTC에 24시간마다 재설정됩니다. |
| **파일 시스템** | 허용되는 총 스토리지 양입니다. |

*기본*, *표준* 및 *프리미엄* 계획에 호스트되는 앱에 적용 가능한 유일한 할당량은 파일 시스템입니다.

다양한 App Service SKU에 사용할 수 있는 특정 할당량, 한도 및 기능에 대한 자세한 내용은 [Azure 구독 서비스 제한](../azure-subscription-service-limits.md#app-service-limits)을 참조하세요.

### <a name="quota-enforcement"></a>할당량 적용

앱이 *CPU(짧음)*, *CPU(일)*, *대역폭* 할당량을 초과하는 경우 할당량이 재설정될 때까지 앱이 중지됩니다. 이 시간 중에는 들어오는 모든 요청에서 HTTP 403 오류가 발생합니다.

![403 오류 메시지][http403]

앱의 메모리 할당량을 초과하는 경우 앱이 다시 시작됩니다.

파일 시스템 할당량을 초과하는 경우 모든 쓰기 작업이 실패합니다. 쓰기 작업 실패에는 로그에 대한 쓰기가 포함됩니다.

App Service 계획을 업그레이드하여 앱에서 할당량을 늘리거나 제거할 수 있습니다.

## <a name="understand-metrics"></a>메트릭 이해

메트릭은 앱 또는 App Service 계획의 동작에 대한 정보를 제공합니다.

앱에 사용 가능한 메트릭은 다음과 같습니다.

| 메트릭 | 설명 |
| --- | --- |
| **평균 응답 시간** | 앱에서 요청을 처리하는 데 소요된 평균 시간(ms)입니다. |
| **평균 메모리 작업 집합** | 앱에 사용된 메가바이트(MiB) 크기의 평균 메모리 양입니다. |
| **연결** | 샌드박스(w3wp.exe 및 해당 자식 프로세스)에 있는 바인딩된 소켓의 수입니다.  바인딩된 소켓은 bind()/connect() API를 호출하여 만들어지며, 해당 소켓이 CloseHandle()/closesocket()으로 닫힐 때까지 유지됩니다. |
| **CPU 시간** | 앱에서 사용한 CPU의 양(초)입니다. 이 메트릭에 대한 자세한 내용은 [CPU 시간 및 CPU 백분율](#cpu-time-vs-cpu-percentage)을 참조하세요. |
| **현재 어셈블리 수** | 이 애플리케이션의 모든 AppDomains에 로드된 어셈블리의 현재 개수입니다. |
| **데이터 입력** | 앱에서 사용한 들어오는 대역폭 양(MiB)입니다. |
| **데이터 출력** | 앱에서 사용한 나가는 대역폭 양(MiB)입니다. |
| **Gen 0 가비지 수집** | 앱 프로세스가 시작된 이후 0세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다.|
| **Gen 1 가비지 수집** | 앱 프로세스가 시작된 이후 1세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다.|
| **Gen 2 가비지 수집** | 앱 프로세스가 시작된 이후 2세대 개체가 가비지 수집된 횟수입니다.|
| **핸들 수** | 앱 프로세스에서 현재 열려 있는 총 핸들 수입니다.|
| **Http 2xx** | 결과로 나타나는 HTTP 상태 코드가 200 이상, 300 미만인 요청 수입니다. |
| **Http 3xx** | 결과로 나타나는 HTTP 상태 코드가 300 이상, 400 미만인 요청 수입니다. |
| **Http 401** | 결과로 HTTP 401 상태 코드가 나타나는 요청의 수입니다. |
| **Http 403** | 결과로 HTTP 403 상태 코드가 나타나는 요청의 수입니다. |
| **Http 404** | 결과로 HTTP 404 상태 코드가 나타나는 요청의 수입니다. |
| **Http 406** | 결과로 HTTP 406 상태 코드가 나타나는 요청의 수입니다. |
| **Http 4xx** | 결과로 나타나는 HTTP 상태 코드가 400 이상, 500 미만인 요청 수입니다. |
| **Http 서버 오류** | 결과로 나타나는 HTTP 상태 코드가 500 이상, 600 미만인 요청 수입니다. |
| **초당 IO 기타 바이트 수** | 애플리케이션 프로세스에서 제어 작업과 같이 데이터가 포함되지 않은 I/O 작업에 바이트를 보내는 속도입니다.|
| **초당 IO 기타 작업 수** | 앱 프로세스에서 읽기 또는 쓰기 작업이 아닌 I/O 작업을 실행하는 속도입니다.|
| **초당 IO 읽기 바이트 수** | 앱 프로세스에서 I/O 작업의 바이트를 읽는 속도입니다.|
| **초당 IO 읽기 작업 수** | 앱 프로세스에서 읽기 I/O 작업을 실행하는 속도입니다.|
| **초당 IO 쓰기 바이트 수** | 앱 프로세스에서 I/O 작업에 바이트를 쓰는 속도입니다.|
| **초당 IO 쓰기 작업 수** | 앱 프로세스에서 쓰기 I/O 작업을 실행하는 속도입니다.|
| **메모리 작업 집합** | 현재 앱에 사용된 메모리 양(MiB)입니다. |
| **전용 바이트 수** | 전용 바이트 수는 다른 프로세스와 공유할 수 없는 앱 프로세스에서 할당한 메모리의 현재 크기(바이트)입니다.|
| **요청** | 결과 HTTP 상태 코드에 관계 없이 총 요청 수입니다. |
| **애플리케이션 큐의 요청 수** | 애플리케이션 요청 큐의 요청 수입니다.|
| **스레드 수** | 앱 프로세스에서 현재 활성 상태인 스레드의 수입니다.|
| **총 앱 도메인 수** | 이 애플리케이션에 로드된 AppDomains의 현재 수입니다.|
| **언로드된 총 앱 도메인 수** | 애플리케이션이 시작된 이후 언로드된 총 AppDomains 수입니다.|


App Service 계획에 대해 사용 가능한 메트릭은 다음과 같습니다.

> [!NOTE]
> App Service 계획 메트릭은 *기본*, *표준* 및 *프리미엄* 계층의 계획에만 사용할 수 있습니다.
> 

| 메트릭 | 설명 |
| --- | --- |
| **CPU 비율** | 계획의 모든 인스턴스 간에 사용된 평균 CPU입니다. |
| **메모리 비율** | 계획의 모든 인스턴스 간에 사용된 평균 메모리입니다. |
| **데이터 입력** | 계획의 모든 인스턴스 간에 사용된 평균 들어오는 대역폭입니다. |
| **데이터 출력** | 계획의 모든 인스턴스 간에 사용된 평균 나가는 대역폭입니다. |
| **디스크 큐 길이** | 저장소에 대기 중인 평균 읽기 및 쓰기 요청 수입니다. 디스크 큐 길이 값이 높으면 과도한 디스크 I/O로 인해 앱의 속도가 느려질 수 있음을 나타냅니다. |
| **Http 큐 길이** | 처리하기 전에 큐에 배치해야 하는 평균 HTTP 요청 수입니다. HTTP 큐 길이 값이 높거나 증가하면 계획이 과부하 상태에 있음을 나타냅니다. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 시간 및 CPU 비율
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

CPU 사용량을 반영하는 두 가지 메트릭이 있습니다.

**CPU 시간**: 해당 할당량 중 하나가 앱에 사용된 CPU 분으로 정의되므로 무료 또는 공유 계획으로 호스트된 앱에 유용합니다.

**CPU 백분율**: 확장이 가능하므로 기본, 표준 및 프리미엄 계획에서 호스트되는 앱에 유용합니다. CPU 백분율은 모든 인스턴스에서 전반적인 사용량을 나타내는 좋은 지표입니다.

## <a name="metrics-granularity-and-retention-policy"></a>메트릭 세분성 및 보존 정책
앱 및 App Service 계획에 대한 메트릭은 다음 세분성 및 보존 정책에 따라 서비스에 의해 기록 및 집계됩니다.

* **분** 세분성 메트릭은 30시간 동안 보존됩니다.
* **시** 세분성 메트릭은 30일 동안 보존됩니다.
* **일** 세분성 메트릭은 30일 동안 보존됩니다.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Azure Portal에서 할당량 및 메트릭을 모니터링합니다.
앱에 영향을 주는 다양한 할당량 및 메트릭 상태를 검토하려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

![Azure Portal의 할당량 차트][quotas]

할당량을 찾으려면 **설정** > **할당량**을 선택합니다. 차트에서 다음을 검토할 수 있습니다. 
1. 할당량 이름
1. 재설정 간격
1. 현재 한도
1. 현재 값

![Azure Portal의 메트릭 차트][metrics] **리소스** 페이지에서 직접 메트릭에 액세스할 수 있습니다. 차트를 사용자 지정하려면 
1. 차트를 선택합니다.
1. **차트 편집**을 선택합니다.
1. **시간 범위**를 편집합니다.
1. **차트 종류**를 편집합니다.
1. 표시할 메트릭을 편집합니다.  

메트릭에 대한 자세한 내용은 [서비스 메트릭 모니터링](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)을 참조하세요.

## <a name="alerts-and-autoscale"></a>경고 및 자동 크기 조정
앱 또는 App Service 계획의 메트릭은 경고에 연결할 수 있습니다. 자세한 내용은 [경고 알림 받기](../monitoring-and-diagnostics/insights-alerts-portal.md)를 참조하세요.

기본, 표준 또는 프리미엄 App Service 계획에 호스팅된 App Service 앱은 자동 크기 조정을 지원합니다. 자동 크기 조정을 사용하여 App Service 계획 메트릭을 모니터링하는 규칙을 구성할 수 있습니다. 규칙은 필요에 따라 추가 리소스를 제공할 수 있도록 인스턴스 수를 눌리거나 줄일 수 있습니다. 규칙을 사용하면 앱이 과도하게 프로비전될 때 비용을 절감할 수도 있습니다.

자동 크기 조정에 대한 자세한 내용은 [크기 조정 방법](../monitoring-and-diagnostics/insights-how-to-scale.md) 및 [Azure Monitor 자동 크기 조정에 대한 모범 사례](../azure-monitor/platform/autoscale-best-practices.md)를 참조하세요.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png