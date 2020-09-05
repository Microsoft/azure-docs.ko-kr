---
title: 사용자 지정 Azure Active Directory 암호 보호 목록 구성
description: 이 자습서에서는 Azure Active Directory에 대해 사용자 지정 금지 암호 보호 목록을 구성하여 사용자 환경에서 일반적인 단어를 제한하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24e0cfa4227ba5d7c38449d3cf1304add5ae5c1f
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718886"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>자습서: Azure Active Directory 암호 보호를 위한 사용자 지정 금지 암호 구성

사용자가 학교, 스포츠 팀 또는 유명 인사와 같은 일반적인 로컬 단어를 사용하는 암호를 만드는 경우가 많습니다. 이러한 암호는 쉽게 추측할 수 있으며 사전 기반 공격에 취약합니다. 조직에서 강력한 암호를 적용하기 위해 Azure AD(Active Directory) 사용자 지정 금지 암호 목록을 사용하여 평가하고 차단할 특정 문자열을 추가할 수 있습니다. 사용자 지정 금지 암호 목록에 일치하는 항목이 있으면 암호 변경 요청이 실패합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 금지 암호 사용
> * 사용자 지정 금지 암호 목록에 항목 추가
> * 금지 암호를 사용하여 암호 변경 테스트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* Azure AD Premium P1 또는 평가판 이상의 라이선스를 사용하도록 설정되어 작동하는 Azure AD 테넌트.
    * 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *글로벌 관리자* 권한이 있는 계정
* 알고 있는 암호가 있는 관리자가 아닌 사용자(예: *testuser*). 이 자습서에서는 이 계정을 사용하여 암호 변경 이벤트를 테스트합니다.
    * 사용자를 만들어야 하는 경우 [빠른 시작: Azure Active Directory에 새 사용자 추가](../fundamentals/add-users-azure-active-directory.md)의 정보를 사용할 수 있습니다.
    * 금지 암호를 사용하여 암호 변경 작업을 테스트하려면 [셀프 서비스 암호를 다시 설정하도록 Azure AD 테넌트를 구성](tutorial-enable-sspr.md)해야 합니다.

## <a name="what-are-banned-password-lists"></a>금지 암호 목록이란?

Azure AD에는 글로벌 금지 암호 목록이 포함되어 있습니다. 글로벌 금지 암호 목록의 내용은 외부 데이터 원본을 기반으로 하지 않습니다. 대신, 글로벌 금지 암호 목록은 Azure AD 보안 원격 분석 및 분석의 지속적인 결과를 기반으로 합니다. 사용자 또는 관리자가 자격 증명을 변경하거나 다시 설정하려고 하면 금지 암호 목록과 대조하여 원하는 암호를 확인합니다. 글로벌 금지 암호 목록에 일치하는 항목이 있으면 암호 변경 요청이 실패합니다. 이 기본 글로벌 금지 암호 목록은 편집할 수 없습니다.

허용되는 암호를 유연하게 제공하기 위해 사용자 지정 금지 암호 목록을 정의할 수도 있습니다. 사용자 지정 금지 암호 목록은 조직에서 강력한 암호를 적용하기 위해 글로벌 금지 암호 목록과 함께 작동합니다. 다음 예와 같은 조직 특정 용어를 사용자 지정 금지 암호 목록에 추가할 수 있습니다.

* 브랜드 이름
* 제품 이름
* 회사 본부와 같은 위치
* 회사 특정 내부 용어
* 회사 고유의 의미가 있는 약어

사용자가 암호를 글로벌 또는 사용자 지정 금지 암호 목록에 있는 특정 항목으로 다시 설정하려고 하면 다음 오류 메시지 중 하나가 표시됩니다.

* *암호에 추측하기 쉽게 만드는 단어, 구 또는 패턴이 있습니다. 다른 암호를 사용하여 다시 시도하세요.*
* *관리자가 차단한 단어 또는 문자가 포함되어 있으므로 해당 암호를 사용할 수 없습니다. 다른 암호를 사용하여 다시 시도하세요.*

