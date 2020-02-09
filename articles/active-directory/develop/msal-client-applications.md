---
title: MSAL (공용 및 비밀 클라이언트 앱) | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL (Microsoft 인증 라이브러리)의 공용 클라이언트 및 기밀 클라이언트 응용 프로그램에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d59819c0ab614b0f6cc102c7ebe8c760fb851599
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084128"
---
# <a name="public-client-and-confidential-client-applications"></a>공용 클라이언트 및 기밀 클라이언트 응용 프로그램
MSAL (Microsoft 인증 라이브러리)은 두 가지 유형의 클라이언트, 즉 공용 클라이언트와 기밀 클라이언트를 정의 합니다. 두 클라이언트 유형은 권한 부여 서버와 안전 하 게 인증 하 고 클라이언트 자격 증명의 기밀성을 유지 하는 기능으로 구분 됩니다. 반면, ADAL (Azure AD 인증 라이브러리)은 Azure AD에 대 한 연결 인 *인증 컨텍스트* 를 사용 합니다.

- **기밀 클라이언트 응용 프로그램** 은 서버 (웹 앱, 웹 API 앱 또는 서비스/디먼 앱)에서 실행 되는 앱입니다. 액세스 하기 어렵고 응용 프로그램 암호를 유지할 수 있는 이유 때문입니다. 기밀 클라이언트는 구성 시간 암호를 보유할 수 있습니다. 클라이언트의 각 인스턴스에는 개별 구성 (클라이언트 ID 및 클라이언트 암호 포함)이 있습니다. 이러한 값은 최종 사용자가 추출 하기 어렵습니다. 웹 앱은 가장 일반적인 기밀 클라이언트입니다. 클라이언트 ID는 웹 브라우저를 통해 노출 되지만 비밀은 백 채널에만 전달 되 고 직접 노출 되지 않습니다.

    기밀 클라이언트 앱: <BR>
    웹 앱](media/msal-client-applications/web-app.png) ![웹 API](media/msal-client-applications/web-api.png) ![데몬/서비스](media/msal-client-applications/daemon-service.png)를 ![합니다.

- **공용 클라이언트 응용 프로그램** 은 장치 또는 데스크톱 컴퓨터에서 또는 웹 브라우저에서 실행 되는 앱입니다. 응용 프로그램 암호를 안전 하 게 유지 하는 것은 신뢰 되지 않으므로 사용자를 대신 하 여 Web Api에 액세스 하기만 하면 됩니다. (공용 클라이언트 흐름이 지원 됩니다.) 공용 클라이언트는 구성 타임 암호를 포함할 수 없으므로 클라이언트 암호를 갖지 않습니다.

    공용 클라이언트 앱: <BR>
    ![데스크톱 앱](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![모바일 앱](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL에서는 공용 및 기밀 클라이언트 앱을 분리 하지 않습니다.  MSAL는 클라이언트 앱을 사용자 에이전트 기반 앱으로, 클라이언트 코드가 웹 브라우저와 같은 사용자 에이전트에서 실행 되는 공용 클라이언트를 나타냅니다. 브라우저 컨텍스트는 공개적으로 액세스할 수 있기 때문에 이러한 클라이언트는 비밀을 저장 하지 않습니다.

## <a name="comparing-the-client-types"></a>클라이언트 유형 비교
공용 클라이언트와 기밀 클라이언트 앱 간의 유사점과 차이점은 다음과 같습니다.

- 두 종류의 앱 모두 사용자 토큰 캐시를 유지 하 고 토큰을 자동으로 가져올 수 있습니다 (토큰이 토큰 캐시에 이미 있는 경우). 기밀 클라이언트 앱에는 앱 자체에 대 한 토큰에 대 한 앱 토큰 캐시도 있습니다.
- 두 앱 유형 모두 사용자 계정을 관리 하 고 사용자 토큰 캐시에서 계정을 가져오거나, 해당 id에서 계정을 가져오거나, 계정을 제거할 수 있습니다.
- 공용 클라이언트 앱에는 토큰을 획득 하는 네 가지 방법 (4 개의 인증 흐름)이 있습니다. 기밀 클라이언트 앱에는 토큰을 획득 하는 세 가지 방법, 즉 id 공급자 권한 부여 엔드포인트의 URL을 계산 하는 한 가지 방법이 있습니다. 자세한 내용은 [토큰 가져오기](msal-acquire-cache-tokens.md)를 참조 하세요.

ADAL을 사용한 경우 ADAL의 인증 컨텍스트와 달리 MSAL에서 클라이언트 ID ( *응용 프로그램 id* 또는 *앱 id*라고도 함)는 응용 프로그램을 생성할 때 한 번만 전달 되는 것을 알 수 있습니다. 앱에서 토큰을 획득 하는 경우 다시 전달할 필요가 없습니다. 이는 공용 및 비밀 클라이언트 앱 모두에 적용 됩니다. 기밀 클라이언트 앱의 생성자도 클라이언트 자격 증명으로 전달 됩니다 .이는 id 공급자와 공유 하는 암호입니다.

## <a name="next-steps"></a>다음 단계
자세한 정보:
- [클라이언트 응용 프로그램 구성 옵션](msal-client-application-configuration.md)
- [MSAL.NET를 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-net-initializing-client-applications.md)
- [MSAL를 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-js-initializing-client-applications.md)
