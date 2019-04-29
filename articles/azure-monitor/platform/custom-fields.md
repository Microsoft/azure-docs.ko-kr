---
title: Azure Monitor에서 사용자 지정 필드 | Microsoft Docs
description: Azure Monitor의 사용자 정의 필드 기능을 사용 하면 Log Analytics 작업 영역에서 수집된 된 레코드의 속성을 추가 하는 레코드에서 고유한 검색 가능 필드를 만들 수 있습니다.  이 문서는 사용자 지정 필드를 만드는 프로세스를 설명하고 샘플 이벤트에 대한 자세한 연습을 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: bwren
ms.openlocfilehash: 974a3391c592a1caf7bdcc6d9e01032f0c73aaa6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461944"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 작업 영역에서 사용자 지정 필드 만들기

> [!NOTE]
> 이 문서에서는 수집 되는 Log Analytics 작업 영역에서 텍스트 데이터를 구문 분석 하는 방법을 설명 합니다. 에 설명 된 대로 수집 된 후에 쿼리에서 텍스트 데이터를 구문 분석 이점이 [Azure Monitor에서 텍스트 데이터를 구문 분석](../log-query/parse-text.md)합니다.

합니다 **사용자 지정 필드** Azure Monitor의 기능을 사용 하면 자체 검색 가능한 필드를 추가 하 여 Log Analytics 작업 영역에서 기존 레코드를 확장할 수 있습니다.  사용자 지정 필드는 동일한 레코드의 다른 속성에서 추출한 데이터로 자동으로 채워집니다.

![개요](media/custom-fields/overview.png)

예를 들어, 아래 샘플 레코드에는 이벤트 설명에 파묻혀 있는 유용한 데이터가 있습니다. 이 데이터를 별도 속성을 추출 정렬 및 필터링 같은 작업에 사용할 수 있도록 합니다.

![샘플 추출](media/custom-fields/sample-extract.png)

> [!NOTE]
> 미리 보기에서는 작업 영역 내 사용자 지정 필드가 100개로 제한됩니다.  이러한 제한은 이 기능이 일반 공급이 될 때 확장됩니다.

## <a name="creating-a-custom-field"></a>사용자 지정 필드 만들기
사용자 지정 필드를 만드는 경우, Log Analytics는 값을 채우는 데 사용할 데이터를 이해해야 합니다.  Microsoft Research의 FlashExtract라는 기술을 사용하여 이러한 데이터를 신속하게 식별합니다.  Azure Monitor는 명시적 지침을 제공 하도록 요구를 하지 않고 사용자가 제공한 예제에서 추출 하려는 데이터에 대 한 알아냅니다.

다음 섹션은 사용자 지정 필드를 만드는 절차를 제공합니다.  이 문서의 맨 아래에 샘플 추출 연습이 있습니다.

> [!NOTE]
> 사용자 지정 필드는 사용자 지정 필드가 생성 된 후 수집 된 레코드에만 나타납니다 지정된 된 조건과 일치 하는 레코드가 Log Analytics 작업 영역에 추가 될 때 채워집니다.  사용자 지정 필드는 생성 당시 데이터 저장소에 이미 있었던 레코드에는 추가되지 않습니다.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>1단계 – 사용자 지정 필드를 갖게 될 레코드를 식별합니다.
첫 번째 단계는 사용자 지정 필드를 갖게 될 레코드를 식별하는 것입니다.  시작을 [표준 로그 쿼리](../log-query/log-query-overview.md) 한 다음 Azure Monitor에서 알아 모델로 작동 하는 레코드를 선택 합니다.  사용자 지정 필드에 데이터를 추출할 것이라고 지정하면, **Field Extraction Wizard** (필드 추출 마법사)가 열리고 여기서 조건의 유효성을 검사하고 구체화합니다.

1. 로 이동 **로그** 사용 하는 [레코드를 검색 하는 쿼리](../log-query/log-query-overview.md) 사용자 지정 필드가 있는 합니다.
2. Log Analytics가 사용자 지정 필드를 채울 데이터 추출을 위한 모델 역할을 하기 위해서 사용할 레코드를 선택합니다.  사용자는 이 레코드로부터 추출할 데이터를 식별하고, Log Analytics는 이 정보를 사용하여 유사한 모든 레코드의 사용자 지정 필드를 채울 논리를 결정합니다.
3. 레코드 속성을 확장 하 고, 왼쪽 레코드의 최상위 속성의 줄임표를 클릭 하 고, 선택 **에서 필드 추출**합니다.
4. 합니다 **필드 추출 마법사** 가 열리고 선택한 레코드에 표시 됩니다는 **기본 예제** 열입니다.  사용자 지정 필드가, 선택한 속성에 동일한 값을 포함하는 레코드에 대해 정의됩니다.  
5. 선택 내용이 정확히 원하는 내용이 아니면, 추가적인 필드를 선택하여 조건을 좁혀 나갑니다.  조건에 대한 필드 값을 변경하기 위해서, 취소하고 원하는 조건에 맞는 다른 레코드를 선택해야 합니다.

