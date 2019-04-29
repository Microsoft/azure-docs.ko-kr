---
title: Azure Digital Twins 참조 Swagger를 사용하는 방법 이해 | Microsoft Docs
description: Azure Digital Twins Swagger 참조 설명서를 사용하는 방법을 알아봅니다.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 1746e1d53be01e6c40b5d1948c666960970b75a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922999"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Azure Digital Twins Swagger 참조 설명서

프로비전된 Azure Digital Twins 인스턴스에는 각각 자동으로 생성된 고유한 Swagger 참조 설명서가 포함됩니다.

[Swagger](https://swagger.io/) 또는 [OpenAPI](https://www.openapis.org/)는 복잡한 API 정보를 대화형 및 언어 중립적 참조 리소스로 통합합니다. Swagger는 API에 대한 작업을 수행하는 데 사용할 JSON 페이로드, HTTP 메서드 및 특정 엔드포인트에 대한 중요 참고 자료를 제공합니다.

## <a name="swagger-summary"></a>Swagger 요약

Swagger는 다음을 비롯한 API의 대화형 요약을 제공합니다.

* API 및 개체 모델 정보.
* 필수 요청 페이로드, 헤더, 매개 변수, 컨텍스트 경로 및 HTTP 메서드를 지정하는 REST API 엔드포인트.
* API 기능 테스트
* HTTP 응답의 유효성을 검사하고 확인하는 예제 응답 정보.
* 오류 코드 정보

Swagger는 Azure Digital Twins 관리 API에 대한 호출 테스트와 개발을 보조하는 편리한 도구입니다.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>참조 자료

자동으로 생성되는 Swagger 참조 자료는 중요한 개념에 대한 간단한 개요, 사용 가능한 관리 API 엔드포인트 및 개발과 테스트를 보조하는 각 개체 모델에 대한 설명을 제공합니다.

간결한 요약에서는 API를 설명합니다.

![Swagger 상위][1]

관리 API 개체 모델도 나열됩니다.

![Swagger 모델][2]

키 특성의 더 자세한 요약은 각 나열된 개체 모델을 선택하면 됩니다.

![Swagger 모델][3]

생성된 Swagger 개체 모델은 사용 가능한 모든 Azure Digital Twins [개체 및 API](./concepts-objectmodel-spatialgraph.md)를 보는 데 편리합니다. 개발자는 Azure Digital Twins에서 솔루션을 빌드할 때 이 리소스를 사용할 수 있습니다.

## <a name="endpoint-summary"></a>엔드포인트 요약

또한 Swagger는 관리 API를 구성하는 모든 엔드포인트에 대한 철저한 개요를 제공합니다.

나열된 각 엔드포인트에는 다음과 같은 필수 요청 정보도 포함됩니다.

* 필수 매개 변수
* 필수 매개 변수 데이터 형식
* 리소스에 액세스하는 HTTP 메서드.

![Swagger 엔드포인트][4]

더 자세한 개요를 보려면 각 리소스를 선택합니다.

## <a name="use-swagger-to-test-endpoints"></a>Swagger를 사용하여 엔드포인트 테스트

Swagger의 강력한 기능 중 하나는 문서 UI를 통해 직접 API 엔드포인트를 테스트할 수 있는 기능입니다.

특정 엔드포인트를 선택하면 **사용해보기**가 표시됩니다.

![Swagger 사용][5]

해당 섹션을 확장하면 각 필수 및 선택적 매개 변수에 대한 입력 필드가 표시됩니다. 올바른 값을 입력하고 **실행**을 선택합니다.

![Swagger 사용됨][6]

테스트를 실행한 후 응답 데이터의 유효성을 검사할 수 있습니다.

## <a name="swagger-response-data"></a>Swagger 응답 데이터

나열된 각 엔드포인트에는 개발 및 테스트의 유효성을 검사하는 응답 본문 데이터도 포함됩니다. 이러한 예제에는 성공적인 HTTP 요청에 대해 원하는 상태 코드 및 JSON이 포함됩니다.

![Swagger 응답][7]

예제에는 실패한 테스트를 디버그하거나 개선하는 오류 코드도 포함됩니다.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0 권한 부여

OAuth 2.0으로 보호되는 요청을 대화형으로 테스트하는 방법에 대해 자세히 알아보려면 [공식 설명서](https://swagger.io/docs/specification/authentication/oauth2/)를 참조하세요.

> [!NOTE]
> Azure 디지털 쌍 리소스를 만든 사용자 보안 주체는 공간 관리자 역할 할당을 있고 다른 사용자에 대 한 추가 역할 할당을 만들 수 있게 됩니다.

1. 단계를 따릅니다 [이 빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) 유형의 Azure AD 응용 프로그램을 만들려면 ***웹 앱 / API***합니다. 또는 기존 앱 등록을 다시 사용할 수 있습니다.

2. 앱 등록에 다음 회신 url을 추가 합니다.

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | 이름  | 다음 항목으로 교체 | 예 |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | 포털에 있는 관리 REST API 설명서 URL  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Azure 디지털 쌍에 액세스 하려면 앱에 대 한 권한을 부여 합니다. **필수 사용 권한** 아래에서 `Azure Digital Twins`를 입력하고 **위임된 권한**을 선택합니다. 그런 다음, **사용 권한 부여**를 선택합니다.

    ![Azure AD 앱 등록 api 추가](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. OAuth 2.0 암시적 흐름을 허용 하도록 응용 프로그램 매니페스트를 구성 합니다. **매니페스트**를 클릭하여 앱에 대한 애플리케이션 매니페스트를 엽니다. *oauth2AllowImplicitFlow*를 `true`로 설정합니다.

    ![Azure AD 암시적 흐름](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Azure AD 앱의 ID를 복사 합니다.

6. Swagger 페이지에서 권한 부여 단추를 클릭 합니다.

    ![Swagger를 권한 부여 단추](../../includes/media/digital-twins-permissions/swagger-select-authorize-btn.png)

7. Client_id 필드에 응용 프로그램 ID를 붙여 넣습니다.

    ![Swagger client_id 필드](../../includes/media/digital-twins-permissions/swagger-auth-form.png)

    ![Swagger 응용 프로그램 권한 부여](../../includes/media/digital-twins-permissions/swagger-grant-application-permissions.png)

8. 이제 전달자 표시 결과에 표시 되는 로그인된 한 사용자의 id 및 권한 부여 헤더에서 인증 토큰 전달 합니다.

    ![Swagger 토큰 결과](../../includes/media/digital-twins-permissions/swagger-token-example.png)

## <a name="next-steps"></a>다음 단계

- Azure Digital Twins 개체 모델 및 공간 인텔리전스 그래프에 대해 자세히 알아보려면 [Azure Digital Twins 개체 모델 이해](./concepts-objectmodel-spatialgraph.md)를 읽어보세요.

- 관리 API를 사용하여 인증하는 방법을 알아보려면 [API를 사용하여 인증](./security-authenticating-apis.md)을 읽어보세요.

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
