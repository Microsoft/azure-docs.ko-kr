---
title: 조직의 네트워크에 회사 디바이스 가입 - AD
description: 회사 디바이스를 조직의 네트워크에 가입하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: 17c0b7bd802d4a50343f88b9517313489943e10c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799488"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>조직의 네트워크에 회사 디바이스 가입
잠재적으로 제한된 리소스에 액세스할 수 있도록 회사 소유의 Windows 10 디바이스를 조직의 네트워크에 가입합니다.

## <a name="what-happens-when-you-join-your-device"></a>디바이스를 가입할 때 나타나는 결과
조직의 네트워크에 Windows 10 디바이스를 가입하는 동안 다음 작업이 발생합니다.

- 개인 계정을 사용하여 리소스에 액세스할 수 있도록 Windows가 조직의 네트워크에 디바이스를 등록해 줍니다. 디바이스가 등록되면 Windows는 네트워크에 디바이스를 가입하므로 조직의 사용자 이름 및 암호를 사용하여 로그인하고 제한된 리소스에 액세스할 수 있습니다.

- 조직의 선택에 따라, [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) 또는 [보안 정보](./security-info-setup-signin.md)를 통해 2단계 인증을 설정하도록 요구될 수 있습니다.

- 조직의 선택에 따라, Microsoft Intune과 같은 모바일 디바이스 관리 기능에 자동으로 등록될 수도 있습니다. Microsoft Intune에 등록하는 방법에 대한 자세한 내용은 [Intune에서 디바이스 등록](/intune-user-help/enroll-your-device-in-intune-all)을 참조하세요.

- 조직 계정을 사용한 자동 로그인 프로세스를 진행합니다.

## <a name="to-join-a-brand-new-windows-10-device"></a>새로운 Windows 10 디바이스를 가입하려면
디바이스가 새 제품이며 아직 설정한 적이 없으면, Windows OOBE(첫 실행 경험) 프로세스를 진행하여 네트워크에 디바이스를 가입할 수 있습니다.

1. 새 디바이스를 시작하고 OOBE 프로세스를 시작합니다.

2. **Microsoft에 로그인** 화면에서 회사 또는 학교 이메일 주소를 입력합니다.

    ![이메일 주소를 사용하는 로그인 화면](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. **암호 입력** 화면에서 암호를 입력합니다.

    ![암호 입력 화면](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. 모바일 디바이스에서 계정에 액세스할 수 있도록 디바이스를 승인합니다. 

    ![모바일 알림 화면](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. 개인 정보 및 Windows Hello(필요한 경우) 설정을 포함하여 OOBE 프로세스를 완료합니다.

    디바이스는 이제 조직의 네트워크에 가입되었습니다.

## <a name="to-make-sure-youre-joined"></a>가입되었는지 확인하려면
설정을 통해 가입되었는지 확인할 수 있습니다.

1. **설정**을 열고 **계정**을 선택합니다.

    ![설정 화면의 계정](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. **회사 또는 학교 액세스**를 선택 하 고 ** *\<your_organization>* Azure AD에 연결 된**것과 같은 텍스트가 표시 되는지 확인 합니다.

    ![연결된 contoso 계정을 사용하여 회사 또는 학교 액세스 화면](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>이미 구성된 Windows 10 디바이스를 가입하려면
디바이스를 얼마 동안 사용했으며 이미 설정된 경우 다음 단계에 따라 네트워크에 디바이스를 가입할 수 있습니다.

1. **설정**을 열고 **계정**을 선택합니다.

2. **회사 또는 학교 액세스**를 선택한 후 **연결**을 선택합니다.

    ![회사 또는 학교 액세스 및 연결 링크](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. **회사 또는 학교 계정 설정** 화면에서 **Azure Active Directory에 이 디바이스 가입**을 선택합니다.

    ![회사 또는 학교 계정 설정 화면](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. **이제 로그인해 보겠습니다.** 화면에서 이메일 주소(예: alain@contoso.com)를 입력하고 **다음**을 선택합니다.

    ![이제 로그인해 보겠습니다.](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. **암호 입력** 화면에 암호를 입력하고 **로그인**을 선택합니다.

    ![암호 입력](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. 모바일 디바이스에서 계정에 액세스할 수 있도록 디바이스를 승인합니다. 

    ![모바일 알림 화면](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. **Make sure this is your organization**(내 조직인지 확인) 화면에서 정보가 올바른지 검토한 후 **가입**을 선택합니다.

    ![내 조직인지 확인](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. **모든 설정을 완료했습니다.** 화면에서 **완료**를 클릭합니다.

    ![모든 설정을 완료했습니다 화면](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>가입되었는지 확인하려면
설정을 통해 가입되었는지 확인할 수 있습니다.

1. **설정**을 열고 **계정**을 선택합니다.

    ![설정 화면의 계정](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. **회사 또는 학교 액세스**를 선택 하 고 ** *\<your_organization>* Azure AD에 연결 된**것과 같은 텍스트가 표시 되는지 확인 합니다.

    ![연결된 contoso 계정을 사용하여 회사 또는 학교 액세스 화면](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>다음 단계
조직의 네트워크에 디바이스를 가입한 후에는 회사 또는 학교 계정 정보를 사용하여 모든 리소스에 액세스할 수 있습니다.

- 조직이 휴대폰과 같은 개인 디바이스를 등록하도록 요구할 경우 [조직의 네트워크에 개인 디바이스 등록](user-help-register-device-on-network.md)을 참조하세요.

- 조직이 Microsoft Intune을 사용하여 관리되고 있는데 등록, 로그인 또는 기타 Intune 관련 문제에 대한 질문이 있는 경우 [Intune 사용자 도움말 콘텐츠](/intune-user-help/use-managed-devices-to-get-work-done)를 참조하세요.