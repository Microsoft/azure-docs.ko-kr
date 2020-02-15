---
title: Microsoft Flow를 사용하여 Azure Monitor 로그 프로세스 자동화
description: Microsoft Flow를 통해 Azure Log Analytics 커넥터를 사용하여 반복 가능한 프로세스를 신속하게 자동화하는 방법을 알아봅니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: a6097d38d3335be356ca75f5a9d0eadeed414b03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166945"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Microsoft Flow용 커넥터를 사용하여 Azure Monitor 로그 프로세스 자동화
[Microsoft Flow](https://ms.flow.microsoft.com)를 사용하면 다양한 서비스에 수백 가지 작업을 통해 자동화된 워크플로를 만들 수 있습니다. 한 작업의 출력을 서로 다른 서비스 간의 통합을 만들 수 있는 다른 작업에 대한 입력으로 사용할 수 있습니다.  Microsoft Flow에 대한 Azure Log Analytics 커넥터를 사용하면 Azure Monitor에서 Log Analytics 작업 영역에서 로그 쿼리로 검색한 데이터를 포함하는 워크플로를 빌드할 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

예를 들어 Microsoft Flow를 사용 하 여 Office 365의 전자 메일 알림에서 Azure Monitor 로그 데이터를 사용 하거나, Azure DevOps에서 버그를 만들거나, 여유 시간 메시지를 게시할 수 있습니다.  메일이나 트윗이 수신될 때와 같이 연결된 서비스의 일부 작업 또는 단순 일정에서 워크플로를 트리거할 수 있습니다.  

이 문서의 자습서에서는 Microsoft Flow의 Log Analytics 커넥터 사용 방법에 관한 한 가지 예제를 통해 Azure Monitor 로그 쿼리의 결과를 이메일로 자동으로 보내는 흐름을 만드는 방법을 보여 줍니다. 


## <a name="step-1-create-a-flow"></a>1단계: 흐름 만들기
1. [Microsoft Flow](https://flow.microsoft.com)에 로그인하고 **내 흐름**을 선택합니다.
2. **+빈 페이지에서 만들기**를 클릭합니다.

## <a name="step-2-create-a-trigger-for-your-flow"></a>2단계: 흐름에 대한 트리거 만들기
1. **다양한 커넥터 및 트리거 검색**을 클릭합니다.
2. 검색 상자에 **일정**을 입력합니다.
3. **일정**을 선택한 다음, **일정 - 되풀이**를 선택합니다.
4. **빈도** 상자에서 **일**을 선택하고 **간격** 상자에 **1**을 입력합니다.<br><br>![Microsoft Flow 트리거 대화 상자](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>3단계: Log Analytics 작업 추가
1. **+새 단계**를 클릭한 다음 **작업 추가**를 클릭합니다.
2. **Log Analytics**를 검색합니다.
3. **Azure Log Analytics - 쿼리 실행 및 결과 시각화**를 클릭합니다.<br><br>![Log Analytics 쿼리 실행 창](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>4단계: Log Analytics 작업 구성

1. 구독 ID, 리소스 그룹 및 작업 영역 이름을 포함한 작업 영역에 대한 세부 정보를 지정합니다.
2. 다음 로그 쿼리를 **쿼리** 창에 추가합니다.  이는 예제 쿼리일 뿐, 데이터를 반환하는 다른 항목으로 바꿀 수 있습니다.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. **차트 형식**에 **HTML 테이블**을 선택합니다.<br><br>![Log Analytics 작업](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>5단계: 메일을 보내도록 흐름 구성

1. **새 단계**를 클릭한 다음 **+작업 추가**를 클릭합니다.
2. **Office 365 Outlook**을 검색합니다.
3. **Office 365 Outlook - 이메일 보내기**를 클릭합니다.<br><br>![Office 365 Outlook 선택 창](media/flow-tutorial/flow04.png)

4. **받는 사람** 창에서 수신자의 이메일 주소 및 **제목**에서 전자 메일에 대한 제목을 지정합니다.
5. **본문** 상자에서 아무 곳이나 클릭합니다.  이전 작업의 값이 포함된 **동적 콘텐츠** 창이 열립니다.  
6. **본문**을 선택합니다.  Log Analytics 작업에 대한 쿼리의 결과입니다.
6. **고급 옵션 표시**를 클릭합니다.
7. **HTML임** 상자에서 **예**를 선택합니다.<br><br>![Office 365 전자 메일 구성 창](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>6단계: 흐름 저장 및 테스트
1. **흐름 이름** 상자에 흐름 이름을 추가하고 **흐름 만들기**를 클릭합니다.<br><br>![흐름 저장](media/flow-tutorial/flow06.png)
2. 이제 흐름이 만들어졌으며, 사용자가 지정한 일정인 날짜에 실행됩니다. 
3. 즉시 흐름을 테스트하려면 **지금 실행**, **흐름 실행**을 차례로 클릭합니다.<br><br>![흐름 실행](media/flow-tutorial/flow07.png)
3. 흐름이 완료되면 사용자가 지정한 수신자의 메일을 확인하세요.  다음과 유사한 본문이 있는 메일을 받아야 합니다.<br><br>![샘플 메일](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md)에 대해 자세히 알아봅니다.
- [Microsoft Flow](https://ms.flow.microsoft.com)에 대해 자세히 알아봅니다.


