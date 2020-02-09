---
title: 웹 사이트의 가용성 및 응답성 모니터링 | Microsoft Docs
description: Application Insights에서 웹 테스트를 설정합니다. 웹 사이트가 사용할 수 없게 되거나 느리게 응답하는 경우 알림이 제공됩니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 074b5c175305131cd67cc6660d13756a83386c11
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819288"
---
# <a name="monitor-the-availability-of-any-website"></a>모든 웹 사이트의 가용성 모니터링

웹 앱/웹 사이트를 배포한 후에는 가용성 및 응답성을 모니터링 하도록 되풀이 테스트를 설정할 수 있습니다. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)는 전세계 지점에서 정기적인 간격으로 애플리케이션에 웹 요청을 보냅니다. 응용 프로그램이 응답 하지 않거나 너무 느리게 응답 하는 경우 사용자에 게 경고할 수 있습니다.

공용 인터넷에서 액세스 가능한 모든 HTTP 또는 HTTPS 엔드포인트에 대해 가용성 테스트를 설정할 수 있습니다. 테스트 하는 웹 사이트를 변경할 필요가 없습니다. 실제로 사용자가 소유 하는 사이트 일 필요는 없습니다. 서비스가 종속 된 REST API의 가용성을 테스트할 수 있습니다.

### <a name="types-of-availability-tests"></a>가용성 테스트의 유형:

가용성 테스트에는 세 가지 유형이 있습니다.

