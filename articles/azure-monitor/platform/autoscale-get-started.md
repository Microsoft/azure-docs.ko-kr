---
title: Azure에서 자동 크기 조정 시작
description: Azure에서 리소스 웹앱, 클라우드 서비스, 가상 머신 또는 가상 머신 확장 집합의 크기를 조정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 710d4e1aa77f8ab3153dafc77a72eec2192cf205
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794534"
---
# <a name="get-started-with-autoscale-in-azure"></a>Azure에서 자동 크기 조정 시작
이 문서에서는 Microsoft Azure Portal에서 리소스에 대한 자동 크기 조정을 설정하는 방법에 대해 설명합니다.

Azure Monitor 자동 크기 조정은 [가상 컴퓨터 확장 집합](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/) 및 [API Management 서비스](../../api-management/api-management-key-concepts.md)에만 적용됩니다.

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>구독에서 자동 크기 조정 설정 검색

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Azure Monitor에서 자동 크기 조정을 적용할 수 있는 리소스를 모두 검색할 수 있습니다. 단계별 연습을 진행하려면 다음 단계를 수행합니다.

1. Azure Portal를 엽니다 [.][1]
1. 왼쪽 창에서 Azure Monitor 아이콘을 클릭합니다.
  ![Azure Monitor 열기][2]
1. **자동 크기 조정**을 클릭하여 자동 크기 조정을 적용할 수 있는 모든 리소스와 해당 리소스의 현재 자동 크기 조정 상태를 확인합니다.
  ![Azure Monitor에서 자동 크기 조정 검색][3]

위쪽의 필터 창을 사용하면 목록 범위를 좁혀 특정 리소스 그룹의 리소스, 특정 리소스 종류 또는 특정 리소스를 선택할 수 있습니다.

각 리소스에 대해 현재 인스턴스 수와 자동 크기 조정 상태를 확인할 수 있습니다. 자동 크기 조정 상태는 다음과 같을 수 있습니다.

- **구성되지 않음**: 이 리소스에 대해 자동 크기 조정 설정을 아직 사용하도록 설정하지 않았습니다.
- **사용**: 이 리소스에 대해 자동 크기 조정 설정을 사용하도록 설정했습니다.
- **사용 안 함**: 이 리소스에 대해 자동 크기 조정 설정을 사용하지 않도록 설정했습니다.

## <a name="create-your-first-autoscale-setting"></a>첫 번째 자동 크기 조정 설정 만들기

이제 간단한 단계별 연습을 통해 첫 번째 자동 크기 조정 설정을 만들어 보겠습니다.

1. Azure Monitor에서 **자동 크기 조정** 블레이드를 열고 크기를 조정할 리소스를 선택합니다. 아래 단계에서는 웹앱과 연결된 App Service 계획을 사용합니다. [Azure에서 5분 내에 첫 번째 ASP.NET 웹앱을 만들 수 있습니다.][4]
1. 현재 인스턴스 수는 1개입니다. **자동 크기 조정 사용**을 클릭합니다.
  ![새 웹앱에 대한 크기 조정 설정][5]
1. 크기 조정 설정의 이름을 입력하고 **규칙 추가**를 클릭합니다. 오른쪽에 컨텍스트 창으로 열리는 크기 조정 규칙 옵션을 확인합니다. 이 규칙은 기본적으로 리소스의 CPU 백분율이 70%를 초과하면 인스턴스 수를 하나씩 늘리도록 옵션을 설정합니다. 규칙을 기본값으로 유지하고 **추가**를 클릭합니다.
  ![웹앱에 대한 크기 조정 설정 만들기][6]
1. 이제 첫 번째 크기 조정 규칙을 만들었습니다. UX에 권장 모범 사례와 "하나 이상의 규모 감축 규칙이 있는 것이 좋습니다."라는 메시지가 표시됩니다. 이렇게 하려면 다음을 수행합니다.

    a. **규칙 추가**를 클릭합니다.

    b. **연산자**를 **보다 작음**으로 설정합니다.

    c. **임계값**을 **20**으로 설정합니다.

    d. **작업**을 **다음을 기준으로 개수 줄이기**로 설정합니다.

   이제 CPU 사용량을 기준으로 확장/축소하는 크기 조정 설정이 있어야 합니다.
   ![CPU 기준 크기 조정][8]
