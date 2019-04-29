---
title: Azure Application Insights Profiler 설정 창 사용 | Microsoft Docs
description: Profiler 상태 보기 및 프로파일링 세션 시작
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 9603c45443c6339a127f977600eeff2ba57a283f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228235"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler 구성

## <a name="profiler-settings-pane"></a>Profiler 설정 창

Azure Application Insights Profiler 설정 창을 열려면 Application Insights 성능 창으로 이동하여 **Profiler** 단추를 선택합니다.

![Profiler 창 구성][configure-profiler-entry]

**Application Insights Profiler 구성** 창에는 다음의 4개 기능이 포함되어 있습니다. 
* **지금 프로필**: 이 Application Insights 인스턴스에 연결된 모든 앱에 대해 프로파일링 세션이 시작됩니다.
* **연결된 앱**: 이 Application Insights 리소스에 프로파일링 데이터를 전송하는 애플리케이션의 목록을 표시합니다.
* **진행 중인 세션**: **지금 프로필**을 선택하면 세션의 상태가 표시됩니다. 
* **최근 프로파일링 세션**: 이전 프로파일링 세션에 대한 정보가 표시됩니다.

![주문형 프로파일러][profiler-on-demand]

## <a name="app-service-environment"></a>App Service 환경
Azure App Service Environment가 구성된 방식에 따라 에이전트 상태를 확인하기 위한 호출이 차단될 수 있습니다. 에이전트가 실행되고 있는데도 창에는 에이전트가 실행되고 있지 않다는 메시지가 표시될 수 있습니다. 에이전트가 실행되고 있는지 확인하려면 애플리케이션의 웹 작업을 점검합니다. 모든 앱 설정 값이 올바르며 애플리케이션에 Application Insights 사이트 확장이 설치되어 있으면 Profiler가 실행되고 있는 것입니다. 애플리케이션이 충분한 트래픽을 수신하고 있다면 목록에 최근 프로파일링 세션이 표시되어야 합니다.

## <a id="profileondemand"></a> 수동으로 Profiler 트리거

### <a name="minimum-requirements"></a>최소 요구 사항 
수동으로 프로파일러 세션을 트리거하는 사용자에 대 한 최소한 "쓰기" 액세스 권한 Application Insights 구성 요소에 대해 해당 역할에 필요 합니다. 대부분의 경우에서이 액세스 권한을 자동으로 만들어지고 추가 작업이 필요. 문제가 있는 경우에 "Application Insights 구성 요소 기여자" 역할이 추가할 구독 범위 역할을 것입니다. [Azure Monitoring과 역할 액세스 제어에 대해 자세히 알아봅니다](https://docs.microsoft.com/en-us/azure/azure-monitor/app/resources-roles-access-control)합니다.

클릭 한 번으로 Profiler를 수동 트리거할 수 있습니다. 웹 성능 테스트를 실행 중이라고 가정해 보겠습니다. 이 경우 추적을 통해 부하 발생 시의 웹앱 성능을 파악해야 합니다. 부하 테스트를 실행할 시점은 알고 있으므로 추적이 캡처되는 시기를 제어할 수 있어야 합니다. 하지만 샘플링 간격이 무작위이면 추적이 캡처되는 시기를 확인하지 못할 수도 있습니다.

다음 섹션에서는 이 시나리오의 작동 방식에 대해 설명합니다.

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>1단계: (선택 사항) 웹 성능 테스트를 시작하여 웹앱에 트래픽 생성

웹앱에 들어오는 트래픽을 이미 있거나 수동으로 트래픽을 생성하려는 경우 이 섹션을 건너뛰고 2단계를 계속 진행합니다.

1. Application Insights 포털에서 **구성** > **성능 테스트**를 선택합니다. 

1. 새 성능 테스트를 시작하려면 **새로 만들기** 단추를 선택합니다.

   ![새 성능 테스트 만들기][create-performance-test]

1. **새 성능 테스트** 창에서 테스트 대상 URL을 구성합니다. 모든 기본 설정을 그대로 적용하고 **테스트 실행**을 선택하여 부하 테스트 실행을 시작합니다.

    ![부하 테스트 구성][configure-performance-test]

    새 테스트는 먼저 큐에 대기되었다가 *진행 중* 상태로 전환됩니다.

    ![부하 테스트가 제출되고 큐에 대기됨][load-test-queued]

    ![부하 테스트 진행이 진행 중임][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>2단계: Profiler 주문형 세션 시작

1. 부하 테스트가 실행되면 Profiler를 시작하여 부하를 수신하는 동안 웹앱에서 추적을 캡처합니다.

1. **Profiler 구성** 창으로 이동합니다.


### <a name="step-3-view-traces"></a>3단계: 추적 보기

Profiler 실행이 완료되면 알림의 지침에 따라 성능 창으로 이동하여 추적을 확인합니다.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Profiler 주문형 세션 문제 해결

주문형 세션 이후에 Profiler 시간 제한 오류 메시지가 표시될 수 있습니다.

![프로파일러 시간 제한 오류][profiler-timeout]

이 오류는 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 주문형 Profiler 세션은 정상 완료되었지만 Application Insights가 수집된 데이터를 처리하는 데 시간이 예상보다 오래 걸렸습니다.  

  데이터가 15분 내에 처리되지 않으면 포털에 시간 제한 메시지가 표시됩니다. 그러나 잠시 후에는 Profiler 추적이 표시됩니다. 오류 메시지가 나타나면 일단은 무시합니다. 해결하기 위해 적극적으로 노력하고 있습니다.

* 웹앱의 Profiler 에이전트가 주문형 기능을 포함하지 않는 오래된 버전입니다.  

  Application Insights Profiler를 이전에 사용하도록 설정한 경우 주문형 기능 사용을 시작하려면 Profiler 에이전트를 업데이트해야 할 수 있습니다.
  
App Services **앱 설정** 창으로 이동하여 다음 설정을 확인합니다.
* **APPINSIGHTS_INSTRUMENTATIONKEY**: Application Insights에 대한 적절한 계측 키로 바꿉니다.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

위의 값 중 하나라도 설정되어 있지 않으면 다음 단계를 수행하여 최신 사이트 확장을 설치합니다.

1. App Services 포털에서 **Application Insights** 창으로 이동합니다.

    ![App Services 포털에서 Application Insights를 사용하도록 설정][enable-app-insights]

1. **Application Insights** 창에 **업데이트** 단추가 표시되면 해당 단추를 선택하여 Application Insights 사이트 확장을 업데이트합니다. 그러면 최신 Profiler 에이전트가 설치됩니다.

    ![사이트 확장 업데이트][update-site-extension]

1. Profiler를 설정하려면 **변경**, **확인**을 차례로 선택하여 변경 내용을 저장합니다.

    ![앱 인사이트 변경 및 저장][change-and-save-appinsights]

1. App Services의 **앱 설정** 탭으로 돌아가서 다음 값이 설정되어 있는지 확인합니다.
   * **APPINSIGHTS_INSTRUMENTATIONKEY**: Application Insights에 대한 적절한 계측 키로 바꿉니다.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![Profiler의 앱 설정][app-settings-for-profiler]

1. 필요에 따라 **확장**을 선택한 다음 확장 버전을 점검하고 업데이트를 사용할 수 있는지 확인합니다.

    ![확장 업데이트 점검][check-for-extension-update]

## <a name="next-steps"></a>다음 단계
[Profiler 사용 및 추적 보기](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
