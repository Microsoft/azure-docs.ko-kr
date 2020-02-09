---
title: SAP 시스템에 연결
description: Azure Logic Apps로 워크플로를 자동화 하 여 SAP 리소스 액세스 및 관리
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 3990a9d53a8e53ddd29683440917031bc1bb6448
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904660"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps에서 SAP 시스템에 연결

> [!IMPORTANT]
> 이전 SAP 응용 프로그램 서버 및 SAP Message Server 커넥터는 2020 년 2 월 29 일에 사용이 중단 될 예정입니다. 현재 SAP 커넥터는 연결 형식을 변경할 필요가 없으며, 이전 커넥터와 완전히 호환 되며, 많은 추가 기능을 제공 하 고, SAP .Net 커넥터 라이브러리를 계속 사용 하도록 이전 SAP 커넥터를 통합 합니다. SAP NCo).
>
> 이전 커넥터를 사용 하는 논리 앱의 경우 사용 중단 날짜 전에 [최신 커넥터로 마이그레이션하십시오](#migrate) . 그렇지 않으면 이러한 논리 앱에서 실행 오류가 발생 하 여 SAP 시스템에 메시지를 보낼 수 없습니다.

이 문서에서는 SAP 커넥터를 사용 하 여 논리 앱 내에서 온-프레미스 SAP 리소스에 액세스할 수 있는 방법을 보여 줍니다. 커넥터는 온-프레미스에서 R/3 및 ECC 시스템과 같은 SAP의 클래식 릴리스와 함께 작동 합니다. 또한 커넥터를 사용 하면 온-프레미스 또는 클라우드에서 호스트 되는지에 관계 없이 S/4 HANA와 같은 SAP의 최신 HANA 기반 SAP 시스템과 통합할 수 있습니다. SAP 커넥터는 IDoc (중간 문서), BAPI (비즈니스 응용 프로그래밍 인터페이스) 또는 RFC (원격 함수 호출)를 통해 SAP NetWeaver 기반 시스템과의 메시지 또는 데이터 통합을 지원 합니다.

Sap 커넥터는 [NCo (sap .Net connector) 라이브러리](https://support.sap.com/en/product/connectors/msnet.html) 를 사용 하 여 다음과 같은 작업을 제공 합니다.

* **Sap로 메시지 보내기**: trfc를 통해 idoc를 보내거나, rfc를 통해 bapi 함수를 호출 하거나, sap 시스템에서 Rfc/trfc를 호출 합니다.
* **Sap에서 메시지를 받으면**trfc를 통해 idoc를 수신 하 고 trfc를 통해 bapi 함수를 호출 하거나 sap 시스템에서 RFC/trfc를 호출 합니다.
* **스키마 생성**: IDOC, bapi 또는 RFC에 대 한 SAP 아티팩트에 대 한 스키마를 생성 합니다.

이러한 작업의 경우 SAP 커넥터는 사용자 이름 및 암호를 통한 기본 인증을 지원 합니다. 커넥터는 [SNC (보안 네트워크 통신)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)도 지원 합니다. SNC는 SAP NetWeaver Single Sign-On (SSO) 또는 외부 보안 제품에서 제공 하는 추가 보안 기능에 사용할 수 있습니다.

SAP 커넥터는 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 통해 온-프레미스 SAP 시스템과 통합됩니다. 예를 들어 보내기 시나리오에서 논리 앱에서 SAP 시스템으로 메시지를 보내는 경우 데이터 게이트웨이는 RFC 클라이언트 역할을 하 고 논리 앱에서 받은 요청을 SAP로 전달 합니다. 마찬가지로 수신 시나리오에서 데이터 게이트웨이는 SAP에서 요청을 수신 하 고 논리 앱에 전달 하는 RFC 서버 역할을 합니다.

이 문서에서는 앞에서 설명한 통합 시나리오를 다루면서 SAP와 통합되는 논리 앱 예제를 만드는 방법을 보여줍니다. 이전 SAP 커넥터를 사용 하는 논리 앱의 경우이 문서에서는 논리 앱을 최신 SAP 커넥터로 마이그레이션하는 방법을 보여 줍니다.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>필수 조건

이 문서를 따르려면 다음 항목이 필요합니다.

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* SAP 시스템에 액세스하려는 논리 앱과 논리 앱의 워크플로를 시작하는 트리거. 논리 앱을 처음 접하는 경우 [Azure Logic Apps?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조 하세요.

* [Sap 응용 프로그램 서버](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) 또는 [sap 메시지 서버](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* 온-프레미스 컴퓨터에서 최신 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127)를 다운로드하여 설치합니다. 계속 진행하기 전에 Azure Portal에서 게이트웨이를 설정하세요. 게이트웨이를 사용 하면 온-프레미스 데이터 및 리소스에 안전 하 게 액세스할 수 있습니다. 자세한 내용은 [Azure Logic Apps에 대 한 온-프레미스 데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md)를 참조 하세요.

* SSO와 함께 SNC를 사용 하는 경우 게이트웨이가 SAP 사용자에 대해 매핑된 사용자로 실행 되 고 있는지 확인 합니다. 기본 계정을 변경 하려면 **계정 변경**을 선택 하 고 사용자 자격 증명을 입력 합니다.

  ![게이트웨이 계정 변경](./media/logic-apps-using-sap-connector/gateway-account.png)

* 외부 보안 제품으로 SNC를 사용 하도록 설정한 경우에는 게이트웨이가 설치 된 컴퓨터와 동일한 컴퓨터에 SNC 라이브러리를 복사 합니다. SNC 제품의 예로는 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS 및 NTLM이 있습니다.

* 온-프레미스 데이터 게이트웨이와 동일한 컴퓨터에 [.NET Framework 4.0-Windows 64-bit (x64)를 사용 하 여 컴파일된 Microsoft .NET 3.0.22.0에 대 한](https://softwaredownloads.sap.com/file/0020000001000932019)최신 sap 클라이언트 라이브러리 3.0를 다운로드 하 여 설치 합니다. 이 버전 이상을 설치해야 하는 이유는 다음과 같습니다.

  * 이전 SAP NCo 버전은 동시에 두 개 이상의 IDoc 메시지가 전송 될 때 교착 상태가 될 수 있습니다. 이 조건은 SAP 대상으로 전송 되는 이후의 모든 메시지를 차단 하므로 메시지의 시간 제한이 초과 됩니다.
  
  * 온-프레미스 데이터 게이트웨이는 64비트 시스템에서만 실행됩니다. 64비트 시스템이 아니면 데이터 게이트웨이 호스트 서비스가 32비트 어셈블리를 지원하지 않기 때문에 “잘못된 이미지” 오류가 표시됩니다.
  
  * 데이터 게이트웨이 호스트 서비스와 Microsoft SAP Adapter는 둘 다 .NET Framework 4.5를 사용합니다. .NET Framework 4.0용 SAP NCo는 .NET 런타임 4.0 - 4.7.1을 사용하는 프로세스에서 작동합니다. .NET Framework 2.0에 대 한 SAP NCo는 .NET runtime 2.0을 3.5에 사용 하는 프로세스와 함께 작동 하지만 더 이상 최신 온-프레미스 데이터 게이트웨이와 작동 하지 않습니다.

* 샘플 IDoc 파일과 같이 SAP 서버에 보낼 수 있는 메시지 콘텐츠는 XML 형식 이어야 하며 사용 하려는 SAP 작업에 대 한 네임 스페이스를 포함 해야 합니다.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>현재 커넥터로 마이그레이션

1. 아직 수행 하지 않은 경우 최신 버전을 사용할 수 있도록 [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127) 를 업데이트 합니다. 자세한 내용은 [Azure Logic Apps에 대 한 온-프레미스 데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md)를 참조 하세요.

1. 이전 SAP 커넥터를 사용 하는 논리 앱에서 **sap로 보내기** 작업을 삭제 합니다.

1. 최신 SAP 커넥터에서 **sap로 메시지 보내기** 작업을 추가 합니다. 이 작업을 사용 하려면 먼저 SAP 시스템에 대 한 연결을 다시 만듭니다.

1. 완료되면 논리 앱을 저장합니다.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>SAP로 메시지 보내기

이 예제에서는 HTTP 요청으로 트리거할 수 있는 논리 앱을 사용합니다. 논리 앱은 IDoc를 SAP 서버에 보내고 논리 앱을 호출한 요청자에 게 응답을 반환 합니다.

### <a name="add-an-http-request-trigger"></a>HTTP 요청 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 실행될 때마다 Logic Apps 엔진에서 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행하기 시작합니다.

이 예제에서는 논리 앱에 *HTTP POST 요청*을 보낼 수 있도록 Azure의 엔드포인트를 사용하여 논리 앱을 만듭니다. 논리 앱이 이러한 HTTP 요청을 받으면 트리거가 실행되고 워크플로의 다음 단계를 실행합니다.

1. [Azure Portal](https://portal.azure.com)에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다.

1. 검색 상자에서 필터로 `http request`을 입력합니다. **트리거** 목록에서 **HTTP 요청을 받을 때**를 선택 합니다.

   ![HTTP 요청 트리거 추가](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 이제 논리 앱에 대 한 엔드포인트 URL을 생성할 수 있도록 논리 앱을 저장 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   이제 엔드포인트 URL이 트리거에 표시됩니다. 예를 들면 다음과 같습니다.

   ![엔드포인트 URL 생성](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>SAP 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 논리 앱에 트리거를 아직 추가하지 않은 경우 이 예제를 따르려면 [이 섹션에 설명된 트리거를 추가](#add-trigger)합니다.

1. 논리 앱 디자이너의 트리거 아래에서 **새 단계**를 선택 합니다.

   ![논리 앱에 새 단계 추가](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 검색 상자에서 필터로 `sap`을 입력합니다. **작업** 목록에서 **SAP로 메시지 보내기**를 선택 합니다.
  
   !["SAP로 메시지 보내기" 작업을 선택 합니다.](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   또는 **Enterprise** 탭을 선택 하 고 SAP 작업을 선택할 수 있습니다.

   ![Enterprise 탭에서 "SAP로 메시지 보내기"를 선택 합니다.](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 연결이 이미 있는 경우 다음 단계를 계속 진행 하 여 SAP 작업을 설정할 수 있습니다. 그러나 연결 정보를 입력 하 라는 메시지가 표시 되 면 온-프레미스 SAP 서버에 대 한 연결을 만들 수 있도록 정보를 제공 합니다.

   1. 연결에 대 한 이름을 제공 합니다.

   1. **데이터 게이트웨이** 섹션의 **구독**에서 먼저 게이트웨이 설치에 대 한 Azure Portal에서 만든 게이트웨이 리소스에 대 한 Azure 구독을 선택 합니다. 
   
   1. **연결 게이트웨이**에서 게이트웨이 리소스를 선택 합니다.

   1. 연결에 대 한 정보를 계속 제공 합니다. **Logon 유형** 속성의 경우 속성이 **응용 프로그램 서버** 또는 **그룹**으로 설정 되었는지 여부에 따라 단계를 수행 합니다.
   
      * **응용 프로그램 서버의**경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹**의 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. [안전 입력 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 완료 되 면 **만들기**를 선택 합니다.

      Logic Apps는 연결을 설정 하 고 테스트 하 여 연결이 제대로 작동 하는지 확인 합니다.

1. 이제 SAP 서버에서 작업을 찾아 선택합니다.

   1. **SAP 작업** 상자에서 폴더 아이콘을 선택 합니다. 파일 목록에서 사용하려는 SAP 메시지를 찾아서 선택합니다. 목록을 탐색하려면 화살표를 사용합니다.

      이 예에서는 **Orders** 형식이 포함 된 idoc를 선택 합니다.

      ![IDoc 작업을 찾아서 선택합니다.](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      원하는 작업을 찾을 수 없는 경우 경로를 수동으로 입력할 수 있습니다. 예를 들면 다음과 같습니다.

      ![수동으로 IDoc 작업 경로 입력](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 식 편집기를 통해 **SAP 동작** 에 대 한 값을 제공 합니다. 이렇게 하면 여러 메시지 유형에 동일한 작업을 사용할 수 있습니다.

      IDoc 작업에 대한 자세한 내용은 [IDOC 작업에 대한 메시지 스키마](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조하세요.

   1. **입력 메시지** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 해당 목록의 **HTTP 요청을 수신하는 경우** 아래에서 **본문** 필드를 선택합니다.

      이 단계에서는 HTTP 요청 트리거의 본문 콘텐츠를 포함 하 고 해당 출력을 SAP 서버에 보냅니다.

      ![트리거에서 "Body" 속성을 선택 합니다.](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      완료 되 면 SAP 작업은 다음 예제와 같습니다.

      ![SAP 작업 완료](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>HTTP 응답 작업 추가

이제 논리 앱의 워크플로에 응답 작업을 추가하고 SAP 작업의 출력을 포함합니다. 이렇게 하면 논리 앱이 SAP 서버의 결과를 원래 요청자에게 반환합니다.

1. 논리 앱 디자이너의 SAP 작업에서 **새 단계**를 선택 합니다.

1. 검색 상자에서 필터로 `response`을 입력합니다. **작업** 목록에서 **응답**을 선택 합니다.

1. **본문** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 이 목록에서 **SAP로 메시지 보내기**에서 **본문** 필드를 선택 합니다.

   ![SAP 작업 완료](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 논리 앱을 저장합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱을 아직 사용 하도록 설정 하지 않은 경우 논리 앱 메뉴에서 **개요**를 선택 합니다. 도구 모음에서 **사용**을 선택 합니다.

1. 디자이너 도구 모음에서 **실행**을 선택 합니다. 이 단계에서는 논리 앱을 수동으로 시작합니다.

1. HTTP 요청 트리거의 URL에 HTTP POST 요청을 전송 하 여 논리 앱을 트리거합니다.
요청에 메시지 콘텐츠를 포함 합니다. 요청을 보내려면 [Postman](https://www.getpostman.com/apps)과 같은 도구를 사용할 수 있습니다.

   이 문서에서는 요청이 XML 형식으로, 사용 중인 SAP 작업에 대한 네임스페이스를 포함해야 하는 IDoc 파일을 보냅니다. 예를 들면 다음과 같습니다.

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. HTTP 요청을 보낸 후 논리 앱의 응답을 기다립니다.

   > [!NOTE]
   > 응답에 필요한 모든 단계가 [요청 시간 제한](./logic-apps-limits-and-config.md) 내에 완료되지 않을 경우 논리 앱이 시간 초과될 수 있습니다. 이 조건이 발생하면 요청이 차단될 수 있습니다. 문제를 진단하려면 [논리 앱을 확인하고 모니터링](../logic-apps/monitor-logic-apps.md)하는 방법을 알아보세요.

이제 SAP 서버와 통신할 수 있는 논리 앱을 만들었습니다. 논리 앱에 대한 SAP 연결을 설정했으므로, BAPI 및 RFC와 같은 다른 사용 가능한 SAP 작업을 살펴볼 수 있습니다.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>SAP에서 메시지 받기

이 예제에서는 앱이 SAP 시스템에서 메시지를 받을 때 트리거하는 논리 앱을 사용 합니다.

### <a name="add-an-sap-trigger"></a>SAP 트리거 추가

1. Azure Portal에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다.

1. 검색 상자에서 필터로 `sap`을 입력합니다. **트리거** 목록에서 **SAP에서 메시지를 받을 때**를 선택 합니다.

   ![SAP 트리거 추가](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   또는 **Enterprise** 탭을 선택한 다음 트리거를 선택할 수 있습니다.

   ![Enterprise 탭에서 SAP 트리거 추가](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 연결이 이미 있는 경우 다음 단계를 계속 진행 하 여 SAP 작업을 설정할 수 있습니다. 그러나 연결 정보를 입력 하 라는 메시지가 표시 되 면 온-프레미스 SAP 서버에 대 한 연결을 만들 수 있도록 정보를 제공 합니다.

   1. 연결에 대 한 이름을 제공 합니다.

   1. **데이터 게이트웨이** 섹션의 **구독**에서 먼저 게이트웨이 설치에 대 한 Azure Portal에서 만든 게이트웨이 리소스에 대 한 Azure 구독을 선택 합니다. 

   1. **연결 게이트웨이**에서 게이트웨이 리소스를 선택 합니다.

   1. 연결에 대 한 정보를 계속 제공 합니다. **Logon 유형** 속성의 경우 속성이 **응용 프로그램 서버** 또는 **그룹**으로 설정 되었는지 여부에 따라 단계를 수행 합니다.

      * **응용 프로그램 서버의**경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹**의 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. [안전 입력 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 완료 되 면 **만들기**를 선택 합니다.

      Logic Apps는 연결을 설정 하 고 테스트 하 여 연결이 제대로 작동 하는지 확인 합니다.

1. SAP 시스템 구성에 따라 필요한 매개 변수를 제공합니다.

   필요에 따라 하나 이상의 SAP 작업을 제공할 수 있습니다. 이 작업 목록은 트리거가 데이터 게이트웨이를 통해 SAP 서버에서 수신하는 메시지를 지정합니다. 빈 목록은 트리거가 모든 메시지를 수신하도록 지정합니다. 목록에 메시지가 두 개 이상 있는 경우 트리거는 목록에 지정된 메시지만 수신합니다. SAP 서버에서 보낸 그 외의 메시지는 게이트웨이에 의해 거부됩니다.

   파일 선택기에서 SAP 작업을 선택할 수 있습니다.

   ![논리 앱에 SAP 작업 추가](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   또는 작업을 수동으로 지정할 수 있습니다.

   ![SAP 작업을 수동으로 입력](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   다음은 두 개 이상의 메시지를 수신하도록 트리거를 설정할 때 작업이 어떻게 나타나는지 보여주는 예제입니다.

   ![여러 메시지를 받는 트리거 예제](media/logic-apps-using-sap-connector/example-trigger.png)

   SAP 작업에 대한 자세한 내용은 [IDOC 작업에 대한 메시지 스키마](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조하세요.

1. 이제 SAP 시스템에서 메시지 수신을 시작할 수 있도록 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제 논리 앱이 SAP 시스템에서 메시지를 수신할 준비가 완료되었습니다.

> [!NOTE]
> SAP 트리거는 폴링 트리거가 아니라 webhook 기반 트리거입니다. 이 트리거는 메시지가 있는 경우에만 게이트웨이에서 호출되므로 폴링이 필요 없습니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱을 트리거하기 위해, SAP 시스템에서 메시지를 보냅니다.

1. 논리 앱 메뉴에서 **개요**를 선택 합니다. 논리 앱에 대 한 모든 새 실행에 대 한 **실행 기록을** 검토 합니다.

1. 가장 최근 실행을 엽니다. 그러면 SAP 시스템에서 보낸 메시지가 트리거 출력 섹션에 표시됩니다.

## <a name="receive-idoc-packets-from-sap"></a>SAP에서 IDOC 패킷 수신

SAP를 설정 하 여 일괄 처리 또는 IDOCs 그룹인 [패킷으로 IDOCs를 보낼](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)수 있습니다. IDOC 패킷, SAP 커넥터 및 특히 트리거를 수신 하려면 추가 구성이 필요 하지 않습니다. 그러나 트리거가 패킷을 받은 후 IDOC 패킷에서 각 항목을 처리 하려면 패킷을 개별 Idoc로 분할 하기 위해 몇 가지 추가 단계가 필요 합니다.

[`xpath()` 함수](./workflow-definition-language-functions-reference.md#xpath)를 사용 하 여 패킷에서 개별 idocs를 추출 하는 방법을 보여 주는 예제는 다음과 같습니다.

1. 시작 하기 전에 SAP 트리거를 사용 하는 논리 앱이 필요 합니다. 이 논리 앱이 아직 없는 경우이 항목의 이전 단계를 수행 하 여 [SAP 트리거를 사용 하 여 논리 앱을 설정](#receive-from-sap)합니다.

   예:

   ![논리 앱에 SAP 트리거 추가](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. 논리 앱이 SAP에서 받는 XML IDOC에서 루트 네임 스페이스를 가져옵니다. XML 문서에서이 네임 스페이스를 추출 하려면 로컬 문자열 변수를 만들고 `xpath()` 식을 사용 하 여 해당 네임 스페이스를 저장 하는 단계를 추가 합니다.

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![IDOC에서 루트 네임 스페이스 가져오기](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 개별 IDOC를 추출 하려면 배열 변수를 만들고 다른 `xpath()` expression을 사용 하 여 IDOC 컬렉션을 저장 하는 단계를 추가 합니다.

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![항목의 배열 가져오기](./media/logic-apps-using-sap-connector/get-array.png)

   배열 변수를 사용 하면 논리 앱에서 각 IDOC를 컬렉션을 열거 하 여 개별적으로 처리할 수 있습니다. 이 예제에서 논리 앱은 루프를 사용 하 여 각 IDOC를 SFTP 서버로 전송 합니다.

   ![SFTP 서버에 IDOC 보내기](./media/logic-apps-using-sap-connector/loop-batch.png)

   각 IDOC는 루트 네임 스페이스를 포함 해야 합니다 .이는 IDOC를 다운스트림 앱 이나이 경우 SFTP 서버에 보내기 전에 루트 네임 스페이스와 함께 `<Receive></Receive` 요소 내에 파일 콘텐츠를 래핑하는 이유입니다.

새 논리 앱을 만들 때 논리 앱 디자이너에서이 템플릿을 선택 하 여이 패턴에 대 한 빠른 시작 템플릿을 사용할 수 있습니다.

![Batch 논리 앱 템플릿 선택](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP에서 아티팩트에 대한 스키마 생성

이 예제에서는 HTTP 요청으로 트리거할 수 있는 논리 앱을 사용합니다. SAP 작업은 SAP 시스템에 지정 된 IDoc 및 BAPI에 대 한 스키마를 생성 하는 요청을 보냅니다. 응답에서 반환 되는 스키마는 Azure Resource Manager 커넥터를 사용 하 여 통합 계정에 업로드 됩니다.

### <a name="add-an-http-request-trigger"></a>HTTP 요청 트리거 추가

1. Azure Portal에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다.

1. 검색 상자에서 필터로 `http request`을 입력합니다. **트리거** 목록에서 **HTTP 요청을 받을 때**를 선택 합니다.

   ![HTTP 요청 트리거 추가](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 이제 논리 앱을 위한 엔드포인트 URL을 생성할 수 있도록 논리 앱을 저장합니다.
디자이너 도구 모음에서 **저장**을 선택합니다.

   이제 엔드포인트 URL이 트리거에 표시됩니다. 예를 들면 다음과 같습니다.

   ![엔드포인트 URL 생성](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>SAP 작업을 추가 하 여 스키마 생성

1. 논리 앱 디자이너의 트리거 아래에서 **새 단계**를 선택 합니다.

   ![논리 앱에 새 단계 추가](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 검색 상자에서 필터로 `sap`을 입력합니다. **작업** 목록에서 **스키마 생성**을 선택 합니다.
  
   ![논리 앱에 "스키마 생성" 작업 추가](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   또는 **Enterprise** 탭을 선택 하 고 SAP 작업을 선택할 수 있습니다.

   ![Enterprise 탭에서 SAP 보내기 작업 선택](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 연결이 이미 있는 경우 다음 단계를 계속 진행 하 여 SAP 작업을 설정할 수 있습니다. 그러나 연결 정보를 입력 하 라는 메시지가 표시 되 면 온-프레미스 SAP 서버에 대 한 연결을 만들 수 있도록 정보를 제공 합니다.

   1. 연결에 대 한 이름을 제공 합니다.

   1. **데이터 게이트웨이** 섹션의 **구독**에서 먼저 게이트웨이 설치에 대 한 Azure Portal에서 만든 게이트웨이 리소스에 대 한 Azure 구독을 선택 합니다. 
   
   1. **연결 게이트웨이**에서 게이트웨이 리소스를 선택 합니다.

   1. 연결에 대 한 정보를 계속 제공 합니다. **Logon 유형** 속성의 경우 속성이 **응용 프로그램 서버** 또는 **그룹**으로 설정 되었는지 여부에 따라 단계를 수행 합니다.
   
      * **응용 프로그램 서버의**경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹**의 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. [안전 입력 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 완료 되 면 **만들기**를 선택 합니다.

      Logic Apps는 연결을 설정 하 고 테스트 하 여 연결이 제대로 작동 하는지 확인 합니다.

1. 스키마를 생성할 아티팩트의 경로를 입력합니다.

   파일 선택기에서 SAP 작업을 선택할 수 있습니다.

   ![SAP 작업 선택](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   또는 작업을 수동으로 입력할 수 있습니다.

   ![SAP 작업을 수동으로 입력](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   두 개 이상의 아티팩트에 대한 스키마를 생성하려면 다음과 같이 각 아티팩트에 대한 SAP 작업 세부 정보를 제공합니다.

   ![새 항목 추가 선택](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![두 개의 항목 표시](media/logic-apps-using-sap-connector/schema-generator-example.png)

   SAP 동작에 대 한 자세한 내용은 [IDOC 작업에 대 한 메시지 스키마](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조 하세요.

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 디자이너 도구 모음에서 **실행** 을 선택 하 여 논리 앱에 대 한 실행을 트리거합니다.

1. 실행을 열고 **스키마 생성** 작업의 출력을 확인 합니다.

   출력은 지정된 메시지 목록에 대해 생성된 스키마를 보여줍니다.

### <a name="upload-schemas-to-an-integration-account"></a>통합 계정에 스키마 업로드

필요에 따라 생성된 스키마를 Blob, 스토리지, 통합 계정 등의 리포지토리에 다운로드하거나 저장할 수 있습니다. 통합 계정은 다른 XML 작업을 통해 최고의 환경을 제공하므로, 이 예제에서는 Azure Resource Manager 커넥터를 사용하여 동일한 논리 앱의 통합 계정에 스키마를 업로드하는 방법을 보여줍니다.

1. 논리 앱 디자이너의 트리거 아래에서 **새 단계**를 선택 합니다.

1. 검색 상자에서 필터로 `Resource Manager`을 입력합니다. **리소스 만들기 또는 업데이트를**선택 합니다.

   ![Azure Resource Manager 작업 선택](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Azure 구독, Azure 리소스 그룹 및 통합 계정을 포함 하 여 작업에 대 한 세부 정보를 입력 합니다. 필드에 SAP 토큰을 추가 하려면 해당 필드의 상자 내부를 클릭 하 고 나타나는 동적 콘텐츠 목록에서 선택 합니다.

   1. **새 매개 변수 추가** 목록을 열고 **위치** 및 **속성** 필드를 선택 합니다.

   1. 이 예에 표시 된 대로 이러한 새 필드에 대 한 세부 정보를 제공 합니다.

      ![Azure Resource Manager 작업의 세부 정보 입력](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP **스키마 생성** 작업은 스키마를 컬렉션으로 생성하므로, 디자이너가 자동으로 **For each** 루프를 작업에 추가합니다. 다음은 이 작업이 어떻게 나타나는지 보여주는 예제입니다.

   !["for each" 루프를 사용하는 Azure Resource Manager 작업](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

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

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 디자이너 도구 모음에서 **실행** 을 선택 하 여 논리 앱을 수동으로 트리거합니다.

1. 성공적으로 실행 된 후 통합 계정으로 이동 하 여 생성 된 스키마가 있는지 확인 합니다.

## <a name="enable-secure-network-communications"></a>보안 네트워크 통신 사용

시작 하기 전에 이전에 나열 된 [필수 구성 요소](#pre-reqs)를 충족 하는지 확인 합니다.

* 온-프레미스 데이터 게이트웨이는 SAP 시스템과 동일한 네트워크에 있는 컴퓨터에 설치 됩니다.
* SSO의 경우 게이트웨이는 SAP 사용자에 매핑된 사용자로 실행 됩니다.
* 추가 보안 기능을 제공 하는 SNC 라이브러리는 데이터 게이트웨이와 동일한 컴퓨터에 설치 됩니다. 몇 가지 예로는 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS 및 NTLM이 있습니다.

   SAP 시스템에 대 한 요청에 대해 SNC를 사용 하도록 설정 하려면 SAP 연결에서 **Snc 사용** 확인란을 선택 하 고 다음 속성을 제공 합니다.

   ![연결에서 SAP SNC 구성](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 속성 | Description |
   |----------| ------------|
   | **SNC 라이브러리 경로** | NCo 설치 위치나 절대 경로를 기준으로 하는 SNC 라이브러리 이름 또는 경로입니다. 예는 `sapsnc.dll` 또는 `.\security\sapsnc.dll` 또는 `c:\security\sapsnc.dll`입니다. |
   | **SNC SSO** | SNC를 통해 연결 하는 경우 SNC id는 일반적으로 호출자를 인증 하는 데 사용 됩니다. 또 다른 옵션은를 재정의 하 여 호출자를 인증 하는 데 사용자 및 암호 정보를 사용할 수 있지만 줄은 여전히 암호화 되도록 하는 것입니다. |
   | **SNC 내 이름** | 대부분의 경우이 속성은 생략할 수 있습니다. 설치 된 SNC 솔루션은 일반적으로 자체 SNC 이름을 알고 있습니다. 여러 id를 지 원하는 솔루션에 대해서만이 특정 대상 또는 서버에 사용할 id를 지정 해야 할 수 있습니다. |
   | **SNC 파트너 이름** | 백 엔드 SNC의 이름입니다. |
   | **SNC 보호 품질** | 이 특정 대상 또는 서버의 SNC 통신에 사용할 서비스의 품질입니다. 기본값은 백 엔드 시스템에 의해 정의 됩니다. 최 댓 값은 SNC에 사용 되는 보안 제품에 의해 정의 됩니다. |
   |||

   > [!NOTE]
   > 환경 변수 SNC_LIB 설정 하지 말고 데이터 게이트웨이와 SNC 라이브러리가 있는 컴퓨터에서 SNC_LIB_64 합니다. 설정 되 면 커넥터를 통해 전달 되는 SNC 라이브러리 값 보다 우선적으로 적용 됩니다.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>안전 입력

기본적으로 SAP 연결을 만들 때 강력한 형식화는 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. **안전**형식 지정을 선택 하는 경우 SAP에서 DATS TYPE 및는 해당 `xs:date` XML이 아닌 문자열로 처리 됩니다. 여기서 `xmlns:xs="http://www.w3.org/2001/XMLSchema"`는 `xs:time`. 안전 형식 지정은 모든 스키마 생성에 대 한 동작, "보낸 사람" 페이로드에 대 한 전송 메시지, "received" 응답 및 트리거 모두에 영향을 줍니다. 

강력한 형식화를 사용 하는 경우 (**안전 입력** 은 사용 하지 않도록 설정 됨) 스키마는 DATS 및 대상 s 형식을 보다 간단한 XML 형식으로 매핑합니다.

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

강력한 형식을 사용 하 여 메시지를 전송 하는 경우 DATS 및의 응답은 일치 하는 XML 형식 형식을 준수 합니다.

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

**안전 입력** 을 사용 하도록 설정 하면 스키마는 DATS 및 대상 s 유형을 길이가 제한 된 XML 문자열 필드에 매핑합니다. 예를 들면 다음과 같습니다.

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

**안전 입력** 을 사용 하도록 설정 하 여 메시지를 보낼 때 DATS 및의 응답은 다음 예제와 같습니다.

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="confirm-transaction-explicitly"></a>명시적으로 트랜잭션 확인

Logic Apps에서 SAP로 트랜잭션을 보낼 때이 exchange는 SAP 문서 [트랜잭션 RFC 서버 프로그램](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)에 설명 된 대로 두 단계로 이루어집니다. 기본적으로 **SAP로 보내기** 작업은 한 번의 호출로 함수 전송 및 트랜잭션 확인에 대 한 단계를 모두 처리 합니다. SAP 커넥터는 이러한 단계를 분리 하는 옵션을 제공 합니다. 자동으로 트랜잭션을 확인 하는 대신 IDOC를 보낼 수 있으며, 명시적 **트랜잭션 ID 확인** 작업을 사용할 수 있습니다.

트랜잭션 ID 확인을 분리 하는이 기능은 네트워크 문제와 같은 원인으로 인해 오류가 발생할 수 있는 시나리오와 같이 SAP에서 트랜잭션을 복제 하지 않으려는 경우에 유용 합니다. 트랜잭션 ID를 별도로 확인 하 여 트랜잭션은 SAP 시스템에서 한 번만 완료 됩니다.

이 패턴을 보여 주는 예제는 다음과 같습니다.

1. 빈 논리 앱을 만들고 HTTP 트리거를 추가 합니다.

1. SAP 커넥터에서 **SEND IDOC** 동작을 추가 합니다. SAP 시스템에 보내는 IDOC에 대 한 세부 정보를 제공 합니다.

1. 별도의 단계에서 트랜잭션 ID를 명시적으로 확인 하려면 **TID 확인** 필드에서 **아니요**를 선택 합니다. 선택적 **트랜잭션 ID GUID** 필드의 경우 값을 수동으로 지정 하거나 커넥터에서이 guid를 자동으로 생성 하 여 SEND idoc 동작의 응답에 반환 하도록 할 수 있습니다.

   ![IDOC 동작 속성 보내기](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 트랜잭션 ID를 명시적으로 확인 하려면 **트랜잭션 Id 확인** 작업을 추가 합니다. 동적 콘텐츠 목록이 표시 되도록 **트랜잭션 ID** 상자 내부를 클릭 합니다. 해당 목록에서 **SEND IDOC** 동작에서 반환 된 **트랜잭션 ID** 값을 선택 합니다.

   ![트랜잭션 ID 작업 확인](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   이 단계가 실행 된 후에는 SAP 커넥터 쪽 및 SAP 시스템 쪽에서 현재 트랜잭션이 완료로 표시 됩니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

다음은 SAP 커넥터에 대해 현재 알려진 문제와 제한 사항입니다.

* SAP 트리거가 데이터 게이트웨이 클러스터를 지원하지 않습니다. 일부 장애 조치 (failover)의 경우 SAP 시스템과 통신 하는 데이터 게이트웨이 노드가 활성 노드와 다를 수 있으며이로 인해 예기치 않은 동작이 발생할 수 있습니다. 보내기 시나리오의 경우 데이터 게이트웨이 클러스터가 지원 됩니다.

* 현재 SAP 커넥터는 SAP 라우터 문자열을 지원하지 않습니다. 온-프레미스 데이터 게이트웨이가 연결하려는 SAP 시스템과 동일한 LAN에 있어야 합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI (이전의 Swagger) 설명에서 설명 하는 트리거, 작업 및 제한에 대 한 자세한 기술 정보는 [커넥터의 참조 페이지를 참조](/connectors/sap/)하세요.

## <a name="next-steps"></a>다음 단계

* Azure Logic Apps에서 온 [-프레미스 시스템에 연결](../logic-apps/logic-apps-gateway-connection.md) 합니다.
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)를 사용 하 여 다른 메시지 작업의 유효성을 검사 하 고 변환 하 고 사용 하는 방법에 대해 알아봅니다.
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