사용자 지정 금지 암호 목록은 최대 1,000개의 용어로 제한됩니다. 많은 암호 목록을 차단하도록 설계되지 않았습니다. 사용자 지정 금지 암호 목록의 이점을 극대화하려면 [사용자 지정 금지 암호 목록 개념](concept-password-ban-bad.md#custom-banned-password-list) 및 [암호 평가 알고리즘 개요](concept-password-ban-bad.md#how-are-passwords-evaluated)을 검토하세요.

## <a name="configure-custom-banned-passwords"></a>사용자 지정 금지 암호 목록 구성

사용자 지정 금지 암호 목록을 사용하도록 설정하고 일부 항목을 추가해 보겠습니다. 항목은 언제든지 사용자 지정 금지 암호 목록에 추가할 수 있습니다.

사용자 지정 금지 암호 목록을 사용하도록 설정하고 항목을 이 목록에 추가하려면 다음 단계를 완료합니다.

1. *글로벌 관리자* 권한이 있는 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택한 다음, 왼쪽 메뉴에서 **보안**을 선택합니다.
1. **관리** 메뉴 헤더 아래에서 **인증 방법**, **암호 보호**를 차례로 선택합니다.
1. **사용자 지정 목록 적용** 옵션을 *예*로 설정합니다.
1. 문자열을 한 줄에 하나씩 **사용자 지정 금지 암호 목록**에 추가합니다. 사용자 지정 금지 암호 목록에 적용되는 고려 사항과 제한 사항은 다음과 같습니다.

    * 사용자 지정 금지 암호 목록에는 최대 1,000개의 용어가 포함될 수 있습니다.
    * 사용자 지정 금지 암호 목록에서는 대/소문자가 구별됩니다.
    * 사용자 지정 금지 암호 목록에서는 일반적인 문자를 바꾸는 것이 좋습니다(예: "o" 및 "0" 또는 "a" 및 "\@").
    * 최소 문자열 길이는 4자이며, 최대 길이는 16자입니다.

    다음 예와 같이 사용자 고유의 사용자 지정 금지 암호를 지정합니다.

    [ ![Azure Portal의 인증 방법에서 사용자 지정 금지 암호 목록 수정](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. **Windows Server Active Directory에 대한 암호 보호를 사용하도록 설정** 옵션을 *아니요*로 둡니다.
1. 사용자 지정 금지 암호 및 항목을 사용하도록 설정하려면 **저장**을 선택합니다.

사용자 지정 금지 암호 목록을 적용하기 위한 업데이트에는 몇 시간 정도가 소요됩니다.

하이브리드 환경의 경우 [Azure AD 암호 보호를 온-프레미스 환경에 배포](howto-password-ban-bad-on-premises-deploy.md)할 수도 있습니다. 클라우드 및 온-프레미스 암호 변경 요청 모두에 동일한 글로벌 및 사용자 지정 금지 암호 목록이 사용됩니다.

## <a name="test-custom-banned-password-list"></a>사용자 지정 금지 암호 목록에 대한 테스트

사용자 지정 금지 암호 목록이 실제로 작동하는지 확인하려면 암호를 이전 섹션에서 추가한 항목의 변형으로 변경하여 시도합니다. Azure AD에서 암호 변경을 처리하려고 하면 해당 암호가 사용자 지정 금지 암호 목록의 항목과 일치합니다. 그러면 오류가 사용자에게 표시됩니다.

> [!NOTE]
> 사용자가 웹 기반 포털에서 자신의 암호를 다시 설정하려면 먼저 [셀프 서비스 암호를 다시 설정하도록 Azure AD 테넌트를 구성](tutorial-enable-sspr.md)해야 합니다. 필요한 경우 사용자는 [https://aka.ms/ssprsetup 에서 SSPR에 등록](https://aka.ms/ssprsetup)할 수 있습니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에서 **내 앱** 페이지로 이동합니다.
1. 오른쪽 위 모서리에서 이름을 선택한 다음, 드롭다운 메뉴에서 **프로필**을 선택합니다.

    ![프로필 선택](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. **프로필** 페이지에서 **암호 변경**을 선택합니다.
1. **암호 변경** 페이지에서 기존(이전) 암호를 입력합니다. 이전 섹션에서 정의한 사용자 지정 금지 암호 목록에 있는 새 암호를 입력하고 확인한 다음, **제출**을 선택합니다.
1. 다음 예와 같이 관리자가 암호를 차단했다고 알리는 오류 메시지가 반환됩니다.

    ![사용자 지정 금지 암호 목록에 있는 암호를 사용하려고 하면 표시되는 오류 메시지](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 일부로 구성한 사용자 지정 금지 암호 목록을 더 이상 사용하지 않으려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택한 다음, 왼쪽 메뉴에서 **보안**을 선택합니다.
1. **관리** 메뉴 헤더 아래에서 **인증 방법**, **암호 보호**를 차례로 선택합니다.
1. **사용자 지정 목록 적용** 옵션을 *아니요*로 설정합니다.
1. 사용자 지정 금지 암호 구성을 업데이트하려면 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure AD에 대한 사용자 지정 암호 보호 목록을 사용하도록 설정하고 구성했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 사용자 지정 금지 암호 사용
> * 사용자 지정 금지 암호 목록에 항목 추가
> * 금지 암호를 사용하여 암호 변경 테스트

> [!div class="nextstepaction"]
> [위험 기반 Azure Multi-Factor Authentication 사용](./tutorial-enable-azure-mfa.md)