---
title: Azure AD 조건부 액세스의 사용자 지정 컨트롤
description: Azure Active Directory 조건부 액세스의 사용자 지정 컨트롤의 작동 방식에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59f83d232874a7702598b6ea5fdd458c101e7e79
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266581"
---
# <a name="custom-controls-preview"></a>사용자 지정 컨트롤(미리 보기)

사용자 지정 컨트롤은 Azure Active Directory의 미리 보기 기능입니다. 사용자 지정 컨트롤을 사용 하는 경우 사용자는 Azure Active Directory 외부에서 인증 요구 사항을 충족 하기 위해 호환 서비스로 리디렉션됩니다. 이 제어를 충족 하기 위해 사용자의 브라우저는 외부 서비스로 리디렉션되고 필요한 인증을 수행한 다음 Azure Active Directory 다시 리디렉션됩니다. Azure Active Directory는 응답을 확인 하 고 사용자가 성공적으로 인증 되거나 유효성이 확인 되 면 사용자는 조건부 액세스 흐름을 계속 진행 합니다.

> [!NOTE]
> 사용자 지정 컨트롤 기능을 계획 하는 변경 내용에 대 한 자세한 내용은 [새로운 기능에 대 한](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)2 월 2020 보관을 참조 하세요.

## <a name="creating-custom-controls"></a>사용자 지정 컨트롤 만들기

사용자 지정 컨트롤은 승인 된 인증 공급자의 제한 된 집합으로 작동 합니다. 사용자 지정 컨트롤을 만들려면 먼저 사용할 공급자에게 문의해야 합니다. 각 비 Microsoft 공급자에는 등록, 구독 또는 서비스의 일부가 될 수 있는 고유한 프로세스 및 요구 사항이 있으며 조건부 액세스와 통합 하려는 경우를 표시 합니다. 이 시점에서 공급자는 JSON 형식의 데이터 블록을 제공합니다. 이 데이터를 사용 하면 공급자 및 조건부 액세스가 테 넌 트에 대해 함께 작동 하 고, 새 컨트롤이 만들어지고, 사용자가 공급자를 사용 하 여 확인을 성공적으로 수행 했는지 여부를 조건부 액세스에서 확인할 수 있는 방법을 정의 합니다.

JSON 데이터를 복사한 다음 관련 텍스트 상자에 붙여넣습니다. 사용자가 변경한 내용을 명시적으로 이해 하지 않으면 JSON을 변경 하지 마세요. 변경하면 공급자와 Microsoft 간의 연결이 끊어져 잠재적으로 사용자가 사용자 계정 밖에서 잠길 수 있습니다.

사용자 지정 컨트롤을 만드는 옵션은 **조건부 액세스** 페이지의 **관리** 섹션에 있습니다.

![조건부 액세스의 사용자 지정 컨트롤 인터페이스](./media/controls/custom-controls-conditional-access.png)

**새 사용자 지정 컨트롤**을 클릭하면 컨트롤의 JSON 데이터에 대한 텍스트 상자가 있는 블레이드가 열립니다.  

![새 사용자 지정 컨트롤](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>사용자 지정 컨트롤 삭제

사용자 지정 컨트롤을 삭제 하려면 먼저 조건부 액세스 정책에서 사용 되 고 있지 않은지 확인 해야 합니다. 작업이 완료되면 다음을 수행합니다.

1. 사용자 지정 컨트롤 목록으로 이동합니다.
1. …를 클릭합니다.  
1. **삭제**를 선택합니다.

## <a name="editing-custom-controls"></a>사용자 지정 컨트롤 편집

사용자 지정 컨트롤을 편집하려면 현재 컨트롤을 삭제하고 업데이트된 정보로 새 컨트롤을 만들어야 합니다.

## <a name="known-limitations"></a>알려진 제한 사항

사용자 지정 컨트롤은 Azure Multi-Factor Authentication, azure AD 셀프 서비스 암호 다시 설정 (SSPR), multi-factor Authentication 클레임 요구 사항 충족, Intune 장치 등록의 일부로 또는 장치를 Azure AD에 조인할 때 azure AD에 대 한 권한 있는 Id 관리자의 역할을 승격 하는 Id 보호의 자동화에 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)

- [조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
