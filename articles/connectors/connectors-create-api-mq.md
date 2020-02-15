---
title: IBM MQ server에 연결
description: Azure 또는 온-프레미스 IBM MQ 서버를 사용 하 여 메시지를 보내고 검색 하 고 Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 489f53a4f4c1c0d5bd782f42a9daf73217234793
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118057"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure Logic Apps에서 IBM MQ 서버에 연결

IBM MQ 커넥터는 온-프레미스 또는 Azure에서 IBM MQ 서버에 저장 된 메시지를 보내고 검색 합니다. 이 커넥터는 TCP/IP 네트워크를 통해 원격 IBM MQ 서버와 통신하는 Microsoft MQ 클라이언트를 포함합니다. 이 문서에서는 MQ 커넥터를 사용 하기 위한 시작 가이드를 제공 합니다. 먼저 큐에서 단일 메시지를 검색 한 다음 다른 작업을 시도할 수 있습니다.

IBM MQ 커넥터에는 이러한 작업이 포함 되지만 트리거는 제공 되지 않습니다.

- IBM MQ 서버에서 메시지를 삭제 하지 않고 단일 메시지 찾아보기
- IBM MQ 서버에서 메시지를 삭제 하지 않고 메시지 일괄 처리 찾아보기
- 단일 메시지 수신 및 IBM MQ 서버에서 메시지 삭제
- 메시지 일괄 처리를 받고 IBM MQ 서버에서 메시지 삭제
- IBM MQ 서버에 단일 메시지 보내기

## <a name="prerequisites"></a>사전 요구 사항

* 온-프레미스 MQ 서버를 사용 하는 경우 네트워크 내의 서버에 [온-프레미스 데이터 게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md) 합니다. MQ 커넥터가 작동 하려면 온-프레미스 데이터 게이트웨이를 설치한 서버에 .NET Framework 4.6도 설치 되어 있어야 합니다. 또한 Azure에서 온-프레미스 데이터 게이트웨이에 대 한 리소스를 만들어야 합니다. 자세한 내용은 [data gateway 연결 설정](../logic-apps/logic-apps-gateway-connection.md)을 참조 하세요.

  그러나 Azure 내에서 MQ 서버를 공개적으로 사용할 수 있거나 사용할 수 있는 경우에는 데이터 게이트웨이를 사용할 필요가 없습니다.

