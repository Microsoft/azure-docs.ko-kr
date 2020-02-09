---
title: Postman을 구성 하는 방법-Azure Digital Twins | Microsoft Docs
description: Postman을 구성 하 고 사용 하 여 Azure Digital Twins Api를 테스트 하는 방법을 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 377639d7a88478308709743ab842db71028686ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023313"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Azure Digital Twins용 Postman을 구성하는 방법

이 문서에서는 Azure Digital Twins 관리 API와 상호 작용하고 테스트하기 위해 Postman REST 클라이언트를 구성하는 방법을 설명합니다. 구체적으로 다음을 설명합니다.

* OAuth 2.0 암시적 허용 흐름을 사용하도록 Azure Active Directory 애플리케이션을 구성하는 방법
* Postman REST 클라이언트를 사용하여 관리 API에 토큰-관련 HTTP 요청을 수행하는 방법
* Postman을 사용하여 관리 API에 대해 다중 파트 POST 요청을 수행하는 방법

## <a name="postman-summary"></a>Postman 요약

[Postman](https://www.getpostman.com/)과 같은 REST 클라이언트 도구를 사용하여 Azure Digital Twins를 시작하여 로컬 테스트 환경을 준비합니다. Postman 클라이언트를 통해 복잡한 HTTP 요청을 신속하게 만들 수 있습니다. [www.getpostman.com/apps](https://www.getpostman.com/apps)로 이동하여 Postman 클라이언트의 데스크톱 버전을 다운로드합니다.

[Postman](https://www.getpostman.com/)은 유용한 데스크톱 및 플러그 인 기반 GUI에 대한 주요 HTTP 요청을 찾는 REST 테스트 도구입니다.

Postman 클라이언트를 통해 솔루션 개발자는 HTTP 요청의 종류(*POST*, *GET*, *UPDATE*, *PATCH* 및 *DELETE*), 호출할 API 엔드포인트 및 SSL의 사용을 지정할 수 있습니다. Postman은 또한 HTTP 요청 헤더, 매개 변수, 양식 데이터 및 본문 추가를 지원합니다.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>OAuth 2.0 암시적 허용 흐름을 사용하도록 Azure Active Directory를 구성합니다.

1. [빠른](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) 시작의 단계에 따라 Azure Active Directory 응용 프로그램을 만들고 구성 합니다. 또는 기존 앱 등록을 다시 사용할 수 있습니다.

    [새 Postman 리디렉션 URI를 구성 ![](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. 이제 `https://www.getpostman.com/oauth2/callback`에 **리디렉션 URI** 를 추가 합니다.

1.  > **액세스 토큰** **암시적 부여** 확인란을 선택 하 여 OAuth 2.0 암시적 허용 흐름을 사용할 수 있습니다. **구성**을 선택한 다음 **저장**을 선택 합니다.

1. Azure Active Directory 앱의 **클라이언트 ID** 를 복사 합니다.

## <a name="obtain-an-oauth-20-token"></a>OAuth 2.0 토큰 가져오기

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Azure Active Directory 토큰을 가져오기 위해 Postman을 설정 하 고 구성 합니다. 그런 다음, 획득한 토큰을 사용하여 Azure Digital Twins로 인증된 HTTP 요청을 만듭니다.

1. **권한 부여 URL**이 올바른지 확인합니다. 다음과 같은 형식이어야 합니다.

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | 이름  | 다음 항목으로 교체 | 예 |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | 테넌트 또는 조직의 이름입니다. Azure Active Directory 앱 등록의 영숫자 **테넌트 ID** 대신 사용자에 게 친숙 한 이름을 사용 합니다. | `microsoft` |

1. [www.getpostman.com](https://www.getpostman.com/)으로 이동하여 앱을 다운로드합니다.

1. GET 요청을 수행 하려고 합니다. **권한 부여** 탭을 선택 하 고 OAuth 2.0을 선택한 다음 **새 액세스 토큰 가져오기**를 선택 합니다.

    | 필드  | 값 |
    |---------|---------|
    | 권한 부여 유형 | `Implicit` |
    | 콜백 URL | `https://www.getpostman.com/oauth2/callback` |
    | 인증 URL | **1 단계의** **권한 부여 URL** 사용 |
    | 클라이언트 ID | 이전 섹션에서 만들었거나 다시 사용한 Azure Active Directory 앱의 **응용 프로그램 ID** 를 사용 합니다. |
    | 범위 | 비워 둠 |
    | 상태 | 비워 둠 |
    | 클라이언트 인증 | `Send as Basic Auth header` |

1. 클라이언트는 이제 다음과 같아야 합니다.

    [![Postman 클라이언트 토큰 예제](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. **토큰 요청**을 선택합니다.
  
1. 아래로 스크롤하고 **토큰 사용**을 선택합니다.

## <a name="make-a-multipart-post-request"></a>다중 파트 POST 요청 수행

이전 단계를 완료한 후 인증된 HTTP 다중 파트 POST 요청을 수행하도록 Postman을 구성합니다.

1. **헤더** 탭에서 값 `multipart/mixed`를 사용 하 여 HTTP 요청 헤더 키 **콘텐츠 형식을** 추가 합니다.

   [내용 유형을 multipart/mixed로 지정 ![](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. 텍스트가 아닌 데이터를 파일로 직렬화합니다. JSON 데이터는 JSON 파일로 저장됩니다.
1. **본문** 탭에서 `form-data`를 선택 합니다. 
1. **키** 이름을 할당 하 여 각 파일을 추가 하 고 `File`를 선택 합니다.
1. 그런 후 **파일 선택** 단추를 사용하여 각 파일을 선택합니다.

   [![Postman 클라이언트 양식 본문 예제](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * Postman 클라이언트에서는 다중 파트 청크에 **Content-Type** 또는 **Content-Disposition**을 수동으로 할당할 필요가 없습니다.
   > * 각 파트에 대해 해당 헤더를 지정할 필요가 없습니다.
   > * 전체 요청에 대해서는 `multipart/mixed` 또는 다른 적절한 **Content-Type**을 선택해야 합니다.

1. 마지막으로, **보내기** 를 선택 하 여 MULTIPART HTTP POST 요청을 제출 합니다. `200` 또는 `201`의 상태 코드는 성공적인 요청을 나타냅니다. 적절 한 응답 메시지가 클라이언트 인터페이스에 표시 됩니다.

1. API 엔드포인트을 호출 하 여 HTTP POST 요청 데이터의 유효성을 검사 합니다. 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>다음 단계

- Digital Twins 관리 API 및 이를 사용하는 방법에 대해 자세히 알아보려면 [Azure Digital Twins 관리 API 사용 방법](how-to-navigate-apis.md)을 읽어보세요.

- 다중 파트 요청을 사용하여 [Azure Digital Twins 엔터티에 blob을 추가](./how-to-add-blobs.md)합니다.

- Management API를 사용하여 인증에 대해 알아보려면 [API를 사용하여 인증](./security-authenticating-apis.md)을 참조하세요.
