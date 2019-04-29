---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5940195207f85d8011f61336c0318e456c2a8a4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300589"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>애플리케이션의 등록 정보를 사용하여 ASP.NET 웹앱 구성

이 단계에서는 SSL을 사용한 다음 SSL URL을 사용하여 애플리케이션 등록 정보를 구성하도록 프로젝트를 구성합니다. 그런 다음 *web.config*를 통해 솔루션에 애플리케이션 등록 정보를 추가합니다.

1. 솔루션 탐색기에서 프로젝트를 선택하고 `Properties` 창을 확인합니다([속성] 창이 보이지 않으면 F4 누르기).
2. `SSL Enabled`를 `True`로 변경
3. 위의 `SSL URL`에서 값을 복사해 이 페이지 맨 위에 있는 `Redirect URL` 필드에 붙여넣은 다음 [업데이트]를 클릭합니다.<br/><br/>![](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4. 다음 내용을 루트 폴더에 있는 `web.config` 파일의 `configuration\appSettings` 섹션 아래에 추가합니다.

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
