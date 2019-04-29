---
title: Azure Mobile Apps용 JavaScript SDK를 사용하는 방법
description: Azure Mobile Apps에 v를 사용하는 방법
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 16871bdc59d141334bc2c95f26929f270d7971cf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106747"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Azure Mobile Apps용 JavaScript 클라이언트 라이브러리를 사용하는 방법
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

이 가이드에서는 최신 [Azure Mobile Apps용 JavaScript SDK]를 사용하여 일반적인 시나리오를 수행하는 방법을 알려줍니다. Azure Mobile Apps를 처음 접하는 경우 먼저 [Azure Mobile Apps 빠른 시작] 을 완료하여 백 엔드를 만들고 테이블을 만듭니다. 이 가이드에서는 HTML/JavaScript 웹 애플리케이션에서 모바일 백 엔드를 사용하는 데 초점을 둡니다.

## <a name="supported-platforms"></a>지원되는 플랫폼
브라우저 지원을 다음 주요 브라우저의 최신 버전으로 제한합니다.  Google Chrome, Microsoft Edge, Microsoft Internet Explorer 및 Mozilla Firefox.  SDK가 비교적 최신 브라우저와는 호환될 것으로 예상됩니다.

패키지는 범용 JavaScript 모듈로 배포되므로 전역, AMD 및 CommonJS 서식을 지원합니다.

## <a name="Setup"></a>설정 및 필수 조건
이 가이드에서는 테이블과 함께 백 엔드를 만들었다고 가정합니다. 이 가이드에서는 해당 테이블에 이러한 자습서의 테이블과 동일한 스키마가 있다고 가정합니다.

`npm` 명령을 통해 Azure Mobile Apps JavaScript SDK를 설치할 수 있습니다.

```
npm install azure-mobile-apps-client --save
```

또한 라이브러리는 Browserify 및 Webpack과 같은 CommonJS 환경 내에서 ES2015 모듈로 사용할 수 있으며 AMD 라이브러리로 사용할 수도 있습니다.  예를 들면 다음과 같습니다.

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

또한 우리의 CDN에서 직접 다운로드 하 여 미리 작성 된 버전의 SDK 사용할 수 있습니다.

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>방법: 사용자 인증
Azure App Service는 Facebook, Google, Microsoft 계정 및 Twitter와 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 [인증 시작] 자습서를 참조하십시오.

두 가지의 인증 흐름, 즉 서버 흐름과 클라이언트 흐름이 지원됩니다.  서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급 기업별 SDK를 사용하므로 Single Sign-On과 같은 디바이스 특정 기능을 통해 심도 깊은 통합이 가능합니다.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>방법: 외부 리디렉션 URL에 대해 모바일 App Service 구성
여러 가지 유형의 JavaScript 애플리케이션은 루프백 기능을 사용하여 OAuth UI 흐름을 처리합니다.  이러한 기능은 다음과 같습니다.

* 로컬로 서비스 실행
* Ionic Framework와 라이브 다시 로드 사용
* 인증을 위해 App Service로 리디렉션

로컬로 실행하면 기본적으로 App Service 인증이 모바일 앱 백 엔드에서 액세스만 허용하도록 구성되므로 문제가 발생할 수 있습니다. 다음 단계에 따라 App Service 설정을 변경하여 서버를 로컬로 실행할 때 인증을 사용하도록 설정합니다.

1. [Azure Portal]에 로그인
2. 모바일 앱 백 엔드로 이동합니다.
3. **개발 도구** 메뉴에서 **리소스 Explorer**를 선택합니다.
4. **이동** 을 클릭하여 새 탭 또는 창에서 모바일 앱 백 엔드에 대한 리소스 Explorer를 엽니다.
5. 앱에 대한 **config** > **authsettings** 노드를 확장합니다.
6. **편집** 단추를 클릭하여 리소스의 편집을 활성화합니다.
7. null이어야 하는 **allowedExternalRedirectUrls** 요소를 찾습니다. 배열에 URL을 추가합니다.

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    배열의 URL을 서비스 URL로 바꿉니다. 이 예에서 로컬 Node.js 샘플 서비스의 경우 `http://localhost:3000`입니다. 또한 앱이 구성된 방식에 따라 Ripple 서비스 또는 기타 URL에 대해 `http://localhost:4400`을 사용할 수도 있습니다.
8. 페이지 맨 위에서 **읽기/쓰기**를 클릭한 후 **PUT**을 클릭하여 업데이트를 저장합니다.

또한 CORS 허용 목록 설정에 동일한 루프백 URL을 추가해야 합니다.

1. [Azure Portal]로 다시 이동합니다.
2. 모바일 앱 백 엔드로 이동합니다.
3. **API** 메뉴에서 **CORS**를 클릭합니다.
4. 빈 **허용된 원본** 텍스트 상자에 각 URL을 입력합니다.  새 텍스트 상자가 생성됩니다.
5. **저장**을 클릭합니다.

백 엔드가 업데이트되면 앱에서 새 루프백 URL을 사용할 수 있습니다.

<!-- URLs. -->
[Azure Mobile Apps 빠른 시작]: app-service-mobile-cordova-get-started.md
[인증 시작]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portal]: https://portal.azure.com/
[Azure Mobile Apps용 JavaScript SDK]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
