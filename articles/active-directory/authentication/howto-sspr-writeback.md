---
title: SSPR에 대 한 비밀 번호 쓰기 저장 구성-Azure Active Directory
description: Azure AD와 Azure AD Connect를 사용하여 온-프레미스 디렉터리에 대한 비밀번호 쓰기 저장
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1fa447312ad6a1f92eaed1164020cb6ee95606e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161599"
---
# <a name="how-to-configure-password-writeback"></a>방법: 비밀번호 쓰기 저장 구성

다음 단계에서는 사용자 환경에서 [기본](../hybrid/how-to-connect-install-express.md) 또는 [사용자 지정](../hybrid/how-to-connect-install-custom.md) 설정을 사용하여 Azure AD Connect를 이미 구성했다고 가정합니다.

1. 비밀번호 쓰기 저장을 구성하고 사용하도록 설정하려면 Azure AD Connect 서버에 로그인하고 **Azure AD Connect** 구성 마법사를 시작합니다.
2. **시작** 페이지에서 **구성**을 선택합니다.
3. **추가 작업** 페이지에서 **동기화 옵션 사용자 지정**을 선택하고 **다음**을 선택합니다.
4. **Azure AD에 연결** 페이지에서 전역 관리자 자격 증명을 입력하고 **다음**을 선택합니다.
5. **디렉터리 연결** 및 **도메인/OU** 필터링 페이지에서 **다음**을 선택합니다.
6. **선택적 기능** 페이지에서 **비밀번호 쓰기 저장** 옆의 확인란을 선택한 후 **다음**을 선택합니다.
   ![Azure AD Connect에서 비밀번호 쓰기 저장 사용][Writeback]
7. **구성 준비 완료** 페이지에서 **구성**을 선택하고 프로세스가 완료될 때까지 기다립니다.
8. 구성이 완료된 것이 확인되면 **마침**을 선택합니다.

비밀번호 쓰기 저장과 관련된 일반적인 문제 해결 작업은 문제 해결 문서에서 [비밀번호 쓰기 저장 문제 해결](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) 섹션을 참조하세요.

> [!WARNING]
> [2018년 11월 7일에 ACS(Azure Access Control) 서비스 사용이 중지되면](../azuread-dev/active-directory-acs-migration.md) Azure AD Connect 1.0.8641.0 이하 버전을 사용 중인 고객의 경우 비밀번호 쓰기 저장 기능의 작동이 중지됩니다. 해당 날짜부터는 Azure AD Connect 1.0.8641.0 이하 버전에서 더 이상 비밀번호 쓰기 저장이 허용되지 않습니다. 이러한 버전에서는 해당 기능에 ACS를 사용하기 때문입니다.
>
> 서비스 중단을 방지하려면 이전 버전 Azure AD Connect에서 최신 버전으로 업그레이드하세요([Azure AD Connect: 이전 버전에서 최신 버전으로 업그레이드](../hybrid/how-to-upgrade-previous-version.md) 문서 참조).
>

## <a name="licensing-requirements-for-password-writeback"></a>비밀번호 쓰기 저장에 대한 라이선스 요구 사항

**셀프 서비스 암호 재설정/변경/온-프레미스 쓰기 저장으로 잠금 해제는 Azure AD의 프리미엄 기능입니다**. 라이선스에 대한 자세한 내용은 [Azure Active Directory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

비밀번호 쓰기 저장을 사용하려면 테넌트에 다음과 같은 라이선스 중 하나가 할당되어 있어야 합니다.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 또는 A3
* Enterprise Mobility + Security E5 또는 A5
* Microsoft 365 E3 또는 A3
* Microsoft 365 E5 또는 A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> 독립 실행형 Office 365 라이선스 요금제는 *"셀프 서비스 암호 재설정/변경/온-프레미스 쓰기 저장으로 잠금 해제"를 지원하지 않습니다*. 이 기능을 사용하려면 위의 요금제 중 하나가 필요합니다.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory 권한 및 온-프레미스 암호 복잡성 정책 

SSPR 범위 내에 있으려면 Azure AD Connect 유틸리티에 지정된 계정에 다음 항목이 설정되어야 합니다.

* **암호 다시 설정** 
* **암호 변경** 
* **쓰기 권한** 켜짐`lockoutTime`
* **쓰기 권한** 켜짐`pwdLastSet`
* 다음에 대해 **확장 권한** 켜짐:
   * 포리스트에 있는 *각 도메인*의 루트 개체
   * SSPR 범위에 포함되도록 하려는 사용자 OU(조직 구성 단위)

설명된 계정이 참조하는 계정을 모르는 경우, Azure Active Directory Connect 구성 UI를 열고 **현재 구성 보기** 옵션을 선택합니다. 권한을 추가해야 하는 계정은 **동기화된 디렉터리** 아래에 나열됩니다.

이러한 사용 권한을 설정하면 각 포리스트에 대한 MA 서비스 계정이 해당 포리스트 내에서 사용자 계정을 대신하여 암호를 관리할 수 있습니다. 

> [!IMPORTANT]
> 이러한 사용 권한을 할당하는 것을 잊은 경우, 쓰기 저장이 올바르게 구성된 것으로 표시되면, 클라우드에서 온-프레미스 암호 관리를 시도하면 오류가 발생합니다.
>

> [!NOTE]
> 이 사용 권한이 디렉터리의 모든 개체를 복제하려면 한 시간 이상이 걸릴 수 있습니다.
>

비밀번호 쓰기 저장이 발생하도록 적절한 사용 권한을 설정하려면 다음 단계를 완료하세요.

1. 적절한 도메인 관리 권한이 있는 계정으로 Active Directory 사용자 및 컴퓨터를 엽니다.
2. **보기** 메뉴에서 **고급 기능**이 켜져 있어야 합니다.
3. 왼쪽 패널에서, 도메인의 루트를 나타내는 개체를 마우스 오른쪽 단추로 클릭하고 **속성** > **보안** > **고급**을 선택합니다.
4. **사용 권한** 탭에서 **추가**를 선택합니다.
5. (Azure AD Connect 설정에서) 사용 권한이 적용되는 계정을 선택합니다.
6. **적용 대상** 드롭다운 목록에서 **하위 사용자 개체**를 선택합니다.
7. **권한** 아래에서 다음 옵션의 확인란을 선택합니다.
    * **암호 변경**
    * **암호 다시 설정**
8. **속성** 아래에서 다음 옵션의 확인란을 선택합니다.
    * **lockoutTime 쓰기**
    * **pwdLastSet 쓰기**
9. **적용/확인**을 선택하여 변경 내용을 적용하고 열려 있는 대화 상자를 모두 끝냅니다.

인증 원본이 온-프레미스에 있으므로 암호 복잡성 정책은 동일한 연결 된 데이터 원본에서 적용 됩니다. "최소 암호 사용 기간"에 대 한 기존 그룹 정책을 변경 했는지 확인 합니다. 그룹 정책은 1로 설정 해서는 안 됩니다. 즉, 암호를 업데이트 하려면 먼저 하루 이상 이전 이어야 합니다. 0으로 설정 되어 있는지 확인 해야 합니다. 이러한 설정은 **컴퓨터 구성 > 정책 > Windows 설정 > 보안 설정 > 계정 정책**`gpmc.msc`에서 찾을 수 있습니다. `gpupdate /force`를 실행 하 여 변경 내용이 적용 되도록 합니다. 

## <a name="next-steps"></a>다음 단계

[비밀번호 쓰기 저장이란?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Azure AD Connect에서 비밀번호 쓰기 저장 사용"
