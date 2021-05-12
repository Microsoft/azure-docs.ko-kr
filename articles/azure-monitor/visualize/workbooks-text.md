---
title: Azure Monitor 통합 문서 텍스트 매개 변수
description: 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고를 간소화합니다. 통합 문서 텍스트 매개 변수에 대해 자세히 알아봅니다.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 22078f242ddeb882b39b85769537b9a282741250
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101725529"
---
# <a name="workbook-text-parameters"></a>통합 문서 텍스트 매개 변수

텍스트 상자 매개 변수는 통합 문서 사용자로부터 텍스트 입력을 수집하는 간단한 방법을 제공합니다. 드롭다운을 사용하여 입력을 수집하는 것이 실용적이지 않을 때 사용됩니다(예: 임의의 임계값 또는 일반 필터). 통합 문서를 사용하면 작성자가 쿼리에서 텍스트 상자의 기본값을 가져올 수 있습니다. 이를 통해 메트릭의 p95를 기반으로 기본 임계값을 설정하는 것과 같은 흥미로운 시나리오가 가능합니다.

텍스트 상자의 일반적인 용도는 다른 통합 문서 컨트롤에서 사용하는 내부 변수입니다. 기본값에 대한 쿼리를 활용하고 읽기 모드에서 입력 컨트롤을 보이지 않게 하여 이 작업을 완료합니다. 예를 들어 사용자가 아닌 수식에서 임계값을 가져온 다음 후속 쿼리에서 임계값을 사용하기를 원할 수 있습니다.

## <a name="creating-a-text-parameter"></a>텍스트 매개 변수 만들기
1. 편집 모드에서 빈 통합 문서를 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `SlowRequestThreshold`
    2. 매개 변수 유형: `Text`
    3. 필수: `checked`
    4. 쿼리에서 기본값 가져오기: `unchecked`
5. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.

    ![텍스트 매개 변수 생성을 보여 주는 이미지](./media/workbooks-text/text-create.png)

읽기 모드에서 통합 문서는 이와 같이 표시됩니다.

![읽기 모드에서 텍스트 매개 변수를 보여 주는 이미지](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>텍스트 매개 변수 참조
1. 파란색 `Add query` 링크를 선택하여 통합 문서에 쿼리 컨트롤을 추가하고 Application Insights 리소스를 선택합니다.
2. KQL 상자에 다음 코드 조각을 추가합니다.
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 쿼리 컨트롤과 결합된 값이 500인 텍스트 매개 변수를 사용하면 아래 쿼리를 효과적으로 실행할 수 있습니다.
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 쿼리를 실행하여 결과 확인

    ![KQL에서 참조되는 텍스트 매개 변수를 보여 주는 이미지](./media/workbooks-text/text-reference.png)

> [!NOTE]
> 위의 예제에서 `{SlowRequestThreshold}`(은)는 정수 값을 나타냅니다. `{ComputerName}`와(과) 같은 문자열을 쿼리하는 경우 따옴표 없이 입력을 허용하려면 매개 변수 필드에 따옴표 `"{ComputerName}"`(을)를 추가하도록 Kusto 쿼리를 수정해야 합니다.

## <a name="setting-default-values"></a>기본값 설정
1. 편집 모드에서 빈 통합 문서를 시작합니다.
2. 통합 문서 내의 링크에서 _매개 변수 추가_ 를 선택합니다.
3. 파란색 _매개 변수 추가_ 단추를 클릭합니다.
4. 새 매개 변수 창이 나타나면 다음과 같이 입력합니다.
    1. 매개 변수 이름: `SlowRequestThreshold`
    2. 매개 변수 유형: `Text`
    3. 필수: `checked`
    4. 쿼리에서 기본값 가져오기: `checked`
5. KQL 상자에 다음 코드 조각을 추가합니다.
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    이 쿼리는 앱의 모든 요청에 대해 텍스트 상자의 기본값을 95번째 백분위 수로 설정합니다.
6. 쿼리를 실행하여 결과 확인
7. 도구 모음에서 ‘저장’을 선택하여 매개 변수를 만듭니다.

    ![KQL의 기본값을 포함하는 텍스트 매개 변수를 보여 주는 이미지](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 이 예제에서는 Application Insights 데이터를 쿼리하지만 해당 접근 방식은 모든 로그 기반 데이터 원본(Log Analytics, Azure Resource Graph 등)에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 다양한 시각화 옵션을 갖춘 통합 문서에 대해 [자세히 알아봅니다](./workbooks-overview.md#visualizations).
* 통합 문서 리소스에 대한 액세스를 [제어](./workbooks-access-control.md)하고 공유합니다.