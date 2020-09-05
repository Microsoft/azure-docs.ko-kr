---
title: Azure MFA 서버 모바일 앱 웹 서비스-Azure Active Directory
description: Microsoft Authenticator 앱을 사용하여 사용자에게 푸시 알림을 보내도록 MFA 서버를 구성합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84ed3ed26bc35feff016079117def256e8fe4ab5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919559"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버를 사용하여 모바일 앱 인증 활성화

Microsoft Authenticator 앱은 추가적인 대역외 확인 옵션을 제공합니다. 로그인 하는 동안 사용자에 게 자동 전화 통화 또는 SMS를 배치 하는 대신 Azure Multi-Factor Authentication 사용자의 스마트폰 또는 태블릿에서 Microsoft Authenticator 앱에 알림을 푸시합니다. 사용자는 앱에서 **확인** 을 탭 하거나 PIN을 입력 하 고 "인증"을 탭 하 여 로그인을 완료 하기만 하면 됩니다.

전화 수신을 신뢰할 수 없는 경우 2단계 인증에 모바일 앱을 사용하는 것이 좋습니다. OATH 토큰 생성기로 앱을 사용하는 경우 네트워크 또는 인터넷 연결이 필요하지 않습니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

> [!IMPORTANT]
> Azure Multi-Factor Authentication 서버 v8.x 이상을 설치한 경우 아래 단계의 대부분을 수행할 필요가 없습니다. 모바일 앱 인증은 [모바일 앱 구성](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server)의 단계에 따라 설정할 수 있습니다.

## <a name="requirements"></a>요구 사항

Microsoft Authenticator 앱을 사용하려면 Azure Multi-Factor Authentication 서버 v8.x 이상을 실행해야 합니다.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버에서 모바일 앱 설정 구성

1. Multi-Factor Authentication 서버 콘솔에서 사용자 포털 아이콘을 클릭합니다. 사용자가 자신의 인증 방법을 제어할 수 있도록 허용되면 설정 탭의 **사용자가 방법을 선택할 수 있도록 허용**에서 **모바일 앱**을 선택합니다. 이 기능을 사용하지 않으면 최종 사용자는 지원 센터에 문의하여 Mobile App에 대한 정품 인증을 완료해야 합니다.
2. **사용자가 모바일 앱을 활성화할 수 있도록 허용** 상자를 선택 합니다.
3. **사용자 등록 허용** 확인란을 선택 합니다.
4. **모바일 앱** 아이콘을 클릭 합니다.
5. **계정 이름** 필드를 이 계정의 모바일 애플리케이션에서 표시할 회사 또는 조직 이름으로 채웁니다.
   ![MFA 서버 구성 Mobile App 설정](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 및 타사 VPN을 사용한 고급 시나리오](howto-mfaserver-nps-vpn.md)
