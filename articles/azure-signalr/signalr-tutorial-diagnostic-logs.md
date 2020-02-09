---
title: Azure SignalR Service에 대한 진단 로그
description: Azure SignalR Service에 대한 진단 로그를 설정 하는 방법과이를 활용 하 여 자체 문제를 해결 하는 방법에 대해 알아봅니다.
author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 33d9a338e12fa4b3d2449f0c5b0576895364c3cf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750272"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Azure SignalR Service에 대한 진단 로그

이 자습서에서는 Azure SignalR Service에 대한 진단 로그 및 진단 로그를 설정 하는 방법 및 진단 로그 문제를 해결 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건
진단 로그를 사용 하도록 설정 하려면 로그 데이터를 저장 하는 위치에 있어야 합니다. 이 자습서에서는 Azure Storage와 Log Analytics를 사용 합니다.

* [Azure storage](../azure-monitor/platform/resource-logs-collect-storage.md) -정책 감사, 정적 분석 또는 백업에 대한 진단 로그를 유지 합니다.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) -Azure 리소스에서 생성 된 원시 로그를 분석할 수 있는 유연한 로그 검색 및 분석 도구입니다.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Azure SignalR 서비스에 대한 진단 로그 설정

Azure SignalR 서비스에 대한 진단 로그를 볼 수 있습니다. 이러한 로그는 Azure SignalR 서비스 인스턴스에 대한 연결을 더욱 잘 보여 주는 뷰를 제공 합니다. 진단 로그는 모든 연결에 대한 자세한 정보를 제공 합니다. 예를 들어 기본 정보 (사용자 ID, 연결 ID 및 전송 유형 등)와 연결의 이벤트 정보 (연결, 연결 끊기 및 중단 이벤트 등)가 있습니다. 진단 로그는 문제 식별, 연결 추적 및 분석에 사용할 수 있습니다.

### <a name="enable-diagnostic-logs"></a>진단 로그 활성화

