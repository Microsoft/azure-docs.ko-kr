---
title: Azure Monitor 경고에 대 한 작업 규칙
description: 작업 규칙은 항목 및 구성 및 관리 하는 방법을 이해 합니다.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 212e6b042caec5f24a620dc491dc674417816df7
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310372"
---
# <a name="action-rules-preview"></a>작업 규칙 (미리 보기)

작업 규칙을 사용 하 여 모든 리소스 관리자 범위 (구독, 리소스 그룹 또는 리소스)에서 작업 (또는 작업의 표시 안 함)를 정의할 수 있습니다. 기업은 다양 한 필터에 역할을 경고 인스턴스 특정 하위 집합 범위를 좁힐 수 있습니다. 

## <a name="why-and-when-should-you-use-action-rules"></a>이유와 시기 작업 규칙을 사용 하나요?

### <a name="suppression-of-alerts"></a>경고 표시 안 함

다양 한 시나리오는 유용 경고 표시 안 함 업무 외 시간에 계획 된 유지 관리 기간 중에 비 표시 범위의 수에 의해 생성 된 알림을 표시 하지 않으려면 많습니다. 예를 들어, 'ContosoVM' 팀 'ContosoVM' 하는 계획 된 유지 관리 중 이므로 향후 주말에 대 한 알림을 표시 하지 않으려면 하려고 합니다. 가능 하지만 각 경고 'ContosoVM'에서 수동으로 구성 하는 규칙 (및 해당 게시를 유지 관리를 다시 사용 하도록 설정)를 사용 하지 않도록 설정, 간단한 환경을 아닙니다. 작업 규칙을 사용 하 여 경고 표시 안 함 대규모로 유연 하 게 제거 기간을 구성 하는 기능을 사용 하 여 정의할 수 있습니다. 이전 예로 돌아가서, 팀에서 주말에 대 한 모든 알림을 표시 하지 'ContosoVM'에서 작업 규칙을 하나를 정의 이제 수 있습니다.


### <a name="actions-at-scale"></a>대규모 작업

경고 규칙을 사용 하면 경고가 생성 되 면 트리거되는 작업 그룹을 정의할 수 있습니다, 있지만 고객은 종종 작업의 범위에서 일반 작업 그룹을 경향이 있습니다. 예를 들어, 'ContosoRG' 리소스 그룹에 대 한 팀 아마도 'ContosoRG' 내에 정의 된 모든 경고 규칙에 대해 동일한 작업 그룹을 정의 됩니다. 작업 규칙을 사용 하면 대규모 작업을 정의할 수 있습니다 작업 그룹은 구성된 범위에 생성 된 모든 경고에 대 한 트리거될 수 있도록 허용 하 여이 프로세스를 간소화 하기 위해 수행할 수 있습니다. 이전 예로 돌아가서, 팀 이제 하나의 작업 규칙 정의에서 동일한 작업 그룹에 생성 된 모든 경고를 트리거하는 'ContosoRG'.

> [!NOTE]
> 작업 규칙이 현재 적용 되지 않습니다 서비스 상태 경고 합니다.

## <a name="configuring-an-action-rule"></a>작업 규칙 구성

선택 하 여 기능을 사용할 수 있습니다 **작업을 관리할** 경고 Azure Monitor에서 페이지를 방문 합니다. 선택한 **작업 규칙 (미리 보기)** 합니다. 선택 하 여 액세스할 수 있습니다 **작업 규칙 (미리 보기)** 대시보드에서 경고에 대 한 방문 페이지입니다.

![Azure Monitor 방문 페이지에서 작업 규칙](media/alerts-action-rules/action-rules-landing-page.png)

선택 **+ 새 작업 규칙**합니다. 

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-new-rule.png)

또는 경고 규칙을 구성 하는 동안 작업 규칙을 만들 수도 있습니다.

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-alert-rule.png)

이제 엽니다 작업 규칙 만들기 흐름을 표시 됩니다. 다음 요소를 구성 합니다. 

