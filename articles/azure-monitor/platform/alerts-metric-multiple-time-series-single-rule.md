---
title: 단일 메트릭 경고 규칙에서 여러 시간 계열 모니터링
description: 여러 시계열에 대 한 단일 경고 규칙을 사용 하 여 규모에서 경고
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 08/11/2020
ms.subservice: alerts
ms.openlocfilehash: f7ca91ca49d9357285e1307c5051ef5685ad24c9
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88187000"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>단일 메트릭 경고 규칙에서 여러 시간 계열 모니터링

단일 메트릭 경고 규칙을 사용 하 여 하나 이상의 메트릭 시계열을 모니터링할 수 있으므로 대규모로 리소스를 보다 쉽게 모니터링할 수 있습니다.

## <a name="metric-time-series"></a>메트릭 시계열

메트릭 시계열은 일정 기간 동안 캡처된 일련의 측정값 (또는 "메트릭 값")입니다. 

예를 들어:

- 가상 컴퓨터의 CPU 사용률
- 저장소 계정에 대 한 수신 바이트 (수신)
- 웹 응용 프로그램의 실패 한 요청 수



## <a name="alert-rule-on-a-single-time-series"></a>단일 시계열에 대 한 경고 규칙
경고 규칙은 다음 조건을 모두 충족 하는 단일 시계열을 모니터링 합니다.
-   규칙은 단일 대상 리소스를 모니터링 합니다. 
-   단일 조건을 포함 합니다.
-   차원을 선택 하지 않고 메트릭을 평가 합니다 (메트릭이 차원을 지원함을 전제로 함).

관련 속성만 표시 되는 이러한 경고 규칙의 예는 다음과 같습니다.
-   대상 리소스: *myVM1*
-   메트릭: *CPU 백분율*
-   연산자: *보다 큼*
-   임계값: *70*


이 경고 규칙의 경우 단일 메트릭 시계열이 모니터링 됩니다.
-   *리소스*= ' myVM1 ' > 70% 인 CPU 백분율