* [URL ping 테스트](#create-a-url-ping-test): Azure 포털에서 만들 수 있는 간단한 테스트입니다.
* [다단계 웹 테스트](availability-multistep.md): 더 복잡 한 시나리오를 테스트 하기 위해 재생할 수 있는 웹 요청 시퀀스의 기록입니다. 다중 단계 웹 테스트는 Visual Studio Enterprise에서 만들어지고 실행을 위해 포털에 업로드 됩니다.
* [사용자 지정 추적 가용성 테스트](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): 가용성 테스트를 실행 하는 사용자 지정 응용 프로그램을 만들 경우 `TrackAvailability()` 메서드를 사용 하 여 Application Insights에 결과를 보낼 수 있습니다.

**Application Insights 리소스 당 최대 100 개의 가용성 테스트를 만들 수 있습니다.**

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

가용성 테스트를 만들려면 먼저 Application Insights 리소스를 만들어야 합니다. 리소스를 이미 만든 경우 다음 섹션으로 이동 하 여 [URL Ping 테스트를 만듭니다](#create-a-url-ping-test).

Azure Portal에서 **리소스 만들기** > **개발자 도구** > **Application Insights** 를 선택 하 고 [Application Insights 리소스를 만듭니다](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>URL ping 테스트 만들기

"URL ping 테스트" 이름은 점에서 명칭입니다. 명확 하 게 하기 위해이 테스트에서는 ICMP (Internet Control Message Protocol)를 사용 하 여 사이트의 가용성을 확인 하지 않습니다. 대신, 엔드포인트이 응답 하는지 여부를 확인 하는 고급 HTTP 요청 기능을 사용 합니다. 또한 해당 응답과 관련 된 성능을 측정 하 고, 종속 요청 구문 분석과 같은 고급 기능을 사용 하 여 사용자 지정 성공 조건을 설정 하 고 다시 시도를 허용 하는 기능을 추가 합니다.

첫 번째 가용성 요청을 만들려면 가용성 창을 열고 **테스트 만들기**를 선택 합니다.

![웹 사이트의 최소 URL 채우기](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>테스트 만들기

|설정| 설명
|----|----|----|
|**URL** |  URL은 테스트 하려는 모든 웹 페이지 일 수 있지만 공용 인터넷에서 볼 수 있어야 합니다. URL에 쿼리 문자열을 포함할 수 있습니다. 따라서 데이터베이스 사용 등을 연습해 볼 수 있습니다. URL이 리디렉션으로 확인되면 최대 10개의 리디렉션을 따릅니다.|
|**종속 요청 구문 분석**| 테스트에서 테스트 중인 웹 페이지의 일부인 이미지, 스크립트, 스타일 파일 및 기타 파일을 요청 합니다. 기록된 응답 시간에는 이러한 파일을 가져오는 데 걸리는 시간이 포함됩니다. 전체 테스트에 대 한 시간 제한 내에 이러한 리소스를 성공적으로 다운로드할 수 없는 경우 테스트에 실패 합니다. 옵션을 선택하지 않으면 테스트는 지정한 URL에서만 파일을 요청합니다. 이 옵션을 사용 하면 더 엄격한 검사를 수행할 수 있습니다. 사례에 대해 테스트가 실패할 수 있으며,이는 사이트를 수동으로 검색할 때 눈에 띄지 않을 수 있습니다.
|**다시 시도 사용**|테스트에 실패 하면 잠시 후에 다시 시도 됩니다. 연속 된 세 번의 시도가 실패하는 경우에 실패가 보고됩니다. 후속 테스트는 일반적인 테스트 빈도로 수행됩니다. 다음 성공까지 다시 시도는 일시적으로 중단됩니다. 이 규칙은 각 테스트 위치에서 독립적으로 적용됩니다. **이 옵션을 선택 하는 것이 좋습니다**. 평균 실패의 약 80%는 다시 시도에서 사라집니다.|
|**테스트 빈도**| 각 테스트 위치에서 테스트를 실행하는 빈도를 설정합니다. 5분에 5번의 테스트를 하는 기본 빈도로 사이트를 평균 1분마다 테스트합니다.|
|**테스트 위치**| 서버가 URL로 웹 요청을 보내는 위치입니다. 웹 사이트의 문제를 네트워크 문제와 구별할 수 있도록 권장 되는 **테스트 위치의 최소 수는 5입니다** . 최대 16 개의 위치를 선택할 수 있습니다.

**공용 인터넷에서 URL을 볼 수 없는 경우를 통해 테스트 트랜잭션만 허용 하도록 방화벽을 선택적으로 열 수**있습니다. 가용성 테스트 에이전트에 대 한 방화벽 예외에 대해 자세히 알아보려면 [IP 주소 가이드](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)를 참조 하세요.

> [!NOTE]
> **최소 5 개 위치**를 사용 하 여 여러 위치에서 테스트 하는 것이 좋습니다. 이렇게 하면 특정 위치의 일시적인 문제 때문에 잘못된 경보가 발생하지 않도록 예방할 수 있습니다. 또한 최적의 구성이 **테스트 위치 수를 경고 위치 임계값 + 2와 동일**하 게 설정 하는 것을 발견 했습니다.

### <a name="success-criteria"></a>성공 조건

|설정| 설명
|----|----|----|
| **테스트 시간 제한** |느린 응답에 대한 알림을 받으려면 이 값을 감소시킵니다. 해당 기간 내에 사이트에서 응답을 받지 못한 경우 테스트는 실패로 계산됩니다. **종속 요청 구문 분석**을 선택한 경우 모든 이미지, 스타일 파일, 스크립트 및 다른 종속된 리소스도 해당 기간 내에 받아야 합니다.|
| **HTTP 응답** | 성공으로 계산되어 반환된 상태 코드입니다. 200은 일반적인 웹 페이지의 반환을 나타내는 코드입니다.|
| **콘텐츠 일치** | 문자열 (예: "환영!") 정확한 대/소문자 구분 일치가 모든 응답에서 발생하는지 테스트합니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다. **내용 일치에서는 영어 문자만 지원 됩니다.** |

### <a name="alerts"></a>경고

|설정| 설명
|----|----|----|
|**거의 실시간 (미리 보기)** | 거의 실시간으로 경고를 사용 하는 것이 좋습니다. 이 유형의 경고 구성은 가용성 테스트를 만든 후에 수행 됩니다.  |
|**클래식** | 새 가용성 테스트에 대 한 클래식 경고를 사용 하는 것이 더 이상 권장 되지 않습니다.|
|**경고 위치 임계값**|최소 3/5 위치를 사용하는 것이 좋습니다. 경고 위치 임계값과 테스트 위치 수 간의 최적 관계는**최소 5 개의 테스트 위치를 사용 하는 테스트 위치-2의 수**  =  **경고 위치 임계값** 입니다.|

## <a name="see-your-availability-test-results"></a>가용성 테스트 결과 참조

가용성 테스트 결과는 선 및 산 점도 뷰를 사용 하 여 시각화할 수 있습니다.

몇 분 후에 **새로 고침** 을 클릭 하 여 테스트 결과를 확인 합니다.

![줄 뷰](./media/monitor-web-app-availability/availability-refresh-002.png)

산 점도 보기에는 진단 테스트가 포함 된 테스트 결과 샘플이 표시 됩니다. 테스트 엔진은 실패한 테스트에 대한 진단 정보를 저장합니다. 성공한 테스트의 경우 실행의 하위 집합에 대한 진단 정보가 저장됩니다. 녹색/빨간색 점을 마우스로 가리켜서 테스트, 테스트 이름 및 위치를 확인 합니다.

![줄 뷰](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

특정 테스트, 위치를 선택하거나 기간을 줄여 대상 기간에서 더 많은 결과를 볼 수 있습니다. 검색 탐색기를 사용하여 모든 실행의 결과를 확인하거나 분석 쿼리를 사용하여 이 데이터에 대한 사용자 지정 보고서를 실행합니다.

## <a name="inspect-and-edit-tests"></a>테스트 검사 및 편집

테스트를 편집 하거나 일시적으로 사용 하지 않도록 설정 하거나 삭제 하려면 테스트 이름 옆의 줄임표를 클릭 합니다. 변경을 수행한 후에 구성 변경 내용이 모든 테스트 에이전트로 전파 되는 데 최대 20 분이 걸릴 수 있습니다.

![테스트 세부 정보를 봅니다. 웹 테스트 편집 및 사용 안 함](./media/monitor-web-app-availability/edit.png)

서비스에 대한 유지 관리를 수행하는 동안 가용성 테스트 또는 관련된 경고 규칙을 사용하지 않도록 설정할 수 있습니다.

## <a name="if-you-see-failures"></a>오류가 표시 되는 경우

빨간 점을 클릭합니다.

![빨간 점을 클릭 합니다.](./media/monitor-web-app-availability/open-instance-3.png)

가용성 테스트 결과에서 모든 구성 요소에서 트랜잭션 세부 정보를 볼 수 있습니다. 다음을 수행할 수 있습니다.

* 서버로부터 수신한 응답을 검사합니다.
* 실패한 가용성 테스트를 처리하는 동안 수집된 상관 관련된 서버 쪽 원격 분석 데이터로 실패를 진단합니다.
* Git 또는 Azure Boards에 문제 또는 작업 항목을 기록하여 문제를 추적합니다. 버그에는 이 이벤트에 대한 링크가 포함됩니다.
* 웹 테스트 결과를 Visual Studio에서 엽니다.

[여기에서](../../azure-monitor/app/transaction-diagnostics.md) 종단 간 트랜잭션 진단 환경에 대해 자세히 알아봅니다.

가상 가용성 테스트를 실패하게 만든 서버 쪽 예외 세부 정보를 확인하려면 예외 행을 클릭합니다. 다양한 코드 수준 진단에 대한 [디버그 스냅샷](../../azure-monitor/app/snapshot-debugger.md)을 가져올 수도 있습니다.

![서버 쪽 진단](./media/monitor-web-app-availability/open-instance-4.png)

원시 결과 외에도 [메트릭 탐색기](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)의 두 가지 주요 가용성 메트릭을 볼 수 있습니다.

1. 가용성: 모든 테스트 실행에서 성공한 테스트의 비율입니다.
2. 테스트 지속 시간: 모든 테스트 실행에서의 평균 테스트 지속 시간입니다.

## <a name="automation"></a>Automation

* [PowerShell 스크립트를 사용하여 가용성 테스트를 자동으로 설정](../../azure-monitor/app/powershell.md#add-an-availability-test)합니다.
* 경고가 발생하면 호출되는 [웹후크](../../azure-monitor/platform/alerts-webhooks.md)를 설정합니다.

## <a name="troubleshooting"></a>문제 해결

전용 [문제 해결 문서](troubleshoot-availability.md).

## <a name="next-steps"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [다단계 웹 테스트](availability-multistep.md)


