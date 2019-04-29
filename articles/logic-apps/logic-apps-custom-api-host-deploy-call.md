---
title: Azure Logic Apps에서 웹 API 및 REST API 배포 및 호출 | Microsoft Docs
description: Azure Logic Apps에서 시스템 통합 워크플로에 대한 웹 API 및 REST API 배포 및 호출
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: a9049ba1fbd7d3bdce061d277f6a7a02d9b1e4b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740414"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로에서 사용자 지정 API 배포 및 호출

논리 앱 워크플로에서 사용할 [사용자 지정 API를 만든](./logic-apps-create-api-app.md) 후에 API를 먼저 배포해야 호출할 수 있습니다. API를 [웹앱](../app-service/overview.md)으로 배포할 수 있지만 [API 앱](../app-service/app-service-web-tutorial-rest-api.md)으로 배포하는 것이 좋습니다. 이렇게 하면 클라우드에서 API를 빌드, 호스팅 및 사용할 때 작업을 더 쉽게 수행할 수 있습니다. API에서 코드를 변경할 필요가 없이 API 앱에 코드를 배포하기만 하면 됩니다. 확장성이 뛰어나고 쉬운 API 호스팅을 제공하는 PaaS(Platform-as-a-Service) 제품인 [Azure App Service](../app-service/overview.md)에서 API를 호스트할 수 있습니다.

논리 앱에서 원하는 API를 호출할 수 있지만 최상의 환경을 위해 API 작업 및 매개 변수를 설명하는 [OpenAPI(이전 Swagger) 메타데이터](https://swagger.io/specification/)를 추가합니다. OpenAPI 파일을 사용하면 API를 논리 앱과 보다 쉽게 통합하고 효과적으로 사용할 수 있습니다.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>API를 웹앱 또는 API 앱으로 배포

논리 앱에서 사용자 지정 API를 호출하려면 먼저 API를 웹앱 또는 API 앱으로 Azure App Service에 배포해야 합니다. 또한 Logic Apps Designer에서 OpenAPI 파일을 읽을 수 있게 하려면 API 정의 속성을 설정하고 웹앱 또는 API 앱에 대해 [CORS(원본 간 리소스 공유)](../app-service/overview.md)를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)에서 웹앱 또는 API 앱을 선택합니다.

2. 열린 메뉴의 **API** 아래에서 **API 정의**을 선택합니다. **API 정의 위치**를 OpenAPI swagger.json 파일의 URL로 설정합니다.

   일반적으로 URL은 `https://{name}.azurewebsites.net/swagger/docs/v1)` 형식으로 표시됩니다.

   ![사용자 지정 API에 대한 OpenAPI 파일에 연결](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. **API** 아래에서 **CORS**를 선택합니다. **허용된 원본**에 대한 CORS 정책을 **'*'**(모두 허용)로 설정합니다.

   이 설정은 Logic App Designer의 요청을 허용합니다.

   ![Logic App Designer에서 사용자 지정 API로의 요청 허용](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

자세한 내용은 [Azure App Service에서 CORS를 통해 RESTful API 호스팅](../app-service/app-service-web-tutorial-rest-api.md)을 참조하세요.

## <a name="call-your-custom-api-from-logic-app-workflows"></a>논리 앱 워크플로에서 사용자 지정 API 호출

API 정의 속성과 CORS를 설정한 후에는 사용자 지정 API의 트리거 및 동작은 논리 앱 워크플로에 포함할 수 있습니다. 

*  OpenAPI URL이 있는 웹 사이트를 보려면 Logic Apps Designer에서 구독 웹 사이트를 찾아보면 됩니다.

*  OpenAPI 문서를 가리켜 사용할 수 있는 동작과 입력을 보려면 [HTTP + Swagger 동작](../connectors/connectors-native-http-swagger.md)을 사용합니다.

*  OpenAPI 문서가 없거나 공개되지 않는 API를 포함하여 모든 API를 호출하려면 언제든지 [HTTP 동작](../connectors/connectors-native-http.md)을 사용하여 요청을 만들면 됩니다.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 커넥터 개요](../logic-apps/custom-connector-overview.md)