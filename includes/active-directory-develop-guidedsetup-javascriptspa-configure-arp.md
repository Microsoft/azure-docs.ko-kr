---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 7a6fa45d0f4ea09fbc84d3185aa0e58db165ac19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300620"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>앱에 애플리케이션의 등록 정보 추가

이 단계에서는 애플리케이션 등록 정보의 리디렉션 URL을 구성하고 JavaScript SPA 애플리케이션에 애플리케이션 ID를 추가해야 합니다.

### <a name="configure-redirect-url"></a>리디렉션 URL 구성

웹 서버에 따라 index.html 페이지에 대한 URL로 `Redirect URL` 필드를 구성하고 *업데이트*를 클릭합니다.

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>리디렉션 URL을 얻기 위한 Visual Studio 지침
> 리디렉션 URL을 가져오려면 다음 단계를 수행하세요.
> 1. **솔루션 탐색기**에서 프로젝트를 선택하고 **속성** 창을 확인합니다. **속성** 창이 보이지 않으면 **F4** 키를 누릅니다.
> 2. **URL**의 값을 클립보드로 복사합니다.<br/> ![](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. 이 페이지 맨 위에서 값을 **리디렉션 URL**로 붙여넣은 다음, **업데이트**를 선택합니다.

<p>

> #### <a name="setting-redirect-url-for-node"></a>Node에 대한 리디렉션 URL 설정
> Node.js의 경우 *server.js* 파일에서 웹 서버 포트를 설정할 수 있습니다. 이 자습서는 참조를 위해 포트 30662를 사용하지만 사용 가능한 다른 포트도 사용할 수 있습니다. 아래 지침에 따라 애플리케이션 등록 정보에 리디렉션 URL을 설정합니다.<br/>
> 이 페이지 맨 위에서 `http://localhost:30662/`를 **리디렉션 URL**로 설정하거나 사용자 지정 TCP 포트를 사용하는 경우 `http://localhost:[port]/`를 사용하고(여기서 *[port]* 는 사용자 지정 TCP 포트 번호임) **업데이트**를 클릭합니다.

### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA 애플리케이션 구성

1. 프로젝트 설정 중에 생성된 `index.html` 파일에서 애플리케이션 등록 정보를 추가합니다. 맨 위에 있는 다음 코드를 `index.html` 파일 본문의 `<script></script>` 태그 내에 추가합니다.

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
