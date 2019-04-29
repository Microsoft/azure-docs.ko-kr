---
title: SAP 시스템에 연결 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 워크플로를 자동화하여 SAP 리소스에 액세스하고 관리하는 방법
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 09/14/2018
tags: connectors
ms.openlocfilehash: 468e73c64037a76da612cba8d6c2e9507dd3ac87
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120164"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps에서 SAP 시스템에 연결

이 문서에서는 SAP ECC(ERP Central Component) 커넥터를 사용하여 논리 앱 내부에서 온-프레미스 SAP 리소스에 액세스할 수 있는 방법을 보여줍니다. 커넥터는 온-프레미스 ECC 및 S/4 HANA 시스템을 사용 하 여 작동합니다. SAP ECC 커넥터는 IDoc(Intermediate Document) 또는 BAPI(비즈니스 애플리케이션 프로그래밍 인터페이스) 또는 RFC(원격 함수 호출)를 통해 SAP Netweaver 기반 시스템과의 메시지 또는 데이터 통합을 지원합니다.

SAP ECC 커넥터는 사용 된 <a href="https://support.sap.com/en/product/connectors/msnet.html">SAP.NET Connector (NCo) 라이브러리</a> 하 고 이러한 작업 또는 동작이 제공:

- **SAP로 보내기**: SAP 시스템의 tRFC를 통해 IDoc를 보내거나 BAPI 함수를 호출합니다.
- **SAP에서 수신**: SAP 시스템의 tRFC를 통해 IDoc 또는 BAPI 함수 호출을 수신합니다.
- **스키마 생성**: IDoc, BAPI 또는 RFC에 대한 SAP 아티팩트의 스키마를 생성합니다.

SAP 커넥터는 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127)를 통해 온-프레미스 SAP 시스템과 통합됩니다. 예를 들어 보내기 시나리오에서 Logic Apps의 메시지를 SAP 시스템으로 보낼 때 데이터 게이트웨이는 RFC 클라이언트 역할을 하여 Logic Apps에서 받은 요청을 SAP로 전달합니다.
마찬가지로 수신 시나리오에서 데이터 게이트웨이는 SAP에서 요청을 수신하여 논리 앱에 전달하는 RFC 서버 역할을 합니다. 

이 문서에서는 앞에서 설명한 통합 시나리오를 다루면서 SAP와 통합되는 논리 앱 예제를 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 따르려면 다음 항목이 필요합니다.

* Azure 구독. 아직 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* SAP 시스템에 액세스하려는 논리 앱과 논리 앱의 워크플로를 시작하는 트리거. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP 애플리케이션 서버</a> 또는 <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP 메시지 서버</a>

* 온-프레미스 컴퓨터에서 최신 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127)를 다운로드하여 설치합니다. 계속 진행하기 전에 Azure Portal에서 게이트웨이를 설정하세요. 게이트웨이는 온-프레미스의 데이터 및 리소스에 안전하게 액세스하는 데 도움이 됩니다. 자세한 내용은 [Azure Logic Apps용 온-프레미스 데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md)를 참조하세요.

* 현재 <a href="https://softwaredownloads.sap.com/file/0020000001865512018" target="_blank">Microsoft .NET Framework 4.0 및 Windows 64비트(x64)용 SAP Connector(NCo) 3.0.21.0</a>인 최신 SAP 클라이언트 라이브러리를 온-프레미스 데이터 게이트웨이와 동일한 컴퓨터에서 다운로드하여 설치합니다. 이 버전 이상을 설치해야 하는 이유는 다음과 같습니다.

  * 이전 SAP NCo 버전은 둘 이상의 IDoc 메시지가 동시에 전송될 때 교착 상태가 될 수 있습니다. 
  이 조건에서는 SAP 대상으로 전송되는 이후 메시지가 모두 차단되어 메시지가 시간 초과됩니다.

  * 온-프레미스 데이터 게이트웨이는 64비트 시스템에서만 실행됩니다. 
  64비트 시스템이 아니면 데이터 게이트웨이 호스트 서비스가 32비트 어셈블리를 지원하지 않기 때문에 “잘못된 이미지” 오류가 표시됩니다.

  * 데이터 게이트웨이 호스트 서비스와 Microsoft SAP Adapter는 둘 다 .NET Framework 4.5를 사용합니다. .NET Framework 4.0용 SAP NCo는 .NET 런타임 4.0 - 4.7.1을 사용하는 프로세스에서 작동합니다. 
  .NET Framework 2.0용 SAP NCo는 .NET 런타임 2.0 - 3.5를 사용하는 프로세스에서 작동하며 최신 온-프레미스 데이터 게이트웨이에서는 더 이상 작동하지 않습니다.