![새 작업 규칙 만들기 흐름](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

먼저 범위, 즉, 대상 리소스, 리소스 그룹 또는 구독을 선택 합니다. 해야 다중 선택 하는 기능 (내에서 단일 구독) 위 조합 합니다. 

![작업 규칙 범위](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>필터 조건

또한 정의에서 경고의 특정 하위 집합으로 좁힙니다에 필터를 정의할 수 있습니다. 

사용 가능한 필터는 다음과 같습니다. 

* **심각도**: 하나 이상의 경고 심각도 선택 합니다. 심각도 = Sev1 작업 규칙 심각도 Sev1로을 사용 하 여 모든 경고에 대 한 해당 임을 의미 합니다.
* **서비스 모니터링**: 원래 모니터링 서비스를 기반으로 하는 필터입니다. 다중 선택 이기도합니다. 예를 들어 모니터 서비스 = "Application Insights" 기반의 경고를 작업 규칙은 "Application Insights"에 적용할 수는 것을 의미 합니다.
* **리소스 종류**: 특정 리소스 종류를 기준으로 필터링 합니다. 다중 선택 이기도합니다. 예를 들어, 리소스 유형 = "가상 머신" 의미 작업 규칙은 모든 가상 컴퓨터에 적용 됩니다.
* **경고 규칙 ID**: 경고 규칙의 Resource Manager ID를 사용 하 여 특정 경고 규칙에 대 한 필터링 할 수 있습니다.
* **조건 모니터링**: 모니터 조건으로 "실행 됨" 또는 "Resolved" 중 하나를 사용 하 여 경고 인스턴스를 필터링 합니다.
* **설명**: 경고 규칙의 일부로 정의 된 설명 내에서 일치 하는 정규식입니다.
* **경고 컨텍스트 (페이로드)** : 정규식 내에서 일치 합니다 [경고 컨텍스트](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) 경고 인스턴스 필드입니다.

이러한 필터는 서로 함께에서 적용 됩니다. 예를 들어, '리소스 종류' 설정 = ' 가상 컴퓨터 ' 및 '심각도' = 'Sev0' 내 Vm에만에 모든 'Sev0' 경고에 대 한 필터링 한 합니다. 

![작업 규칙 필터](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>표시 안 함 또는 작업 그룹 구성

다음 경고 표시 안 함 또는 작업 그룹 지원에 대 한 작업 규칙을 구성 합니다. 둘 다를 선택할 수 없습니다. 구성 이전에 정의 된 범위 및 필터와 일치 하는 모든 경고 인스턴스에서 작동 합니다.

#### <a name="suppression"></a>표시 안 함

선택 하는 경우 **억제**, 작업 및 알림 억제에 대 한 기간을 구성 합니다. 다음 중 하나를 선택합니다.
* **지금부터 (항상)** : 무기한 모든 알림을 표시 하지 않습니다.
* **예약된 된 시간에**: 제한 기간 내에서 알림을 표시 합니다.
* **되풀이 사용 하 여**: 매일, 매주 또는 매월 수 있는 되풀이 일정에 따라 표시 하지 않습니다.

![작업 규칙 표시 안 함](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>작업 그룹

선택 하는 경우 **작업 그룹** 에서 설정/해제, 기존 작업 그룹을 추가 하거나 새로 만듭니다. 

> [!NOTE]
> 작업 규칙을 사용 하 여 하나의 작업 그룹을 연결할 수 있습니다.

![작업 규칙 동작 그룹](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>작업 규칙 세부 정보

마지막으로, 작업 규칙에 대 한 다음 세부 정보 구성
* Name
* 이 저장 될 리소스 그룹
* 설명 

## <a name="example-scenarios"></a>예제 시나리오

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>시나리오 1: 심각도에 따라 경고 표시 안 함

Contoso는 'ContosoSub' 주말 마다 해당 구독 내의 모든 Vm에 대 한 모든 Sev4 경고에 대 한 알림을 표시 하려고 합니다.

**해결 방법:** 사용 하 여 작업 규칙 만들기
* Scope = 'ContosoSub'
* 필터
    * Severity = 'Sev4'
    * 리소스 종류 ' 가상 컴퓨터 ' =
* 되풀이 사용 하 여 제거를 매주로 설정 하 고 '토요일' 및 '일요일' 검사

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>시나리오 2: 경고 컨텍스트 (페이로드)를 기반으로 경고 표시 안 함

Contoso에서는 모든 ' 컴퓨터-01'에 대해 생성 된 경고를 로그에 대 한 알림을 표시 하지 않으려면 'ContosoSub'에서 무기한으로 유지 관리를 통과 합니다.

**해결 방법:** 사용 하 여 작업 규칙 만들기
* Scope = 'ContosoSub'
* 필터
    * Monitor Service = 'Log Analytics'
    * 경고 컨텍스트 (페이로드) ' 컴퓨터-01'를 포함합니다.
* 비 표시로 ' 지금부터 (항상)'

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>시나리오 3: 리소스 그룹에서 정의한 작업 그룹

Contoso가 정의 [구독 수준에서 메트릭 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), 해당 리소스 그룹 'ContosoRG'에서 생성 된 경고에 대 한 특히 트리거하는 동작을 정의 하려고 합니다.

**해결 방법:** 사용 하 여 작업 규칙 만들기
* Scope = 'ContosoRG'
* 필터가 없습니다
* 'ContosoActionGroup'로 설정 하는 작업 그룹

> [!NOTE]
> **작업 규칙 내에 정의 된 작업 그룹 및 경고 규칙 독립적으로 작동할 수 없는 중복 제거를 사용 하 여**입니다. 작업 그룹에 없는 경우 위에서 설명한 시나리오에서 경고 규칙에 대 한 정의 트리거합니다 함께에서 작업 규칙에 정의 된 작업 그룹을 사용 하 여 합니다. 

## <a name="managing-your-action-rules"></a>작업 규칙 관리

확인 하 고 아래와 같이 목록 뷰에서 작업 규칙을 관리할 수 있습니다.

![작업 규칙 목록 보기](media/alerts-action-rules/action-rules-list-view.png)

여기에서 옆에 있는 확인란을 선택 하 여 대규모 규칙 사용/사용 안 함/삭제 작업을 수 있습니다. 작업 규칙 클릭 하면 해당 정의 업데이트 하 고 해당 활성화/비활성화 하 수 있도록 해당 구성 페이지에서 열립니다.

## <a name="best-practices"></a>모범 사례

로그 경고를 사용 하 여 만든 합니다 ['결과 수'](alerts-unified-log.md) 옵션 생성 **단일 경고 인스턴스** (수 있는 여러 컴퓨터에서 예를 들어) 전체 검색 결과 사용 하 여 합니다. 이 시나리오에서는 작업 규칙을 ' 경고 컨텍스트 (페이로드)' 필터를 사용 하는 경우 작동 경고 인스턴스에서으로 일치 하는 것입니다. 시나리오 2 ' 컴퓨터-01' 및 ' 컴퓨터-02'에서 생성 된 로그 경고에 대 한 검색 결과 포함 하는 경우 이전에 설명 된 대로 전체 알림이 표시 되지 않습니다 (즉,가 전혀 생성 ' 컴퓨터-02'에 대 한 알림 없음).

![작업 규칙 및 로그 경고 (결과 수)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

작업 규칙을 사용 하 여 최상의 활용 하 여 로그 경고로 좋습니다 로그 경고를 만들 수는 ['메트릭 측정'](alerts-unified-log.md) 옵션입니다. 이 옵션을 사용 하 여 별도 경고 인스턴스 정의 된 그룹 필드를 기반으로 생성 됩니다. 다음 시나리오 2에서는 ' 컴퓨터-01' 및 ' 컴퓨터-02'에 대 한 별도 경고 인스턴스가 생성 됩니다. 시나리오에서 설명한 작업 규칙을 사용 하 여 ' 컴퓨터-01'에 대 한 알림만 표시 하지 않을 ' 컴퓨터-02'에 대 한 알림을 계속 정상적으로 실행 됩니다.

![작업 규칙 및 로그 경고 (결과 수)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

* Q. 작업 규칙을 구성 하는 동안 알림이 중복 수신 방지할 수 있도록 작업 겹치는 모든 가능한 규칙을 보려면 싶습니다. 이렇게 할 수는?

    a. 작업 규칙을 구성 하는 동안 범위를 정의 하 고 나면 (있는 경우) 동일한 범위에서 겹치는 동작 규칙의 목록을 볼 수 있습니다. 이 처럼 중복 되는 다음 옵션 중 하나일 수 있습니다.
    * 정확히 일치: 예를 들어, 정의 하는 작업 규칙와 겹치는 작업 규칙 동일한 구독에 있습니다.
    * 하위 집합: 예를 들어, 구독을 정의 하는 작업 규칙은 이며, 겹치는 작업 규칙 구독 내에서 리소스 그룹입니다.
    * 상위 집합: 예를 들어, 리소스 그룹을 정의 하는 작업 규칙은 겹치는 작업 규칙은 리소스 그룹을 포함 하는 구독을
    * 교집합: 예를 들어, 'VM1' 및 'VM2'에 정의 하는 작업 규칙은 이며 겹치는 작업 규칙 'VM2' 및 'VM3'.

    ![겹치는 작업 규칙](media/alerts-action-rules/action-rules-overlapping.png)

* Q. 경고 규칙을 구성 하는 동안가 이미 작업 규칙을 정의 하는 알 수를 정의 하는 경고 규칙에서 실행 될 수 있습니까?

    a. 경고 규칙의 대상 리소스를 정의 하 고 나면 'Actions' 섹션에서 '보기 작업을 구성'을 클릭 하 여 (있는 경우) 동일한 범위에서 작동 하는 작업 규칙 목록을 볼 수 있습니다. 이 목록은 범위에 대 한 다음과 같은 시나리오에 따라 채워집니다.
    * 정확히 일치: 예를 들어, 정의 하는 경고 규칙 및 작업 규칙에 동일한 구독입니다.
    * 하위 집합: 예를 들어, 구독을 정의 하는 경고 규칙은 구독 내의 리소스 그룹에 있는 작업 규칙 인 하며
    * 상위 집합: 예를 들어, 리소스 그룹을 정의 하는 경고 규칙은 리소스 그룹을 포함 하는 구독에 있는 작업 규칙 인 하며
    * 교집합: 예를 들어, 'VM1' 및 'VM2'에 정의 하는 경고 규칙은 및 작업 규칙은 'VM2' 및 'vm3 은'.
    
    ![겹치는 작업 규칙](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* Q. 작업 규칙에 의해 기록 되지 않은 경고를 볼 수 있나요?

    a. 에 [경고 목록 페이지](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), 호출된 ' 표시 안 함 상태 ' 선택할 수 있는 추가 열이 있습니다. 경고 인스턴스에 대 한 알림 억제 된 경우 해당 상태 목록에 나타납니다.

    ![경고 억제 된 인스턴스](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* Q. 작업 그룹을 사용 하 여 작업 규칙을 지정 하 고 제거를 사용 하 여 다른 활성에 있으면 같은 범위 어떻게 되나요?

    a. **동일한 범위에 항상 표시 안 함 우선**합니다.

* Q. 두 개의 별도 작업 규칙에서 모니터링 하는 리소스를 질문이 있으면 어떻게 되나요? 하나 또는 두 개의 알림을 가져오나 요? 예를 들어가이 시나리오에서는 'VM2'을 선택 합니다.

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    a. 'VM1' 및 'vm3 은'에서 모든 경고에 대 한 작업 그룹 'AG1' 트리거될 수 한 번입니다. 'VM2'에서 모든 경고에 대 한 작업 그룹 'AG1' 트리거될 수 두 번 (**작업 규칙 작업을 취소 중복 되지 않는**). 

* Q. 두 개의 별도 작업 규칙에서 모니터링 리소스 있고 제거에 대 한 다른 작업에 대 한 하나를 호출 하는 경우 어떻게 됩니까? 예를 들어,이 시나리오에서는 ' VM2'.

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    a. 'VM1'에서 모든 경고에 대 한 작업 그룹 'AG1' 트리거될 수 한 번입니다. 작업 및 'VM2' 및 'vm3 은'에 대 한 모든 경고에 대 한 알림을 표시 되지 것입니다. 

* Q. 경고 규칙 및 동일한 리소스 호출 하는 다른 작업 그룹에 대해 정의 된 작업 규칙 질문이 있으면 어떻게 되나요? 예를 들어,이 시나리오에서는 ' VM1'.

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    a. 'VM1'에서 모든 경고에 대 한 작업 그룹 'AG1' 트리거될 수 한 번입니다. 경고 규칙 'rule1' 트리거될 때마다도 트리거합니다 'AG2를' 또한. **작업 규칙 내에 정의 된 작업 그룹 및 경고 규칙 독립적으로 작동할 수 없는 중복 제거를 사용 하 여**입니다. 

## <a name="next-steps"></a>다음 단계

- [Azure에서 경고에 자세히 알아보기](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
