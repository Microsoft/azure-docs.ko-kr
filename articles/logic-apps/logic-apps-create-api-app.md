---
title: Azure Logic Apps용 웹 API 및 REST API 만들기 | Microsoft Docs
description: Azure Logic Apps에서 시스템 통합을 위해 API, 서비스 또는 시스템을 호출하는 웹 API 및 REST API 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.date: 05/26/2017
ms.openlocfilehash: 620ede672d71338abeff5198fd5f94e92dc193d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681874"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Azure Logic Apps에서 호출할 수 있는 사용자 지정 API 만들기

Azure Logic Apps는 논리 앱 워크플로에서 사용할 수 있는[100개 이상의 기본 제공 커넥터](../connectors/apis-list.md)를 제공하지만, 커넥터로 사용할 수 없는 API, 시스템 및 서비스를 호출하는 것이 좋을 수도 있습니다. 논리 앱에서 사용할 동작과 트리거를 제공하는 자체 API는 직접 만들 수 있습니다. 논리 앱 워크플로에서 호출할 수 있는 API를 직접 만들려는 다른 이유는 다음가 같습니다.

* 현재의 시스템 통합 및 데이터 통합 워크플로를 확장합니다.
* 고객이 서비스를 사용하여 전문적이거나 개인적인 작업을 관리할 수 있도록 지원합니다.
* 서비스에 대한 범위, 검색 기능 및 사용을 확장합니다.

