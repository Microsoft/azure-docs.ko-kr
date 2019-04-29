---
title: BizTalk Services에서 Azure Logic Apps로 앱 이동 | Microsoft Docs
description: Azure BizTalk Services(MABS)에서 Azure Logic Apps로 마이그레이션
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: f813cb5d8d5c442fc17f126c3a2ff6de7b0bdde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61321253"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>BizTalk Services에서 Azure Logic Apps로 마이그레이션

MABS(Microsoft Azure BizTalk Services)가 사용 중지됩니다. MABS 통합 솔루션을 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 이동하려면 이 문서의 지침을 따릅니다. 

## <a name="introduction"></a>소개

BizTalk Services는 다음 두 하위 서비스로 구성됩니다.

* Microsoft BizTalk Services 하이브리드 연결
* EAI 및 EDI 브리지 기반 통합

[Azure App Service 하이브리드 연결](../app-service/app-service-hybrid-connections.md)이 BizTalk Services 하이브리드 연결을 대체합니다. Azure 하이브리드 연결은 Azure Portal을 통해 Azure App Service에서 사용할 수 있습니다. 이 서비스는 하이브리드 연결 관리자를 제공하여 기존 BizTalk Services 하이브리드 연결 및 사용자가 포털에서 만든 새 하이브리드 연결을 관리할 수 있게 합니다. 

[Logic Apps](../logic-apps/logic-apps-overview.md)는 EAI 및 EDI 브리지 기반 통합을 BizTalk Services 등의 동일한 모든 기능으로 바꿉니다. 이 서비스는 브라우저 또는 Visual Studio를 통해 복잡한 통합 솔루션을 빠르고 쉽게 빌드할 수 있도록 클라우드 규모의 소비 기반 워크플로 및 오케스트레이션 기능을 제공합니다.

다음 표에서는 BizTalk Services 기능을 Logic Apps에 매핑하고 있습니다.

| BizTalk Services   | Logic Apps            | 목적                      |
| ------------------ | --------------------- | ---------------------------- |
| 커넥터          | 커넥터             | 데이터 보내기 및 받기   |
| 브리지             | 논리 앱             | 파이프라인 프로세서           |
| 유효성 검사 단계     | XML 유효성 검사 작업 | 스키마에 대해 XML 문서 유효성 검사 | 
| 보강 단계       | 데이터 토큰           | 속성을 메시지로 또는 라우팅 결정을 위해 승격 |
| 변환 단계    | 변환 작업      | XML 메시지를 한 형식에서 다른 형식으로 변환 |
| 디코딩 단계       | 플랫 파일 디코딩 작업 | 플랫 파일에서 XML로 변환 |
| 인코딩 단계       | 플랫 파일 인코딩 작업 | XML에서 플랫 파일로 변환 |
| 메시지 검사기  | Azure Functions 또는 API Apps | 통합에서 사용자 지정 코드 실행 |
| 라우팅 작업       | 조건 또는 스위치 | 지정된 커넥터 중 하나로 메시지 라우팅 |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services 아티팩트

BizTalk Services에는 여러 종류의 아티팩트가 있습니다. 

## <a name="connectors"></a>커넥터

BizTalk Services 커넥터를 사용하면 HTTP 기반 요청/응답과 상호 작용할 수 있게 하는 양방향 브리지를 포함하여 브리지에서 데이터를 보내고 받을 수 있습니다. Logic Apps는 동일한 용어를 사용하고, 광범위한 기술과 서비스에 연결하여 동일한 목적을 수행하는 180개 이상의 커넥터를 가지고 있습니다. 예를 들어 OneDrive, Office365, Dynamics CRM 등과 같은 클라우드 SaaS 및 PaaS 서비스에 사용할 수 있는 커넥터와 BizTalk Services용 BizTalk 어댑터 서비스를 대체하는 온-프레미스 데이터 게이트웨이를 통해 온-프레미스 시스템에 사용할 수 있는 커넥터가 있습니다. BizTalk Services의 원본은 FTP, SFTP 및 Service Bus 큐 또는 토픽 구독으로 제한됩니다.

![](media/logic-apps-move-from-mabs/sources.png)

기본적으로 각 브리지에는 브리지에 대한 런타임 주소 및 상대 주소 속성으로 구성되는 HTTP 엔드포인트가 있습니다. Logic Apps에서 동일한 결과를 얻으려면 [요청 및 응답](../connectors/connectors-native-reqres.md) 작업을 사용합니다.

## <a name="xml-processing-and-bridges"></a>XML 처리 및 브리지

