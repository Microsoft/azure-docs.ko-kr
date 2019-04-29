---
title: SAP 시스템에 연결 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 워크플로를 자동화하여 SAP 리소스에 액세스하고 관리하는 방법
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60847478"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps에서 SAP 시스템에 연결

> [!NOTE]
> 이 SAP 커넥터는 사용 중단 될 예정입니다. 사용 하거나 마이그레이션하는 [새롭고 더 많은 고급 SAP 커넥터](./logic-apps-using-sap-connector.md)합니다. 

이 문서에서는 SAP 애플리케이션 서버 및 SAP 메시지 서버 커넥터를 사용하여 논리 앱 내부에서 SAP 리소스에 액세스할 수 있는 방법을 보여 줍니다. 이런 식으로 작업, 프로세스 및 논리 앱을 만들어 SAP 데이터 및 리소스를 관리하는 워크플로를 자동화할 수 있습니다.

이 예제에서는 HTTP 요청으로 트리거할 수 있는 논리 앱을 사용합니다. 논리 앱은 IDoc(중간 문서)를 SAP 서버로 전송하고, 논리 앱을 호출한 요청자에게 응답을 반환합니다.
현재 SAP 커넥터에는 작업만 있고 트리거가 없으므로 이 예제에서는 논리 앱 워크플로의 첫 번째 단계로 [HTTP 요청 트리거](../connectors/connectors-native-reqres.md)를 사용합니다. SAP 커넥터 관련 기술 정보는 다음 참조 문서를 참조하세요. 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">SAP 애플리케이션 서버 커넥터</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">SAP 메시지 서버 커넥터</a>

아직 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 따르려면 다음 항목이 필요합니다.

* SAP 시스템에 액세스하려는 논리 앱과 논리 앱의 워크플로를 시작하는 트리거. SAP 커넥터는 현재 작업만 제공합니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP 애플리케이션 서버</a> 또는 <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP 메시지 서버</a>

* 온-프레미스 컴퓨터에서 최신 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127)를 다운로드하여 설치합니다. 계속 진행하기 전에 Azure Portal에서 게이트웨이를 설정하세요. 게이트웨이는 온-프레미스의 데이터 및 리소스에 안전하게 액세스하는 데 도움이 됩니다. 자세한 내용은 [Azure Logic Apps용 온-프레미스 데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md)를 참조하세요.

* 현재 <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">Microsoft .NET Framework 4.0 및 Windows 64비트(x64)용 SAP Connector(NCo) 3.0.20.0</a>인 최신 SAP 클라이언트 라이브러리를 온-프레미스 데이터 게이트웨이와 동일한 컴퓨터에서 다운로드하여 설치합니다. 이 버전 이상을 설치해야 하는 이유는 다음과 같습니다.

  * 이전 SAP NCo 버전은 둘 이상의 IDoc 메시지가 동시에 전송될 때 교착 상태가 될 수 있습니다. 
  이 조건에서는 SAP 대상으로 전송되는 이후 메시지가 모두 차단되어 메시지가 시간 초과됩니다.

  * 온-프레미스 데이터 게이트웨이는 64비트 시스템에서만 실행됩니다. 
  64비트 시스템이 아니면 데이터 게이트웨이 호스트 서비스가 32비트 어셈블리를 지원하지 않기 때문에 “잘못된 이미지” 오류가 표시됩니다.

  * 데이터 게이트웨이 호스트 서비스와 Microsoft SAP Adapter는 둘 다 .NET Framework 4.5를 사용합니다. .NET Framework 4.0용 SAP NCo는 .NET 런타임 4.0 - 4.7.1을 사용하는 프로세스에서 작동합니다. 
  .NET Framework 2.0용 SAP NCo는 .NET 런타임 2.0 - 3.5를 사용하는 프로세스에서 작동하며 최신 온-프레미스 데이터 게이트웨이에서는 더 이상 작동하지 않습니다.

* 샘플 IDoc 파일과 같이 SAP 서버로 전송할 수 있는 메시지 내용입니다. 이 콘텐츠는 XML 형식으로, 사용하려는 SAP 작업에 대한 네임스페이스를 포함해야 합니다.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>HTTP 요청 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 실행될 때마다 Logic Apps 엔진에서 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행하기 시작합니다.

이 예제에서는 논리 앱에 *HTTP POST 요청*을 보낼 수 있도록 Azure의 엔드포인트를 사용하여 논리 앱을 만듭니다. 논리 앱이 이러한 HTTP 요청을 받으면 트리거가 실행되고 워크플로의 다음 단계를 실행합니다.

1. Azure Portal에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 