* 샘플 IDoc 파일과 같이 SAP 서버로 전송할 수 있는 메시지 내용입니다. 이 콘텐츠는 XML 형식으로, 사용하려는 SAP 작업에 대한 네임스페이스를 포함해야 합니다.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>SAP로 보내기

이 예제에서는 HTTP 요청으로 트리거할 수 있는 논리 앱을 사용합니다. 논리 앱은 IDoc(중간 문서)를 SAP 서버로 전송하고, 논리 앱을 호출한 요청자에게 응답을 반환합니다. 

### <a name="add-http-request-trigger"></a>HTTP 요청 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 실행될 때마다 Logic Apps 엔진에서 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행하기 시작합니다.

이 예제에서는 논리 앱에 *HTTP POST 요청*을 보낼 수 있도록 Azure의 엔드포인트를 사용하여 논리 앱을 만듭니다. 논리 앱이 이러한 HTTP 요청을 받으면 트리거가 실행되고 워크플로의 다음 단계를 실행합니다.

1. [Azure Portal](https://portal.azure.com)에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 

2. 검색 상자에 “http 요청”을 필터로 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **요청 - HTTP 요청을 수신하는 경우**

   ![HTTP 요청 트리거 추가](./media/logic-apps-using-sap-connector/add-trigger.png)

3. 이제 논리 앱을 위한 엔드포인트 URL을 생성할 수 있도록 논리 앱을 저장합니다.
디자이너 도구 모음에서 **저장**을 선택합니다. 

   이제 엔드포인트 URL이 트리거에 표시됩니다. 예를 들면 다음과 같습니다.

   ![엔드포인트 URL 생성](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>SAP 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 논리 앱에 트리거를 아직 추가하지 않은 경우 이 예제를 따르려면 [이 섹션에 설명된 트리거를 추가](#add-trigger)합니다.

1. 논리 앱 디자이너에서 해당 트리거 아래의 **새 단계** > **작업 추가**를 선택합니다.

   ![작업 추가](./media/logic-apps-using-sap-connector/add-action.png) 

2. 검색 상자에서 필터로 "sap"를 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **SAP로 메시지 보내기**
  
   ![SAP 보내기 작업 선택](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   또는 검색하는 대신 **Enterprise** 탭을 선택하고 SAP 작업을 선택할 수도 있습니다.

   ![Enterprise 탭에서 SAP 보내기 작업 선택](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. 연결 정보를 묻는 메시지가 표시되면 이제 SAP 연결을 만듭니다. 또는 이미 SAP에 연결된 경우, SAP 작업을 설정할 수 있도록 다음 단계를 계속 진행합니다. 

   **온-프레미스 SAP 연결 만들기**

   1. SAP 서버에 대한 연결 정보를 입력합니다. 
   **Data Gateway** 속성에서, 게이트웨이 설치를 위해 Azure Portal에서 만든 데이터 게이트웨이를 선택합니다.

      **Logon Type** 속성을 **Application Server**로 설정하면 다음 속성이 필요합니다(보통은 선택 사항).

      ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      **Logon Type** 속성을 **Group**으로 설정하면 다음 속성이 필요합니다(보통은 선택 사항). 

      ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. 작업을 완료하면 **만들기**를 선택합니다. 
   
      Logic Apps는 연결을 설정하고 테스트하여 제대로 작동되는지 확인합니다.

4. 이제 SAP 서버에서 작업을 찾아 선택합니다. 

   1. **SAP 작업** 상자에서 폴더 아이콘을 선택합니다. 
   파일 목록에서 사용하려는 SAP 메시지를 찾아서 선택합니다. 
   목록을 탐색하려면 화살표를 사용합니다.

      이 예제에서는 **Order** 유형의 IDoc를 선택합니다. 

      ![IDoc 작업을 찾아서 선택합니다.](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      원하는 작업을 찾을 수 없는 경우 경로를 수동으로 입력할 수 있습니다. 예를 들면 다음과 같습니다.

      ![수동으로 IDoc 작업 경로 입력](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 식 편집기를 통해 SAP 작업의 값을 입력합니다. 이와 같은 방식으로 여러 메시지 유형에 동일한 작업을 사용할 수 있습니다.

      IDoc 작업에 대한 자세한 내용은 [IDOC 작업에 대한 메시지 스키마](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조하세요.

   2. **입력 메시지** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 
   해당 목록의 **HTTP 요청을 수신하는 경우** 아래에서 **본문** 필드를 선택합니다. 

      이 단계에서는 HTTP 요청 트리거의 본문 콘텐츠를 포함하며 해당 출력을 SAP 서버로 전송합니다.

      ![“본문” 필드 선택](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      여기까지 마쳤으면 SAP 작업이 다음 예제와 비슷하게 표시됩니다.

      ![SAP 작업 완료](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>HTTP 응답 작업 추가

이제 논리 앱의 워크플로에 응답 작업을 추가하고 SAP 작업의 출력을 포함합니다. 이렇게 하면 논리 앱이 SAP 서버의 결과를 원래 요청자에게 반환합니다. 

1. 논리 앱 디자이너에서 SAP 작업 아래의 **새 단계** > **작업 추가**를 선택합니다.

2. 검색 상자에 “응답”을 필터로 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **요청 - 응답**

3. **본문** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 해당 목록에서 **SAP로 보내기** 아래의 **본문** 필드를 선택합니다. 

   ![SAP 작업 완료](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. 논리 앱을 저장합니다. 

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱을 사용할 수 없는 경우 논리 앱 메뉴에서 **개요**를 선택합니다. 도구 모음에서 **사용**을 선택합니다. 

2. 논리 앱 디자이너 도구 모음에서 **실행**을 선택합니다. 이 단계에서는 논리 앱을 수동으로 시작합니다.

3. HTTP POST 요청을 HTTP 요청 트리거의 URL에 전송하여 논리 앱을 트리거하고 요청에 메시지 콘텐츠를 포함합니다. 요청을 보내려면 [Postman](https://www.getpostman.com/apps)과 같은 도구를 사용할 수 있습니다. 

   이 문서에서는 요청이 XML 형식으로, 사용 중인 SAP 작업에 대한 네임스페이스를 포함해야 하는 IDoc 파일을 보냅니다. 예를 들면 다음과 같습니다. 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. HTTP 요청을 보낸 후 논리 앱의 응답을 기다립니다.

   > [!NOTE]
   > 응답에 필요한 모든 단계가 [요청 시간 제한](./logic-apps-limits-and-config.md) 내에 완료되지 않을 경우 논리 앱이 시간 초과될 수 있습니다. 이 조건이 발생하면 요청이 차단될 수 있습니다. 문제를 진단하려면 [논리 앱을 확인하고 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)하는 방법을 알아보세요.

축하합니다. 이제 SAP 서버와 통신할 수 있는 논리 앱을 만들었습니다. 논리 앱에 대한 SAP 연결을 설정했으므로, BAPI 및 RFC와 같은 다른 사용 가능한 SAP 작업을 살펴볼 수 있습니다.

## <a name="receive-from-sap"></a>SAP에서 수신

이 예제에서는 SAP 시스템에서 메시지를 받을 때 트리거되는 논리 앱을 사용합니다. 

### <a name="add-sap-trigger"></a>SAP 트리거 추가

1. Azure Portal에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 

2. 검색 상자에서 필터로 "sap"를 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **SAP에서 메시지가 수신될 때**

   ![SAP 트리거 추가](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   또는 Enterprise 탭으로 이동하여 트리거를 선택합니다.

   ![ent 탭에서 SAP 트리거 추가](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. 연결 정보를 묻는 메시지가 표시되면 이제 SAP 연결을 만듭니다. 또는 이미 SAP에 연결된 경우, SAP 작업을 설정할 수 있도록 다음 단계를 계속 진행합니다. 

   **온-프레미스 SAP 연결 만들기**

   1. SAP 서버에 대한 연결 정보를 입력합니다. 
   **Data Gateway** 속성에서, 게이트웨이 설치를 위해 Azure Portal에서 만든 데이터 게이트웨이를 선택합니다.

      **Logon Type** 속성을 **Application Server**로 설정하면 다음 속성이 필요합니다(보통은 선택 사항).

      ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      **Logon Type** 속성을 **Group**으로 설정하면 다음 속성이 필요합니다(보통은 선택 사항).

      ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. SAP 시스템 구성에 따라 필요한 매개 변수를 제공합니다. 

   필요에 따라 하나 이상의 SAP 작업을 제공할 수 있습니다. 
   이 작업 목록은 트리거가 데이터 게이트웨이를 통해 SAP 서버에서 수신하는 메시지를 지정합니다. 
   빈 목록은 트리거가 모든 메시지를 수신하도록 지정합니다. 
   목록에 메시지가 두 개 이상 있는 경우 트리거는 목록에 지정된 메시지만 수신합니다. SAP 서버에서 보낸 그 외의 메시지는 게이트웨이에 의해 거부됩니다.

   파일 선택기에서 SAP 작업을 선택할 수 있습니다.

   ![SAP 작업 선택](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   또는 작업을 수동으로 지정할 수 있습니다.

   ![SAP 작업을 수동으로 입력](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   다음은 두 개 이상의 메시지를 수신하도록 트리거를 설정할 때 작업이 어떻게 나타나는지 보여주는 예제입니다.

   ![트리거 예제](media/logic-apps-using-sap-connector/example-trigger.png)  

   SAP 작업에 대한 자세한 내용은 [IDOC 작업에 대한 메시지 스키마](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조하세요.

5. 이제 SAP 시스템에서 메시지 수신을 시작할 수 있도록 논리 앱을 저장합니다.
디자이너 도구 모음에서 **저장**을 선택합니다. 

이제 논리 앱이 SAP 시스템에서 메시지를 수신할 준비가 완료되었습니다. 

> [!NOTE]
> SAP 트리거는 폴링 트리거가 아니라 웹후크 기반 트리거입니다. 이 트리거는 메시지가 있는 경우에만 게이트웨이에서 호출되므로 폴링이 필요 없습니다. 

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱을 트리거하기 위해, SAP 시스템에서 메시지를 보냅니다.

2. 논리 앱 메뉴에서 **개요**를 선택하고, 논리 앱에 대한 새 실행의 **실행 기록**을 검토합니다. 

3. 가장 최근 실행을 엽니다. 그러면 SAP 시스템에서 보낸 메시지가 트리거 출력 섹션에 표시됩니다.

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP에서 아티팩트에 대한 스키마 생성

이 예제에서는 HTTP 요청으로 트리거할 수 있는 논리 앱을 사용합니다. SAP 작업은 지정된 IDoc(Intermediate Document) 및 BAPI에 대한 스키마를 생성하라는 요청을 SAP 시스템에 보냅니다. 응답에서 반환되는 스키마는 Azure Resource Manager 커넥터를 사용하여 통합 계정에 업로드됩니다.

### <a name="add-http-request-trigger"></a>HTTP 요청 트리거 추가

1. Azure Portal에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 

2. 검색 상자에 “http 요청”을 필터로 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **요청 - HTTP 요청을 수신하는 경우**

   ![HTTP 요청 트리거 추가](./media/logic-apps-using-sap-connector/add-trigger.png)

3. 이제 논리 앱을 위한 엔드포인트 URL을 생성할 수 있도록 논리 앱을 저장합니다.
디자이너 도구 모음에서 **저장**을 선택합니다. 

   이제 엔드포인트 URL이 트리거에 표시됩니다. 예를 들면 다음과 같습니다.

   ![엔드포인트 URL 생성](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>스키마를 생성하는 SAP 작업 추가

1. 논리 앱 디자이너에서 해당 트리거 아래의 **새 단계** > **작업 추가**를 선택합니다.

   ![작업 추가](./media/logic-apps-using-sap-connector/add-action.png) 

2. 검색 상자에서 필터로 "sap"를 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **스키마 생성**
  
   ![SAP 보내기 작업 선택](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   또는 **Enterprise** 탭을 선택하고 SAP 작업을 선택할 수도 있습니다.

   ![Enterprise 탭에서 SAP 보내기 작업 선택](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. 연결 정보를 묻는 메시지가 표시되면 이제 SAP 연결을 만듭니다. 또는 이미 SAP에 연결된 경우, SAP 작업을 설정할 수 있도록 다음 단계를 계속 진행합니다. 

   **온-프레미스 SAP 연결 만들기**

   1. SAP 서버에 대한 연결 정보를 입력합니다. 
   **Data Gateway** 속성에서, 게이트웨이 설치를 위해 Azure Portal에서 만든 데이터 게이트웨이를 선택합니다.

      **Logon Type** 속성을 **Application Server**로 설정하면 다음 속성이 필요합니다(보통은 선택 사항).

      ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      **Logon Type** 속성을 **Group**으로 설정하면 다음 속성이 필요합니다(보통은 선택 사항).
   
      ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. 작업을 완료하면 **만들기**를 선택합니다. Logic Apps는 연결을 설정하고 테스트하여 제대로 작동되는지 확인합니다.

4. 스키마를 생성할 아티팩트의 경로를 입력합니다.

   파일 선택기에서 SAP 작업을 선택할 수 있습니다.

   ![SAP 작업 선택](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   또는 작업을 수동으로 입력할 수 있습니다.

   ![SAP 작업을 수동으로 입력](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   두 개 이상의 아티팩트에 대한 스키마를 생성하려면 다음과 같이 각 아티팩트에 대한 SAP 작업 세부 정보를 제공합니다.

   ![새 항목 추가 선택](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![두 개의 항목 표시](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   SAP 작업에 대한 자세한 내용은 [IDOC 작업에 대한 메시지 스키마](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조하세요.

5. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 디자이너 도구 모음에서 **실행**을 선택하여 논리 앱에 대한 실행을 트리거합니다.

2. 실행을 열고, **스키마 생성** 작업의 출력을 확인합니다. 

   출력은 지정된 메시지 목록에 대해 생성된 스키마를 보여줍니다.

### <a name="upload-schemas-to-integration-account"></a>통합 계정에 스키마 업로드

필요에 따라 생성된 스키마를 Blob, 저장소, 통합 계정 등의 리포지토리에 다운로드하거나 저장할 수 있습니다. 통합 계정은 다른 XML 작업을 통해 최고의 환경을 제공하므로, 이 예제에서는 Azure Resource Manager 커넥터를 사용하여 동일한 논리 앱의 통합 계정에 스키마를 업로드하는 방법을 보여줍니다.

1. 논리 앱 디자이너에서 해당 트리거 아래의 **새 단계** > **작업 추가**를 선택합니다. 검색 상자에서 "resource manager"를 필터로 입력합니다. 현재 선택한 작업: **리소스 만들기 또는 업데이트**

   ![Azure Resource Manager 작업 선택](media/logic-apps-using-sap-connector/select-arm-action.png) 

2. Azure 구독, Azure 리소스 그룹, 통합 계정을 포함한 세부 정보를 입력합니다. 다른 필드의 경우 아래 예제를 따릅니다.

   ![Azure Resource Manager 작업의 세부 정보 입력](media/logic-apps-using-sap-connector/arm-action.png)

   SAP **스키마 생성** 작업은 스키마를 컬렉션으로 생성하므로, 디자이너가 자동으로 **For each** 루프를 작업에 추가합니다. 
   다음은 이 작업이 어떻게 나타나는지 보여주는 예제입니다.

   !["for each" 루프를 사용하는 Azure Resource Manager 작업](media/logic-apps-using-sap-connector/arm-action-foreach.png)  

   > [!NOTE]
   > 스키마에는 base64 인코딩 형식이 사용됩니다. 통합 계정에 스키마를 업로드하려면 `base64ToString()` 함수를 사용하여 디코딩해야 합니다. 다음은 `"properties"` 요소의 코드를 보여주는 예제입니다.
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

3. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 디자이너 도구 모음에서 **실행**을 선택하여 논리 앱을 수동으로 트리거합니다.

2. 실행이 성공하면 통합 계정으로 이동하여 생성된 스키마가 있는지 확인합니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

다음은 SAP 커넥터에 대해 현재 알려진 문제와 제한 사항입니다.

* SAP 트리거가 SAP에서 일괄 처리 IDOC 수신을 지원하지 않습니다. 이 작업은 SAP 시스템과 데이터 게이트웨이 간의 RFC 연결 오류로 이어질 수 있습니다.

* SAP 트리거가 데이터 게이트웨이 클러스터를 지원하지 않습니다. 일부 장애 조치(failover)의 경우 SAP 시스템과 통신하는 데이터 게이트웨이 노드가 활성 노드와 달라서 예기치 않은 동작이 발생할 수 있습니다. 보내기 시나리오의 경우 데이터 게이트웨이 클러스터가 지원됩니다.

* 수신 시나리오에서는 null이 아닌 응답 반환이 지원되지 않습니다. 트리거 및 응답 작업을 사용하는 논리 앱이 예기치 않은 동작으로 이어집니다. 

* SAP로 보내기 호출 또는 메시지 중 오직 하나만 tRFC에서 작동합니다. BAPI(비즈니스 애플리케이션 프로그래밍 인터페이스) 커밋 패턴(예: 동일한 세션에서 여러 tRFC 호출 만들기)은 지원되지 않습니다.

* 첨부 파일이 포함된 RFC는 SAP로 보내기 및 스키마 생성 작업을 지원하지 않습니다.

* 현재 SAP 커넥터는 SAP 라우터 문자열을 지원하지 않습니다. 온-프레미스 데이터 게이트웨이가 연결하려는 SAP 시스템과 동일한 LAN에 있어야 합니다.

* DATS 및 TIMS SAP 필드에 대한 없는 값(null), 빈 값, 최솟값 및 최댓값 변환은 온-프레미스 데이터 게이트웨이의 이후 업데이트에서 변경될 수 있습니다.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 논리 앱에서 [온-프레미스 시스템에 연결](../logic-apps/logic-apps-gateway-connection.md)합니다.
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)을 사용하여 유효성 검사, 변환 및 기타 메시지 작업을 수행하는 방법을 알아봅니다.
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
