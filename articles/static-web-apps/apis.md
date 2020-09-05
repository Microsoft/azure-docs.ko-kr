---
title: Azure Functions를 사용하여 Azure Static Web Apps에서 API 지원
description: Azure Static Web Apps가 지원하는 API 기능 알아보기
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 09daffa74ccd279c8187391ba3b86063aed7d204
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607031"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions를 사용하여 Azure Static Web Apps 미리 보기에서 API 지원

Azure Static Web Apps는 [Azure Functions](../azure-functions/functions-overview.md)를 통해 서버리스 API 엔드포인트를 제공합니다. API는 Azure Functions를 활용하여 수요에 따라 동적으로 확장되며 다음 기능을 포함합니다.

- 사용자 [인증 및 역할 기반 권한 부여](user-information.md) 데이터에 직접 액세스할 수 있는 **통합 보안**.
- 사용자 지정 CORS 규칙 없이 웹앱에서 _api_ 경로를 안전하게 사용할 수 있도록 하는 **원활한 라우팅**.
- Node.js 12와 호환되는 **Azure Functions** v3.
- **HTTP 트리거** 및 출력 바인딩.

## <a name="configuration"></a>구성

API 엔드포인트는 _api_ 경로를 통해 웹앱에서 사용할 수 있습니다. 이 경로를 수정하는 동안 연결된 Azure Functions 앱을 찾는 폴더를 제어할 수 있습니다. 리포지토리의 _.github/workflows_에 있는 [워크플로 YAML 파일을 편집](github-actions-workflow.md#build-and-deploy)하여 이 위치를 변경할 수 있습니다.

## <a name="constraints"></a>제약 조건

Azure Static Web Apps는 Azure Functions를 통해 API를 제공합니다. Azure Functions의 기능은 웹앱용 API를 만들고 웹앱이 API에 안전하게 연결할 수 있도록 하는 특정 기능 세트에 중점을 둡니다. 이러한 기능에는 다음을 비롯한 몇 가지 제약 조건이 있습니다.

- API 경로 접두사는 _api_여야 합니다.
- API 함수 앱은 JavaScript에 있어야 합니다.
- API 함수에 대 한 경로 규칙은 역할이 있는 [리디렉션](routes.md#redirects) 및 [보안 경로만](routes.md#securing-routes-with-roles)지원 합니다.
- 트리거는 [HTTP](../azure-functions/functions-bindings-http-webhook.md)로 제한 됩니다.
  - 입력 및 출력 [바인딩이](../azure-functions/functions-triggers-bindings.md#supported-bindings) 지원 됩니다.
- 로그는 함수 앱에 [Application Insights](../azure-functions/functions-monitoring.md)를 추가하는 경우에만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
