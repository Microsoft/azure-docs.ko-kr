---
title: B2B 초대 전자 메일-Azure Active Directory 요소의 | Microsoft Docs
description: Azure Active Directory B2B 협업 초대 이메일 템플릿
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65768363"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Azure Active Directory B2B 협업 초대 이메일의 요소

초대 이메일은 온보드의 파트너를 Azure AD에서 B2B 협업 사용자로 불러오기 위한 중요한 구성 요소입니다. 받는 사람의 신뢰를 높이는 데 사용할 수 있습니다. 전자 메일에 적법성과 사회적 증거를 전자 메일에 추가하여 받는 사람이 안심하고 **시작** 단추를 선택하여 초대를 수락할 수 있습니다. 이 신뢰는 공유 마찰을 줄이는 데 핵심적입니다. 또한 이 템플릿을 통해 전자 메일도 보기 좋게 구성할 수 있습니다.

![B2B 초대 이메일을 보여 주는 스크린샷](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>전자 메일 설명
전자 메일의 몇 가지 요소를 확인하여 이러한 기능을 최대한 활용하는 방법에 대해 살펴보겠습니다.

### <a name="subject"></a>Subject
이메일의 제목은 다음 패턴을 따릅니다. &lt;tenantname&gt; 조직에 초대되었습니다.

### <a name="from-address"></a>보낸 사람 주소
보낸 사람 주소에 대해서는 LinkedIn 유사 패턴을 사용합니다.  초대자가 누구인지와 어떤 회사에 소속되어 있는지를 명확히 하고 전자 메일이 Microsoft 전자 메일 주소에서 전송된 것인지를 명확히 해야 합니다. 형식: Microsoft 초대 <invites@microsoft.com> 나 &lt;초대자 표시 이름&gt; 에서 &lt;tenantname&gt; (Microsoft)를 통해 <invites@microsoft.com>합니다.

### <a name="reply-to"></a>회신 대상
회신 전자 메일은 사용 가능할 때 초대자의 전자 메일로 설정되므로 전자 메일에 회신하면 초대자에게 전자 메일이 다시 전송됩니다.

### <a name="branding"></a>브랜딩
테넌트에서 보낸 초대 전자 메일은 테넌트에 대해 설정했을 수 있는 회사 브랜딩을 사용합니다. 이 기능을 활용하려는 경우 자세한 구성 방법은 [다음](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)과 같습니다. 전자 메일에 배너 로고가 나타납니다. 최상의 결과를 얻으려면 [여기](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal)에 제공되는 이미지 크기 및 품질 지침을 따르세요. 또한 회사 이름이 활용 방안에도 표시됩니다.

### <a name="call-to-action"></a>활용 방안
활용 방안은 받는 사람이 메일을 받은 이유에 대한 설명과 받는 사람이 요청 받은 작업의 두 부분으로 구성됩니다.
- "이유" 섹션은 다음 패턴으로 처리할 수 있습니다. &lt;tenantname&gt; 조직에 있는 애플리케이션에 액세스하도록 초대됨

- 또한 "요청되는 작업" 섹션은 **시작** 단추의 존재 여부에 따라 결정됩니다. 초대 받을 필요가 없는 받는 사람이 추가되면 이 단추가 표시되지 않습니다.

### <a name="inviters-information"></a>초대자에 대한 정보
초대자의 표시 이름이 전자 메일에 포함됩니다. 또한 Azure AD 계정에 대해 프로필 사진을 설정한 경우 초대 전자 메일에 해당 사진도 포함됩니다. 두 가지 항목 모두 전자 메일에 대한 받는 사람의 신뢰도를 높이기 위한 것입니다.

자신의 프로필 사진을 아직 설정하지 않은 경우 다음과 같이 사진 대신 초대자의 이니셜이 있는 아이콘이 표시됩니다.

  ![표시 되는 이니셜 초대를 사용 하 여 초대를 보여 주는 스크린샷](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>본문
본문에는 초대자가 [게스트 사용자를 디렉터리, 그룹 또는 애플리케이션에 초대](add-users-administrator.md)할 때 또는 [초대 API를 사용하여](customize-invitation-api.md) 작성하는 메시지가 포함됩니다. 이것은 텍스트 영역에 불과하며, 보안상의 이유로 HTML 태그를 처리하지 않습니다.

  ![초대 전자 메일의 본문을 보여 주는 스크린샷](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>바닥글 섹션
바닥글에는 Microsoft 회사 브랜드가 포함되며, 받는 사람은 이를 통해 전자 메일이 모니터링되지 않은 별칭에서 전송되었는지 여부를 알 수 있습니다. 

특수 사례:

- 초대자에게 초대 테넌시의 전자 메일 주소가 없습니다.

  ![초대 자가 초대 테넌트에 전자 메일을 없을 때 스크린 샷](media/invitation-email-elements/inviter-no-email.png)


- 받는 사람이 초대를 충전할 필요가 없습니다.

  ![수신자를 초대를 충전할 필요가 없는 경우 스크린 샷](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>언어를 결정하는 방법
초대 이메일에서 게스트 사용자에게 표시되는 언어는 다음 설정에 의해 결정됩니다. 이러한 설정은 우선 순위 순서대로 나열됩니다. 설정이 구성되지 않은 경우 목록의 다음 설정이 언어를 결정합니다. 
- 초대 만들기 API를 사용하는 경우 [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) 개체의 **messageLanguage** 속성
-   게스트의 [사용자 개체](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)에 지정한 **preferredLanguage** 속성
-   게스트 사용자의 홈 테넌트의 속성에 설정된 **알림 언어**(Azure AD 테넌트만 해당)
-   리소스 테넌트의 속성에 설정된 **알림 언어**

이러한 설정을 하나도 구성하지 않은 경우 기본 언어는 영어(미국)입니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
- [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법은 무엇입니까?](add-users-administrator.md)
- [정보 작업자가 B2B 공동 작업 사용자를 추가하는 방법은 무엇입니까?](add-users-information-worker.md)
- [B2B 공동 작업 초대 상환](redemption-experience.md)
- [초대 없이 B2B 공동 작업 사용자 추가](add-user-without-invite.md)
