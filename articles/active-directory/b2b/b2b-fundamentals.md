---
title: Azure Active Directory B2B 모범 사례 및 권장 사항
description: Azure Active Directory에서 B2B (기업 간) 게스트 사용자 액세스에 대 한 모범 사례 및 권장 사항을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa6bc856fc7b7de071a45f3aa11c051e36eca4f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480113"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B 모범 사례
이 문서에는 Azure Active Directory (Azure AD)에서 B2B (기업 간) 공동 작업을 위한 권장 사항 및 모범 사례가 포함 되어 있습니다.

## <a name="b2b-recommendations"></a>B2B 권장 사항
| 권장 | 의견 |
| --- | --- |
| 최적의 로그인 환경을 위해 id 공급자와 페더레이션 | 가능 하면 언제 든 지 id 공급자와 직접 페더레이션 하 여 초대 된 사용자가 Microsoft 계정 (MSAs) 또는 Azure AD 계정을 만들지 않고도 공유 앱 및 리소스에 로그인 할 수 있도록 합니다. [Google 페더레이션 기능](google-federation.md) 을 사용 하 여 B2B 게스트 사용자가 google 계정으로 로그인 할 수 있습니다. 또는 [직접 페더레이션 (미리 보기) 기능](direct-federation.md) 을 사용 하 여 IdP (id 공급자)가 SAML 2.0 또는 WS-급지됨 프로토콜을 지 원하는 조직과 직접 페더레이션을 설정할 수 있습니다. |
| 다른 방법으로 인증할 수 없는 B2B 게스트에 대해 전자 메일 일회용 암호 (미리 보기) 기능을 사용 합니다. | [전자 메일 일회용 암호 (미리 보기)](one-time-passcode.md) 기능은 Azure AD, MICROSOFT 계정 (MSA) 또는 Google 페더레이션의 다른 방법으로 인증 될 수 없는 B2B 게스트 사용자를 인증 합니다. 게스트 사용자는 초대를 사용하거나 공유 리소스에 액세스할 때 메일 주소로 전송되는 임시 코드를 요청할 수 있습니다. 그런 다음, 이 코드를 입력하여 로그인을 계속합니다. |
| 로그인 페이지에 회사 브랜딩 추가 | B2B 게스트 사용자에 게 보다 직관적인 로그인 페이지를 사용자 지정할 수 있습니다. [로그인 및 액세스 패널 페이지에 회사 브랜딩을 추가](../fundamentals/customize-branding.md)하는 방법을 참조 하세요. |
| B2B 게스트 사용자 상환 환경에 개인 정보 취급 방침 추가 | 초대 된 사용자가 계속 하려면 개인 정보 취급 방침에 동의 해야 하도록 조직의 개인 정보 취급 방침에 대 한 URL을 첫 번째 시간 초대 상환 프로세스에 추가할 수 있습니다. [방법: Azure Active Directory에서 조직의 개인 정보에 대 한 추가](https://aka.ms/adprivacystatement)정보를 참조 하세요. |
| 대량 초대 (미리 보기) 기능을 사용 하 여 여러 B2B 게스트 사용자를 동시에 초대 | Azure Portal에서 대량 초대 미리 보기 기능을 사용 하 여 동시에 여러 게스트 사용자를 조직에 초대 합니다. 이 기능을 사용 하면 CSV 파일을 업로드 하 여 B2B 게스트 사용자를 만들고 대량 초대를 보낼 수 있습니다. [B2B 사용자를 대량 초대 하는 방법에 대 한 자습서를](tutorial-bulk-invite.md)참조 하세요. |
| Multi-Factor Authentication에 대 한 조건부 액세스 정책 적용 (MFA) | 파트너 B2B 사용자와 공유 하려는 앱에 MFA 정책을 적용 하는 것이 좋습니다. 이러한 방식으로 MFA는 파트너 조직에서 MFA를 사용 하는지 여부에 관계 없이 테넌트의 앱에 일관 되 게 적용 됩니다. [B2B 공동 작업 사용자에 대 한 조건부 액세스를](conditional-access.md)참조 하세요. |
| 장치 기반 조건부 액세스 정책을 적용 하는 경우에는 제외 목록을 사용 하 여 B2B 사용자에 대 한 액세스를 허용 합니다. | 조직에서 장치 기반 조건부 액세스 정책을 사용 하도록 설정 하는 경우 B2B 게스트 사용자 장치는 조직에서 관리 되지 않으므로 차단 됩니다. 특정 파트너 사용자가 포함 된 제외 목록을 만들어 장치 기반 조건부 액세스 정책에서 제외할 수 있습니다. [B2B 공동 작업 사용자에 대 한 조건부 액세스를](conditional-access.md)참조 하세요. |
| B2B 게스트 사용자에 게 직접 링크를 제공 하는 경우 테넌트 별 URL 사용 | 초대 전자 메일의 대 안으로 게스트에 앱 또는 포털에 대 한 직접 링크를 제공할 수 있습니다. 이 직접 링크는 테넌트에 따라 달라 야 합니다. 즉, 테넌트 ID 또는 확인 된 도메인을 포함 해야 하므로 공유 앱이 있는 테넌트에서 게스트를 인증할 수 있습니다. [게스트 사용자를 위한 상환 환경을](redemption-experience.md)참조 하세요. |
| 앱을 개발할 때 UserType를 사용 하 여 게스트 사용자 환경을 확인 합니다.  | 응용 프로그램을 개발 중이 고 테넌트 사용자와 게스트 사용자에 게 다른 환경을 제공 하려면 UserType 속성을 사용 합니다. UserType 클레임이 현재 토큰에 포함 되어 있지 않습니다. 응용 프로그램은 Graph API를 사용 하 여 사용자가 자신의 UserType을 얻기 위해 디렉터리를 쿼리해야 합니다. |
| 조직과의 사용자 관계가 변경 되는 경우에 *만* UserType 속성을 변경 합니다. | PowerShell을 사용 하 여 사용자의 UserType 속성을 Member에서 Guest로 변환 하는 것이 가능 하지만 (그 반대의 경우도 해당) 사용자의 조직에 대 한 사용자의 관계가 변경 되는 경우에만이 속성을 변경 해야 합니다. [B2B 게스트 사용자의 속성](user-properties.md)을 참조 하세요.|

## <a name="next-steps"></a>다음 단계

[B2B 공유 관리](delegate-invitations.md)
