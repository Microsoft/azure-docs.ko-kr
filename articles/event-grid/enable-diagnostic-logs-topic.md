---
title: Azure Event Grid-항목에 대한 진단 로그를 사용 하도록 설정
description: 이 문서에서는 Azure event grid 토픽에 대해 진단 로그를 사용 하도록 설정 하는 방법에 대한 단계별 지침을 제공 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960504"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Azure event grid 토픽에 대한 진단 로그
진단 설정을 사용 하면 Azure storage 계정, 이벤트 허브 또는 Log Analytics 작업 영역 중 하나에서 게시 및 배달 오류 로그를 캡처 및 볼 수 Event Grid 있습니다. 이 문서에서는 event grid 토픽에 대해 진단 로그를 사용 하도록 설정 하는 단계별 지침을 제공 합니다.

## <a name="prerequisites"></a>필수 조건

- 프로 비전 된 event grid 항목
- 진단 로그를 캡처하기 위해 프로 비전 된 대상입니다. 다음 대상 중 하나일 수 있습니다.
    - Azure Storage 계정
    - 이벤트 허브
    - Log Analytics 작업 영역


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>항목에 대한 진단 로그를 사용 하도록 설정 하는 단계

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 진단 로그 설정을 사용 하도록 설정 하려는 event grid 항목으로 이동 합니다. 
3. 왼쪽 메뉴의 **모니터링** 아래에서 **진단 설정** 을 선택 합니다.
4. **진단 설정** 페이지에서 **새 진단 설정 추가**를 선택 합니다. 
    
    ![진단 설정 추가 단추](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 진단 설정의 **이름을** 지정 합니다. 

    ![진단 설정-이름](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. 로그에 대한 캡처 대상을 하나 이상 사용 하도록 설정 하 고 이전에 만든 캡처 리소스를 선택 하 여 구성 합니다. 
    - **저장소 계정에 보관**을 선택 하는 경우 **저장소 계정-구성**을 선택 하 고 Azure 구독에서 저장소 계정을 선택 합니다. 

        ![Azure storage 계정에 보관](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **이벤트 허브로 스트림**을 선택 하는 경우 **이벤트 허브-구성**을 선택한 다음 Event Hubs 네임 스페이스, 이벤트 허브 및 액세스 정책을 선택 합니다. 
        이벤트 허브로 스트림 ![](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **Log Analytics 보내기를**선택 하는 경우 Log Analytics 작업 영역을 선택 합니다.
        Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png) ![보내기
7. **로그** 섹션에서 **DeliveryFailures** 및 기타 **오류** 옵션을 선택 합니다. 
    오류 ![선택](./media/enable-diagnostic-logs-topic/log-failures.png)
8. **저장**을 선택합니다. 오른쪽 모서리에 있는 **X** 를 선택 하 여 페이지를 닫습니다. 
9. 이제 **진단 설정** 페이지로 돌아가 **진단 설정** 테이블에 새 항목이 표시 되는지 확인 합니다. 
    목록에서 진단 설정 ![](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     토픽의 모든 메트릭 수집을 사용 하도록 설정할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid) 에 문제를 게시 하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요. 
