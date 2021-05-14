---
title: Azure Active Directory B2C에서 셀프 서비스 암호 재설정 | Microsoft Docs
description: Azure Active Directory B2C 미리 보기에서 고객을 위해 셀프 서비스 암호 재설정을 설정하는 방법 보여주기
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87481498"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>고객을 위해 셀프 서비스 암호 재설정 구성

셀프 서비스 암호 재설정 기능을 사용하면 로컬 계정에 등록된 고객은 자체적으로 암호를 재설정할 수 있습니다. 특히 정기적으로 애플리케이션을 사용하는 수백 만 명의 고객 있는 경우 지원 담당자의 부담을 크게 줄여줍니다. 현재는 검증된 이메일 주소만 복구 방법으로 지원됩니다.

> [!NOTE]
> 이 문서는 ID 공급자로 **로컬 계정 로그인** 을 사용하는 표준 **로그인** 사용자 흐름 컨텍스트에서 사용되는 셀프 서비스 암호 재설정에 적용됩니다. 완전히 사용자 지정 가능한 암호 재설정 사용자 흐름이 앱에서 호출되어야 하는 경우 [이 문서](user-flow-overview.md)를 참조하세요.
>
>

기본적으로 디렉터리에는 셀프 서비스 암호 재설정이 설정되어 있지 않습니다. 설정하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com/)에 구독 관리자로 로그인합니다. 이는 동일한 직장이나 학교 계정 또는 디렉터리를 만드는 데 사용한 동일한 Microsoft 계정입니다.
2. **Azure Active Directory** 를 엽니다(왼쪽 탐색 모음).
3. 옵션 블레이드에서 아래로 스크롤하여 **암호 재설정** 을 선택합니다.
4. **셀프 서비스 암호 재설정이 사용하도록 설정됨** 을 **모두** 로 설정합니다.
5. 페이지 위쪽에서 **저장** 을 클릭합니다. 완료되었습니다!

테스트하려면 로컬 계정을 ID 공급자로 가진 로그인 사용자 흐름에서 "지금 실행" 기능을 사용합니다. 메일 주소 및 암호 또는 사용자 이름 및 암호를 입력하는 로컬 계정 로그인 페이지에서 **계정에 액세스할 수 없나요?** 를 클릭하여 고객 환경을 확인합니다.

> [!NOTE]
> 셀프 서비스 암호 재설정 페이지는 [회사 브랜딩 기능](../active-directory/fundamentals/customize-branding.md)을 사용하여 사용자 지정할 수 있습니다.
>
>

