---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/03/2016
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 4d5c8373f2a8f02ccfbe51cb970ab08744950e7e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128216"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

웹 애플리케이션을 등록하려면 표에 지정된 설정을 사용합니다.

![새 웹앱에 대한 등록 설정 예](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| 설정      | 샘플 값  | 설명                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name** | Contoso B2C 앱 | 소비자에게 애플리케이션을 설명하는 애플리케이션 **이름**을 입력합니다. | 
| **웹앱/웹 API 포함** | 예 | 웹 애플리케이션에 **예**를 선택합니다. |
| **암시적 흐름 허용** | 예 | 애플리케이션이 [OpenID Connect 로그인](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)을 사용할 경우 **예** 선택 |
| **회신 URL** | `https://localhost:44316` | 회신 URL은 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트입니다. [적절한](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **회신 URL**을 입력합니다. 이 예에서는 앱이 로컬이고 포트 44316에서 수신하고 있습니다. |

**만들기**를 클릭하여 애플리케이션을 등록합니다.

새로 등록된 애플리케이션은 B2C 테넌트에 대한 애플리케이션 목록에 표시됩니다. 목록에서 웹앱을 선택합니다. 웹 애플리케이션의 속성 창이 표시 됩니다.

![웹앱 속성](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

전역적으로 고유한 **애플리케이션 클라이언트 ID**를 기록합니다. 애플리케이션 코드의 ID를 사용합니다.