진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 활성화하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)의 **모니터링**에서 **진단 설정**을 클릭 합니다.

    ![진단 설정에 대한 창 탐색](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1.  그런 다음 **진단 설정 추가**를 클릭 합니다.

    ![진단 로그 추가](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1.  원하는 보관 대상을 설정 합니다. 현재 **는 저장소 계정에 대한 보관** 을 지원 하 고 **Log Analytics으로 보냅니다**.

1. 보관 하려는 로그를 선택 합니다.

    ![진단 설정 창](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1.  새 진단 설정을 저장합니다.

새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 창에 로그가 나타납니다.

진단 구성에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/platform-logs-overview.md)를 참조하세요.

### <a name="diagnostic-logs-categories"></a>진단 로그 범주

Azure SignalR Service는 한 범주에서 진단 로그를 캡처합니다.

* **모든 로그**: Azure SignalR Service에 연결 하는 연결을 추적 합니다. 로그는 연결/연결 끊기, 인증 및 제한에 대한 정보를 제공 합니다. 자세한 내용은 다음 섹션을 참조하세요.

### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관

로그는 **진단 로그** 창에 구성 된 저장소 계정에 저장 됩니다. `insights-logs-alllogs` 이라는 컨테이너가 자동으로 만들어져 진단 로그를 저장 합니다. 컨테이너 내에서 로그는 `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`파일에 저장 됩니다. 기본적으로 경로는 `resource ID` 및 `Date Time`으로 결합 됩니다. 로그 파일은 `hour`로 분할 됩니다. 따라서 분은 항상 `m=00`합니다.

모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

보관 로그 JSON 문자열에는 다음 표에 나열 된 요소가 포함 됩니다.

**형식**

이름 | Description
------- | -------
time | 로그 이벤트 시간
level | 로그 이벤트 수준
resourceId | Azure SignalR 서비스의 리소스 ID
위치 | Azure SignalR 서비스의 위치
category | 로그 이벤트의 범주입니다.
operationName | 이벤트의 작업 이름입니다.
callerIpAddress | 서버/클라이언트의 IP 주소
properties | 이 로그 이벤트와 관련 된 자세한 속성입니다. 자세한 내용은 아래 속성 표를 참조 하세요.

**속성 테이블**

이름 | Description
------- | -------
type | 로그 이벤트의 유형입니다. 현재 Azure SignalR 서비스에 대한 연결 정보를 제공 합니다. `ConnectivityLogs` 유형만 사용할 수 있습니다.
collection | 로그 이벤트의 컬렉션입니다. 허용 되는 값은 `Connection`, `Authorization` 및 `Throttling`입니다.
connectionId | 연결 id
transportType | 연결의 전송 유형입니다. 허용 되는 값은 `Websockets` \| `ServerSentEvents` \| `LongPolling`
connectionType | 연결 유형입니다. 허용 되는 값은 `Server` \| `Client`입니다. `Server`: 서버 쪽에서 연결 `Client`: 클라이언트 쪽에서 연결
userId | 사용자 id
message | 로그 이벤트의 세부 메시지

다음 코드는 보관 로그 JSON 문자열에 대한 예입니다.

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Log Analytics에 대한 보관 로그 스키마

진단 로그를 보려면 다음 단계를 수행 합니다.

1. 대상 Log Analytics에서 `Logs`를 클릭 합니다.

    ![Log Analytics 메뉴 항목](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. `SignalRServiceDiagnosticLogs`를 입력 하 고 시간 범위를 선택 하 여 진단 로그를 쿼리 합니다. 고급 쿼리는 [에서 Log Analytics 시작](../azure-monitor/log-query/get-started-portal.md) 을 참조 하세요 Azure Monitor

    ![Log Analytics 쿼리 로그](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

보관 로그 열에는 다음 표에 나열 된 요소가 포함 됩니다.

이름 | Description
------- | ------- 
TimeGenerated | 로그 이벤트 시간
컬렉션 | 로그 이벤트의 컬렉션입니다. 허용 되는 값은 `Connection`, `Authorization` 및 `Throttling`입니다.
OperationName | 이벤트의 작업 이름입니다.
위치 | Azure SignalR 서비스의 위치
수준 | 로그 이벤트 수준
callerIpAddress | 서버/클라이언트의 IP 주소
메시지 | 로그 이벤트의 세부 메시지
UserId | 사용자 id
ConnectionId | 연결 id
ConnectionType | 연결 유형입니다. 허용 되는 값은 `Server` \| `Client`입니다. `Server`: 서버 쪽에서 연결 `Client`: 클라이언트 쪽에서 연결
TransportType | 연결의 전송 유형입니다. 허용 되는 값은 `Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>진단 로그 문제 해결

Azure SignalR 서비스에 대한 문제를 해결 하기 위해 서버/클라이언트 쪽 로그를 사용 하도록 설정 하 여 오류를 캡처할 수 있습니다. 현재 Azure SignalR Service는 진단 로그를 노출 하 고, 서비스 쪽에 대해 로그를 사용 하도록 설정할 수도 있습니다.

예기치 않은 연결이 늘어나고 발생 하는 상황이 발생 하면 문제를 해결 하기 위해 진단 로그를 활용할 수 있습니다.

일반적인 문제는 연결의 예기치 않은 수량 변경, 연결 제한 및 권한 부여 오류에 대한 경우가 많습니다. 문제를 해결 하는 방법에 대한 다음 섹션을 참조 하세요.

#### <a name="unexpected-connection-number-changes"></a>예기치 않은 연결 번호 변경

##### <a name="unexpected-connection-dropping"></a>예기치 않은 연결 삭제

예기치 않은 연결 삭제를 발생 하는 경우 먼저 서비스, 서버 및 클라이언트 쪽에서 로그를 사용 하도록 설정 합니다.

연결의 연결이 끊어지면 진단 로그가이 연결 끊기 이벤트를 기록 하 고 `operationName`에 `ConnectionAborted` 또는 `ConnectionEnded` 표시 됩니다.

`ConnectionAborted`와 `ConnectionEnded`의 차이점은 `ConnectionEnded`은 클라이언트 또는 서버 쪽에서 트리거되는 예상 되는 연결이 끊어지는 것입니다. `ConnectionAborted`은 일반적으로 예기치 않은 연결 삭제 이벤트 이지만 중단 이유는 `message`제공 됩니다.

중단 이유는 다음 표에 나와 있습니다.

이유 | Description
------- | ------- 
연결 수가 제한에 도달 합니다. | 연결 수는 현재 가격 책정 계층의 제한에 도달 합니다. 서비스 단위 확장 고려
응용 프로그램 서버에서 연결을 닫았습니다. | 앱 서버는 abortion를 트리거합니다. 예상 된 abortion 간주 될 수 있습니다.
연결 ping 제한 시간 | 일반적으로 네트워크 문제로 인해 발생 합니다. 인터넷에서 앱 서버 가용성을 확인 하는 것이 좋습니다.
서비스 다시 로드, 다시 연결 | Azure SignalR 서비스를 다시 로드 하 고 있습니다. Azure SignalR는 자동 다시 연결을 지원 하 고, 다시 연결 되거나 수동으로 Azure SignalR Service에 다시 연결할 때까지 대기할 수 있습니다.
내부 서버 임시 오류 | Azure SignalR Service에서 일시적인 오류가 발생 하며, 자동으로 복구 되어야 합니다.
서버 연결 끊김 | 서버 연결이 알 수 없는 오류와 함께 삭제 됩니다. 먼저 서비스/서버/클라이언트 쪽 로그를 사용 하 여 자체 문제 해결을 고려 하십시오. 기본 문제 (예: 네트워크 문제, 앱 서버 쪽 문제 등)를 제외 하려고 합니다. 문제가 해결 되지 않으면 microsoft에 문의 하 여 도움을 받으십시오. 자세한 내용은 [도움말 가져오기](#get-help) 섹션을 참조 하세요. 

##### <a name="unexpected-connection-growing"></a>예기치 않은 연결 증가

예기치 않은 연결 증가에 대해 문제를 해결 하려면 먼저 추가 연결을 필터링 해야 합니다. 테스트 클라이언트 연결에 고유한 테스트 사용자 ID를 추가할 수 있습니다. 그런 다음 진단 로그를 사용 하 여 확인 하 고, 둘 이상의 클라이언트 연결이 동일한 테스트 사용자 ID 또는 IP를 사용 하는 경우 클라이언트 쪽에서 예상 보다 많은 연결을 만들고 설정할 가능성이 높습니다. 클라이언트 쪽을 확인 합니다.

#### <a name="authorization-failure"></a>권한 부여 실패

클라이언트 요청에 대해 인증 되지 않은 401이 반환 되는 경우 진단 로그를 확인 합니다. `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`발생 하는 경우 액세스 토큰의 모든 대상 그룹이 유효 하지 않음을 의미 합니다. 로그에 제안 된 올바른 대상 그룹을 사용 하십시오.


#### <a name="throttling"></a>제한

Azure SignalR Service에 대한 SignalR 클라이언트 연결을 설정할 수 없는 경우 진단 로그를 확인 합니다. 진단 로그에 `Connection count reaches limit` 발생 한 경우 연결 수 제한에 도달 하는 SignalR Service에 대한 연결을 너무 많이 설정 합니다. SignalR 서비스를 확장 하는 것이 좋습니다. 진단 로그에 `Message count reaches limit` 발생 하는 경우 무료 계층을 사용 하 고 메시지 할당량을 사용 하는 것을 의미 합니다. 더 많은 메시지를 보내려면 SignalR 서비스를 표준 계층으로 변경 하 여 추가 메시지를 전송 하는 것이 좋습니다. 자세한 내용은 [Azure SignalR Service 가격 책정](https://azure.microsoft.com/pricing/details/signalr-service/)을 참조 하세요.

### <a name="get-help"></a>도움 받기

먼저 문제를 해결 하는 것이 좋습니다. 대부분의 문제는 앱 서버 또는 네트워크 문제로 인해 발생 합니다. 진단 로그 및 기본적인 문제 해결 [가이드](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) 의 [문제 해결 가이드](#troubleshooting-with-diagnostic-logs) 에 따라 근본 원인을 찾으십시오.
그래도 문제를 해결할 수 없는 경우 GitHub에서 문제를 열거나 Azure Portal에서 티켓을 만듭니다.
다음을 제공합니다.
1. 문제가 발생 하는 시간 범위는 30 분입니다.
2. Azure SignalR 서비스의 리소스 ID
3. 가능한 한 특정 한 문제 세부 정보: 예를 들어 microsoft.windows.appserver.2008는 메시지를 보내고, 클라이언트 연결은 삭제 하지 않습니다.
4. 서버/클라이언트 쪽에서 수집 된 로그 및 유용 하 게 사용할 수 있는 기타 자료
5. 필드 재현 코드

> 참고: GitHub에서 문제를 여는 경우 중요 한 정보 (예: 리소스 ID, 서버/클라이언트 로그)를 비공개로 유지 합니다. Microsoft 조직의 구성원 에게만 보냅니다.  
