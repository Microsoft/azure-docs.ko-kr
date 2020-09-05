---
title: Azure Logic Apps용 커넥터
description: 기본 제공, 관리, 온-프레미스, 통합 계정, ISE 및 엔터프라이즈 커넥터와 같은 Azure Logic Apps 커넥터를 사용 하 여 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 1bd3cb1c18d1bac078ac1344f574914dba73d07b
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871567"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps용 커넥터

커넥터를 사용하면 Azure Logic Apps에서 다른 앱, 서비스, 시스템, 프로토콜 및 플랫폼에 걸쳐 이벤트, 데이터 및 작업에 빠르게 액세스할 수 있습니다. 이미 만들어 사용하고 있는 데이터로 작업을 수행할 수 있도록, 논리 앱에서 커넥터를 사용하여 클라우드 및 온-프레미스 앱에 대한 기능을 확장합니다.

Logic Apps는 [수백 개의 커넥터](/connectors)를 제공 하지만,이 문서에서는 수천 개의 앱에서 성공적으로 사용 되는 자주 *사용* 되는 커넥터와 데이터 및 정보를 처리 하는 수백만 개의 실행을 설명 합니다. 커넥터의 전체 목록과 각 커넥터의 참조 정보 (예: 트리거, 작업 및 제한)를 찾으려면 커넥터 [개요](/connectors)에서 커넥터 참조 페이지를 검토 합니다. 또한 [트리거 및 작업](#triggers-actions), [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)및 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)에 대해 자세히 알아보세요.

> [!TIP]
> 커넥터가 없는 서비스 또는 API와 통합 하려면 HTTP와 같은 프로토콜을 통해 서비스를 직접 호출 하거나 [사용자 지정 커넥터](#custom)를 만들 수 있습니다.

## <a name="connector-types"></a>커넥터 유형

커넥터는 기본 제공 트리거 및 작업으로 사용 하거나 관리 되는 커넥터로 사용할 수 있습니다.

<a name="built-in"></a>

* [**기본**](#built-ins)제공: 기본 제공 트리거 및 작업은 Azure Logic Apps 하는 "기본" 이며 논리 앱에 대해 이러한 작업을 수행 하는 데 도움이 됩니다.

  * 사용자 지정 및 고급 일정에서 실행 합니다.

  * 루프 및 조건과 같은 논리 앱의 워크플로를 구성 하 고 제어 하며 변수와 데이터 작업을 수행 합니다.

  * 다른 끝점과 통신 합니다.

  * 요청을 받고 응답 합니다.

  * Azure 함수, azure API Apps (Web Apps), Azure API Management를 사용 하 여 관리 하 고 게시 한 자체 Api, 요청을 받을 수 있는 중첩 된 논리 앱을 호출 합니다.

<a name="managed-connectors"></a>

* [**관리 되는 커넥터**](#managed-api-connectors): Microsoft에서 배포 및 관리 하는 이러한 커넥터는 Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint 등을 포함 하 여 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 액세스 하기 위한 트리거와 작업을 제공 합니다. 일부 커넥터는 B2B (기업 간) 통신 시나리오를 구체적으로 지원 하 고 논리 앱에 연결 된 [통합 계정이](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 필요 합니다. 특정 커넥터를 사용 하기 전에 먼저 Azure Logic Apps에서 관리 되는 연결을 만들어야 할 수 있습니다.

  예를 들어 Microsoft BizTalk Server를 사용 하는 경우 논리 앱은 [BizTalk Server 온-프레미스 커넥터](#on-premises-connectors)를 사용 하 여 BizTalk Server에 연결 하 고 해당 사용자와 통신할 수 있습니다. 그런 다음, [통합 계정 커넥터](#integration-account-connectors)를 사용하여 논리 앱에서 BizTalk와 비슷한 작업을 확장하거나 수행할 수 있습니다.

  커넥터는 Standard 또는 Enterprise 중 하나로 분류 됩니다. [엔터프라이즈 커넥터](#enterprise-connectors) 는 SAP, ibm MQ 및 IBM 3270 같은 엔터프라이즈 시스템에 대 한 액세스를 제공 하 여 추가 비용을 제공 합니다. 커넥터가 Standard 또는 Enterprise 인지 확인 하려면 커넥터 [개요](/connectors)에서 각 커넥터의 참조 페이지의 기술 세부 정보를 참조 하세요.

  또한 이러한 범주를 사용 하 여 커넥터를 식별할 수 있습니다. 하지만 일부 커넥터는 여러 범주에 있을 수 있습니다. 예를 들어 SAP는 엔터프라이즈 커넥터 및 온-프레미스 커넥터입니다.

  | 범주 | 설명 |
  |----------|-------------|
  | [**관리 되는 커넥터**](#managed-api-connectors) | Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online 등과 같은 서비스를 사용하는 논리 앱을 만듭니다. |
  | [**온-프레미스 커넥터**](#on-premises-connectors) | [온-프레미스 데이터 게이트웨이][gateway-doc]가 설치 및 설정되면, 이러한 커넥터를 통해 논리 앱에서 온-프레미스 시스템(예: SQL Server, SharePoint Server, Oracle DB, 파일 공유 등)에 액세스할 수 있습니다. |
  | [**통합 계정 커넥터**](#integration-account-connectors) | 통합 계정을 만들고 지불할 때 사용할 수 있는 이러한 커넥터는 AS2, EDIFACT 및 X12 프로토콜을 사용하여 XML 변환 및 유효성 검사, 플랫 파일 인코딩 및 디코딩, B2B(Business-to-Business) 메시지 처리를 수행합니다. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>ISE (통합 서비스 환경)에서 연결

Azure 가상 네트워크의 리소스에 직접 액세스 해야 하는 논리 앱의 경우 전용 리소스에서 논리 앱을 빌드, 배포 및 실행할 수 있는 전용 [ISE (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 를 만들 수 있습니다. 논리 앱 디자이너에서 ISE의 논리 앱에 사용 하려는 커넥터를 검색할 때 기본 제공 트리거 및 작업에 대 한 **핵심** 레이블이 표시 되 고, **ise** 레이블은 일부 커넥터에 표시 됩니다.

> [!NOTE]
> ISE와 해당 커넥터에서 실행 되는 논리 앱은 이러한 커넥터가 실행 되는 위치에 관계 없이 고정 요금제 및 소비 기반 요금제를 따릅니다. 자세한 내용은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md) 및 [Logic Apps 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/logic-apps/)를 참조 하세요.

| 레이블 | 예제 | 설명 |
|-------|---------|-------------|
| **CORE** | ![핵심 커넥터 예](./media/apis-list/example-core-connector.png) | 이 레이블을 사용 하는 기본 제공 트리거 및 동작은 논리 앱과 동일한 ISE에서 실행 됩니다. |
| **ISE** | ![ISE 커넥터 예](./media/apis-list/example-ise-connector.png) | 이 레이블이 있는 관리 커넥터는 논리 앱과 동일한 ISE에서 실행 됩니다. Azure 가상 네트워크에 연결 된 온-프레미스 시스템이 있는 경우 ISE를 사용 하면 논리 앱 [에서 온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)없이 해당 시스템에 직접 액세스할 수 있습니다. 대신, 사용 가능한 경우 해당 시스템의 **ISE** 커넥터를 사용 하거나, HTTP 작업을 사용 하거나, [사용자 지정 커넥터](#custom)를 사용할 수 있습니다. **ISE** 커넥터가 없는 온-프레미스 시스템의 경우 온-프레미스 데이터 게이트웨이를 사용 합니다. 사용 가능한 ISE 커넥터를 검토 하려면 [ise 커넥터](#ise-connectors)를 참조 하세요. |
| 레이블 없음 | ![다중 테 넌 트 커넥터 예](./media/apis-list/example-multi-tenant-connector.png) | **핵심** 또는 **ISE** 레이블이 없는 모든 커넥터 (계속 사용할 수 있음)는 전역 다중 테 넌 트 Logic Apps 서비스에서 실행 합니다. |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>기본 제공

Logic Apps는 일정 기반 워크플로를 만들고, 논리 앱이 다른 앱 및 서비스와 통신 하 고, 논리 앱을 통해 워크플로를 제어 하 고, 데이터를 관리 또는 조작 하는 데 사용할 수 있도록 기본 제공 트리거와 작업을 제공 합니다.

| 이름 | 설명 |
|------|-------------|
| [![기본 제공 커넥터 ][schedule-icon]<br> **일정** 예약][schedule-doc] | - [ **되풀이** 트리거][schedule-recurrence-doc]를 사용 하 여 기본부터 고급 일정까지 지정 된 되풀이에서 논리 앱을 실행 합니다. <br>- [ **슬라이딩 윈도우** 트리거][schedule-sliding-window-doc]를 사용 하 여 연속 청크의 데이터를 처리 해야 하는 논리 앱을 실행 합니다. <br>- [ **지연** 작업][schedule-delay-doc]을 사용 하 여 지정 된 기간 동안 논리 앱을 일시 중지 합니다. <br>-지정 된 날짜 및 시간까지 [ **지연** 된 작업까지][schedule-delay-until-doc]논리 앱을 일시 중지 합니다. |
| [![기본 제공 커넥터 ][batch-icon]<br> **일괄 처리**][batch-doc] | - **일괄 처리 메시지** 트리거를 사용하여 메시지를 일괄적으로 처리합니다. <br>- **일괄 처리로 메시지 보내기** 작업을 사용하여 기존 일괄 처리 트리거가 있는 논리 앱을 호출합니다. |
| [![HTTP 기본 제공 커넥터 ][http-icon]<br> **http**][http-doc] | HTTP에 대한 트리거 및 작업을 사용하여 HTTP 또는 HTTPS 엔드포인트를 호출합니다. 다른 HTTP 기본 제공 트리거 및 작업에는 [http + Swagger 기본 제공 커넥터][http-swagger-doc] 및 [http + Webhook][http-webhook-doc]가 포함 됩니다. |
| [![기본 제공 커넥터 ][http-request-icon]<br> **요청** 요청][http-request-doc] | - 논리 앱을 다른 앱이나 서비스에서 호출할 수 있게 하고, Event Grid 리소스 이벤트를 트리거하거나, **요청** 트리거를 사용하여 Azure Security Center 경고에 대한 응답을 트리거합니다. <br>- **응답** 작업을 사용하여 앱이나 서비스에 응답을 보냅니다. |
| [![Azure API Management 기본 제공 커넥터 ][azure-api-management-icon]<br> **azure API <br> Management**][azure-api-management-doc] | Azure API Management를 통해 관리하고 게시하는 사용자 고유의 API로 정의된 트리거 및 작업을 호출합니다. |
| [![Azure 앱 Services 기본 제공 커넥터 ][azure-app-services-icon]<br> **Azure 앱 <br> 서비스**][azure-app-services-doc] | Azure App Service에서 호스팅되는 Azure API Apps 또는 Web Apps를 호출합니다. Swagger가 포함되는 경우 이러한 앱에서 정의된 트리거 및 작업은 다른 모든 고급 트리거 및 작업처럼 나타납니다. |
| [![Azure Logic Apps 기본 제공 커넥터 ][azure-logic-apps-icon]<br> **Azure 논리 <br> 앱**][nested-logic-app-doc] | **요청** 트리거로 시작 하는 다른 논리 앱을 호출 합니다. |
|||

### <a name="run-code-from-logic-apps"></a>논리 앱에서 코드 실행

Logic Apps는 논리 앱의 워크플로에서 사용자 고유의 코드를 실행 하기 위한 기본 제공 작업을 제공 합니다.

| 이름 | 설명 |
|------|-------------|
| [![Azure Functions 기본 제공 커넥터 ][azure-functions-icon]<br> **Azure Functions**][azure-functions-doc] | 논리 앱에서 사용자 지정 코드 조각(C# 또는 Node.js)을 실행하는 Azure 함수를 호출합니다. |
| [![인라인 코드 기본 제공 커넥터 ][inline-code-icon]<br> **인라인 코드**][inline-code-doc] | 논리 앱에서 JavaScript 코드 조각을 추가 하 고 실행 합니다. |
|||

### <a name="control-workflow"></a>워크플로 제어

Logic Apps는 논리 앱의 워크플로에서 작업을 구성 하 고 제어 하기 위한 기본 제공 작업을 제공 합니다.

| 이름 | 설명 |
|------|-------------|
| [![조건 기본 제공 작업 ][condition-icon]<br> **조건**][condition-doc] | 조건을 평가하고, 조건이 true 또는 false인지에 따라 별도의 작업을 실행합니다. |
| [![][for-each-icon]<br>**For each** 각 기본 제공 작업에 대해][for-each-doc] | 배열의 모든 항목에 대해 동일한 작업을 수행합니다. |
| [![기본 제공 작업 ][scope-icon]<br> **범위** 범위][scope-doc] | 범위에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 *범위*로 작업을 그룹화합니다. |
| [![기본 제공 동작 ][switch-icon]<br> **스위치** 전환][switch-doc] | 기본 사례를 제외하고는 고유한 값이 할당된 *사례*로 작업을 그룹화합니다. 할당된 값이 식, 개체 또는 토큰의 결과와 일치하는 사례만 실행합니다. 일치하는 사례가 없는 경우 기본 사례를 실행합니다. |
| [![기본 ][terminate-icon]<br> **Terminate** 제공 작업 종료 종료][terminate-doc] | 활발히 실행 중인 논리 앱 워크플로를 중지합니다. |
| [![다음까지 기본 제공 작업 ][until-icon]<br> **Until** 까지][until-doc] | 지정된 조건이 true이거나 일부 상태가 변경될 때까지 작업을 반복합니다. |
|||

### <a name="manage-or-manipulate-data"></a>데이터 관리 또는 조작

Logic Apps는 데이터 출력 및 해당 형식으로 작업 하기 위한 기본 제공 작업을 제공 합니다.

| 이름 | 설명 |
|------|-------------|
| [![데이터 작업 기본 제공 작업 ][data-operations-icon]<br> **데이터 작업**][data-operations-doc] | 데이터를 사용하여 수행하는 작업: <p>- **작성**: 다양한 형식의 여러 입력에서 하나의 출력을 만듭니다. <br>- **CSV 테이블 만들기**: JSON 개체를 사용하여 배열에서 CSV(쉼표로 구분된 값) 테이블을 만듭니다. <br>- **HTML 테이블 만들기**: JSON 개체를 사용하여 배열에서 HTML 테이블을 만듭니다. <br>- **배열 필터링**: 조건을 충족하는 다른 배열의 항목에서 배열을 만듭니다. <br>- **조인**: 배열의 모든 항목에서 문자열을 만들고, 지정한 구분 기호를 사용하여 해당 항목을 구분합니다. <br>- **Json 구문 분석**: 워크플로에서 이러한 속성을 사용할 수 있도록 json 콘텐츠에서 속성 및 해당 값에서 사용자에 게 친숙 한 토큰을 만듭니다. <br>- **선택**: 다른 배열의 항목이나 값을 변환하고 해당 항목을 지정한 속성에 매핑하여 JSON 개체가 포함된 배열을 만듭니다. |
| ![날짜 시간 기본 제공 작업][date-time-icon]<br>**날짜 시간** | 타임스탬프를 사용하여 수행하는 작업: <p>- **시간에 추가**: 타임스탬프에 지정한 단위 수를 추가합니다. <br>- **표준 시간대 변환**: 타임스탬프를 원본 표준 시간대에서 대상 표준 시간대로 변환합니다. <br>- **현재 시간**: 현재 타임스탬프를 문자열로 반환합니다. <br>- **미래 시간 가져오기**: 현재 타임스탬프에 지정한 시간 단위를 더한 값을 반환합니다. <br>- **과거 시간 가져오기**: 현재 타임스탬프에서 지정한 시간 단위를 뺀 값을 반환합니다. <br>- **시간에서 빼기**: 타임스탬프에서 시간 단위 수를 뺍니다. |
| [![변수 기본 제공 작업 ][variables-icon]<br> **변수**][variables-doc] | 변수를 사용하여 수행하는 작업: <p>- **배열 변수에 추가**: 값을 변수로 저장되는 배열의 마지막 항목으로 삽입합니다. <br>- **문자열 변수에 추가**: 값을 변수로 저장되는 문자열의 마지막 문자로 삽입합니다. <br>- **변수 감소**: 변수를 상수 값만큼 줄입니다. <br>- **변수 증가**: 변수를 상수 값만큼 늘립니다. <br>- **변수 초기화**: 변수를 만들고 해당 데이터 형식과 초기 값을 선언합니다. <br>- **변수 설정**: 기존 변수에 다른 값을 할당합니다. |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>관리되는 커넥터

Logic Apps은 이러한 서비스 또는 시스템을 사용 하 여 작업, 프로세스 및 워크플로를 자동화 하는 데 널리 사용 되는 표준 커넥터를 제공 합니다.

| 이름 | 설명 |
|------|-------------|
| [![Azure Service Bus 관리 커넥터 ][azure-service-bus-icon]<br> **Azure Service Bus**][azure-service-bus-doc] | Logic Apps에서 가장 일반적으로 사용되는 커넥터를 사용하여 비동기 메시지, 세션 및 토픽 구독을 관리합니다. |
| [![SQL Server 관리 커넥터 ][sql-server-icon]<br> **SQL Server**][sql-server-doc] | 레코드를 관리 하거나 저장 프로시저를 실행 하거나 쿼리를 수행할 수 있도록 클라우드의 SQL Server 온-프레미스 또는 Azure SQL Database에 연결 합니다. |
| [![][azure-blob-storage-icon]<br>**Azure Blob <br> Storage** Azure Blob Storage 관리 되는 커넥터][azure-blob-storage-doc] | Blob 콘텐츠를 만들고 관리할 수 있도록 저장소 계정에 연결 합니다. |
| [![Office 365 Outlook 관리 커넥터 ][office-365-outlook-icon]<br> **office 365 <br> outlook**][office-365-outlook-doc] | 전자 메일, 작업, 일정 이벤트 및 모임, 연락처, 요청 등을 만들고 관리할 수 있도록 Office 365 전자 메일 계정에 연결 합니다. |
| [![SFTP-SSH 관리 커넥터 ][sftp-ssh-icon]<br> **SFTP-ssh**][sftp-ssh-doc] | 파일 및 폴더에 대 한 작업을 수행할 수 있도록 SSH를 사용 하 여 인터넷에서 액세스할 수 있는 SFTP 서버에 연결 합니다. |
| [![SharePoint Online 관리 커넥터 ][sharepoint-online-icon]<br> **sharepoint <br> online**][sharepoint-online-doc] | 파일, 첨부 파일, 폴더 등을 관리할 수 있도록 SharePoint Online에 연결 합니다. |
| [![Azure 큐 관리 커넥터 ][azure-queues-icon]<br> **azure <br> 큐**][azure-queues-doc] | 큐와 메시지를 만들고 관리할 수 있도록 Azure Storage 계정에 연결 합니다. |
| [![FTP 관리 커넥터 ][ftp-icon]<br> **ftp**][ftp-doc] | 파일 및 폴더에 대 한 작업을 수행할 수 있도록 인터넷에서 액세스할 수 있는 FTP 서버에 연결 합니다. |
| [![파일 시스템 관리 커넥터 ][file-system-icon]<br> **파일 <br> 시스템**][file-system-doc] | 파일을 만들고 관리할 수 있도록 온-프레미스 파일 공유에 연결 합니다. |
| [![Azure Event Hubs 관리 커넥터 ][azure-event-hubs-icon]<br> **azure Event Hubs**][azure-event-hubs-doc] | Event Hub를 통해 이벤트를 사용하고 게시합니다. 예를 들어 Event Hubs를 사용하여 논리 앱에서 출력을 가져온 다음, 해당 출력을 실시간 분석 공급자에게 보냅니다. |
| [![Azure Event Grid managed connector ][azure-event-grid-icon]<br> **Azure Event** <br> **Grid**][azure-event-grid-doc] | Azure 리소스 또는 타사 리소스가 변경되는 경우와 같이 Event Grid에서 게시한 이벤트를 모니터링합니다. |
| [![Salesforce 관리 커넥터 ][salesforce-icon]<br> **salesforce**][salesforce-doc] | 레코드, 작업, 개체 등의 항목을 만들고 관리할 수 있도록 Salesforce 계정에 연결 합니다. |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>온-프레미스 커넥터

다음은 온-프레미스 시스템의 데이터 및 리소스에 액세스 하기 위해 Logic Apps 제공 하는 일반적으로 사용 되는 표준 커넥터입니다. 온-프레미스 시스템에 대한 연결을 만들려면 먼저 [온-프레미스 데이터 게이트웨이를 다운로드, 설치 및 설정][gateway-doc]해야 합니다. 이 게이트웨이는 필요한 네트워크 인프라를 설정하지 않고도 보안 통신 채널을 제공합니다.

:::row:::
    :::column:::
        [![BizTalk Server 커넥터 ][biztalk-server-icon]<br> **BizTalk** <br> **Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![파일 시스템 커넥터 ][file-system-icon]<br> **파일 <br> 시스템**][file-system-doc]
    :::column-end:::
    :::column:::
        [![DB2 커넥터 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Informix 커넥터 ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL 커넥터 ][mysql-icon]<br> **mysql**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB 커넥터 ][oracle-db-icon]<br> **Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL 커넥터 ][postgre-sql-icon]<br> **PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![SharePoint Server 커넥터 ][sharepoint-server-icon]<br> **sharepoint <br> 서버**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server 커넥터 ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Teradata connector ][teradata-icon]<br> **teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>통합 계정 커넥터

Logic Apps은 Azure에서 EIP (엔터프라이즈 통합 팩)를 통해 사용할 수 있는 [통합 계정을](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)만들고 지불할 때 논리 앱을 사용 하 여 b2b (기업 간) 솔루션을 빌드하기 위한 표준 커넥터를 제공 합니다. 이 계정을 사용하여 거래 파트너, 계약, 맵, 스키마, 인증서 등과 같은 B2B 아티팩트를 만들고 저장할 수 있습니다. 이러한 아티팩트를 사용하려면 논리 앱을 통합 계정과 연결합니다. 현재 BizTalk Server를 사용하는 경우 이러한 커넥터는 이미 익숙한 것처럼 보일 수 있습니다.

:::row:::
    :::column:::
        [![AS2 디코딩 작업 ][as2-icon]<br> **as2 <br> 디코딩**][as2-doc]
    :::column-end:::
    :::column:::
        [![AS2 인코딩 동작 ][as2-icon]<br> **as2 <br> 인코딩**][as2-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 디코딩 작업 ][edifact-icon]<br> **edifact <br> 디코딩**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 인코딩 작업 ][edifact-icon]<br> **edifact <br> 인코딩**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![플랫 파일 디코딩 동작 ][flat-file-decode-icon]<br> **플랫 파일 <br> 디코딩**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![플랫 파일 인코딩 동작 ][flat-file-encode-icon]<br> **플랫 파일 <br> 인코딩**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![통합 계정 작업 ][integration-account-icon]<br> **통합 <br> 계정**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![액체 변형 작업 ][liquid-icon]<br> **액체** <br> **변환**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![X12 디코딩 동작 ][x12-icon]<br> **x12 <br> 디코딩**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 인코딩 동작 ][x12-icon]<br> **x12 <br> 인코딩**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Xml 변환 작업 ][xml-transform-icon]<br> **xml** <br> **변환**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![XML 유효성 검사 동작 ][xml-validate-icon]<br> **xml <br> 유효성 검사**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>엔터프라이즈 커넥터

Logic Apps는 SAP 및 IBM MQ와 같은 엔터프라이즈 시스템에 액세스 하기 위한 엔터프라이즈 커넥터를 제공 합니다.

:::row:::
    :::column:::
        [![IBM 3270 커넥터 ][ibm-3270-icon]<br> **ibm 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![MQ 커넥터 ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP 커넥터 ][sap-icon]<br> **sap**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE 커넥터

논리 앱 디자이너는 전용 [integration service environment (ise)](#integration-service-environment)에서 만들고 실행 하는 논리 앱에 대해 **핵심** 레이블을 사용 하 여 ise에서 실행 되는 기본 제공 트리거와 작업을 식별 합니다. ISE에서 실행 되는 관리 커넥터는 **ise** 레이블을 표시 하 고, 전역 다중 테 넌 트 Logic Apps 서비스에서 실행 되는 커넥터는 두 레이블을 표시 하지 않습니다. 이 목록에는 현재 ISE 버전이 있는 커넥터가 표시 됩니다.

:::row:::
    :::column:::
        [![AS2 ISE 커넥터 ][as2-icon]<br> **as2**][as2-doc]
    :::column-end:::
    :::column:::
        [![][azure-automation-icon]<br>**Azure <br> Automation** Azure Automation ISE 커넥터][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![][azure-blob-storage-icon]<br>**Azure Blob <br> Storage** Azure Blob Storage ISE 커넥터][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![][azure-cosmos-db-icon]<br>**Azure Cosmos <br> DB** Azure Cosmos DB ISE 커넥터][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ISE 커넥터 ][azure-event-hubs-icon]<br> **azure Event <br> Hubs**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE 커넥터 ][azure-event-grid-icon]<br> **Azure Event <br> Grid**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Azure File Storage ISE 커넥터 ][azure-file-storage-icon]<br> **azure File <br> Storage**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE 커넥터 ][azure-key-vault-icon]<br> **Azure 주요 <br> 자격 증명 모음**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor 로그 ISE 커넥터 ][azure-monitor-logs-icon]<br> **Azure Monitor <br> 로그**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus ISE 커넥터 ][azure-service-bus-icon]<br> **Azure Service <br> Bus**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![][azure-sql-data-warehouse-icon]<br>**Azure SQL Data <br> Warehouse** Azure SQL Data Warehouse ISE 커넥터][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Azure Table Storage ISE 커넥터 ][azure-table-storage-icon]<br> **azure Table <br> Storage**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure 큐 ISE 커넥터 ][azure-queues-icon]<br> **azure <br> 큐**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT ISE 커넥터 ][edifact-icon]<br> **edifact**][edifact-doc]
    :::column-end:::
    :::column:::
        [![파일 시스템 ISE 커넥터 ][file-system-icon]<br> **파일 <br> 시스템**][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP ISE 커넥터 ][ftp-icon]<br> **ftp**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![IBM 3270 ISE 커넥터 ][ibm-3270-icon]<br> **ibm 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![DB2 ISE 커넥터 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![MQ ISE 커넥터 ][ibm-mq-icon]<br> **IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE 커넥터 ][sap-icon]<br> **sap**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE 커넥터 ][sftp-ssh-icon]<br> **SFTP-ssh**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE 커넥터 ][smtp-icon]<br> **smtp**][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE 커넥터 ][sql-server-icon]<br> **SQL <br> Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![X12 ISE 커넥터 ][x12-icon]<br> **x12**][x12-doc]
    :::column-end:::
