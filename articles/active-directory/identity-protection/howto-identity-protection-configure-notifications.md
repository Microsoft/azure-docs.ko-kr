---
title: Azure Active Directory ID 보호 알림
description: 알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d770336099611d69fa9e44d04fff4dd37696707
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120117"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림

Azure AD ID 보호는 사용자 위험 및 위험 검색을 관리 하는 데 도움이 되는 두 가지 유형의 자동화 된 알림 전자 메일을 보냅니다.

- 위험에 노출된 사용자가 검색된 전자 메일
- 주간 다이제스트 전자 메일

이 문서에는 두 알림 전자 메일의 개요가 나와 있습니다.

>[!NOTE]
>이메일 알림은 공용 클라우드에서만 사용할 수 있으며 미국 정부 클라우드에서는 현재 사용할 수 없습니다.

## <a name="users-at-risk-detected-email"></a>위험에 노출된 사용자가 검색된 전자 메일

위험에 노출된 계정 검색에 대한 응답으로 Azure AD ID 보호는 **위험에 노출된 사용자 검색됨**이란 제목의 전자 메일 경고를 생성합니다. 전자 메일에는 **[위험 플래그가 지정된 사용자](../reports-monitoring/concept-user-at-risk.md)** 보고서에 대한 링크가 포함됩니다. 모범 사례로, 위험에 노출된 사용자를 즉시 조사해야 합니다.

이 경고에 대 한 구성을 사용 하 여 경고를 생성 하려는 사용자 위험 수준을 지정할 수 있습니다. 사용자의 위험 수준이 지정한 내용에 도달 하면 전자 메일이 생성 됩니다. 그러나이 사용자가이 사용자 위험 수준으로 이동한 후에는이 사용자에 대해 검색 된 전자 메일 경고에 새 사용자가 수신 되지 않습니다. 예를 들어 보통 사용자 위험에 대해 경고를 표시 하는 정책을 설정 하 고 사용자가 보통 위험으로 이동 하면 John에 대해 위험 검색 된 전자 메일이 사용자에 게 표시 됩니다. 그러나 John이 높은 위험으로 이동 하거나 추가 위험 검색을 발생 시킬 경우 두 번째 사용자에 게 위험 감지 됨 경고가 표시 되지 않습니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>위험 검색 됨 경고에 대 한 사용자 구성

관리자로서 다음을 설정할 수 있습니다.

- **이 전자 메일의 생성을 트리거하는 사용자 위험 수준** -기본적으로 위험 수준은 "높음" 위험으로 설정 됩니다.
- **이 전자 메일의 받는 사람** - 기본적으로 받는 사람에는 모든 전역 관리자가 포함됩니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.
   - 필요에 따라 **추가 전자 메일을 추가 하 여 경고 알림을 받을** 수 있습니다 .이 기능은 미리 보기 이며 사용자 정의 된 사용자가 Azure Portal에서 링크 된 보고서를 볼 수 있는 적절 한 권한이 있어야 합니다.

**Azure Portal** 에서 위험에 노출 된 사용자를 **Azure Active Directory** > **보안** > id > **보호** 사용자에 게 위험 **감지 됨 경고**에서 사용자를 구성 합니다.

## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일

주간 다이제스트 전자 메일에는 새로운 위험 검색에 대 한 요약이 포함 되어 있습니다.  
다음을 포함합니다.

- 위험에 노출된 사용자
- 의심스러운 활동
- 감지된 취약점
- ID 보호에서 관련된 보고서에 대한 링크

![주간 다이제스트 전자 메일](./media/howto-identity-protection-configure-notifications/400.png)

기본적으로 받는 사람은 모든 전역 관리자를 포함 합니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.

### <a name="configure-weekly-digest-email"></a>주별 다이제스트 전자 메일 구성

관리자는 주별 다이제스트 전자 메일 보내기 또는 끄기를 전환 하 고 전자 메일을 받도록 할당 된 사용자를 선택할 수 있습니다.

**Azure Active Directory** > **보안** > **id 보호** > **주간 다이제스트**의 **Azure Portal** 에서 주별 다이제스트 전자 메일을 구성 합니다.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory ID 보호](../active-directory-identityprotection.md)