![단일 시계열에 대 한 경고 규칙](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>여러 시계열에 대 한 경고 규칙
경고 규칙은 다음과 같은 기능 중 하나 이상을 사용 하는 경우 여러 시계열을 모니터링 합니다. 
-   여러 리소스
-   여러 조건 
-   여러 차원


## <a name="multiple-resources-multi-resource"></a>여러 리소스 (다중 리소스)

리소스가 동일한 형식이 고 동일한 Azure 지역에 있는 경우 단일 메트릭 경고 규칙은 여러 리소스를 모니터링할 수 있습니다. 이러한 종류의 규칙을 사용 하면 복잡성 및 유지 관리 해야 하는 경고 규칙의 총 수가 줄어듭니다. 

이러한 경고 규칙의 예는 다음과 같습니다.
-   대상 리소스: *myVM1, myVM2*
-   메트릭: *CPU 백분율*
-   연산자: *보다 큼*
-   임계값: *70*

이 경고 규칙의 경우 두 가지 메트릭 시계열이 개별적으로 모니터링 됩니다.
-   *리소스*= ' myVM1 ' > 70% 인 CPU 백분율
-   *리소스*= ' myVM2 ' > 70% 인 CPU 백분율

![다중 리소스 경고 규칙](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
다중 리소스 경고 규칙에서 조건은 각 리소스에 대해 **개별적** 으로 평가 됩니다. 각 리소스에 대 한 메트릭 시계열 속하는지를 각각에 대해 정확 하 게 평가 됩니다. 즉, 각 리소스에 대해 개별적으로 경고가 발생 합니다.

예를 들어 70% 이상의 CPU를 모니터링 하기 위해 위의 경고 규칙을 설정 했다고 가정 합니다. 평가 기간 (즉, 최근 5 분)
-   *MyVM1* 의 *CPU 백분율* 은 70% 보다 큽니다. 
-   *MyVM2* 의 *CPU 백분율* 은 50%입니다.

경고 규칙은 *myVM1*에 대해 트리거되고 *myVM2*은 트리거합니다. 이러한 트리거된 경고는 독립적입니다. 각 가상 컴퓨터의 개별 동작에 따라 서로 다른 시간에 확인할 수도 있습니다.

다중 리소스 경고 규칙 및이 기능에 대해 지원 되는 리소스 유형에 대 한 자세한 내용은 [Azure Monitor에서 메트릭 경고를 사용 하 여 규모에 맞게 모니터링](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)을 참조 하세요.

> [!NOTE] 
> 여러 리소스를 모니터링 하는 메트릭 경고 규칙에서는 단일 조건만 허용 됩니다.

## <a name="multiple-conditions-multi-condition"></a>여러 조건 (여러 조건)

단일 메트릭 경고 규칙은 경고 규칙에 따라 최대 5 개의 조건을 모니터링할 수도 있습니다. 

예를 들어:

- 대상 리소스: *myVM1*
- Condition1
  - 메트릭: *CPU 백분율*
  - 연산자: *보다 큼*
  - 임계값: *70*
- Condition2
  - 메트릭: *총 네트워크*
  - 연산자: *보다 큼*
  - 임계값: *20mb*

이 경고 규칙의 경우 두 가지 메트릭 시계열이 모니터링 됩니다.

- *리소스*= ' myVM1 ' > 70% 인 CPU 백분율
- *리소스*= ' myVM1 ' > 20Mb 인 총 네트워크

![다중 조건 경고 규칙](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
조건 사이에 ' AND ' 연산자가 사용 됩니다. 경고 규칙은 **모든** 조건이 충족 될 때 경고를 발생 시킵니다. 발생 한 경고는 조건 중 하나 이상이 더 이상 충족 되지 않으면 확인 됩니다. 

> [!NOTE]
> 여러 조건이 있는 경고 규칙에서 차원을 사용 하는 경우 제한 사항이 있습니다. 자세한 내용은 [여러 조건이 있는 메트릭 경고 규칙에서 차원을 사용할 때의 제한 사항](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions)을 참조 하세요.


## <a name="multiple-dimensions-multi-dimension"></a>여러 차원 (다중 차원)

단일 메트릭 경고 규칙은 메트릭의 여러 차원 값을 모니터링할 수도 있습니다. 메트릭의 차원은 메트릭 값을 설명 하기 위해 추가 데이터를 전달 하는 이름-값 쌍입니다. 예를 들어 저장소 계정의 **트랜잭션** 메트릭은 **api 이름**이라는 차원을 포함 하며 각 트랜잭션에 의해 호출 된 api의 이름 (예: Getblob, Deleteblob, putpage)을 설명 합니다. 차원 사용은 선택 사항 이지만, 메트릭을 필터링 하 고 모든 차원 값의 집계로 메트릭을 모니터링 하는 대신 특정 시계열만 모니터링 하도록 허용 합니다. 

예를 들어 트랜잭션 수가 모든 API 이름 (집계 된 데이터)에 걸쳐 있는 경우 경고를 발생 하도록 선택 하거나, 특정 API 이름에 대 한 트랜잭션 수가 높은 경우에만 경고로 나눌 수 있습니다. 

여러 차원을 모니터링 하는 경고 규칙의 예는 다음과 같습니다.

- 대상 리소스: *myStorage1*
- 메트릭: *트랜잭션*
- 차원
  * API 이름 = *Getblob, DeleteBlob, PutPage*
- 연산자: *보다 큼*
- 임계값: *70*

이 경고 규칙의 경우 세 가지 메트릭 시계열이 모니터링 되 고 있습니다.

- *리소스*= ' myStorage1 ' 및 *API 이름*= ' getblob ' > 70 인 트랜잭션
- *리소스*= ' myStorage1 ' 및 *API 이름*= ' deleteblob ' > 70 인 트랜잭션
- *리소스*= ' myStorage1 ' 및 *API 이름*= ' putpage ' 인 트랜잭션 > 70

![한 차원의 값을 포함 하는 다중 차원 경고 규칙](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

여러 차원 메트릭 경고 규칙은 메트릭의 여러 **차원에서 여러 차원 값을 모니터링할** 수도 있습니다. 이 경우 경고 규칙은 선택한 차원 값의 모든 차원 값 조합을 **개별적** 으로 모니터링 합니다.

이 경고 규칙 유형의 예는 다음과 같습니다.

- 대상 리소스: *myStorage1*
- 메트릭: *트랜잭션*
- 차원
  * API 이름 = *Getblob, DeleteBlob, PutPage*
  * 인증 = *SAS, AccountKey*
- 연산자: *보다 큼*
- 임계값: *70*

이 경고 규칙의 경우 6 개의 메트릭 시계열은 별도로 모니터링 됩니다.

- *리소스*= ' myStorage1 ' 및 *API 이름*= ' Getblob ' 및 *Authentication*= ' SAS ' > 70 인 트랜잭션
- *리소스*= ' myStorage1 ' 및 *API 이름*= ' Getblob ' 및 *Authentication*= ' AccountKey ' 인 트랜잭션 > 70
- *리소스*= ' myStorage1 ' 및 *API 이름*= ' Deleteblob ' 및 *Authentication*= ' SAS ' > 70 인 트랜잭션
- *리소스*= ' myStorage1 ' 및 *API 이름*= ' Deleteblob ' 및 *Authentication*= ' AccountKey ' 인 트랜잭션 > 70
- *리소스*= ' myStorage1 ' 및 *API 이름*= ' Putpage ' 및 *Authentication*= ' SAS ' > 70 인 트랜잭션
- *리소스*= ' myStorage1 ' 및 *API 이름*= ' Putpage ' 및 *Authentication*= ' AccountKey ' 인 트랜잭션 > 70

![여러 차원의 값을 포함 하는 다중 차원 경고 규칙](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>고급 다중 차원 기능

1.  **현재 및 이후 차원 모두 선택** – 미래 값을 포함 하 여 차원의 모든 가능한 값을 모니터링 하도록 선택할 수 있습니다. 이러한 경고 규칙은 차원 값이 추가 되거나 제거 될 때마다 경고 규칙을 수정 하지 않고도 차원의 모든 값을 모니터링 하도록 자동으로 크기가 조정 됩니다.
2.  차원 **제외** – 차원 값에 대해 ' ≠ ' (제외) 연산자를 선택 하는 것은 미래 값을 포함 하 여 해당 차원의 다른 모든 값을 선택 하는 것과 같습니다.
3.  **새 차원 및 사용자 지정 차원** – Azure Portal에 표시 되는 차원 값은 지난 3 일간 수집 된 메트릭 데이터를 기반으로 합니다. 찾고자 하는 차원 값을 아직 내보내지 않은 경우 사용자 지정 차원 값을 추가할 수 있습니다.

![고급 다중 차원 기능](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>메트릭 경고 가격

메트릭 경고 규칙의 가격은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에서 확인할 수 있습니다.

메트릭 경고 규칙을 만들 때 제공 된 가격 예측은 선택한 기능 및 모니터링 되는 시계열 수를 기준으로 합니다 .이는 규칙 구성 및 현재 메트릭 값에서 결정 됩니다. 그러나 월별 요금은 시계열의 실제 평가를 기반으로 하므로 특정 시계열에 평가할 데이터가 없거나 경고 규칙이 동적으로 확장 될 수 있는 기능을 사용 하는 경우 원래 예측과 차이가 있습니다.

예를 들어 경고 규칙은 다중 차원 기능을 활용 하 고 많은 수의 차원 값 조합을 선택 하 여 많은 시계열을 모니터링 하는 경우 높은 가격의 예측을 표시할 수 있습니다. 그러나 차원 값 조합에서 발생 하는 모든 시계열이 실제로 평가할 데이터가 없는 경우에는 해당 경고 규칙에 대 한 실제 요금이 낮아질 수 있습니다.

## <a name="next-steps"></a>다음 단계

메트릭 경고 및 [동적 임계값](alerts-dynamic-thresholds.md)을 사용 하 여 대규모로 모니터링 하는 방법에 대해 자세히 알아보세요.
