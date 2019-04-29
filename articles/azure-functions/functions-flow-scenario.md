---
title: Microsoft Flow에서 Azure 함수 호출 | Microsoft Docs
description: 사용자 지정 커넥터를 만든 후 해당 커넥터를 사용하여 함수를 호출합니다.
services: functions
keywords: 클라우드 앱, 클라우드 서비스, Microsoft Flow, 비즈니스 프로세스, 비즈니스 애플리케이션
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 31e18285bf6211e73d994e037a91adc396972715
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106973"
---
# <a name="call-a-function-from-microsoft-flow"></a>Microsoft Flow에서 함수 호출

[Microsoft Flow](https://flow.microsoft.com/)는 자주 사용하는 앱과 서비스 간의 워크플로 및 비즈니스 프로세스를 간단하게 자동화할 수 있습니다. 전문 개발자는 Azure Functions를 사용하여 흐름 빌더에게 기술적 세부 사항은 숨기면서 Microsoft Flow의 기능을 확장할 수 있습니다.

이 토픽의 흐름은 풍차에 대한 유지 관리 시나리오를 토대로 빌드합니다. 이 항목에서는 [함수에 대한 OpenAPI 정의 만들기](functions-openapi-definition.md)에 정의된 함수를 호출하는 방법을 보여 줍니다. 이 함수는 풍차에 대한 응급 복구가 비용 효율적인지 여부를 확인합니다. 비용 효율적이면 흐름에서 복구를 권장하는 전자 메일을 보냅니다.

PowerApps에서 동일한 함수를 호출하는 방법에 대한 내용은 [PowerApps에서 함수 호출](functions-powerapps-scenario.md)을 참조하세요.

이 항목에서는 다음 내용을 배웁니다.

> [!div class="checklist"]
> * SharePoint에서 목록을 만듭니다.
> * API 정의를 내보냅니다.
> * API에 연결을 추가합니다.
> * 복구가 비용 효율적인 경우 전자 메일을 보내는 흐름을 만듭니다.
> * 흐름을 실행합니다.

## <a name="prerequisites"></a>필수 조건

+ Azure 계정과 동일한 로그인 자격 증명을 사용하는 활성 [Microsoft Flow 계정](https://flow.microsoft.com/documentation/sign-up-sign-in/). 
+ 이 흐름의 데이터 원본으로 사용하는 SharePoint. 아직 SharePoint가 없는 경우 [Office 365 평가판](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1)에 등록합니다.
+ [함수에 대한 OpenAPI 정의 만들기](functions-openapi-definition.md) 자습서를 완료합니다.

## <a name="create-a-sharepoint-list"></a>SharePoint 목록 만들기
흐름의 데이터 원본으로 사용할 목록 만들기부터 시작합니다. 목록에는 다음과 같은 열이 있습니다.

| 목록 열     | 데이터 형식           | 메모                                    |
|-----------------|---------------------|------------------------------------------|
| **제목**           | 한 줄의 텍스트 | 터빈의 이름                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Number              | 터빈의 출력, KwH 단위            |
| **ServiceRequired** | 예/아니요              |                                          |
| **EstimatedEffort** | Number              | 예상 수리 시간, 시간 단위 |

1. SharePoint 사이트에서 **새로 만들기**, **목록**을 차례로 클릭하거나 탭합니다.

    ![새 SharePoint 목록 만들기](./media/functions-flow-scenario/new-list.png)

2. 이름 `Turbines`을 입력한 다음 **만들기**를 클릭 또는 탭합니다.

    ![새 목록의 이름 지정](./media/functions-flow-scenario/create-list.png)

    **터빈** 목록이 생성되고, 기본 **제목** 필드가 포함됩니다.

    ![터빈 목록](./media/functions-flow-scenario/initial-list.png)

3. ![새 항목 아이콘](./media/functions-flow-scenario/icon-new.png), **날짜**를 차례대로 클릭 또는 탭합니다.

    ![한 줄의 텍스트 필드 추가](./media/functions-flow-scenario/add-column.png)

4. 이름 `LastServiceDate`를 입력한 다음 **만들기**를 클릭 또는 탭합니다.

    ![LastServiceDate 열 만들기](./media/functions-flow-scenario/date-column.png)

5. 목록의 나머지 세 열에 대해 마지막 두 단계를 반복합니다.

    1. **번호** > "MaxOutput"

    2. **예/아니요** > "ServiceRequired"

    3. **변호** > "EstimatedEffort"

다 됐습니다. 다음 이미지처럼 빈 목록이 있을 것입니다. 흐름을 만든 후 목록에 데이터를 추가합니다.

![빈 목록](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>API에 연결 추가
사용자 지정 API(사용자 지정 커넥터라고도 함)는 Microsoft Flow에서 사용할 수 있지만 흐름에서 사용하려면 API에 연결해야 합니다.

1. [flow.microsoft.com](https://flow.microsoft.com)에서 기어 아이콘(오른쪽 위)을 클릭한 다음 **연결**을 클릭합니다.

    ![흐름 연결](media/functions-flow-scenario/flow-connections.png)

1. **연결 만들기**를 클릭하고 아래로 스크롤하여 **Turbine Repair** 커넥터로 이동한 후 클릭합니다.

    ![연결 만들기](media/functions-flow-scenario/create-connection.png)

1. API 키를 입력하고 **연결 만들기**를 클릭합니다.

    ![API 키를 입력하고 만들기](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> 다른 사용자와 흐름을 공유하는 경우 흐름에서 작업하거나 흐름을 사용하는 각 사용자도 API에 연결하기 위해 API 키를 입력해야 합니다. 이 동작은 나중에 변경될 수 있으며 변경도면 이 항목도 업데이트될 것입니다.


## <a name="create-a-flow"></a>흐름 만들기

이제 사용자 지정 커넥터 및 앞에서 만든 SharePoint 목록을 사용하는 흐름을 만들 준비가 완료되었습니다.

### <a name="add-a-trigger-and-specify-a-condition"></a>트리거를 추가하고 조건 지정

먼저 빈 페이지에서(템플릿 없이) 흐름을 만들고, SharePoint 목록에 항목이 생성될 때 실행되는 *트리거*를 추가합니다. 그런 후 그 다음에 발생할 일을 결정하는 *조건*을 추가합니다.

1. [flow.microsoft.com](https://flow.microsoft.com)에서 **내 흐름**을 클릭한 다음 **빈 페이지에서 만들기**를 클릭합니다.

    ![빈 페이지에서 만들기](media/functions-flow-scenario/create-from-blank.png)

2. SharePoint 트리거 **항목이 만들어진 경우**를 클릭합니다.

    ![트리거 선택](media/functions-flow-scenario/choose-trigger.png)

    아직 SharePoint에 로그인하지 않은 경우 로그인하라는 메시지가 표시됩니다.

3. **사이트 주소**에는 SharePoint 사이트 이름을 입력하고, **목록 이름**에는 터빈 데이터가 포함된 목록을 입력합니다.

    ![트리거 선택](media/functions-flow-scenario/site-list.png)

4. **새 단계**를 클릭한 다음 **조건 추가**를 클릭합니다.

    ![조건 추가](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow 흐름에 두 개의 분기를 추가합니다. **그렇다면** 하 고 **없으면**합니다. 일치시킬 조건을 정의한 후 분기 하나 또는 둘 모두에 단계를 추가합니다.

    ![조건 분기](media/functions-flow-scenario/condition-branches.png)

5. **조건** 카드에서 첫 번째 상자를 클릭한 다음 **동적 콘텐츠** 대화 상자에서 **ServiceRequired**를 선택합니다.

    ![조건에 대한 필드 선택](media/functions-flow-scenario/condition1-field.png)

6. 조건에 대한 `True` 값을 입력합니다.

    ![조건에 대해 true 입력](media/functions-flow-scenario/condition1-yes.png)

    이 값은 SharePoint 목록에 `Yes` 또는 `No`로 표시되지만 *부울* `True` 또는 `False`로 저장됩니다. 

7. 페이지 맨 위에서 **흐름 만들기**를 클릭합니다. **흐름 업데이트**를 주기적으로 클릭합니다.

목록에 생성된 항목에 대해 흐름에서는 **ServiceRequired** 필드가 `Yes`로 설정되었는지 확인한 후 **그렇다면** 분기 또는 **그렇지 않다면** 분기로 적절하게 이동합니다. 시간을 절약하기 위해 이 토픽에서는 **그렇다면** 분기에 대한 작업만 지정합니다.

### <a name="add-the-custom-connector"></a>사용자 지정 커넥터 추가

이제 Azure에서 함수를 호출하는 사용자 지정 커넥터를 추가합니다. 표준 커넥터와 마찬가지로 흐름에 사용자 지정 커넥터를 추가합니다. 

1. **그렇다면** 분기에서 **작업 추가**를 클릭합니다.

    ![작업 추가](media/functions-flow-scenario/condition1-yes-add-action.png)

2. **작업 선택** 대화 상자에서 `Turbine Repair`를 검색한 후 **터빈 수리 - 비용 계산** 작업을 선택합니다.

    ![작업 선택](media/functions-flow-scenario/choose-turbine-repair.png)

    다음 이미지는 흐름에 추가되는 카드를 보여줍니다. 필드와 설명은 커넥터에 대한 OpenAPI 정의에서 옵니다.

    ![비용 기본값 계산](media/functions-flow-scenario/calculates-costs-default.png)

3. **비용 계산** 카드에서 **동적 콘텐츠** 대화 상자를 사용하여 함수의 입력을 선택합니다. OpenAPI 정의가 **시간** 및 **용량**을 숫자로 지정하기 때문에 Microsoft Flow는 숫자 필드를 표시하지만 날짜 필드를 표시하지 않습니다.

    **시간**에서 **EstimatedEffort**를 선택하고, **용량**에서 **MaxOutput**을 선택합니다.

    ![작업 선택](media/functions-flow-scenario/calculates-costs-fields.png)

     이제 함수의 출력에 따라 또 다른 조건을 추가합니다.

4. **그렇다면** 분기의 맨 아래에서 **추가**를 클릭한 다음 **조건 추가**를 클릭합니다.

    ![조건 추가](media/functions-flow-scenario/condition2-add.png)

5. **조건 2** 카드에서 첫 번째 상자를 클릭한 다음 **동적 콘텐츠** 대화 상자에서 **메시지**를 선택합니다.

    ![조건에 대한 필드 선택](media/functions-flow-scenario/condition2-field.png)

6. `Yes` 값을 입력합니다. 흐름은 함수에서 반환한 메시지가 예(수리함) 또는 아니요(수리 안 함) 중 어느 것인지에 따라 그 다음 **그렇다면** 분기 또는 **그렇지 않다면** 분기로 이동합니다. 

    ![조건에 대해 예 입력](media/functions-flow-scenario/condition2-yes.png)

이제 흐름이 다음 이미지처럼 표시됩니다.

![조건에 대해 예 입력](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>함수 결과에 따라 전자 메일 보내기

흐름의 현 시점에서, 함수는 **메시지** 값 `Yes` 또는 `No` 그리고 비용 및 잠재적 수익에 대한 기타 정보를 반환했습니다. 두 번째 조건의 **그렇다면** 분기에서는 전자 메일을 보내겠지만 SharePoint 목록에 다시 쓰기, [승인 프로세스](https://flow.microsoft.com/documentation/modern-approvals/) 시작 등의 작업을 개수 제한 없이 수행할 수 있습니다.

1. 두 번째 조건의 **그렇다면** 분기에서 **작업 추가**를 클릭합니다.

    ![작업 추가](media/functions-flow-scenario/condition2-yes-add-action.png)

2. **작업 선택** 대화 상자에서 `email`을 검색한 후 사용하는 전자 메일 시스템에 따라(이 예에서는 Outlook) 전자 메일 보내기 작업을 선택합니다.

    ![Outlook 전자 메일 보내기](media/functions-flow-scenario/outlook-send-email.png)

3. **전자 메일 보내기** 카드에서 전자 메일을 작성합니다. **받는 사람** 필드에 조직 내에서 유효한 이름을 입력합니다. 아래 이미지에서 다른 필드는 **동적 콘텐츠** 대화 상자의 텍스트와 토큰 조합인 것을 볼 수 있습니다. 

    ![전자 메일 필드](media/functions-flow-scenario/email-fields.png)

    **제목** 토큰은 SharePoint 목록에서 오고, **CostToFix** 및 **RevenueOpportunity**는 함수를 통해 반환됩니다.

    완료된 흐름은 다음 이미지처럼 표시됩니다(공간을 절약하기 위해 첫 번째 **그렇지 않다면** 분기를 생략함).

    ![완전한 흐름](media/functions-flow-scenario/complete-flow.png)

4. 페이지 맨 위에서 **흐름 업데이트**를 클릭한 다음 **완료**를 클릭합니다.

## <a name="run-the-flow"></a>흐름 실행

흐름이 완료되었으니, SharePoint 목록에 행을 추가하고 흐름이 어떻게 응답하는지 살펴봅니다.

1. SharePoint 목록으로 돌아가서 **빠른 편집**을 클릭합니다.

    ![빠른 편집](media/functions-flow-scenario/quick-edit.png)

2. 편집 그리드에 다음 값을 입력합니다.

    | 목록 열     | 값           |
    |-----------------|---------------------|
    | **제목**           | Turbine 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | 예 |
    | **EstimatedEffort** | 10 |

3. **Done**을 클릭합니다.

    ![빠른 편집 완료](media/functions-flow-scenario/quick-edit-done.png)

    항목을 추가하면 흐름을 트리거되며, 다음으로 살펴볼 주제입니다.

4. [flow.microsoft.com](https://flow.microsoft.com)에서 **내 흐름**을 클릭한 다음 앞에서 만든 흐름을 클릭합니다.

    ![내 흐름](media/functions-flow-scenario/my-flows.png)

5. **실행 기록** 아래에서 흐름 실행을 클릭합니다.

    ![실행 기록](media/functions-flow-scenario/run-history.png)

    실행이 성공하면 다음 페이지에서 흐름 작업을 검토할 수 있습니다. 어떤 이유로 실행이 실패하면 다음 페이지에 문제 해결 정보가 제공됩니다.

6. 카드를 확장하면 흐름 중 발생한 일을 볼 수 있습니다. 예를 들어 **비용 계산** 카드를 확장하면 함수의 입력 및 출력을 볼 수 있습니다. 

    ![비용 입력 및 출력 계산](media/functions-flow-scenario/calculates-costs-outputs.png)

7. **전자 메일 보내기** 카드의 **받는 사람** 필드에 지정한 사람의 전자 메일 계정을 확인합니다. 흐름에서 보낸 전자 메일은 다음 이미지처럼 표시됩니다.

    ![흐름 전자 메일](media/functions-flow-scenario/flow-email.png)

    토큰이 어떤 방식으로 SharePoint 목록 및 함수의 올바른 값으로 대체되는지 살펴볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 항목에서는 다음 내용을 배웁니다.

> [!div class="checklist"]
> * SharePoint에서 목록을 만듭니다.
> * API 정의를 내보냅니다.
> * API에 연결을 추가합니다.
> * 복구가 비용 효율적인 경우 전자 메일을 보내는 흐름을 만듭니다.
> * 흐름을 실행합니다.

Microsoft Flow에 대한 자세한 내용은 [Microsoft Flow 시작](https://flow.microsoft.com/documentation/getting-started/)을 참조하세요.

Azure Functions를 사용하는 다른 흥미로운 시나리오에 대해 알아보려면 [PowerApps에서 함수 호출](functions-powerapps-scenario.md) 및 [Azure Logic Apps와 통합하는 함수 만들기](functions-twitter-email.md)를 참조하세요.
