---
title: MSAL (Microsoft 인증 라이브러리)로 마이그레이션
titleSuffix: Microsoft identity platform
description: MSAL (Microsoft 인증 라이브러리) 및 ADAL (Azure AD 인증 라이브러리)의 차이점 및 MSAL으로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ac9264ea8d6cc71d19d2c9bbd23b2123bdf1f924
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224359"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>MSAL (Microsoft 인증 라이브러리)로 응용 프로그램 마이그레이션

많은 개발자 들이 ADAL (Azure Active Directory Authentication Library)을 사용 하 여 응용 프로그램을 빌드하고 배포 했습니다. 이제 Azure AD 엔터티의 인증 및 권한 부여에 MSAL (Microsoft 인증 라이브러리)을 사용 하는 것이 좋습니다.

ADAL 대신 MSAL 사용:

- 더 광범위 한 id 집합을 인증할 수 있습니다.
  - Azure AD id
  - Microsoft 계정
  - Azure AD B2C를 사용 하 여 소셜 및 로컬 계정
- 사용자는 최상의 single sign-on 환경을 이용할 수 있습니다.
- 응용 프로그램에서 증분 동의를 설정할 수 있습니다.
- 조건부 액세스를 보다 쉽게 지원할 수 있습니다.
- 혁신을 통해 혜택을 누릴 수 있습니다. 이제 모든 Microsoft 개발 노력이 MSAL에 초점을 맞추고 있으므로 ADAL에서 새로운 기능이 구현 되지 않습니다.

**이제 Msal은 Microsoft id 플랫폼과 함께 사용할 수 있도록 권장 되는 인증 라이브러리**입니다.

## <a name="migration-guidance"></a>마이그레이션 지침

다음 문서는 MSAL으로 마이그레이션하는 데 도움이 될 수 있습니다.

- [MSAL.Android로 마이그레이션](migrate-android-adal-msal.md)
- [MSAL.iOS / macOS로 마이그레이션](migrate-objc-adal-msal.md)
- [MSAL Java로 마이그레이션](migrate-adal-msal-java.md)
- [MSAL.js로 마이그레이션](msal-compare-msal-js-and-adal-js.md)
- [MSAL.NET으로 마이그레이션](msal-net-migration.md)
- [MSAL Python으로 마이그레이션](migrate-python-adal-msal.md)
- [브로커를 사용하여 MSAL.NET으로 Xamarin 앱 마이그레이션](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

__Q: ADAL이 사용 되지 않나요?__  
A: 예. 2020 6 월 30 일부 터 더 이상 ADAL에 새 기능을 추가 하지 않습니다. 2022 년 6 월 30 일까 야 ADAL에 중요 보안 픽스를 계속 해 서 추가 합니다. 이 날짜 이후에 ADAL을 사용 하는 앱은 계속 작동 하지만 최신 기능을 활용 하 고 보안을 유지 하기 위해 MSAL으로 업그레이드 하는 것이 좋습니다.

__Q: 기존 ADAL 앱이 작동을 중지 하나요?__  
A: 아니요. 기존 앱은 수정 하지 않고 계속 해 서 작업 합니다. 2022 년 6 월 30 일까 지 유지 하려는 경우 안전 하 게 유지 하기 위해 앱을 MSAL으로 업데이트 하는 것이 좋습니다. 그러나 기존 기능을 유지 관리 하기 위해 MSAL으로 마이그레이션하는 것은 필요 하지 않습니다.

__Q: 어떤 앱이 ADAL을 사용 하 고 있는지 알 어떻게 할까요? 있나요?__  
A: 응용 프로그램에 대 한 소스 코드가 있는 경우 위의 마이그레이션 가이드를 참조 하 여 앱에서 사용 하는 라이브러리와 MSAL으로 마이그레이션하는 방법을 확인할 수 있습니다. ISV와 파트너 관계를 맺고 있는 경우 해당 사용자에 게 직접 연락 하 여 MSAL에 대 한 마이그레이션 경험을 이해 하는 것이 좋습니다.

__Q: MSAL으로 이동 하는 데 투자 해야 하는 이유는 무엇 인가요?__  
A: MSAL에는 증분 동의, Single Sign-On 및 토큰 캐시 관리를 포함 하 여 ADAL에 없는 새로운 기능이 포함 되어 있습니다. 또한 ADAL과 달리 MSAL은 2022 년 6 월 30 일까 지 보안 패치를 계속 받을 수 있습니다. [자세히 알아보기](msal-overview.md).

__Q: Microsoft는 자신의 앱을 MSAL으로 업데이트 하나요?__  
예. Microsoft는 지원 종료 최종 기한에 의해 응용 프로그램을 MSAL로 마이그레이션하는 과정을 진행 하 여 MSAL의 지속적인 보안 및 기능 향상을 활용할 수 있도록 합니다.

__Q: 앱을 ADAL에서 MSAL으로 이동 하는 데 도움이 되는 도구를 릴리스할 예정 인가요?__  
A: 아니요. 라이브러리 간의 차이점으로 인해 MSAL을 개선 하는 데 사용할 수 있는 도구를 개발 하 고 유지 관리 하는 데에는 전용 리소스가 필요 합니다. 그러나 응용 프로그램에서 필요한 변경을 수행 하는 데 도움이 되는 이전 마이그레이션 가이드 집합을 제공 합니다.

__Q: MSAL은 AD FS에서 어떻게 작동 하나요?__  
A: MSAL.NET는 AD FS 2019에 대 한 인증을 위한 특정 시나리오를 지원 합니다. 앱이 이전 버전의 AD FS에서 직접 토큰을 획득 해야 하는 경우 ADAL에 남아 있어야 합니다. [자세히 알아보기](msal-net-adfs-support.md).

__Q: 내 응용 프로그램을 마이그레이션하는 데 도움이 어떻게 할까요??__  
A:이 문서의 [마이그레이션 지침](#migration-guidance) 섹션을 참조 하세요. 앱의 플랫폼에 대 한 가이드를 읽은 후 추가 질문이 있으면 태그를 사용 하 여 Stack Overflow에 게시 `[adal-deprecation]` 하거나 라이브러리의 GitHub 리포지토리에서 문제를 열 수 있습니다. 각 라이브러리 리포지토리의 링크는 MSAL 개요 문서의 [언어 및 프레임 워크](msal-overview.md#languages-and-frameworks) 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 인증 라이브러리 및 Microsoft Graph API를 사용 하도록 응용 프로그램 업데이트](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Microsoft id 플랫폼 개요](v2-overview.md)
- [MSAL 코드 샘플을 검토 합니다.](sample-v2-code.md)