2. 검색 상자에 “http 요청”을 필터로 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **요청 - HTTP 요청을 수신하는 경우**

   ![HTTP 요청 트리거 추가](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. 이제 논리 앱을 위한 엔드포인트 URL을 생성할 수 있도록 논리 앱을 저장합니다.
디자이너 도구 모음에서 **저장**을 선택합니다. 

   이제 엔드포인트 URL이 트리거에 표시됩니다. 예를 들면 다음과 같습니다.

   ![엔드포인트 URL 생성](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>SAP 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 논리 앱에 트리거를 아직 추가하지 않은 경우 이 예제를 따르려면 [이 섹션에 설명된 트리거를 추가](#add-trigger)합니다.

1. 논리 앱 디자이너에서 해당 트리거 아래의 **새 단계** > **작업 추가**를 선택합니다.

   ![작업 추가](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. 검색 상자에 “sql server”를 필터로 입력합니다. 작업 목록에서 SAP 서버에 대한 작업을 선택합니다. 

   * **SAP 애플리케이션 서버 - SAP로 보내기**
   * **SAP 메시지 서버 - SAP로 보내기**

   이 예제에서는이 작업을 사용합니다. **SAP 애플리케이션 서버 - SAP로 보내기**

   ![“SAP 애플리케이션 서버” 또는 “SAP 메시지 서버” 선택](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. 연결 정보를 묻는 메시지가 표시되면 이제 SAP 연결을 만듭니다. 또는 이미 SAP에 연결된 경우, SAP 작업을 설정할 수 있도록 다음 단계를 계속 진행합니다. 

   **온-프레미스 SAP 연결 만들기**

   1. **게이트웨이**에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택하여 온-프레미스 연결 속성을 표시합니다.

   2. SAP 서버에 대한 연결 정보를 입력합니다. 
   **게이트웨이** 속성에서 게이트웨이 설치에 대해 Azure Portal에서 만든 데이터 게이트웨이를 선택합니다. 예를 들면 다음과 같습니다.

      **SAP 애플리케이션 서버**

      ![SAP 애플리케이션 서버 연결 만들기](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP 메시지 서버**

      ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. 작업을 완료하면 **만들기**를 선택합니다.

      Logic Apps는 연결을 설정하고 테스트하여 제대로 작동되는지 확인합니다.

4. 이제 SAP 서버에서 작업을 찾아 선택합니다. 

   1. **SAP 작업** 상자에서 폴더 아이콘을 선택합니다. 
   폴더 목록에서 사용하려는 작업을 찾아서 선택합니다. 

      이 예제에서는 IDoc 작업의 **IDOC** 범주를 선택합니다. 

      ![IDoc 작업을 찾아서 선택합니다.](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      원하는 작업을 찾을 수 없는 경우 경로를 수동으로 입력할 수 있습니다. 예를 들면 다음과 같습니다.

      ![수동으로 IDoc 작업 경로 입력](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      IDoc 작업에 대한 자세한 내용은 [IDOC 작업에 대한 메시지 스키마](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조하세요.

   2. **입력 메시지** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 
   이 목록의 **HTTP 요청을 수신하는 경우** 아래에서 **본문** 필드를 선택합니다. 

      이 단계에서는 HTTP 요청 트리거의 본문 콘텐츠를 포함하며 해당 출력을 SAP 서버로 전송합니다.

      ![“본문” 필드 선택](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      여기까지 마쳤으면 SAP 작업이 다음 예제와 비슷하게 표시됩니다.

      ![SAP 작업 완료](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>HTTP 응답 작업 추가

이제 논리 앱의 워크플로에 응답 작업을 추가하고 SAP 작업의 출력을 포함합니다. 이렇게 하면 논리 앱이 SAP 서버의 결과를 원래 요청자에게 반환합니다. 

1. 논리 앱 디자이너에서 SAP 작업 아래의 **새 단계** > **작업 추가**를 선택합니다.

2. 검색 상자에 “응답”을 필터로 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **요청 - 응답**

3. **본문** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 해당 목록에서 **SAP로 보내기** 아래의 **본문** 필드를 선택합니다. 

   ![SAP 작업 완료](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. 논리 앱을 저장합니다. 

## <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱을 사용할 수 없는 경우 논리 앱 메뉴에서 **개요**를 선택합니다. 도구 모음에서 **사용**을 선택합니다. 

2. 논리 앱 디자이너 도구 모음에서 **실행**을 선택합니다. 이 단계에서는 논리 앱을 수동으로 시작합니다.

3. HTTP POST 요청을 HTTP 요청 트리거의 URL에 전송하여 논리 앱을 트리거하고 요청에 메시지 콘텐츠를 포함합니다. 요청을 보내려면 [Postman](https://www.getpostman.com/apps)과 같은 도구를 사용할 수 있습니다. 

   이 문서에서는 요청이 XML 형식으로, 사용 중인 SAP 작업에 대한 네임스페이스를 포함해야 하는 IDoc 파일을 보냅니다. 예를 들면 다음과 같습니다. 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. HTTP 요청을 보낸 후 논리 앱의 응답을 기다립니다.

> [!NOTE]
> 응답에 필요한 모든 단계가 [요청 시간 제한](./logic-apps-limits-and-config.md) 내에 완료되지 않을 경우 논리 앱이 시간 초과될 수 있습니다. 이 조건이 발생하면 요청이 차단될 수 있습니다. 문제를 진단하려면 [논리 앱을 확인하고 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)하는 방법을 알아보세요.

축하합니다. 이제 SAP 서버와 통신할 수 있는 논리 앱을 만들었습니다. 논리 앱에 대한 SAP 연결을 설정했으므로, BAPI 및 RFC와 같은 다른 사용 가능한 SAP 작업을 살펴볼 수 있습니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 커넥터에 대한 기술 정보는 다음 참조 문서를 참조하세요. 

* [SAP 애플리케이션 서버](/connectors/sapapplicationserver/)
* [SAP 메시지 서버](/connectors/sapmessageserver/)

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 논리 앱에서 [온-프레미스 시스템에 연결](../logic-apps/logic-apps-gateway-connection.md)합니다.
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)을 사용하여 유효성 검사, 변환 및 기타 메시지 작업을 수행하는 방법을 알아봅니다.
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
