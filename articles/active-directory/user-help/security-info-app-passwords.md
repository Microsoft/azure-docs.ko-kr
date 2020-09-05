---
title: 보안 정보(미리 보기) 페이지에서 앱 암호 만들기 - Azure AD
description: 조직에서 비브라우저 앱 또는 2단계 인증을 지원하지 않는 앱에 사용할 자동 생성 암호(앱 암호)를 만듭니다. 이 앱 암호는 일반 암호와 별개이며 보안 정보 페이지에서 설정할 수 있습니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 9de054162a59b10fbd02bed9b3e84634ee12125b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799199"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>보안 정보(미리 보기) 페이지에서 앱 암호 만들기

Outlook 2010과 같은 특정 앱은 2단계 인증을 지원하지 않습니다. 따라서 조직에서 2단계 인증을 사용하는 경우 앱이 작동하지 않습니다. 이 문제를 해결하려면 사용자의 일반 암호와는 별도로 자동으로 생성된 암호를 만들어 각 비 브라우저 앱과 함께 사용할 수 있습니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>관리자가 사용자의 앱 암호 사용을 허용하지 않을 수 있습니다. **앱 암호**가 옵션으로 표시되지 않으면 조직에서 사용할 수 없는 것입니다.

앱 암호를 사용하는 경우 다음 사항을 고려해야 합니다.

- 앱 암호는 자동으로 생성되며 앱당 한 번 만들어 입력해야 합니다.

- 사용자당 40개의 암호로 제한되어 있습니다. 해당 제한을 초과한 후 암호를 만들려고 하면 새 암호를 만들기 전에 기존 암호를 삭제할 것인지를 묻는 메시지가 표시됩니다.

    >[!Note]
    >Outlook을 포함한 Office 2013 클라이언트는 새로운 인증 프로토콜을 지원하며 2단계 인증과 함께 사용할 수 있습니다. 이 지원으로 2단계 인증을 켜고 나면 Office 2013 클라이언트에는 더 이상 앱 암호가 필요하지 않습니다. 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) 문서를 참조하세요.

## <a name="create-new-app-passwords"></a>새 앱 암호 만들기

회사 또는 학교 계정에 2단계 인증을 사용하고 관리자가 보안 정보 환경을 설정한 경우 **보안 정보** 페이지를 사용하여 앱 암호를 만들고 삭제할 수 있습니다.

>[!Note]
>관리자가 보안 정보 환경을 설정하지 않은 경우 [2단계 인증을 위한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md) 섹션의 지침과 정보를 따라야 합니다.

### <a name="to-create-a-new-app-password"></a>새로운 앱 암호를 만들려면

1. 회사 또는 학교 계정에 로그인한 다음, https://myaccount.microsoft.com/ 페이지로 이동합니다.

    ![보안 정보 링크가 강조 표시된 내 프로필 페이지](media/security-info/securityinfo-myprofile-page.png)

2. 왼쪽 탐색 창 또는 **보안 정보** 블록의 링크에서 **보안 정보**를 선택한 다음, **보안 정보** 페이지에서 **메서드 추가**를 선택합니다.

    ![메서드 추가 옵션이 강조 표시된 보안 정보 페이지](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **방법 추가** 페이지의 드롭다운 목록에서 **앱 암호**를 선택한 다음, **추가**를 선택합니다.

    ![앱 암호가 선택된 방법 추가 상자](media/security-info/securityinfo-myprofile-addpassword.png)

4. 앱 암호가 필요한 앱의 이름을 입력하고 **다음**을 선택합니다.

    ![앱 이름이 표시되는 앱 암호 페이지](media/security-info/securityinfo-myprofile-password-appname.png)

5. **암호** 상자의 텍스트를 복사한 후 앱(이 예제에서는 Outlook 2010)의 암호 영역에 암호를 붙여넣고 **완료**를 선택합니다.

    ![앱 이름이 표시되는 앱 암호 페이지](media/security-info/securityinfo-myprofile-password-copytext.png)

    암호가 추가되며 앞으로 앱에 성공적으로 로그인할 수 있습니다.

## <a name="delete-your-app-passwords"></a>앱 암호 삭제

앱 암호가 필요한 앱이 더 이상 필요하지 않은 경우 연결된 앱 암호를 삭제할 수 있습니다. 앱 암호를 삭제하면 사용 가능한 앱 암호 스폿 중 하나가 나중에 사용할 수 있도록 확보됩니다.

>[!Important]
>앱 암호를 실수로 삭제하면 실행 취소할 수 있는 방법이 없습니다. 이 문서의 [새 앱 암호 만들기](#create-new-app-passwords) 섹션 단계에 따라 새 앱 암호를 만들고 앱에 다시 입력해야 합니다.

### <a name="to-delete-an-app-password"></a>앱 암호를 삭제하려면

1. **보안 정보** 페이지에서 특정 앱의 **앱 암호** 옵션 옆에 있는 **삭제** 링크를 선택합니다.

    ![보안 정보에서 앱 암호 방법을 삭제하기 위한 링크](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. 확인 상자에서 **예**를 선택하여 **앱 암호**를 삭제합니다. 앱 암호가 삭제되면 보안 정보에서 제거되고 **보안 정보** 페이지에서 사라집니다.

## <a name="for-more-information"></a>참조 항목

- **보안 정보** 페이지 및 설정 방법에 대한 자세한 내용은 [보안 정보 개요](./security-info-setup-signin.md)를 참조하세요.