:::row-end:::

자세한 내용은 다음 항목을 참조하세요.

* [Azure Logic Apps에서 Azure virtual network 리소스에 대 한 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>트리거 및 동작 형식

커넥터는 *트리거*, *작업*또는 둘 다를 제공할 수 있습니다. *트리거* 는 논리 앱의 첫 번째 단계로, 일반적으로 트리거를 발생 시키고 논리 앱 실행을 시작 하는 이벤트를 지정 합니다. 예를 들어 FTP 커넥터에는 "파일이 추가 되거나 수정 될 때" 논리 앱을 시작 하는 트리거가 있습니다. 일부 트리거는 지정 된 이벤트 또는 데이터를 정기적으로 확인 한 다음 지정 된 이벤트 또는 데이터를 검색할 때 발생 합니다. 다른 트리거는 대기 하지만 특정 이벤트가 발생 하거나 새 데이터를 사용할 수 있는 경우 즉시 발생 합니다. 또한 트리거는 논리 앱에 필요한 데이터를 함께 전달 합니다. 논리 앱은 워크플로 전체에서 해당 데이터를 읽고 사용할 수 있습니다. 예를 들어 Office 365 Outlook 커넥터에는 해당 전자 메일의 콘텐츠를 논리 앱의 워크플로로 전달할 수 있는 "새 전자 메일이 도착 하는 경우" 트리거가 있습니다.

트리거를 실행 한 후에는 Azure Logic Apps 논리 앱의 인스턴스를 만들고 논리 앱의 워크플로에서 *작업* 을 실행 하기 시작 합니다. 작업은 트리거를 따르고 논리 앱의 워크플로에서 태스크를 수행 하는 단계입니다. 예를 들어 SQL 데이터베이스에서 고객 데이터를 가져오고 이후 작업에서 해당 데이터를 처리 하는 논리 앱을 만들 수 있습니다.

Azure Logic Apps에서 제공 하는 트리거의 일반적인 종류는 다음과 같습니다.

* *되풀이 트리거*:이 트리거는 지정 된 일정에 따라 실행 되며 특정 서비스 또는 시스템과 긴밀 하 게 연결 되지 않습니다.

* *폴링 트리거*:이 트리거는 지정 된 일정에 따라 특정 서비스 또는 시스템을 정기적으로 폴링하여 새 데이터를 확인 하거나 특정 이벤트가 발생 했는지 여부를 확인 합니다. 새 데이터를 사용할 수 있거나 특정 이벤트가 발생 한 경우 트리거는 논리 앱의 새 인스턴스를 만들고 실행 합니다. 그러면 이제 입력으로 전달 된 데이터를 사용할 수 있습니다.

* *밀어넣기 트리거*:이 트리거는 새 데이터 또는 이벤트 발생을 대기 하 고 수신 합니다. 새 데이터를 사용할 수 있거나 이벤트가 발생할 때 트리거는 이제 입력으로 전달 된 데이터를 사용할 수 있는 논리 앱의 새 인스턴스를 만들고 실행 합니다.

<a name="connections"></a>

## <a name="connector-configuration"></a>커넥터 구성

각 커넥터의 트리거 및 작업은 사용자가 구성할 수 있는 고유한 속성을 제공 합니다. 또한 대부분의 커넥터에서는 먼저 대상 서비스 또는 시스템에 대 한 *연결* 을 만들고 인증 자격 증명 또는 기타 구성 정보를 제공 해야 논리 앱에서 트리거 또는 동작을 사용할 수 있습니다. 예를 들어 Office 365 Outlook 전자 메일 계정에 액세스 하 여 작업을 수행 하려면 먼저 해당 계정에 대 한 연결에 권한을 부여 해야 합니다.

Azure Active Directory (Azure AD) OAuth를 사용 하는 커넥터의 경우 연결을 만들면 액세스 토큰이 [암호화](../security/fundamentals/encryption-overview.md) 되어 Azure 비밀 저장소에 안전 하 게 저장 되는 Office 365, Salesforce 또는 GitHub와 같은 서비스에 로그인 하는 것을 의미 합니다. FTP 및 SQL과 같은 다른 커넥터에는 서버 주소, 사용자 이름 및 암호와 같은 구성 세부 정보를 포함 하는 연결이 필요 합니다. 이러한 연결 구성 세부 정보 또한 암호화된 후 안전하게 저장됩니다. [Azure의 암호화](../security/fundamentals/encryption-overview.md)에 대해 자세히 알아보세요.

서비스 또는 시스템이 허용 하는 한 연결에서 대상 서비스 또는 시스템에 액세스할 수 있습니다. Office 365 및 Dynamics와 같은 Azure AD OAuth 연결을 사용 하는 서비스의 경우 액세스 토큰을 무기한 새로 고칠 Azure Logic Apps. 다른 서비스의 경우 Azure Logic Apps를 새로 고치지 않고 토큰을 사용할 수 있는 기간에 제한이 있을 수 있습니다. 일반적으로 일부 동작은 암호 변경과 같은 모든 액세스 토큰을 무효화 합니다.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>사용자 지정 API 및 커넥터

사용자 지정 코드를 실행하거나 커넥터로 사용할 수 없는 API를 호출하려면 [사용자 지정 API Apps를 만들어](../logic-apps/logic-apps-create-api-app.md) Logic Apps 플랫폼을 확장할 수 있습니다. 또한 *모든* REST 또는 SOAP 기반 API에 대한 [사용자 지정 커넥터를 만들면](../logic-apps/custom-connector-overview.md) Azure 구독의 모든 논리 앱에서 해당 API를 사용할 수 있습니다. Azure에서 모든 사용자가 사용할 수 있도록 사용자 지정 API 앱 또는 커넥터를 공개하려면 [Microsoft 인증을 위해 커넥터를 제출](/connectors/custom-connectors/submit-certification)할 수 있습니다.

> [!NOTE]
> [ISE (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 에서 배포 하 고 실행 하는 논리 앱은 Azure virtual network의 리소스에 직접 액세스할 수 있습니다. 온-프레미스 데이터 게이트웨이를 필요로 하는 사용자 지정 커넥터를 사용 하 고 ISE 외부에서 커넥터를 만든 경우 ISE의 논리 앱 에서도 이러한 커넥터를 사용할 수 있습니다.
>
> ISE 내에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이와 작동 하지 않습니다. 그러나 이러한 커넥터는 ISE를 호스트 하는 Azure 가상 네트워크에 연결 된 온-프레미스 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE의 논리 앱은 이러한 리소스와 통신할 때 데이터 게이트웨이가 필요 하지 않을 수 있습니다.
>
> ISEs를 만드는 방법에 대 한 자세한 내용은 [Azure Logic Apps에서 Azure virtual network에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)을 참조 하세요.

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>연결 만들기 차단

조직에서 Azure Logic Apps의 커넥터를 사용 하 여 특정 리소스에 대 한 연결을 허용 하지 않는 경우 [Azure Policy](../governance/policy/overview.md)를 사용 하 여 논리 앱 워크플로에서 특정 커넥터에 대 한 [연결을 만드는 기능을 차단할](../logic-apps/block-connections-connectors.md) 수 있습니다. 자세한 내용은 [Azure Logic Apps에서 특정 커넥터에 의해 생성 된 연결 차단](../logic-apps/block-connections-connectors.md)을 참조 하세요.

## <a name="get-ready-for-deployment"></a>배포 준비 완료

논리 앱 내에서 연결을 만드는 경우에도 연결은 자체 리소스 정의가 포함 된 별도의 Azure 리소스입니다. 이러한 연결 리소스 정의를 검토 하려면 [Azure에서 Visual Studio로 논리 앱을 다운로드](../logic-apps/manage-logic-apps-with-visual-studio.md)합니다 .이는 일반적으로 배포할 준비가 된 유효한 매개 변수가 있는 논리 앱 템플릿을 만드는 가장 쉬운 방법입니다.

## <a name="next-steps"></a>다음 단계

* [전체 커넥터 목록](/connectors) 보기
* [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [논리 앱에 대한 사용자 지정 커넥터 만들기](/connectors/custom-connectors/)
* [논리 앱에 대한 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "온-프레미스 데이터 게이트웨이를 사용하여 논리 앱에서 온-프레미스 데이터 원본에 연결"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API 관리 및 게시를 위한 Azure API Management 서비스 인스턴스 만들기"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "App Service API Apps과 논리 앱 통합"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Azure Functions와 논리 앱 통합"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "그룹 또는 일괄 처리로 메시지 처리"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "조건을 평가 하 고 조건이 true 인지 false 인지에 따라 다른 작업을 실행 합니다."
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "배열의 모든 항목에 대해 동일한 작업 수행"
[http-doc]: ./connectors-native-http.md "논리 앱에서 HTTP 또는 HTTPS 끝점 호출"
[http-request-doc]: ./connectors-native-reqres.md "논리 앱에서 HTTP 요청 수신"
[http-response-doc]: ./connectors-native-reqres.md "논리 앱에서 HTTP 요청에 응답"
[http-swagger-doc]: ./connectors-native-http-swagger.md "논리 앱에서 REST 끝점 호출"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP 또는 HTTPS 끝점의 특정 이벤트 대기"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "논리 앱에서 JavaScript 코드 조각 추가 및 실행"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "중첩된 워크플로와 논리 앱 통합"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "쿼리 작업을 사용하여 배열 선택 및 필터링"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "일정에 따라 논리 앱 실행"
[schedule-delay-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-delay-until-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "되풀이 일정에 따라 논리 앱 실행"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "연속 청크의 데이터를 처리 해야 하는 논리 앱 실행"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "그룹에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 그룹으로 작업을 구성"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "고유 값이 할당 된 사례에 대 한 작업을 구성 합니다. 식, 개체 또는 토큰의 결과와 일치 하는 값을 가진 case만 실행 합니다. 일치 하는 항목이 없는 경우 기본 사례를 실행 합니다."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "논리 앱에 대해 활발히 실행되는 워크플로 중지 또는 취소"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "지정 된 조건이 true 이거나 일부 상태가 변경 될 때까지 작업을 반복 합니다."
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "배열 필터링이나 CSV 및 HTML 테이블 생성과 같은 데이터 작업 수행"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "변수를 사용하여 작업 수행(예: 초기화, 설정, 증가, 감소 및 문자열 또는 배열 변수에 추가)"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "클라우드 및 온-프레미스 인프라에 대 한 자동화 작업 만들기 및 관리"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage 커넥터와 Blob 컨테이너의 파일 관리"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Azure Cosmos DB에 연결 하 여 문서 및 저장 프로시저에 액세스할 수 있습니다."
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Azure 리소스 또는 타사 리소스가 변경 되는 경우와 같이 Event Grid에서 게시 한 이벤트를 모니터링 합니다."
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "논리 앱과 Event Hubs 간에 이벤트를 보내고 받을 수 있도록 Azure Event Hubs에 연결 합니다."
[azure-file-storage-doc]: /connectors/azurefile/ "파일을 만들고, 업데이트 하 고, 가져오고, 삭제할 수 있도록 Azure Storage 계정에 연결 합니다."
[azure-key-vault-doc]: /connectors/keyvault/ "비밀 및 키를 관리할 수 있도록 Azure Key Vault에 연결"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Log Analytics 작업 영역 및 Application Insights 구성 요소에서 Azure Monitor 로그에 대해 쿼리 실행"
[azure-queues-doc]: /connectors/azurequeues/ "큐 및 메시지를 만들고 관리할 수 있도록 Azure Storage 계정에 연결"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus 큐 및 항목에서 메시지를 보내고 Service Bus 큐 및 구독에서 메시지를 받습니다."
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Azure SQL Data Warehouse에 연결 하 여 데이터를 볼 수 있도록 합니다."
[azure-table-storage-doc]: /connectors/azuretables/ "테이블을 만들고, 업데이트 하 고, 쿼리할 수 있도록 Azure Storage 계정에 연결"
[biztalk-server-doc]: /connectors/biztalk/ "BizTalk Server에 연결 하 여 BizTalk 기반 응용 프로그램을 Azure Logic Apps와 나란히 실행할 수 있습니다."
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "온-프레미스 파일 시스템에 연결"
[ftp-doc]: ./connectors-create-api-ftp.md "파일 업로드, 가져오기, 삭제 등과 같은 FTP 태스크의 경우 FTP / FTPS 서버에 연결"
[github-doc]: ./connectors-create-api-github.md "GitHub에 연결 및 문제 추적"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google 캘린더에 연결 하 고 달력을 관리할 수 있음"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "시트를 수정할 수 있도록 Google 시트에 연결"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Google 작업에 연결 하 여 작업을 관리할 수 있도록 합니다."
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM 메인프레임에서 3270 앱에 연결"
[ibm-db2-doc]: ./connectors-create-api-db2.md "클라우드 또는 온-프레미스에서 IBM d b 2에 연결 합니다. 행을 업데이트 하 고 테이블을 가져옵니다."
[ibm-informix-doc]: ./connectors-create-api-informix.md "클라우드 또는 온-프레미스에서 Informix에 연결 합니다. 행을 읽고 테이블을 나열 합니다."
[ibm-mq-doc]: ./connectors-create-api-mq.md "온-프레미스 또는 Azure에서 IBM MQ에 연결하여 메시지 송수신"
[instagram-doc]: ./connectors-create-api-instagram.md "명령이 있는 Agram에 연결 합니다. 이벤트 트리거 또는 동작"
[mandrill-doc]: ./connectors-create-api-mandrill.md "통신을 위해 Mandrill에 연결"
[mysql-doc]: /connectors/mysql/ "데이터를 읽고 쓸 수 있도록 온-프레미스 MySQL 데이터베이스에 연결 합니다."
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "전자 메일을 보내고 받고, 일정 및 연락처를 관리 하는 등의 방법으로 Office 365 계정에 연결 합니다."
[onedrive-doc]: ./connectors-create-api-onedrive.md "개인 Microsoft OneDrive에 연결 하 여 파일 업로드, 삭제, 나열 등을 수행할 수 있습니다."
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "비즈니스 Microsoft OneDrive에 연결 하 여 파일 업로드, 삭제, 나열 등을 수행할 수 있습니다."
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle 데이터베이스에 연결 하 여 행을 추가 하 고 삽입 하 고 삭제할 수 있습니다."
[outlook.com-doc]: ./connectors-create-api-outlook.md "전자 메일, 일정, 연락처 등을 관리할 수 있도록 Outlook 사서함에 연결"
[postgre-sql-doc]: /connectors/postgresql/ "테이블에서 데이터를 읽을 수 있도록 PostgreSQL 데이터베이스에 연결 합니다."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce 계정에 연결 합니다. 계정, 잠재 고객, 기회 등 관리"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "온-프레미스 SAP 시스템에 연결"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid에 연결 합니다. 전자 메일을 보내고 받는 사람 목록을 관리 합니다."
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH를 사용 하 여 SFTP 계정에 연결 합니다. 파일 업로드, 가져오기, 삭제 등"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "SharePoint 온-프레미스 서버에 연결 합니다. 문서, 목록 항목 등 관리"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "SharePoint Online에 연결 합니다. 문서, 목록 항목 등 관리"
[slack-doc]: ./connectors-create-api-slack.md "여유 시간에 연결 하 고 여유 채널에 메시지 게시"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP 서버에 연결 및 첨부 파일이 포함된 이메일 보내기"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "통신을 위해 SparkPost에 연결"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database 또는 SQL Server에 연결 합니다. SQL 데이터베이스 테이블에서 항목 만들기, 업데이트, 가져오기 및 삭제"
[teradata-doc]: /connectors/teradata/ "Teradata 데이터베이스에 연결 하 여 테이블에서 데이터 읽기"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio에 연결 합니다. 메시지 보내기 및 가져오기, 사용 가능한 번호 가져오기, 수신 전화 번호 관리 등"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube에 연결 합니다. 비디오 및 채널 관리"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 프로토콜을 사용 하는 메시지 인코딩 및 디코딩"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT 프로토콜을 사용 하는 메시지 인코딩 및 디코딩"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIFACT 프로토콜을 사용 하는 메시지 디코딩"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT 프로토콜을 사용 하는 메시지 인코딩"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대 한 자세한 정보"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대 한 자세한 정보"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정 아티팩트에 대 한 메타 데이터 관리"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "액체 템플릿으로 JSON 변환"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 프로토콜을 사용 하는 메시지 인코딩 및 디코딩"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 프로토콜을 사용 하는 메시지 디코딩"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 프로토콜을 사용 하는 메시지 인코딩"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사"
