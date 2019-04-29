---
title: Azure Functions의 OpenAPI 메타데이터 | Microsoft Docs
description: Azure Functions에서 OpenAPI 지원 개요
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 6d11961f06a75341e633c7a8963e6b83ed37cf13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341710"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions에서 OpenAPI 2.0 메타데이터 지원(미리 보기)
Azure Functions의 OpenAPI 2.0(이전의 Swagger) 메타데이터 지원은 함수 앱 내에 OpenAPI 2.0 정의를 작성하는 데 사용할 수 있는 미리 보기 기능입니다. 그런 다음 함수 앱을 사용하여 해당 파일을 호스팅할 수 있습니다.

> [!IMPORTANT]
> OpenAPI 미리 보기 기능은 현재 1.x 런타임에서만 사용 가능합니다. 1.x 함수 앱을 만드는 방법에 대한 내용은 [여기서 확인할 수 있습니다](./functions-versions.md#creating-1x-apps).

[OpenAPI 메타데이터](https://swagger.io/)는 다양한 다른 소프트웨어에서 REST API를 호스팅하는 함수를 사용할 수 있게 합니다. 이 소프트웨어에는 Microsoft 제품(예: PowerApps,[Azure App Service의 API Apps 기능](../app-service/overview.md)), 타사 개발자 도구(예: [Postman](https://www.getpostman.com/docs/importing_swagger)) 및 [다양한 추가 패키지](https://swagger.io/tools/)가 포함되어 있습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>우선 [초보자를 위한 자습서](./functions-api-definition-getting-started.md)부터 시작한 다음 이 문서로 돌아와 구체적 기능에 대해 알아보는 것이 좋습니다.

## <a name="enable"></a>OpenAPI 정의 지원 사용
함수 앱의 **플랫폼 기능**에 있는 **API 정의** 페이지에서 모든 OpenAPI 설정을 구성할 수 있습니다.

> [!NOTE]
> 함수 API 정의 기능은 현재 베타 런타임에서 지원되지 않습니다.

호스트된 OpenAPI 정의 및 빠른 시작 정의를 생성하도록 설정하려면 **API 정의 원본**을 **함수(미리 보기)** 로 설정합니다. **외부 URL**을 사용하면 함수가 다른 곳에 호스팅된 OpenAPI 정의를 사용할 수 있습니다.

## <a name="generate-definition"></a>함수의 메타데이터에서 Swagger 구조 생성
템플릿을 사용하면 첫 번째 OpenAPI 정의 작성을 시작할 수 있습니다. 정의 템플릿 기능은 각 HTTP 트리거 기능에 대해 function.json 파일의 모든 메타데이터를 사용하여 스파스 OpenAPI 정의를 만듭니다. [OpenAPI 사양](https://swagger.io/specification/)(예: 요청 및 응답 템플릿)에서 API에 대한 추가 정보를 입력해야 합니다.

단계별 지침은 [시작 자습서](./functions-api-definition-getting-started.md)를 참조하세요.

### <a name="templates"></a>사용 가능한 템플릿

|이름| 설명 |
|:-----|:-----|
|생성된 정의|최대 정보량을 포함하여 함수의 기존 메타데이터에서 유추할 수 있는 OpenAPI 정의|

### <a name="quickstart-details"></a>생성된 정의에 포함된 메타데이터

다음 표에서는 Azure Portal 설정과 생성된 Swagger 구조에 매핑된 function.json의 해당 데이터를 나타냅니다.

|Swagger.json|포털 UI|Function.json|
|:----|:-----|:-----|
|[호스트](https://swagger.io/specification/#fixed-fields-15)|**함수 앱 설정** > **App Service 설정** > **개요** > **URL**|*없음*
|[경로](https://swagger.io/specification/#paths-object-29)|**통합** > **선택한 HTTP 메서드**|바인딩: 라우팅
|[경로 항목](https://swagger.io/specification/#path-item-object-32)|**통합** > **경로 템플릿**|바인딩: 메서드
|[보안](https://swagger.io/specification/#security-scheme-object-112)|**키**|*없음*|
|operationID*|**경로 + 허용되는 동사**|경로 + 허용 동사|

\*작업 ID는 PowerApps 및 Flow와의 통합에만 필요합니다.
> [!NOTE]
> x-ms-summary 확장은 Logic Apps, PowerApps 및 Flow에서 표시 이름을 제공합니다.
>
> 자세한 내용은 [PowerApps에 대한 Swagger 정의 사용자 지정](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)을 확인하세요.

## <a name="CICD"></a>CI/CD를 사용하여 API 정의 설정

 원본 제어에서 API 정의를 수정할 수 있도록 설정하려면 먼저 포털에서 API 정의 호스팅을 활성화해야 합니다. 다음 지침을 따릅니다.

1. 함수 앱 설정에서 **API 정의(미리 보기)** 로 이동합니다.
   1. **API 정의 원본**을 **함수**로 설정합니다.
   1. 나중에 수정할 수 있도록 템플릿 정의를 만들려면 **API 정의 템플릿 생성**을 클릭한 다음 **저장**을 클릭합니다.
   1. API 정의 URL 및 키를 적어 둡니다.
1. [CI/CD(연속 통합/연속 배포)를 설정합니다](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. \site\wwwroot\.azurefunctions\swagger\swagger.json의 원본 제어에서 swagger.json을 수정합니다.

이제 리포지토리의 swagger.json에 대한 변경 내용이 1.c 단계에서 적어 둔 API 정의 URL과 키에서 함수 앱에 의해 호스팅됩니다.

## <a name="next-steps"></a>다음 단계
* [시작 자습서](functions-api-definition-getting-started.md) - 연습을 통해 작동 중인 OpenAPI 정의를 확인합니다.
* [Azure Functions GitHub 리포지토리](https://github.com/Azure/Azure-Functions/) - Functions 리포지토리를 확인하여 API 정의 지원 미리 보기에 대한 의견을 보냅니다. 업데이트하려는 항목에 대한 GitHub 문제를 제기합니다.
* [Azure Functions 개발자 참조](functions-reference.md) - 함수 코딩과 트리거 및 바인딩 정의에 대해 알아봅니다.