### <a name="step-2---perform-initial-extract"></a>2단계 - 초기 추출을 수행합니다.
사용자 지정 필드를 갖게 될 레코드를 식별하고 나면, 추출할 데이터를 식별합니다.  Log Analytics는 이 정보를 사용하여 비슷한 레코드에서 유사한 패턴을 식별합니다.  이 단계가 지나면 결과의 유효성을 검사하고 분석에 사용할 Log Analytics에 대한 자세한 정보를 제공할 수 있게 됩니다.

1. 사용자 지정 필드를 채울 샘플 레코드에서 텍스트를 강조 표시합니다.  그런 다음 나타납니다 필드 이름과 데이터 형식을 제공 하 고 초기 추출을 수행할 대화 상자를 사용 하 여 합니다.  **\_CF** 문자가 자동으로 추가됩니다.
2. **추출** 을 클릭하여 수집된 레코드에 대한 분석을 수행합니다.  
3. **요약** 및 **검색 결과** 섹션에 정확성을 검사할 수 있도록 추출 결과가 표시됩니다.  **요약** 에 식별된 각각의 데이터 값에 대한 개수 및 레코드를 식별하는 데 사용된 조건이 표시됩니다.  **검색 결과** 에 조건에 맞는 레코드의 자세한 목록이 제공됩니다.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>3단계 – 사용자 지정 필드 만들기 및 추출의 정확성을 검사합니다.
초기 추출을 수행하면, Log Analytics에 이미 수집된 데이터에 기반한 결과가 표시됩니다.  결과가 정확해 보이면 더 이상 작업을 수행하지 않고 사용자 지정 필드를 만들 수 있습니다.  그렇지 않으면, Log Analytics의 논리를 개선할 수 있도록 결과를 구체화합니다.

1. 초기 추출에 정확하지 않은 값이 있으면, 정확하지 않은 레코드 옆의 **편집** 아이콘을 클릭하고 선택을 수정하기 위해서 **Modify this highlight**(이 강조 표시 수정)를 선택합니다.
2. 해당 항목이 **Main Example**(기본 예제) 아래 **Additional examples**(추가 예제) 섹션으로 복사됩니다.  여기에서 Log Analytics가 만들어야 하는 섹션을 이해할 수 있도록 강조 표시를 조정합니다.
3. **추출** 을 클릭하여 새로운 정보를 모든 기존 레코드를 평가하는 데 사용합니다.  결과는 새로운 인텔리전스를 기반으로 사용자가 수정한 레코드가 아닌 다른 레코드에 대해 수정될 수 있습니다.
4. 추출 내 모든 레코드가 새 사용자 지정 필드를 채울 데이터를 제대로 식별할 때까지 수정을 계속 추가합니다.
5. 결과에 만족하면 **Save Extract** (추출 저장)을 클릭합니다.  사용자 지정 필드가 정의되었지만 아직 레코드에는 추가되지 않았습니다.
6. 지정된 수집 조건에 일치하는 새 레코드를 기다린 다음 로그 검색을 다시 실행합니다. 새 레코드에 사용자 지정 필드가 생깁니다.
7. 사용자 지정 필드를 다른 레코드 속성처럼 사용합니다.  이것을 사용하여 데이터를 집계하고 그룹화하며 새로운 통찰을 생성하는 데도 사용할 수 있습니다.

## <a name="viewing-custom-fields"></a>사용자 지정 필드 보기
Azure Portal에서 Log Analytics 작업 영역의 **고급 설정** 메뉴에서 관리 그룹의 모든 사용자 지정 필드 목록을 볼 수 있습니다.  **데이터**를 선택한 다음 **사용자 지정 필드**를 선택하여 작업 영역 내 모든 사용자 지정 필드의 목록을 표시합니다.  

