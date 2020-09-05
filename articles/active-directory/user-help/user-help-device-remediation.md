---
title: “여기서는 거기에 접근할 수 없습니다.” 오류 수정 - Azure AD
description: “여기서는 거기에 접근할 수 없습니다.” 오류 메시지가 표시되는 이유에 대한 잠재적 수정 사항을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799420"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>“여기서는 거기에 접근할 수 없습니다.” 오류 메시지가 표시되는 가능한 원인

조직의 내부 웹앱 또는 서비스에 액세스하는 동안 **여기서는 거기에 접근할 수 없습니다.** 라는 오류 메시지가 표시될 수 있습니다. 이 메시지는 조직에서 디바이스가 조직 리소스에 액세스하지 못하도록 방지하는 정책을 설정했음을 의미합니다. 이 문제를 해결하기 위해 결과적으로 기술 지원 팀에 문의해야 할 수 있지만 먼저 다음 작업을 시도할 수 있습니다.

## <a name="make-sure-youre-using-a-supported-browser"></a>지원되는 브라우저를 사용하고 있는지 확인
지원되지 않는 브라우저에서 조직의 사이트에 액세스하려고 함을 나타내는 **여기서는 거기에 접근할 수 없습니다.** 메시지가 표시되면 실행 중인 브라우저를 확인합니다.

![브라우저 지원과 관련된 오류 메시지](media/user-help-device-remediation/browser-version.png)

이 문제를 해결하려면 사용 중인 운영 체제에 따라 지원되는 브라우저를 설치하고 실행해야 합니다. Windows 10을 사용하는 경우 지원되는 브라우저에는 Microsoft Edge, Internet Explorer 및 Google Chrome이 포함됩니다. 다른 운영 체제를 사용하는 경우 [지원되는 브라우저](../conditional-access/concept-conditional-access-conditions.md#supported-browsers) 전체 목록을 확인할 수 있습니다.

## <a name="make-sure-youre-using-a-supported-operating-system"></a>지원되는 운영 체제를 사용하고 있는지 확인
다음을 포함하여 지원되는 운영 체제 버전을 실행하고 있는지 확인합니다.

- **Windows 클라이언트** Windows 7 이상

- **Windows Server** Windows Server 2008 R2 이상

- **macOS** macOS X 이상

- **Android 및 iOS** 최신 버전의 Android 및 iOS 모바일 운영 체제

이 문제를 해결하려면 지원되는 운영 체제를 설치하고 실행해야 합니다.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>디바이스가 네트워크에 가입되어 있는지 확인
디바이스가 조직의 액세스 정책을 준수하지 않음을 나타내는 **여기서는 거기에 접근할 수 없습니다.** 메시지가 표시되면 디바이스를 조직의 네트워크에 가입했는지 확인합니다.

![네트워크에 연결되어 있는지 여부와 관련된 오류 메시지](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>디바이스가 네트워크에 가입되어 있는지 여부를 확인하려면
1. 회사 또는 학교 계정을 사용하여 Windows에 로그인합니다. alain@contoso.com)을 입력합니다.

2. VPN(가상 사설망) 또는 DirectAccess를 통해 회사 네트워크에 연결합니다.

3. 연결한 후에는 **Windows 로고 키 + L**을 눌러 디바이스를 잠급니다.

4. 회사 또는 학교 계정을 사용하여 디바이스 잠금을 해제한 후 문제가 있는 앱 또는 서비스에 다시 액세스해 봅니다.

    **여기서는 거기에 접근할 수 없습니다.** 오류 메시지가 다시 표시되면 **자세한 내용** 링크를 선택하고 세부 정보를 사용하여 기술 지원 팀에 문의합니다.

### <a name="to-join-your-device-to-your-network"></a>디바이스를 네트워크에 가입하려면
디바이스가 조직의 네트워크에 가입되지 않은 경우 다음 두 가지 중 하나를 수행할 수 있습니다.

- **회사 디바이스를 가입합니다.** 잠재적으로 제한된 리소스에 액세스할 수 있도록 회사 소유의 Windows 10 디바이스를 조직의 네트워크에 가입합니다. 자세한 내용 및 단계별 지침은 [조직의 네트워크에 회사 디바이스 가입](user-help-join-device-on-network.md)을 참조하세요.

- **작업할 개인 디바이스 등록** 일반적으로 휴대폰이나 태블릿에 해당하는 개인 디바이스를 조직의 네트워크에 등록합니다. 디바이스가 등록되면 조직의 제한된 리소스에 액세스할 수 있게 됩니다. 자세한 내용 및 단계별 지침은 [조직의 네트워크에 개인 디바이스 등록](user-help-register-device-on-network.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- [MyApps 포털이란?](./my-apps-portal-end-user-access.md)

- [암호가 아닌 휴대폰을 사용하여 로그인](user-help-auth-app-sign-in.md)