1. **저장**을 클릭합니다.

축하합니다! 이제 CPU 사용량을 기준으로 웹앱의 크기를 자동으로 조정하는 첫 번째 크기 조정 설정을 성공적으로 만들었습니다.

> [!NOTE]
> 가상 머신 확장 집합 또는 Cloud Service 역할의 크기 조정을 시작하려는 경우에도 동일한 단계를 적용할 수 있습니다.

## <a name="other-considerations"></a>기타 고려 사항
### <a name="scale-based-on-a-schedule"></a>일정을 기준으로 크기 조정
CPU 기준 크기 조정 외에도 특정 요일에 대한 크기 조정을 다르게 설정할 수 있습니다.

1. **크기 조정 조건 추가**를 클릭합니다.
1. 크기 조정 모드 및 규칙 설정은 기본 조건과 동일합니다.
1. 일정으로 **특정 일 반복**을 선택합니다.
1. 크기 조정 조건을 적용할 요일과 시작/종료 시간을 선택합니다.

![일정 기준 크기 조정 조건][9]
### <a name="scale-differently-on-specific-dates"></a>특정 날짜에 대해 다르게 크기 조정
CPU 기준 크기 조정 외에도 특정 날짜에 대한 크기 조정을 다르게 설정할 수 있습니다.

1. **크기 조정 조건 추가**를 클릭합니다.
1. 크기 조정 모드 및 규칙 설정은 기본 조건과 동일합니다.
1. 일정으로 **시작/종료 날짜 지정**을 선택합니다.
1. 크기 조정 조건을 적용할 시작/종료 날짜와 시작/종료 시간을 선택합니다.

![날짜 크기 조정 조건][10]

### <a name="view-the-scale-history-of-your-resource"></a>리소스의 크기 조정 기록 보기
리소스를 강화하거나 규모 축소할 때마다 이벤트가 활동 로그에 기록됩니다. **실행 기록** 탭으로 전환하여 지난 24시간 동안의 리소스의 크기 조정 기록을 볼 수 있습니다.

![실행 기록][11]

전체 크기 조정 기록(최대 90일 동안)을 보려면 **자세한 내용을 보려면 여기를 클릭하세요.** 를 선택합니다. 그러면 리소스와 범주에 해당하는 자동 크기 조정이 미리 선택된 활동 로그가 열립니다.

### <a name="view-the-scale-definition-of-your-resource"></a>리소스의 크기 조정 정의 보기
자동 크기 조정은 Azure Resource Manager의 리소스입니다. **JSON** 탭으로 전환하여 JSON의 크기 조정 정의를 볼 수 있습니다.

![크기 조정 정의][12]

필요한 경우 JSON에서 직접 변경할 수 있습니다. 이러한 변경 내용은 저장하고 나면 반영됩니다.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>자동 크기 조정을 사용하지 않도록 설정하고 인스턴스 크기를 수동으로 조정
현재 크기 조정 설정을 사용하지 않도록 설정하고 수동으로 리소스의 크기를 조정하려는 경우가 있을 수 있습니다.

이러한 경우에는 위쪽의 **자동 크기 조정 사용 안 함** 단추를 클릭합니다.
![자동 크기 조정 사용 안 함][13]

> [!NOTE]
> 이 옵션은 구성을 사용하지 않도록 설정합니다. 그러나 자동 크기 조정을 다시 사용하도록 설정한 후 해당 구성으로 되돌릴 수 있습니다.

이제 수동으로 크기를 조정하려는 인스턴스의 수를 설정할 수 있습니다.

![수동 크기 조정 설정][14]

**자동 크기 조정 사용**과 **저장**을 차례로 클릭하면 언제든지 자동 크기 조정을 다시 사용하도록 설정할 수 있습니다.

## <a name="route-traffic-to-healthy-instances-app-service"></a>정상적인 인스턴스로 트래픽 라우팅 (App Service)

