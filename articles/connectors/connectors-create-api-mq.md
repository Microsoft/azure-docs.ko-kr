---
title: MQ server에 연결 - Azure Logic Apps | Microsoft Docs
description: Azure 또는 온-프레미스 MQ 서버 및 Azure Logic Apps로 메시지 보내기 및 검색
author: valrobb
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e6ae5cb0afd75a1e87fe4d4d0cf307abab5a02a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60688830"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>MQ 커넥터를 사용하여 논리 앱에서 IBM MQ 서버에 연결

MQ용 Microsoft 커넥터는 MQ 서버 온-프레미스 또는 Azure에 저장된 메시지를 전송하고 검색합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 IBM MQ 서버와 통신하는 Microsoft MQ 클라이언트를 포함합니다. 이 문서는 MQ 커넥터를 사용하는 시작 가이드입니다. 큐에서 단일 메시지를 검색하고 다른 작업을 시도하여 시작하는 것이 좋습니다.

MQ 커넥터에는 다음 작업이 포함됩니다. 트리거는 없습니다.

- IBM MQ 서버에서 메시지를 삭제하지 않고 단일 메시지 찾아보기
- IBM MQ 서버에서 메시지를 삭제하지 않고 일괄 처리 메시지 찾아보기
- 단일 메시지 수신 및 IBM MQ 서버에서 메시지 삭제
- 일괄 처리 메시지 수신 및 IBM MQ 서버에서 메시지 삭제
- IBM MQ 서버에 단일 메시지 전송

## <a name="prerequisites"></a>필수 조건

* 온-프레미스 MQ 서버를 사용하는 경우 네트워크 내의 서버에서 [온-프레미스 데이터 게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md)합니다. MQ 서버가 공개적으로 사용할 수 있거나 Azure 내에서 사용할 수 있는 경우 데이터 게이트웨이를 사용하지 않거나 필요하지 않습니다.

    > [!NOTE]
    > 온-프레미스 데이터 게이트웨이 설치한 서버에는 MQ 커넥터가 작동 하기에 대 한 설치 된.NET Framework 4.6 있어야 합니다.

* 온-프레미스 데이터 게이트웨이에 Azure 리소스 만들기 - [데이터 게이트웨이 연결을 설정합니다](../logic-apps/logic-apps-gateway-connection.md).

* 공식적으로 지원되는 IBM WebSphere MQ 버전:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>논리 앱 만들기

1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. MQTestApp, **구독**, **리소스 그룹** 및 **위치**와 같은 **이름**을 입력합니다(온-프레미스 데이터 게이트웨이 연결이 구성된 위치를 사용). **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.  
![논리 앱 만들기](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>트리거 추가

> [!NOTE]
> MQ 커넥터에는 트리거가 없습니다. 따라서 다른 트리거를 사용하여 **되풀이** 트리거와 같은 논리 앱을 시작합니다.

1. **Logic Apps 디자이너**가 열리면 일반적인 트리거의 목록에서 **되풀이**를 선택합니다.
2. 되풀이 트리거 내에서 **편집**을 선택합니다.
3. **빈도**를 **일**로, **간격**을 **7**로 설정합니다.

## <a name="browse-a-single-message"></a>단일 메시지 찾아보기
1. **+ 새 단계**를 선택한 다음 **작업 추가**를 선택합니다.
2. 검색 상자에 `mq`을 입력한 다음 **MQ - 메시지 찾아보기**를 선택합니다.  
![메시지 찾아보기](media/connectors-create-api-mq/Browse_message.png)

3. 기존 MQ 연결이 없으면 다음 연결을 만듭니다.  

    1. **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택하고 MQ 서버의 속성을 입력합니다.  
    **서버**에서 MQ 서버 이름을 입력하거나 콜론과 포트 번호 뒤에 IP 주소를 입력합니다.
    2. **게이트웨이** 드롭다운은 구성된 기존 게이트웨이 연결을 나열합니다. 게이트웨이를 선택합니다.
    3. 작업을 완료하면 **만들기**를 선택합니다. 연결은 다음과 유사합니다.  
    ![연결 속성](media/connectors-create-api-mq/Connection_Properties.png)

4. 작업 속성에서 다음을 수행할 수 있습니다.  

    * **큐** 속성을 사용하여 연결에 정의된 것과 다른 큐 이름에 액세스합니다.
    * **MessageId**, **CorrelationId**, **GroupId** 및 기타 속성을 사용하여 다른 MQ 메시지 속성에 따라 메시지를 찾아봅니다.
    * **IncludeInfo**를 **True**로 설정하여 출력에서 추가 메시지 정보를 포함하도록 합니다. 또는 해당 항목을 **False**로 설정하여 출력에서 추가 메시지 정보를 포함하지 않도록 합니다.
    * 비어 있는 큐에 도달하는 메시지를 기다리는 기간을 결정하는 **시간 제한** 값을 입력합니다. 아무 것도 입력하지 않은 경우 큐의 첫 번째 메시지를 검색하면 메시지가 표시되는 시간이 없어집니다.  
    ![메시지 속성 찾아보기](media/connectors-create-api-mq/Browse_message_Props.png)

5. 변경 내용을 **저장**한 다음 논리 앱을 **실행**합니다.  
![저장 및 실행](media/connectors-create-api-mq/Save_Run.png)

6. 몇 초 후에 실행 단계가 표시되고 출력을 살펴볼 수 있습니다. 각 단계에 대한 자세한 내용을 보려면 녹색 확인 표시를 선택합니다. **원시 출력 참조**를 선택하여 출력 데이터에 대한 추가 세부 정보를 볼 수 있습니다.  
![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_message_output.png)  

    원시 출력:  
    ![메시지 원시 출력 찾아보기](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. **IncludeInfo** 옵션이 true로 설정되면 다음과 같은 출력이 표시됩니다.  
![메시지 포함 정보 찾아보기](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>여러 메시지 찾아보기
**메시지 찾아보기** 작업은 큐에서 반환되는 메시지의 수를 나타내는 **BatchSize** 옵션을 포함합니다.  **BatchSize**에 항목이 없는 경우 모든 메시지가 반환됩니다. 반환된 출력은 메시지의 배열입니다.

1. **메시지 찾아보기** 작업을 추가하는 경우 기본적으로 구성된 첫 번째 연결이 선택됩니다. **연결 변경**을 선택하여 새 연결을 만들거나 다른 연결을 선택합니다.

2. 찾아보기 메시지의 출력은 다음과 같이 표시됩니다.  
![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>단일 메시지 수신
**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

## <a name="receive-multiple-messages"></a>여러 메시지 수신
**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

찾아보기 또는 수신을 수행하는 경우 큐에 메시지가 없으면 단계는 다음과 같은 출력을 표시하여 실패합니다.  
![MQ 메시지 오류 없음](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>메시지 보내기
1. **메시지 발신** 작업을 추가하는 경우 기본적으로 구성된 첫 번째 연결이 선택됩니다. **연결 변경**을 선택하여 새 연결을 만들거나 다른 연결을 선택합니다. 유효한 **메시지 형식**은 **데이터그램**, **회신** 또는 **요청**입니다.  
![메시지 속성 전송](media/connectors-create-api-mq/Send_Msg_Props.png)

2. 메시지 전송의 출력은 다음과 유사합니다.  
![메시지 출력 전송](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/mq/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.