기본적으로 커넥터는 플러그형 인터페이스에 대한 REST, 문서에 대한 [Swagger 메타데이터 형식](https://swagger.io/specification/) 및 JSON(데이터 교환 형식)을 사용하는 웹 API입니다. 커넥터는 HTTP 엔드포인트를 통해 통신하는 REST API이므로 .NET, Java 또는 Node.js와 같은 언어를 사용하여 커넥터를 빌드할 수 있습니다. API 호스팅을 위한 가장 쉽고, 확장성 있는, 최상의 방법 중 하나를 제공하는 PaaS(Platform-as-a-Service) 제품인 [Azure App Service](../app-service/overview.md)에서 API를 호스팅할 수도 있습니다. 

사용자 지정 API가 논리 앱에서 작동하려면 API가 논리 앱 워크플로에서 특정 작업을 수행하는 [*동작*](./logic-apps-overview.md#logic-app-concepts)을 제공할 수 있습니다. 또한 API는 [*트리거*](./logic-apps-overview.md#logic-app-concepts)의 역할을 수행하여 새 데이터 또는 이벤트가 지정된 조건을 충족할 때 논리 앱 워크플로를 시작할 수도 있습니다. 이 항목에서는 API에서 제공할 동작에 따라 API에서 동작과 트리거를 빌드할 때 따라야 하는 일반적인 패턴에 대해 설명합니다.

확장성이 뛰어나고 쉬운 API 호스팅을 제공하는 PaaS(Platform-as-a-Service) 제품인 [Azure App Service](../app-service/overview.md)에서 API를 호스트할 수 있습니다.

> [!TIP] 
> API를 웹앱으로 배포할 수 있지만 API 앱으로 배포하는 것이 좋습니다. 이렇게 하면 클라우드에서 API를 빌드, 호스트 및 사용할 때 작업을 더 쉽게 수행할 수 있습니다. API에서 코드를 변경할 필요가 없이 API 앱에 코드를 배포하기만 하면 됩니다. 예를 들어 다음 언어를 사용하여 만든 API 앱을 빌드하는 방법을 알아봅니다. 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.JS](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> 논리 앱용으로 빌드된 API 앱 샘플은 [Azure Logic Apps GitHub 리포지토리](https://github.com/logicappsio) 또는 [블로그](https://aka.ms/logicappsblog)를 방문하세요.

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>사용자 지정 API는 사용자 지정 커넥터와 어떻게 다른가요?

사용자 지정 API 및 [사용자 지정 커넥터](../logic-apps/custom-connector-overview.md)는 플러그형 인터페이스에 대한 REST, 문서에 대한 [Swagger 메타데이터 형식](https://swagger.io/specification/) 및 JSON(데이터 교환 형식)을 사용하는 웹 API입니다. 또한 이러한 API 및 커넥터는 HTTP 엔드포인트를 통해 통신하는 REST API이므로 .NET, Java 또는 Node.js와 같은 언어를 사용하여 사용자 지정 API 및 커넥터를 빌드할 수 있습니다.

사용자 지정 API는 커넥터가 아닌 API를 호출할 수 있도록 하고, HTTP + Swagger, Azure SAPI Management 또는 App Services를 사용하여 호출할 수 있는 엔드포인트를 제공합니다. 사용자 지정 커넥터는 사용자 지정 API처럼 작동하지만 다음과 같은 특성도 있습니다.

* Azure에서 Logic Apps 커넥터 리소스로 등록됩니다.
* Logic Apps 디자이너에서 Microsoft 관리 커넥터와 함께 아이콘으로 표시됩니다.
* 커넥터의 작성자와 논리 앱이 배포된 지역에서 동일한 Azure Active Directory 테넌트 및 Azure 구독을 가지는 논리 앱 사용자만 사용할 수 있습니다.

Microsoft 인증에 대해 등록된 커넥터를 지정할 수도 있습니다. 이 프로세스는 등록된 커넥터가 공용 사용 조건을 충족하는지 확인하고 사용자가 Microsoft Flow 및 Microsoft PowerApps에서 해당 커넥터를 사용할 수 있도록 합니다.

사용자 지정 커넥터에 대한 자세한 내용은 다음을 참조하세요. 

* [사용자 지정 커넥터 개요](../logic-apps/custom-connector-overview.md)
* [웹 API에서 사용자 지정 커넥터 만들기](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Azure Logic Apps에서 사용자 지정 커넥터 등록](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>유용한 도구

API 작업 및 매개 변수를 설명하는 [Swagger 문서](https://swagger.io/specification/)도 API에 있으면 사용자 지정 API가 논리 앱에서 가장 잘 작동합니다.
많은 라이브러리(예: [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle))에서 Swagger 파일을 자동으로 생성할 수 있습니다. 또한 Swagger 파일에 표시 이름, 속성 형식 등에 주석을 달려면 [TRex](https://github.com/nihaue/TRex)를 사용하여 Swagger 파일이 논리 앱에서 잘 작동하도록 할 수도 있습니다.

<a name="actions"></a>

## <a name="action-patterns"></a>동작 패턴

논리 앱에서 작업을 수행하려면 사용자 지정 API에서 [*동작*](./logic-apps-overview.md#logic-app-concepts)을 제공해야 합니다. API 작업 각각은 동작에 맵핑됩니다. 기본 동작은 HTTP 요청을 받아들이고 HTTP 응답을 반환하는 컨트롤러입니다. 예를 들어 논리 앱은 웹앱 또는 API 앱에 HTTP 요청을 보냅니다. 그런 다음 앱은 논리 앱에서 처리할 수 있는 내용과 함께 HTTP 응답을 반환합니다.

표준 동작의 경우 API에 HTTP 요청 메서드를 작성하고 해당 메서드를 Swagger 파일에 설명할 수 있습니다. 그런 다음 [HTTP 동작](../connectors/connectors-native-http.md) 또는 [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) 동작을 사용하여 API를 직접 호출할 수 있습니다. 기본적으로 응답은 [요청 시간 제한](./logic-apps-limits-and-config.md) 내에 반환되어야 합니다. 

![표준 동작 패턴](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> API에서 장기 실행 작업을 완료하는 동안 논리 앱이 대기하도록 하려면 API가 이 항목에서 설명하는 [비동기 폴링 패턴](#async-pattern) 또는 [비동기 웹후크 패턴](#webhook-actions)을 따르면 됩니다. 이러한 패턴의 특이한 동작을 시각화할 수 있도록 유추하기 위해 빵집에서 사용자 지정 케이크 주문을 처리하는 프로세스를 가정해 봅니다. 폴링 패턴은 케이크가 준비되었는지 여부를 확인하기 위해 20분마다 고객이 빵집에 전화하는 동작을 미러링합니다. 웹후크 패턴은 케이크가 준비되었을 때 고객에게 전화를 걸도록 하기 위해 빵집에서 고객의 전화 번호를 요구하는 동작을 미러링합니다.

샘플은 [Logic Apps GitHub 리포지토리](https://github.com/logicappsio)를 참조하세요. 또한 [동작 사용량 측정](logic-apps-pricing.md)에 대해서도 자세히 알아보세요.

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>폴링 동작 패턴으로 장기 실행 작업 수행

API에서 [요청 시간 제한](./logic-apps-limits-and-config.md)보다 더 오래 실행될 수 있는 작업을 수행하도록 하려면 비동기 폴링 패턴을 사용할 수 있습니다. 이 패턴에서는 API가 별도의 스레드에서 작동하지만 Logic Apps 엔진에 대한 활성 연결을 유지합니다. 이렇게 하면 API에서 작업을 완료하기 전에 논리 앱에서 시간 초과하지 않거나 워크플로의 다음 단계로 계속 진행합니다.

다음은 일반적인 패턴입니다.

1. 엔진에서 API가 요청을 수락하고 작업을 시작했는지 확인합니다.
2. 엔진에서 작업 상태에 대한 후속 요청을 하면 API에서 작업을 완료할 때 엔진에 알립니다.
3. 논리 앱 워크플로를 계속할 수 있도록 관련 데이터를 엔진에 반환합니다.

<a name="bakery-polling-action"></a> 이제 폴링 패턴에 앞서의 빵집 유추를 적용하고, 빵집에 전화를 걸어 배달용 사용자 지정 케이크를 주문한다고 가정합니다. 케이크를 만드는 과정에 시간이 걸리고, 빵집에서 케이크를 만드는 동안은 빵집의 전화를 기다리지 않으려고 합니다. 빵집에서 주문을 확인하고 케이크 상태에 대해 20분마다 고객에게 전화를 겁니다. 20분이 지나면 빵집에 전화를 걸지만, 케이크가 아직 완성되지 않았고 20분 후에 고객이 다시 전화해야 한다고 알려줍니다. 이 앞뒤 과정은 고객이 전화할 때까지 계속되며, 빵집에서는 주문이 준비되어 케이크를 배달한다고 알려줍니다. 

이제 폴링 패턴을 다시 매핑해 보겠습니다. 빵집은 사용자 지정 API를 나타내고, 케이크를 주문한 고객은 Logic Apps 엔진을 나타냅니다. 엔진에서 요청을 통해 API를 호출하면, API는 요청을 확인하고 엔진에서 작업 상태를 확인할 수 있는 간격으로 응답합니다. API에서 작업을 완료했다고 응답하고 논리 앱에 데이터를 반환할 때까지 엔진에서는 작업 상태를 계속 확인한 다음 워크플로를 계속 진행합니다. 

![폴링 동작 패턴](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

API의 관점에서 설명하는 API의 구체적인 수행 단계는 다음과 같습니다.

1. API에서 작업을 시작하라는 HTTP 요청을 받으면 이 단계의 뒷부분에서 설명하는 `location` 헤더가 있는 HTTP `202 ACCEPTED` 응답을 즉시 반환합니다. Logic Apps 엔진에서는 이 응답을 통해 API가 요청을 받고 요청 페이로드(데이터 입력)를 수락하여 현재 처리 중임을 확인할 수 있습니다. 
   
   `202 ACCEPTED` 응답에는 다음과 같은 헤더가 포함되어야 합니다.
   
   * 필수: Logic Apps 엔진이 API의 작업 상태를 확인할 수 있는 URL의 절대 경로를 지정하는 `location` 헤더

   * *선택 사항*: 작업 상태에 대해 `location` URL을 확인하기 전에 엔진에서 대기해야 하는 시간(초)을 지정하는 `retry-after` 헤더 

     기본적으로 엔진은 매 20초마다 확인합니다. 다른 간격을 지정하려면 `retry-after` 헤더와 다음 폴링까지의 시간(초)을 포함합니다.

2. 지정된 시간이 지나면 Logic Apps 엔진에서 `location` URL을 폴링하여 작업 상태를 확인합니다. API는 이러한 검사를 수행하고 다음과 같은 응답을 반환해야 합니다.
   
   * 작업이 완료되면 응답 페이로드(다음 단계에 대한 입력)와 함께 HTTP `200 OK` 응답을 반환합니다.

   * 작업이 아직 처리 중인 경우 원래 응답과 동일한 헤더가 포함된 다른 HTTP `202 ACCEPTED` 응답을 반환합니다.

API가 이 패턴을 따르는 경우 작업 상태를 계속 확인하기 위해 논리 앱 워크플로 정의에서 어떤 작업도 수행할 필요가 없습니다. 엔진이 HTTP `202 ACCEPTED` 응답과 유효한 `location` 헤더를 가져오면 비동기 패턴을 고려하여 API에서 202가 아닌 응답을 반환할 때까지 엔진에서 `location` 헤더를 확인합니다.

> [!TIP]
> 비동기 패턴 예제는 [GitHub의 비동기 컨트롤러 응답 샘플](https://github.com/logicappsio/LogicAppsAsyncResponseSample)(영문)을 검토하세요.

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>웹후크 동작 패턴으로 장기 실행 작업 수행

대안으로 장기 실행 작업과 비동기 처리에 대해 웹후크 패턴을 사용할 수 있습니다. 이 패턴에는 논리 앱이 일시 중지되고 워크플로를 계속하기 전에 API로부터 "콜백"할 때까지 기다립니다. 이 콜백은 이벤트 발생 시 URL에 메시지를 보내는 HTTP POST입니다. 

<a name="bakery-webhook-action"></a> 이제 웹후크 패턴에 앞서의 빵집 유추를 적용하고, 빵집에 전화를 걸어 배달용 사용자 지정 케이크를 주문한다고 가정합니다. 케이크를 만드는 과정에 시간이 걸리고, 빵집에서 케이크를 만드는 동안은 빵집의 전화를 기다리지 않으려고 합니다. 빵집에서 주문을 확인하지만, 이번에는 케이크가 완성되었을 때 빵집에서 전화할 수 있도록 고객이 자신의 전화 번호를 빵집에 알려줍니다. 이번에는 주문이 준비되어 케이크를 배달할 때 빵집에서 고객에게 알려줍니다.

이 웹후크 패턴을 다시 매핑하는 경우 빵집은 사용자 지정 API를 나타내고, 케이크를 주문한 고객은 Logic Apps 엔진을 나타냅니다. 엔진에서 요청을 통해 API를 호출하고 "콜백" URL을 포함합니다.
작업이 완료되면 API에서 URL을 사용하여 엔진에 알리고, 논리 앱에 데이터를 반환하고, 워크플로를 계속 진행합니다. 

이 패턴의 경우 컨트롤러에 두 개의 엔드포인트(`subscribe` 및 `unsubscribe`)를 설정합니다.

*  `subscribe` 엔드포인트: 실행이 워크플로의 API 동작에 도달하면 Logic Apps 엔진에서 `subscribe` 엔드포인트를 호출합니다. 이 단계에서는 논리 앱이 API에서 저장하는 콜백 URL을 만든 다음, 작업이 완료될 때 API로부터 콜백을 기다립니다. 그런 다음 API에서 URL에 대한 HTTP POST를 통해 콜백하고 반환된 모든 내용과 헤더를 논리 앱에 대한 입력으로 전달합니다.

* `unsubscribe` 엔드포인트: 논리 앱이 실행 취소되면 Logic Apps 엔진에서 `unsubscribe` 엔드포인트를 호출합니다. 그런 다음 API에서 콜백 URL을 등록 취소하고 필요에 따라 모든 프로세스를 중지할 수 있습니다.

![웹후크 동작 패턴](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> 현재 Logic App Designer는 Swagger를 통한 웹후크 엔드포인트 검색을 지원하지 않습니다. 따라서 이 패턴의 경우 [**웹후크** 동작](../connectors/connectors-native-webhook.md)을 추가하고 요청에 대한 URL, 헤더 및 본문을 지정해야 합니다. [워크플로 작업 및 트리거](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action)도 참조하세요. 콜백 URL을 전달하려면 필요에 따라 이전 필드 중 하나에서 `@listCallbackUrl()` 워크플로 함수를 사용할 수 있습니다.

> [!TIP]
> 웹후크 패턴 예제는 [GitHub의 웹후크 트리거 샘플](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)(영문)을 검토하세요.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>트리거 패턴

사용자 지정 API는 [*트리거*](./logic-apps-overview.md#logic-app-concepts)의 역할을 수행하여 새 데이터 또는 이벤트가 지정된 조건을 충족할 때 논리 앱을 시작할 수 있습니다. 이 트리거는 서비스 엔드포인트에서 새 데이터 또는 이벤트를 정기적으로 확인하거나 대기 및 수신 대기할 수 있습니다. 새 데이터 또는 이벤트가 지정된 조건을 충족하면 트리거가 시작되고 해당 트리거를 수신하는 논리 앱이 시작됩니다. 이러한 방식으로 논리 앱을 시작하려면 API에서 [*폴링 트리거*](#polling-triggers) 또는 [*웹후크 트리거*](#webhook-triggers) 패턴을 따르면 됩니다. 이러한 패턴은 [폴링 동작](#async-pattern) 및 [웹후크 동작](#webhook-actions)의 해당 패턴과 비슷합니다. 또한 [트리거 사용량 측정](logic-apps-pricing.md)에 대해서도 자세히 알아보세요.

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>폴링 트리거 패턴으로 정기적인 새 데이터 또는 이벤트 확인

*폴링 트리거*는 이 항목의 앞부분에서 설명한 [폴링 동작](#async-pattern)과 매우 비슷하게 작동합니다. Logic Apps 엔진에서는 새 데이터 또는 이벤트에 대한 트리거 엔드포인트를 정기적으로 호출하고 확인합니다. 엔진에서 지정된 조건을 충족하는 새 데이터 또는 이벤트를 발견하면 트리거가 실행됩니다. 그런 다음 엔진에서 데이터를 입력으로 처리하는 논리 앱 인스턴스를 만듭니다. 

![폴링 트리거 패턴](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> 논리 앱 인스턴스를 만들지 않은 경우에도 각 폴링 요청은 동작 실행으로 간주됩니다. 동일한 데이터를 여러 번 처리하지 않도록 방지하려면 트리거에서 이미 읽고 논리 앱에 전달된 데이터를 정리해야 합니다.

API의 관점에서 설명하는 폴링 트리거의 구체적인 단계는 다음과 같습니다.

| 새 데이터 또는 이벤트가 있습니까?  | API 응답 | 
| ------------------------- | ------------ |
| 있음 | 응답 페이로드(다음 단계의 입력)와 함께 HTTP `200 OK` 상태를 반환합니다. <br/>이 응답은 논리 앱 인스턴스를 만들고 워크플로를 시작합니다. | 
| 찾을 수 없음 | `location` 헤더 및 `retry-after` 헤더와 함께 HTTP `202 ACCEPTED` 상태를 반환합니다. <br/>트리거의 경우 `location` 헤더에 `triggerState` 쿼리 매개 변수(일반적으로 "timestamp")도 있어야 합니다. API에서는 이 식별자를 사용하여 논리 앱이 트리거된 마지막 시간을 추적할 수 있습니다. | 
||| 

예를 들어 새 파일에 대한 서비스를 정기적으로 확인하려면 다음과 같은 동작을 포함하는 폴링 트리거를 빌드할 수 있습니다.

| 요청에 `triggerState`가 있습니까? | API 응답 | 
| -------------------------------- | -------------| 
| 아닙니다. | HTTP `202 ACCEPTED` 상태 및 현재 시간으로 설정된 `triggerState`와 15초로 설정된 `retry-after` 간격이 포함된 `location` 헤더를 반환합니다. | 
| 예 | `triggerState`에 대한 `DateTime` 이후에 추가된 파일에 대한 서비스를 확인합니다. | 
||| 

| 검색된 파일의 수 | API 응답 | 
| --------------------- | -------------| 
| 단일 파일 | HTTP `200 OK` 상태와 콘텐츠 페이로드를 반환하고, `triggerState`를 반환된 파일에 대한 `DateTime`으로 업데이트하고, `retry-after` 간격을 15초로 설정합니다. | 
| 여러 파일 | 한 번에 파일 하나씩 및 HTTP `200 OK` 상태를 반환하고, `triggerState`를 업데이트하고, `retry-after` 간격을 0초로 설정합니다. </br>이러한 단계를 통해 더 많은 데이터를 사용할 수 있고 엔진이 `location` 헤더의 URL에서 데이터를 즉시 요청해야 한다고 엔진에 알립니다. | 
| 파일 없음 | HTTP `202 ACCEPTED` 상태를 반환하고, `triggerState`를 변경하지 않고, `retry-after` 간격을 15초로 설정합니다. | 
||| 

> [!TIP]
> 폴링 트리거 패턴 예제는 [GitHub의 폴 트리거 컨트롤러 샘플](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs)(영문)을 검토하세요.

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>웹후크 트리거 패턴으로 새 데이터 또는 이벤트 대기 및 수신 대기

웹후크 트리거는 서비스 엔드포인트에서 새 데이터 또는 이벤트를 대기하고 수신 대기하는 *푸시 트리거*입니다. 새 데이터 또는 이벤트가 지정된 조건을 충족하면 트리거가 실행되고, 논리 앱 인스턴스가 만들어지고, 데이터가 입력으로 처리됩니다.
웹후크 트리거는 이 항목의 앞부분에서 설명한 [웹후크 동작](#webhook-actions)과 매우 비슷하게 작동하며 `subscribe` 및 `unsubscribe` 엔드포인트로 설정됩니다. 

* `subscribe` 엔드포인트: 논리 앱에 웹후크 트리거를 추가하고 저장하면 Logic Apps 엔진에서 `subscribe` 엔드포인트를 호출합니다. 이 단계에서는 논리 앱이 API에서 저장하는 콜백 URL을 만듭니다. 지정된 조건을 충족하는 새 데이터 또는 이벤트가 있으면 API에서 URL에 대한 HTTP POST를 통해 콜백합니다. 콘텐츠 페이로드와 헤더는 입력으로 논리 앱에 전달됩니다.

* `unsubscribe` 엔드포인트: 웹후크 트리거 또는 전체 논리 앱이 삭제되면 Logic Apps 엔진에서 `unsubscribe` 엔드포인트를 호출합니다. 그런 다음 API에서 콜백 URL을 등록 취소하고 필요에 따라 모든 프로세스를 중지할 수 있습니다.

![웹후크 트리거 패턴](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> 현재 Logic App Designer는 Swagger를 통한 웹후크 엔드포인트 검색을 지원하지 않습니다. 따라서 이 패턴의 경우 [**웹후크** 트리거](../connectors/connectors-native-webhook.md)를 추가하고 요청에 대한 URL, 헤더 및 본문을 지정해야 합니다. [HTTPWebhook 트리거](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger)도 참조하세요. 콜백 URL을 전달하려면 필요에 따라 이전 필드 중 하나에서 `@listCallbackUrl()` 워크플로 함수를 사용할 수 있습니다.
>
> 동일한 데이터를 여러 번 처리하지 않도록 방지하려면 트리거에서 이미 읽고 논리 앱에 전달된 데이터를 정리해야 합니다.

> [!TIP]
> 웹후크 패턴 예제는 [GitHub의 웹후크 트리거 컨트롤러 샘플](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)(영문)을 검토하세요.

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>논리 앱에서의 API 호출에 대해 보안 유지

사용자 지정 API를 만든 후 논리 앱에서 안전하게 호출할 수 있도록 API에 대한 인증을 설정합니다. [논리 앱에서의 사용자 지정 API 호출에 대해 보안을 유지하는 방법](../logic-apps/logic-apps-custom-api-authentication.md)을 알아봅니다.

## <a name="deploy-and-call-your-apis"></a>API 배포 및 호출

인증을 설정한 후에 API에 대한 배포를 설정합니다. [논리 앱에서 사용자 지정 API를 배포 및 호출하는 방법](../logic-apps/logic-apps-custom-api-host-deploy-call.md)을 알아봅니다.

## <a name="publish-custom-apis-to-azure"></a>Azure에 사용자 지정 API 게시

사용자 지정 API를 Azure의 다른 Logic Apps 사용자가 사용할 수 있게 하려면 보안을 추가하고 Logic Apps 커넥터로 등록해야 합니다. 자세한 내용은 [사용자 지정 커넥터 개요](../logic-apps/custom-connector-overview.md)를 참조하세요. 

사용자 지정 API를 Logic Apps, Microsoft Flow 및 Microsoft PowerApps의 모든 사용자가 사용할 수 있게 하려면 보안을 추가하고, API를 Logic Apps 커넥터로 등록하고, [Microsoft Azure 인증 프로그램](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)에 대한 커넥터로 지정해야 합니다. 

## <a name="get-support"></a>지원 받기

* 사용자 지정 API에 대한 구체적인 지원은 [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)에 문의해 주세요.

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.

* Logic Apps 개선에 도움을 주려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요. 

## <a name="next-steps"></a>다음 단계

* [오류 및 예외 처리](../logic-apps/logic-apps-exception-handling.md)
* [HTTP 엔드포인트를 통해 논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)
* [동작 및 트리거에 대한 사용량 측정](../logic-apps/logic-apps-pricing.md)
