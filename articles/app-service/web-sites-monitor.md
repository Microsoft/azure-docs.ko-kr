---
title: 앱 모니터링
description: Azure Portal을 사용하여 Azure App Service에서 앱을 모니터링하는 방법을 알아보세요. 보고 되는 할당량 및 메트릭을 파악 합니다.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 04/23/2020
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 888118d227529110c209d7c8d5a3bb79cfcf3a9a
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959794"
---
# <a name="monitor-apps-in-azure-app-service"></a>Azure App Service에서 앱 모니터링
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 은 [Azure Portal](https://portal.azure.com)에서 웹 앱, 모바일 및 API 앱에 대 한 기본 제공 모니터링 기능을 제공 합니다.

Azure Portal에서 앱에 대 한 *할당량* 및 *메트릭* 을 검토 하 고, 계획을 App Service 하 고, *경고* 를 *설정 하 고 규칙 기반* 메트릭을 자동 크기 조정 할 수 있습니다.

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
| **있어** | 허용되는 총 스토리지 양입니다. |

*Basic*, *Standard*및 *Premium* 에서 호스트 되는 앱에 적용 가능한 유일한 할당량은 파일 시스템입니다.

다양한 App Service SKU에 사용할 수 있는 특정 할당량, 한도 및 기능에 대한 자세한 내용은 [Azure 구독 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)을 참조하세요.

### <a name="quota-enforcement"></a>할당량 적용

앱이 *CPU(짧음)*, *CPU(일)*, *대역폭* 할당량을 초과하는 경우 할당량이 재설정될 때까지 앱이 중지됩니다. 이 시간 중에는 들어오는 모든 요청에서 HTTP 403 오류가 발생합니다.

![403 오류 메시지][http403]

앱 메모리 할당량을 초과 하면 앱이 일시적으로 중지 됩니다.

파일 시스템 할당량을 초과하는 경우 모든 쓰기 작업이 실패합니다. 쓰기 작업 실패에는 로그에 대한 쓰기가 포함됩니다.

App Service 계획을 업그레이드하여 앱에서 할당량을 늘리거나 제거할 수 있습니다.

## <a name="understand-metrics"></a>메트릭 이해

> [!NOTE]
> **파일 시스템 사용량** 은 전역적으로 롤아웃 되는 새 메트릭입니다. 비공개 미리 보기에 대 한 액세스 권한이 부여 되지 않은 경우에는 데이터가 필요 하지 않습니다.
> 

> [!IMPORTANT]
> 메트릭 집계와의 혼동을 피하기 위해 **평균 응답 시간은** 사용 되지 않습니다. **응답 시간** 을 대체 항목으로 사용 합니다.

> [!NOTE]
> 앱에 대 한 메트릭에는 앱의 SCM 사이트에 대 한 요청 (Kudu)이 포함 됩니다.  여기에는 Kudu을 사용 하 여 사이트의 logstream을 볼 수 있는 요청이 포함 됩니다.  Logstream 요청은 몇 분에 걸쳐 있을 수 있으며이는 요청 시간 메트릭에 영향을 줍니다.  자동 크기 조정 논리에서 이러한 메트릭을 사용 하는 경우 사용자는이 관계를 알고 있어야 합니다.
> 

메트릭은 앱 또는 App Service 계획의 동작에 대한 정보를 제공합니다.

앱에 사용 가능한 메트릭은 다음과 같습니다.

| 메트릭 | 설명 |
| --- | --- |
| **응답 시간** | 앱에서 요청을 처리 하는 데 걸린 시간 (초)입니다. |
| **평균 응답 시간 (사용 되지 않음)** | 앱에서 요청을 처리 하는 데 걸린 평균 시간 (초)입니다. |
| **평균 메모리 작업 집합** | 앱에 사용된 메가바이트(MiB) 크기의 평균 메모리 양입니다. |
| **연결** | 샌드박스(w3wp.exe 및 해당 자식 프로세스)에 있는 바인딩된 소켓의 수입니다.  바인딩된 소켓은 bind()/connect() API를 호출하여 만들어지며, 해당 소켓이 CloseHandle()/closesocket()으로 닫힐 때까지 유지됩니다. |
| **CPU 시간** | 앱에서 사용한 CPU의 양(초)입니다. 이 메트릭에 대 한 자세한 내용은 [cpu 시간 및 cpu 비율](#cpu-time-vs-cpu-percentage)을 참조 하세요. |
| **현재 어셈블리** | 이 애플리케이션의 모든 AppDomains에 로드된 어셈블리의 현재 개수입니다. |
| **데이터의** | 앱에서 사용한 들어오는 대역폭 양(MiB)입니다. |
| **데이터 출력** | 앱에서 사용한 나가는 대역폭 양(MiB)입니다. |
| **파일 시스템 사용량** | 앱에서 사용 하는 파일 시스템 할당량의 백분율입니다. |
| **Gen 0 가비지 수집** | 앱 프로세스가 시작된 이후 0세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다.|
| **Gen 1 가비지 수집** | 앱 프로세스가 시작된 이후 1세대 개체가 가비지 수집된 횟수입니다. 상위 세대 GC에는 모든 하위 세대 GC가 포함됩니다.|
| **Gen 2 가비지 수집** | 앱 프로세스가 시작된 이후 2세대 개체가 가비지 수집된 횟수입니다.|
| **핸들 개수** | 앱 프로세스에서 현재 열려 있는 총 핸들 수입니다.|
| **Http 2xx** | 결과로 나타나는 HTTP 상태 코드가 200 이상, 300 미만인 요청 수입니다. |
| **Http 3xx** | 결과로 나타나는 HTTP 상태 코드가 300 이상, 400 미만인 요청 수입니다. |
| **Http 401** | 결과로 HTTP 401 상태 코드가 나타나는 요청의 수입니다. |
| **Http 403** | 결과로 HTTP 403 상태 코드가 나타나는 요청의 수입니다. |
| **Http 404** | 결과로 HTTP 404 상태 코드가 나타나는 요청의 수입니다. |
| **Http 406** | 결과로 HTTP 406 상태 코드가 나타나는 요청의 수입니다. |
| **Http 4xx** | 결과로 나타나는 HTTP 상태 코드가 400 이상, 500 미만인 요청 수입니다. |
| **Http 서버 오류** | 결과로 나타나는 HTTP 상태 코드가 500 이상, 600 미만인 요청 수입니다. |
| **초당 IO 기타 바이트 수** | 응용 프로그램 프로세스가 데이터를 포함 하지 않는 i/o 작업 (예: 제어 작업)에 바이트를 발급 하는 속도입니다.|
| **초당 IO 기타 작업 수** | 앱 프로세스가 읽기 또는 쓰기 작업이 아닌 i/o 작업을 실행 하는 속도입니다.|
| **초당 IO 읽기 바이트 수** | 앱 프로세스에서 I/O 작업의 바이트를 읽는 속도입니다.|
| **초당 IO 읽기 작업 수** | 앱 프로세스에서 읽기 I/O 작업을 실행하는 속도입니다.|
| **초당 IO 쓰기 바이트 수** | 앱 프로세스에서 I/O 작업에 바이트를 쓰는 속도입니다.|
| **초당 IO 쓰기 작업 수** | 앱 프로세스에서 쓰기 I/O 작업을 실행하는 속도입니다.|
| **메모리 작업 집합** | 현재 앱에 사용된 메모리 양(MiB)입니다. |
| **전용 바이트** | Private Bytes는 앱 프로세스가 할당 하 여 다른 프로세스와 공유할 수 없는 메모리의 현재 크기 (바이트)입니다.|
| **요청** | 결과 HTTP 상태 코드에 관계 없이 총 요청 수입니다. |
| **애플리케이션 큐의 요청 수** | 애플리케이션 요청 큐의 요청 수입니다.|
| **스레드 수** | 앱 프로세스에서 현재 활성 상태인 스레드의 수입니다.|
| **총 앱 도메인** | 이 애플리케이션에 로드된 AppDomains의 현재 수입니다.|
| **언로드된 총 앱 도메인** | 애플리케이션이 시작된 이후 언로드된 총 AppDomains 수입니다.|


App Service 계획에 대해 사용 가능한 메트릭은 다음과 같습니다.

> [!NOTE]
> App Service 계획 메트릭은 *기본*, *표준* 및 *프리미엄* 계층의 계획에만 사용할 수 있습니다.
> 

| 메트릭 | 설명 |
| --- | --- |
| **CPU 비율** | 계획의 모든 인스턴스 간에 사용된 평균 CPU입니다. |
| **메모리 비율** | 계획의 모든 인스턴스 간에 사용된 평균 메모리입니다. |
| **데이터의** | 계획의 모든 인스턴스 간에 사용된 평균 들어오는 대역폭입니다. |
| **데이터 출력** | 계획의 모든 인스턴스 간에 사용된 평균 나가는 대역폭입니다. |
| **디스크 큐 길이** | 스토리지에 대기 중인 평균 읽기 및 쓰기 요청 수입니다. 디스크 큐 길이가 높으면 과도 한 디스크 i/o로 인해 속도가 저하 될 수 있는 앱을 나타냅니다. |
| **Http 큐 길이** | 처리하기 전에 큐에 배치해야 하는 평균 HTTP 요청 수입니다. HTTP 큐 길이 값이 높거나 증가하면 계획이 과부하 상태에 있음을 나타냅니다. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 시간 및 CPU 비율
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

CPU 사용량을 반영하는 두 가지 메트릭이 있습니다.

**Cpu 시간**: 할당량 중 하나가 앱에서 사용 되는 CPU 시간 (분)으로 정의 되므로 무료 또는 공유 계획에서 호스트 되는 앱에 유용 합니다.

**CPU 백분율**: 규모를 확장할 수 있으므로 Basic, Standard 및 Premium 요금제에서 호스트 되는 앱에 유용 합니다. CPU 백분율은 모든 인스턴스의 전체 사용량을 표시 하는 데 적합 합니다.

## <a name="metrics-granularity-and-retention-policy"></a>메트릭 세분성 및 보존 정책
앱 및 app service 계획에 대 한 메트릭은 서비스에 의해 기록 및 집계 되며 [이러한 규칙에 따라 유지](../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics)됩니다.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Azure Portal에서 할당량 및 메트릭을 모니터링합니다.
앱에 영향을 주는 다양한 할당량 및 메트릭 상태를 검토하려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

![Azure Portal의 할당량 차트][quotas]

할당량을 찾으려면 **설정**  >  **할당량**을 선택 합니다. 차트에서 다음을 검토할 수 있습니다. 
1. 할당량 이름
1. 재설정 간격
1. 현재 한도
1. 현재 값

![메트릭 차트 Azure Portal ][metrics] 리소스 **개요** 페이지에서 직접 메트릭에 액세스할 수 있습니다. 여기에서 일부 앱 메트릭을 나타내는 차트가 표시 됩니다.

이러한 차트 중 하나를 클릭 하면 사용자 지정 차트를 만들고 다양 한 메트릭을 쿼리할 수 있는 메트릭 뷰로 이동 합니다. 

메트릭에 대한 자세한 내용은 [서비스 메트릭 모니터링](../azure-monitor/platform/data-platform.md)을 참조하세요.

## <a name="alerts-and-autoscale"></a>경고 및 자동 크기 조정
앱 또는 App Service 계획의 메트릭은 경고에 연결할 수 있습니다. 자세한 내용은 [경고 알림 받기](../azure-monitor/platform/alerts-classic-portal.md)를 참조 하세요.

기본 이상 App Service 계획에서 호스트 되는 App Service 앱은 자동 크기 조정을 지원 합니다. 자동 크기 조정을 사용하여 App Service 계획 메트릭을 모니터링하는 규칙을 구성할 수 있습니다. 규칙은 필요에 따라 추가 리소스를 제공할 수 있도록 인스턴스 수를 눌리거나 줄일 수 있습니다. 규칙을 사용하면 앱이 과도하게 프로비전될 때 비용을 절감할 수도 있습니다.

자동 크기 조정에 대한 자세한 내용은 [크기 조정 방법](../azure-monitor/platform/autoscale-get-started.md) 및 [Azure Monitor 자동 크기 조정에 대한 모범 사례](../azure-monitor/platform/autoscale-best-practices.md)를 참조하세요.

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png