![사용자 지정 필드](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>사용자 지정 필드 제거
사용자 지정 필드를 제거하는 방법은 두 가지입니다.  첫 번째는 위의 설명대로 전체 목록을 볼 때 각 필드의 **제거** 옵션입니다.  다른 방법은 레코드를 검색하고 필드 왼쪽의 단추를 클릭하는 것입니다.  메뉴에 사용자 지정 필드를 제거하는 옵션이 표시됩니다.

## <a name="sample-walkthrough"></a>샘플 연습
다음 섹션은 사용자 지정 필드를 만드는 전체 예제를 안내합니다.  이 예제는 서비스 변경 상태를 나타내는 Windows 이벤트의 서비스 이름을 추출합니다.  Windows 컴퓨터에서 시스템 시작 중 서비스 제어 관리자에 의해 생성 된 이벤트에 따라 달라 집니다.  이 예제를 계속하려면, [시스템 로그에 대한 정보 이벤트를 수집](data-sources-windows-events.md)해야 합니다.

서비스 시작 또는 중지를 나타내는 이벤트인, 이벤트 ID가 7036인 서비스 제어 관리자의 모든 이벤트를 반환하기 위해서 다음 쿼리를 입력합니다.

![쿼리](media/custom-fields/query.png)

그런 다음 선택 하 고 이벤트 ID 7036이 있는 모든 레코드를 확장 합니다.

![소스 레코드](media/custom-fields/source-record.png)

Top 속성 옆의 줄임표를 클릭 하 여 사용자 지정 필드를 정의 합니다.

![추출 필드](media/custom-fields/extract-fields.png)

**Field Extraction Wizard**(필드 추출 마법사)가 열리고 **EventLog** 및 **EventID** 필드가 **Main Example**(기본 예제) 열에서 선택됩니다.  이것은 사용자 지정 필드가 이벤트 ID가 7036인 시스템 로그의 이벤트에 대해 정의된다는 것을 나타냅니다.  이것으로 충분하므로 다른 필드를 선택할 필요가 없습니다.

![Main Example](media/custom-fields/main-example.png)

**RenderedDescription** 속성에서 서비스의 이름을 강조 표시하고 **Service**를 사용하여 서비스 이름을 식별합니다.  사용자 지정 필드가 **Service_CF**라고 지정됩니다. 필드 형식이 경우 이므로 문자열을 변경 하지 않고 그대로 사용할 수 있습니다.

![필드 제목](media/custom-fields/field-title.png)

일부 레코드에 대해서는 서비스 이름이 적절하게 식별되었지만 나머지에 대해서는 그렇지 못합니다.   **검색 결과**에 **WMI Performance Adapter**의 이름 일부가 선택되지 않은 것이 표시됩니다.  합니다 **요약** 식별 하는 하나의 레코드를 보여 줍니다 **모듈 설치 관리자** 대신 **Windows 모듈 설치 관리자**합니다.  

![검색 결과](media/custom-fields/search-results-01.png)

**WMI Performance Adapter** 레코드부터 시작합니다.  편집 아이콘을 클릭한 다음 **Modify this highlight**(이 강조 표시 수정)를 클릭합니다.  

![강조 표시 수정](media/custom-fields/modify-highlight.png)

**WMI** 라는 단어를 포함하도록 강조 표시를 키운 다음 추출을 다시 실행합니다.  

![추가 예제](media/custom-fields/additional-example-01.png)

**WMI Performance Adapter**에 대한 항목이 수정되었고, Log Analytics 역시 해당 정보를 사용하여 **Windows모듈 설치 관리자**에 대한 레코드를 수정한 것을 볼 수 있습니다.

![검색 결과](media/custom-fields/search-results-02.png)

이제 확인 하는 쿼리를 실행할 수 있습니다 **Service_CF** 생성 되지만 모든 레코드에 아직 추가 되지 않습니다. 이것은 수집할 새 레코드에 대 한 대기 해야 하므로 사용자 지정 필드를 기존 레코드에 대해 작동 하지 않습니다.

![초기 개수](media/custom-fields/initial-count.png)

새 이벤트가 수집될 만큼 시간이 지나면, **Service_CF** 필드가 조건에 일치하는 레코드에 추가되는 것을 볼 수 있습니다.

![최종 결과](media/custom-fields/final-results.png)

이제 사용자 지정 필드를 다른 레코드 속성처럼 사용할 수 있습니다.  이것을 설명하기 위해, 가장 활동적인 서비스가 무엇인지를 검사하는 새로운 **Service_CF** 필드로 그룹화하는 쿼리를 만듭니다.

![쿼리로 그룹화](media/custom-fields/query-group.png)

## <a name="next-steps"></a>다음 단계
* 에 대 한 자세한 [쿼리를 로깅](../log-query/log-query-overview.md) 조건에 대 한 사용자 지정 필드를 사용 하 여 쿼리를 작성 합니다.
* 사용자 지정 필드를 사용하여 구문 분석하는 [사용자 지정 로그 파일](data-sources-custom-logs.md)을 모니터링합니다.