여러 인스턴스로 확장 하는 경우 인스턴스에 대 한 상태 검사를 수행 하 여 트래픽을 정상 인스턴스로만 라우팅할 수 App Service. 이렇게 하려면 App Service 포털을 열고 **모니터링**아래에서 **상태 확인** 을 선택 합니다. **사용** 을 선택 하 고 응용 프로그램에서 또는와 같은 올바른 URL 경로를 제공 `/health` `/api/health` 합니다. **저장**을 클릭합니다.

### <a name="health-check-path"></a>상태 검사 경로

200에서 299 (포함) 사이의 상태 코드를 사용 하 여 2 분 이내에 경로를 응답 해야 합니다. 경로가 2 분 이내에 응답 하지 않거나 범위 밖의 상태 코드를 반환 하는 경우 인스턴스는 "비정상"으로 간주 됩니다. 상태 검사는 App Service의 인증 및 권한 부여 기능과 통합 되며, 시스템은 이러한 microsoft.powershell.secuity 기능을 사용 하는 경우에도 끝점에 연결 합니다. 사용자 고유의 인증 시스템을 사용 하는 경우 상태 검사 경로에서 익명 액세스를 허용 해야 합니다. 사이트에서 HTTP**s** 를 사용 하는 경우 HEALTHCHECK는 http**s** 를 사용 하 고 해당 프로토콜을 사용 하 여 요청을 보냅니다.

상태 검사 경로는 응용 프로그램의 중요 한 구성 요소를 확인 해야 합니다. 예를 들어 응용 프로그램이 데이터베이스 및 메시징 시스템에 종속 된 경우 상태 검사 끝점은 해당 구성 요소에 연결 해야 합니다. 응용 프로그램에서 중요 한 구성 요소에 연결할 수 없는 경우이 경로는 앱이 비정상 상태임을 나타내기 위해 500 수준 응답 코드를 반환 해야 합니다.

### <a name="behavior"></a>동작

상태 검사 경로를 제공 하면 App Service는 모든 인스턴스의 경로를 ping 합니다. 5 개의 ping 후에 성공적인 응답 코드를 받지 못한 경우 해당 인스턴스는 "비정상"으로 간주 됩니다. 비정상 인스턴스는 부하 분산 장치 순환에서 제외 됩니다. 또한 확장 하거나 축소할 때 상태 검사 경로를 ping 하 여 새 인스턴스가 요청에 대해 준비 되었는지 확인 App Service 합니다.

나머지 정상 인스턴스는 부하가 증가할 수 있습니다. 나머지 인스턴스가 과도 하 게 사용 되지 않도록 하려면 인스턴스 중 절반이 제외 됩니다. 예를 들어 App Service 계획을 4 개의 인스턴스로 확장 하 고 비정상 상태에서 3 개를 확장 하는 경우 최대 2 개는 loadbalancer 순환에서 제외 됩니다. 다른 두 인스턴스 (정상 및 비정상 1 개)는 계속 해 서 요청을 받습니다. 모든 인스턴스가 비정상 인 최악의 시나리오에서는 none이 제외 됩니다.

인스턴스가 1 시간 동안 비정상 상태로 유지 되 면 새 인스턴스로 바뀝니다. 최대 하나의 인스턴스만 시간당 교체 되며 App Service 요금제 당 최대 3 개의 인스턴스가 있습니다.

### <a name="monitoring"></a>모니터링

응용 프로그램의 상태 검사 경로를 제공한 후 Azure Monitor를 사용 하 여 사이트의 상태를 모니터링할 수 있습니다. 포털의 **상태 검사** 블레이드에서 상단 도구 모음에 있는 **메트릭을** 클릭 합니다. 그러면 사이트의 기록 상태를 확인 하 고 새 경고 규칙을 만들 수 있는 새 블레이드가 열립니다. 사이트 모니터링에 대 한 자세한 내용은 [Azure Monitor 가이드를 참조](../../app-service/web-sites-monitor.md)하세요.

## <a name="next-steps"></a>다음 단계
- [구독의 모든 자동 크기 조정 엔진 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [구독에서 실패한 모든 자동 크기 조정 규모 감축/규모 확장 작업을 모니터링하기 위한 활동 로그 경고를 만듭니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
