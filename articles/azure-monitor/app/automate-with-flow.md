---
title: Microsoft Flow를 사용하여 Azure Application Insights 프로세스 자동화
description: Microsoft Flow를 통해 Application Insights 커넥터를 사용하여 반복 가능한 프로세스를 신속하게 자동화하는 방법을 알아봅니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60903660"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow용 커넥터를 사용하여 Azure Application Insights 프로세스 자동화

서비스가 제대로 작동하는지 확인하기 위해 원격 분석 데이터에 대해 같은 쿼리를 반복적으로 실행하고 있나요? 추세와 비정상을 찾기 위해 이러한 쿼리를 자동화하고 여기에 관련된 고유한 워크플로를 빌드하기를 원하나요? Microsoft Flow 용 Azure Application Insights 커넥터를 사용 하는 적합 한 도구를 이러한 용도입니다.

이 통합 덕분에 이제 코드 한 줄 작성하지 않고도 수많은 프로세스를 자동화할 수 있습니다. Application Insights 작업을 사용하여 흐름을 만들고 나면 이 흐름에서 Application Insights Analytics 쿼리가 자동으로 실행됩니다. 

작업을 더 추가할 수도 있습니다. Microsoft Flow는 수백 개의 작업을 사용할 수 있게 해줍니다. 예를 들어 Microsoft Flow를 사용하여 자동으로 메일 알림을 보내거나 Azure DevOps에서 버그를 만들 수 있습니다. Microsoft Flow용 커넥터에 사용할 수 있는 많은 [템플릿](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) 중 하나를 사용할 수 있습니다. 이러한 템플릿을 사용하면 흐름 만들기 프로세스를 빠르게 진행할 수 있습니다. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Application Insights 흐름 만들기

이 자습서에서는 Analytics autocluster 알고리즘을 사용하여 웹 애플리케이션에 대한 데이터에서 특성을 그룹화하는 흐름을 만드는 방법을 알아봅니다. 흐름은 자동으로 메일을 통해 결과를 보내며, 이는 Microsoft Flow 및 Application Insights Analytics를 함께 사용하는 방법의 한 가지 예일 뿐입니다. 

### <a name="step-1-create-a-flow"></a>1단계: 흐름 만들기
1. [Microsoft Flow](https://flow.microsoft.com)에 로그인한 다음 **My Flows**(내 흐름)를 선택합니다.
2. 클릭 **새로 만들기** 한 다음 **빈 페이지에서 만들기**합니다.

    ![비어 있는 상태에서 새 흐름 만들기](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>2단계: 흐름에 대한 트리거 만들기
1. 빌드 기능에 탭을 선택 **일정**를 선택한 후 **일정-되풀이**합니다.

    ![선택 빌드 아래에서 예약](./media/automate-with-flow/2schedule.png)

1. 에 **간격** 상자에 입력 합니다 **1**, 및를 **빈도** 상자에서 **일**합니다.
2. 클릭 **새 단계**

    ![사용 빈도 및 간격을 입력 하 여 되풀이 일정 설정](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>3단계: Application Insights 작업 추가
1. **Azure Application Insights**를 검색합니다.
2. 클릭 **Azure Application Insights-분석 시각화 쿼리**합니다.
 
    ![작업을 선택 합니다. Azure Application Insights 시각화 분석 쿼리](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>4단계: Application Insights 리소스에 연결

이 단계를 완료하려면 리소스의 애플리케이션 ID 및 API 키가 필요합니다. 다음 다이어그램에 표시된 것처럼 Azure Portal에서 리소스의 응용 프로그램 ID 및 API 키를 검색할 수 있습니다.

![Azure Portal의 애플리케이션 ID](./media/automate-with-flow/5apiaccess.png)

![Azure portal에서 API 키](./media/automate-with-flow/6apikey.png)

- 애플리케이션 ID 및 API 키와 함께 연결의 이름을 제공합니다.

    ![Microsoft Flow 연결 창](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>5단계: Analytics 쿼리 및 차트 종류 지정
이 예제 쿼리에서는 어제 중에 실패한 요청을 선택하여 작업의 일부로 발생한 예외와 상관 관계를 지정합니다. Analytics에서는 operation_Id 식별자를 기반으로 하여 상관 관계를 지정합니다. 그런 다음 쿼리는 autocluster 알고리즘을 사용하여 결과를 세그먼트화합니다. 

사용자 고유의 쿼리를 만드는 경우 이러한 쿼리를 흐름에 추가하기 전에 먼저 Analytics에서 제대로 작동하는지 확인합니다.

- 다음 Analytics 쿼리를 추가 하 고 HTML 테이블 차트 종류를 선택 합니다. 선택한 **새 단계**합니다.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Analytics 쿼리 구성 창](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>6단계: 이메일을 보내도록 흐름 구성

1. **Office 365 Outlook**을 검색합니다.
2. **Office 365 Outlook - 이메일 보내기**를 클릭합니다.

    ![Office 365 Outlook 선택 창](./media/automate-with-flow/9outlookaction.png)

1. **메일 보내기** 창에서 다음을 수행합니다.

   a. 받는 사람의 이메일 주소를 입력합니다.

   b. 이메일의 제목을 입력합니다.

   다. **본문** 상자의 임의의 위치를 클릭한 다음, 오른쪽에서 열리는 동적 콘텐츠 메뉴에서 **본문**을 선택합니다.

   d. **고급 옵션 표시**를 클릭합니다.

    ![Office 365 Outlook 구성](./media/automate-with-flow/10sendemailbody.png)

1. 동적 콘텐츠 메뉴에서 다음을 수행합니다.

    a. **첨부 파일 이름**을 선택합니다.

    b. **첨부 파일 콘텐츠**를 선택합니다.
    
    다. **HTML임** 상자에서 **예**를 선택합니다.

    ![Office 365 메일 구성 창](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>7단계: 흐름 저장 및 테스트
- 에 **흐름 이름** 상자에서 흐름 이름을 추가 하 고 클릭 **저장**합니다.

    ![흐름 이름 지정 및 저장](./media/automate-with-flow/12nameflow.png)

트리거가 이 작업을 실행하도록 기다리거나 [요청 시 트리거를 실행](https://flow.microsoft.com/blog/run-now-and-six-more-services/)하여 흐름을 즉시 실행할 수 있습니다.

흐름이 실행되면 메일 목록에 지정한 받는 사람이 다음과 같은 메일 메시지를 받습니다.

![샘플 메일](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>다음 단계

- [Analytics 쿼리](../../azure-monitor/log-query/get-started-queries.md) 만들기에 대해 자세히 알아봅니다.
- [Microsoft Flow](https://ms.flow.microsoft.com)에 대해 자세히 알아봅니다.



<!--Link references-->