BizTalk Services에서 브리지는 처리 파이프라인과 비슷합니다. 브리지는 커넥터에서 받은 데이터를 가져오고, 데이터를 사용하여 일부 작업을 수행한 다음, 결과를 다른 시스템으로 보낼 수 있습니다. Logic Apps는 BizTalk Services와 동일한 파이프라인 기반 상호 작용 패턴을 지원하고 다른 통합 패턴도 제공하여 동일한 작업을 수행합니다. BizTalk Services의 [XML 요청-응답 브리지](https://msdn.microsoft.com/library/azure/hh689781.aspx)는 이러한 작업을 수행하는 단계로 구성된 VETER 파이프라인으로 알려져 있습니다.

* (V) 유효성 검사
* (E) 보강
* (T) 변환
* (E) 보강
* (R) 라우팅

다음 이미지에서는 요청과 회신 간에 처리가 분할되는 방식을 보여 줍니다. 여기서는 요청과 회신 경로를 개별적으로 제어합니다(예 : 각 경로마다 다른 맵 사용).

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

또한 XML 단방향 브리지는 처리의 시작과 끝에 디코드 및 인코드 단계를 추가합니다. 통과 브리지는 단일 강화 단계를 포함합니다.

### <a name="message-processing-decoding-and-encoding"></a>메시지 처리, 디코딩 및 인코딩

BizTalk Services에서는 다양한 유형의 XML 메시지를 받고, 받은 메시지에 대해 일치하는 스키마를 결정할 수 있습니다. 이 작업은 수신 처리 파이프라인의 *메시지 유형* 단계에서 수행됩니다. 그런 다음, 디코드 단계에서 검색된 메시지 유형을 사용하여 제공된 스키마를 통해 메시지를 디코딩합니다. 스키마가 플랫 파일 스키마인 경우 이 단계에서는 들어오는 플랫 파일을 XML로 변환합니다. 

Logic Apps가 비슷한 기능을 제공합니다. 다른 커넥터 트리거(파일 시스템, FTP, HTTP 등)를 사용하여 여러 다른 프로토콜을 통해 플랫 파일을 받고, [플랫 파일 디코드](../logic-apps/logic-apps-enterprise-integration-flatfile.md) 작업을 사용하여 들어오는 데이터를 XML로 변환합니다. 기존 플랫 파일 스키마를 변경하지 않고 Logic Apps로 직접 이동한 다음, 스키마를 통합 계정에 업로드할 수 있습니다.

### <a name="validation"></a>유효성 검사

들어오는 데이터가 XML로 변환된 후(또는 XML이 수신된 메시지 형식인 경우) 유효성 검사가 실행되어 메시지가 XSD 스키마를 준수하는지 확인합니다. Logic Apps에서 이 작업을 수행하려면 [XML 유효성 검사](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) 작업을 사용합니다. BizTalk Services에서 동일한 스키마를 변경하지 않고 사용할 수 있습니다.

### <a name="transform-messages"></a>메시지 변환

BizTalk Services에서 변환 단계는 하나의 XML 기반 메시지 형식을 다른 형식으로 변환합니다. 이 작업은 TRFM 기반 매퍼를 사용하여 맵을 적용함으로써 수행됩니다. Logic Apps에서 프로세스는 비슷합니다. 변환 작업은 통합 계정에서 맵을 실행합니다. 주요 차이점은 Logic Apps의 맵이 XSLT 형식이라는 것입니다. XSLT에는 펑토이드가 포함되고 BizTalk Server용으로 만든 맵을 비롯하여 기존 XSLT를 다시 사용할 수 있는 기능이 포함되어 있습니다. 

### <a name="routing-rules"></a>라우팅 규칙

BizTalk Services는 들어오는 메시지 또는 데이터를 보낼 엔드포인트 또는 커넥터에 대한 라우팅을 결정합니다. 라우팅 필터 옵션을 사용하면 미리 구성된 엔드포인트에서 선택할 수 있습니다.

![](media/logic-apps-move-from-mabs/route-filter.png)

BizTalk Services에서 두 가지 옵션만 있는 경우 *조건*을 사용하는 것이 BizTalk Services의 라우팅 필터를 변환하는 가장 좋은 방법입니다. 세 개 이상 있으면 **스위치**를 사용합니다.

Logic Apps는 [조건문](../logic-apps/logic-apps-control-flow-conditional-statement.md) 및 [switch 문](../logic-apps/logic-apps-control-flow-switch-statement.md)을 사용하여 정교한 논리 기능, 고급 제어 흐름 및 라우팅을 제공합니다.

### <a name="enrich"></a>보강

BizTalk Services 처리에서 보강 단계는 받은 데이터와 관련된 메시지 컨텍스트에 속성을 추가합니다. 예를 들어 데이터베이스 조회에서 라우팅에 사용할 속성을 승격하거나 XPath 식을 사용하여 값을 추출합니다. Logic Apps는 이전 작업의 모든 컨텍스트 데이터 출력에 대한 액세스를 제공하므로 동일한 동작을 복제하는 것이 쉽습니다. 예를 들어 `Get Row` SQL 연결 작업을 사용하면 SQL Server 데이터베이스에서 데이터를 반환하고 라우팅을 위한 결정 작업에서 이 데이터를 사용합니다. 마찬가지로 xpath 워크플로 정의 언어 표현식을 사용하여 XPath뿐만 아니라 트리거에 의해 들어오는 Service Bus 큐 메시지의 속성에 주소를 지정할 수 있습니다.

### <a name="run-custom-code"></a>사용자 지정 코드 실행

BizTalk Services를 사용하면 사용자 고유의 어셈블리에 업로드된 [사용자 지정 코드를 실행](https://msdn.microsoft.com/library/azure/dn232389.aspx)할 수 있습니다. 이 기능은 [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) 인터페이스에 의해 구현됩니다. 브리지의 각 단계에는 이 인터페이스를 구현하기 위해 만든 .NET 형식을 제공하는 두 개의 속성(On Enter Inspector 및 On Exit Inspector)이 있습니다. 사용자 지정 코드를 사용하면 데이터에 대해 더 복잡한 처리를 수행할 수 있으며, 일반적인 비즈니스 논리를 수행하는 어셈블리에서 기존 코드를 다시 사용할 수 있습니다. 

Logic Apps는 사용자 지정 코드를 실행 하는 두 가지를 제공 합니다. Azure Functions 및 API 앱입니다. Azure Functions는 Logic Apps에서 만들고 호출할 수 있습니다. [Azure Functions를 통해 Logic Apps에 대한 사용자 지정 코드 추가 및 실행](../logic-apps/logic-apps-azure-functions.md)을 참조하세요. Azure App Service의 일부인 API Apps를 사용하여 고유한 트리거 및 작업을 만듭니다. [Logic Apps에서 사용할 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)에 대해 자세히 알아보세요. 

BizTalk Services에서 호출하는 어셈블리에 사용자 지정 코드가 있는 경우, 이 코드를 Azure Functions로 이동하거나 구현하는 항목에 따라 API Apps를 사용하여 사용자 지정 API를 만들 수 있습니다. 예를 들어 Logic Apps에 커넥터가 없는 다른 서비스를 래핑하는 코드가 있는 경우, API App을 만들고 API App이 논리 앱 내에서 제공하는 작업을 사용합니다. 도우미 함수 또는 라이브러리가 있는 경우 Azure Functions가 가장 적합할 수 있습니다.

### <a name="edi-processing-and-trading-partner-management"></a>EDI 처리 및 거래 업체 관리

BizTalk Services 및 Logic Apps에는 AS2(Applicability Statement 2), X12 및 EDIFACT를 지원하는 EDI 및 B2B 처리가 포함됩니다. BizTalk Services의 전용 추적 및 관리 포털에서 EDI 브리지를 만들고 거래 업체 및 규약을 만들거나 관리합니다.
Logic Apps에서는 [EIP(엔터프라이즈 통합 팩)](../logic-apps/logic-apps-enterprise-integration-overview.md)를 통해 이 기능을 가져올 수 있습니다. EIP는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)과 EDI 및 B2B 처리를 위한 B2B 작업을 제공합니다. 또한 통합 계정을 사용하여 [거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md) 및 [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md)을 만들고 관리합니다. 통합 계정이 만들어지면 하나 이상의 논리 앱을 계정에 연결할 수 있습니다. 그런 다음, B2B 작업을 사용하여 논리 앱의 거래 업체 정보에 액세스할 수 있습니다. 다음과 같은 작업이 제공됩니다.

* AS2 인코딩
* AS2 디코딩
* X12 인코딩
* X12 디코딩
* EDIFACT 인코딩
* EDIFACT 디코딩

BizTalk Services와는 달리 이러한 작업은 전송 프로토콜에서 분리됩니다. 따라서 논리 앱을 만들 때 데이터를 보내고 받는 데 사용하는 커넥터의 유연성이 향상됩니다. 예를 들어 이메일에서 첨부 파일로 X12 파일을 받은 다음, 논리 앱에서 이러한 파일을 처리할 수 있습니다. 

## <a name="manage-and-monitor"></a>관리 및 모니터링

BizTalk Services의 전용 포털에서 문제를 모니터링하고 해결할 수 있는 추적 기능을 제공했습니다. Logic Apps는 [Azure Portal](../logic-apps/logic-apps-monitor-your-logic-apps.md)을 통해 더 풍부한 추적 및 모니터링 기능을 제공하며, 이동 중인 경우에는 사물을 주시하는 모바일 앱을 포함합니다.

## <a name="high-availability"></a>고가용성

BizTalk Services의 HA(고가용성)를 위해 특정 지역에서 둘 이상의 인스턴스를 사용하여 처리 부하를 공유할 수 있습니다. Logic Apps는 추가 비용 없이 지역 내부 HA를 제공합니다. 

BizTalk Services에서 B2B 처리를 위한 지역 외부 재해 복구에는 백업 및 복원 프로세스가 필요합니다. 비즈니스 연속성을 위해 Logic Apps는 서로 다른 지역의 통합 계정 간에 B2B 데이터를 동기화할 수 있게 하는 지역 간 활성/수동 [DR 기능](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Logic Apps란?](../logic-apps/logic-apps-overview.md)
* [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md) 또는 [미리 빌드된 템플릿](../logic-apps/logic-apps-create-logic-apps-from-templates.md)을 사용하여 신속하게 시작  
* 논리 앱에서 사용할 수 있는 [사용 가능한 모든 커넥터 보기](../connectors/apis-list.md)