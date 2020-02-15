---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149078"
---
## <a name="guidelines-for-using-javascript"></a>JavaScript 사용 지침

JavaScript를 사용하여 애플리케이션의 인터페이스를 사용자 지정하는 경우 다음 지침을 따르세요.

- `<a>` HTML 요소에 클릭 이벤트를 바인딩하지 않습니다.
- Azure AD B2C 코드 또는 주석에 대한 종속성을 사용하지 않습니다.
- Azure AD B2C HTML 요소의 순서나 계층 구조를 변경하지 않습니다. Azure AD B2C 정책을 사용하여 UI 요소의 순서를 제어합니다.
- 다음 사항을 고려하여 RESTful 서비스를 호출할 수 있습니다.
    - 클라이언트 쪽 HTTP 호출을 허용하도록 RESTful 서비스 CORS를 설정해야 할 수 있습니다.
    - RESTful 서비스가 안전하고 HTTPS 프로토콜만 사용하는지 확인합니다.
    - JavaScript를 사용하여 Azure AD B2C 엔드포인트를 직접 호출하지 않습니다.
- JavaScript를 포함하거나 외부 JavaScript 파일에 연결할 수 있습니다. 외부 JavaScript 파일을 사용하는 경우 상대 URL이 아닌 절대 URL을 사용해야 합니다.
- JavaScript 프레임워크:
    - Azure AD B2C에서는 특정 버전의 jQuery를 사용합니다. 다른 버전의 jQuery를 포함하지 않습니다. 동일한 페이지에 둘 이상의 버전을 사용하면 문제가 발생합니다.
    - RequireJS 사용은 지원되지 않습니다.
    - 대부분의 JavaScript 프레임워크는 Azure AD B2C에서 지원되지 않습니다.
- `window.SETTINGS`, `window.CONTENT` 개체를 호출하여 현재 UI 언어와 같은 Azure AD B2C 설정을 읽을 수 있습니다. 이러한 개체의 값을 변경하지 않습니다.
- Azure AD B2C 오류 메시지를 사용자 지정하려면 정책의 지역화를 사용합니다.
- 정책을 통해 수행할 수 있는 작업이 있다면 일반적으로 정책을 사용하는 것이 좋습니다.