* 공식적으로 지원되는 IBM WebSphere MQ 버전:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* MQ 작업을 추가 하려는 논리 앱입니다. 이 논리 앱은 온-프레미스 데이터 게이트웨이 연결과 동일한 위치를 사용 해야 하며 워크플로를 시작 하는 트리거가 이미 있어야 합니다. 

  MQ 커넥터에는 트리거가 없으므로 먼저 논리 앱에 트리거를 추가 해야 합니다. 예를 들어 되풀이 트리거를 사용할 수 있습니다. 논리 앱을 처음 접하는 경우이 [빠른 시작을 사용해 서 첫 번째 논리 앱을 만드세요](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>단일 메시지 찾아보기

1. 논리 앱의 트리거 또는 다른 작업에서 **새 단계**를 선택 합니다. 

1. 검색 상자에 "mq"를 입력 하 고이 작업: **찾아보기 메시지** 를 선택 합니다.

   ![메시지 찾아보기](media/connectors-create-api-mq/Browse_message.png)

1. 기존 MQ 연결이 없는 경우 연결을 만듭니다.  

   1. 작업에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택 합니다.
   
   1. MQ 서버에 대 한 속성을 입력 합니다.  

      **서버**에서 MQ 서버 이름을 입력하거나 콜론과 포트 번호 뒤에 IP 주소를 입력합니다.
    
   1. 이전에 구성 된 게이트웨이 연결을 보여 주는 **게이트웨이** 목록을 엽니다. 게이트웨이를 선택합니다.
    
   1. 작업을 완료하면 **만들기**를 선택합니다. 
   
      연결은 다음 예제와 같습니다.

      ![연결 속성](media/connectors-create-api-mq/Connection_Properties.png)

1. 작업의 속성을 설정 합니다.

   * **Queue**: 연결과 다른 큐를 지정 합니다.

   * **MessageId**, **CorrelationId**, **GroupId**및 기타 속성: 다른 MQ 메시지 속성을 기반으로 메시지 찾아보기

   * **Includeinfo**: 출력에 추가 메시지 정보를 포함 하려면 **True** 를 지정 합니다. 또는 출력에 추가 메시지 정보를 포함 하지 않으려면 **False** 를 지정 합니다.

   * **시간 제한**: 빈 큐에 메시지가 도착할 때까지 대기 하는 시간을 결정 하는 값을 입력 합니다. 아무 것도 입력하지 않은 경우 큐의 첫 번째 메시지를 검색하면 메시지가 표시되는 시간이 없어집니다.

     ![메시지 속성 찾아보기](media/connectors-create-api-mq/Browse_message_Props.png)

1. 변경 내용을 **저장** 한 다음 논리 앱을 **실행** 합니다.

   ![저장 및 실행](media/connectors-create-api-mq/Save_Run.png)

   실행이 완료 되 면 실행의 단계가 표시 되 고 출력을 검토할 수 있습니다.

1. 각 단계에 대 한 세부 정보를 검토 하려면 녹색 확인 표시를 선택 합니다. 출력 데이터에 대 한 자세한 내용을 보려면 **원시 출력 표시**를 선택 합니다.

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_message_output.png)  

   다음은 몇 가지 샘플 원시 출력입니다.

   ![메시지 원시 출력 찾아보기](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. **Includeinfo** 를 true로 설정 하면 다음과 같은 출력이 표시 됩니다.

   ![메시지 포함 정보 찾아보기](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>여러 메시지 찾아보기

**메시지 찾아보기** 작업은 큐에서 반환되는 메시지의 수를 나타내는 **BatchSize** 옵션을 포함합니다.  **BatchSize**에 항목이 없는 경우 모든 메시지가 반환됩니다. 반환된 출력은 메시지의 배열입니다.

1. **메시지 찾아보기** 작업을 추가 하면 이전에 구성 된 첫 번째 연결이 기본적으로 선택 됩니다. 새 연결을 만들려면 **연결 변경**을 선택 합니다. 또는 다른 연결을 선택 합니다.

1. 논리 앱 실행이 완료 된 후 다음은 **메시지 찾아보기** 작업의 몇 가지 샘플 출력입니다.

   ![메시지 출력 찾아보기](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>단일 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

## <a name="receive-multiple-messages"></a>여러 메시지 수신

**메시지 수신** 작업에는 **메시지 찾아보기** 작업과 동일한 입력 및 출력이 포함됩니다. **메시지 수신**을 사용하는 경우 메시지는 큐에서 삭제됩니다.

찾아보기 또는 수신을 수행할 때 큐에 메시지가 없는 경우이 출력으로 단계가 실패 합니다.  

![MQ 메시지 오류 없음](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>메시지 보내기

**메시지 보내기** 작업을 추가 하면 이전에 구성 된 첫 번째 연결이 기본적으로 선택 됩니다. 새 연결을 만들려면 **연결 변경**을 선택 합니다. 또는 다른 연결을 선택 합니다.

1. 올바른 메시지 유형 ( **데이터 그램**, **회신**또는 **요청** )을 선택 합니다.  

   ![메시지 Props 보내기](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 논리 앱의 실행이 완료 된 후 다음은 **메시지 보내기** 작업의 몇 가지 샘플 출력입니다.

   ![메시지 출력 전송](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI (이전의 Swagger) 설명에서 설명 하는 작업 및 제한에 대 한 기술 세부 정보는 커넥터의 [참조 페이지를 참조](/connectors/mq/)하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
