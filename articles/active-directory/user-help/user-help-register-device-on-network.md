---
title: 조직의 네트워크 - Azure AD에 개인 디바이스 등록
description: 조직의 보호된 리소스에 액세스할 수 있도록 조직의 네트워크에서 개인 디바이스를 등록하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/04/2019
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: b59ebe0bdab66a5ce6bdb1eefe6d9ae4b47bba0c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799403"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>조직의 네트워크에 개인 디바이스 등록
개인 디바이스(일반적으로 휴대폰이나 태블릿)를 조직의 네트워크에 등록합니다. 디바이스가 등록되면 조직의 제한된 리소스에 액세스할 수 있게 됩니다.

>[!Note]
>이 문서에서는 데모용으로 Windows 디바이스를 사용하지만 iOS, Android 또는 macOS를 실행하는 디바이스를 등록할 수도 있습니다.

## <a name="what-happens-when-you-register-your-device"></a>디바이스를 등록할 때 나타나는 결과
조직의 네트워크에 디바이스를 등록하는 동안 다음 작업이 발생합니다.

- Windows에서 조직의 네트워크에 디바이스를 등록합니다.

- 조직의 선택에 따라, [Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) 또는 [보안 정보](./security-info-setup-signin.md)를 통해 2단계 인증을 설정하도록 요구될 수 있습니다.

- 조직의 선택에 따라, Microsoft Intune과 같은 모바일 디바이스 관리 기능에 자동으로 등록될 수도 있습니다. Microsoft Intune에 등록하는 방법에 대한 자세한 내용은 [Intune에서 디바이스 등록](/intune-user-help/enroll-your-device-in-intune-all)을 참조하세요.

- 회사 또는 학교 계정의 사용자 이름 및 암호를 사용하여 로그인 프로세스가 진행됩니다.

## <a name="to-register-your-windows-device"></a>Windows 디바이스를 등록하려면

네트워크에서 개인 디바이스를 등록하려면 다음 단계를 수행합니다.

1. **설정**을 열고 **계정**을 선택합니다.

    ![설정 화면의 계정](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **회사 또는 학교 액세스**를 선택한 다음, **회사 또는 학교 액세스** 화면에서 **연결**을 선택합니다.

    ![연결 옵션이 강조 표시된 회사 또는 학교 액세스 화면](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. **회사 또는 학교 계정 추가** 화면에서 회사 또는 학교 계정에 대한 이메일 주소를 입력한 후 **다음**을 선택합니다. alain@contoso.com)을 입력합니다.

4. 회사 또는 학교 계정에 로그인한 다음, **로그인**을 선택합니다.

5. 본인 여부 확인 요청(2단계 인증을 사용하는 경우) 및 Windows Hello 설정(필요한 경우)을 포함하는 나머지 등록 프로세스를 완료합니다.

## <a name="to-verify-that-youre-registered"></a>등록되었는지 확인하려면
설정을 통해 등록되었는지 확인할 수 있습니다.

1. **설정**을 열고 **계정**을 선택합니다.

    ![설정 화면의 계정](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **회사 또는 학교 액세스**를 선택하고 회사 또는 학교 계정이 표시되는지 확인합니다.

    ![연결된 contoso 계정을 사용하여 회사 또는 학교 액세스 화면](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>다음 단계
조직의 네트워크에 개인 디바이스를 등록한 후에는 대부분의 리소스에 액세스할 수 있어야 합니다.

- 조직이 회사 디바이스의 가입을 원할 경우 [조직의 네트워크에 회사 디바이스 가입](user-help-join-device-on-network.md)을 참조하세요.