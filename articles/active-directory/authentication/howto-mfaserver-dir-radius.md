---
title: RADIUS 및 Azure MFA 서버-Azure Active Directory
description: RADIUS 인증 및 Azure Multi-Factor Authentication 서버 배포
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 679a8fee80711e8244cf953e78fd7260d922ff49
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949391"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버와 RADIUS 인증 통합

RADIUS는 인증 요청을 수락하고 이 요청을 처리하는 표준 프로토콜입니다. Azure Multi-Factor Authentication 서버는 RADIUS 서버 역할을 할 수 있습니다. RADIUS 클라이언트(VPN 어플라이언스)와 인증 대상 간에 삽입하여 2단계 인증을 추가합니다. 인증 대상은 Active Directory, LDAP 디렉터리 또는 다른 RADIUS 서버일 수 있습니다. Azure MFA(Multi-Factor Authentication)가 동작하려면 클라이언트 서버와 인증 대상 모두와 통신할 수 있도록 Azure MFA 서버를 구성해야 합니다. Azure MFA 서버는 RADIUS 클라이언트의 요청을 수락하고, 인증 대상에 대해 자격 증명의 유효성을 검사하고, Azure Multi-Factor Authentication을 추가하고 다시 RADIUS 클라이언트로 응답을 보냅니다. 기본 인증 및 Azure Multi-Factor Authentication 모두가 성공한 경우에만 인증 요청이 성공합니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 클라우드 기반 MFA를 사용 하는 경우 [기존 NPS 인프라를 Azure Multi-Factor Authentication와 통합](howto-mfa-nps-extension.md)을 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

> [!NOTE]
> MFA 서버는 RADIUS 서버로 작동하는 경우 PAP(암호 인증 프로토콜)와 MSCHAPv2(Microsoft의 Challenge-Handshake 인증 프로토콜) RADIUS 프로토콜만 지원합니다.  EAP(확장할 수 있는 인증 프로토콜) 같은 기타 프로토콜은 MFA 서버가 프로토콜을 지원하는 또 다른 RADIUS 서버에 대한 RADIUS 프록시로 작동하는 경우에 사용할 수 있습니다.
>
> 이 구성에서 MFA 서버가 대체 프로토콜을 사용하여 성공적인 RADIUS Challenge 응답을 시작할 수 없기 때문에 단방향 SMS 및 OATH 토큰이 작동하지 않습니다.

![MFA 서버의 Radius 인증](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>RADIUS 클라이언트 추가

RADIUS 인증을 구성하려면 Windows 서버에 Azure Multi-Factor Authentication 서버를 설치합니다. Active Directory 환경에 있으면 서버가 네트워크 내의 도메인에 가입되어 있어야 합니다. 다음 절차에 따라 Azure Multi-Factor Authentication 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버에서 왼쪽 메뉴의 RADIUS 인증 아이콘을 클릭합니다.
2. **RADIUS 인증 사용** 확인란을 선택합니다.
3. 클라이언트 탭에서 Azure MFA RADIUS 서비스가 비표준 포트에서 RADIUS 요청을 수신해야 하는 경우 인증 및 계정 포트를 변경합니다.
4. **추가**를 클릭합니다.
5. Azure Multi-Factor Authentication 서버, 애플리케이션 이름(옵션) 및 공유 비밀을 인증하는 어플라이언스/서버의 IP 주소를 입력합니다.

   애플리케이션 이름이 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.

   공유 암호는 Azure Multi-Factor Authentication 서버 및 어플라이언스/서버 모두에서 동일해야 합니다.

6. 모든 사용자를 내부 서버로 가져왔거나 Multi-Factor Authentication을 사용하는 경우 **Azure Multi-Factor Authentication 사용자 일치 필요** 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 2단계 확인에서 제외할 예정이면 이 확인란의 선택을 취소합니다.
7. 백업으로 모바일 확인 앱에서 OATH 암호를 사용하려는 경우 **대체 OATH 토큰 사용** 상자를 선택합니다.
8. **확인**을 클릭합니다.

4-8단계를 반복하여 필요한 만큼 추가 RADIUS 클라이언트를 추가합니다.

## <a name="configure-your-radius-client"></a>RADIUS 클라이언트 구성

1. **대상** 탭을 클릭 합니다.
   * Azure MFA 서버가 Active Directory 환경에서 도메인에 가입 된 서버에 설치 되어 있는 경우 **Windows 도메인**을 선택 합니다.
   * LDAP 디렉터리에 대해 사용자를 인증 해야 하는 경우 **ldap 바인드**를 선택 합니다.
      디렉터리 통합 아이콘을 선택하고 서버가 디렉터리에 바인딩할 수 있도록 설정 탭에서 LDAP 구성을 편집합니다. LDAP 구성 지침은 [Ldap 프록시 구성 가이드](howto-mfaserver-dir-ldap.md)에서 찾을 수 있습니다.
   * 다른 RADIUS 서버에 대해 사용자를 인증 해야 하는 경우 **radius 서버**를 선택 합니다.
1. **추가**를 클릭하여 Azure MFA 서버가 RADIUS 요청을 프록시할 서버를 구성합니다.
1. RADIUS 서버 추가 대화 상자에서 RADIUS 서버의 IP 주소와 공유 암호를 입력합니다.

   공유 암호는 Azure Multi-Factor Authentication 서버 및 RADIUS 서버 모두에서 동일해야 합니다. RADIUS 서버에서 다른 포트를 사용하는 경우 인증 포트 및 계정 포트를 변경합니다.

1. **확인**을 클릭합니다.
1. Azure MFA 서버에서 전송되는 액세스 요청을 처리할 수 있도록 다른 RADIUS 서버에서 RADIUS 클라이언트로 Azure MFA 서버를 추가합니다. Azure Multi-Factor Authentication 서버에 구성된 동일한 공유 암호를 사용합니다.

RADIUS 서버를 더 추가하려면 다음 단계를 반복합니다. **위로 이동** 및 **아래로 이동** 단추를 사용하여 Azure MFA 서버에서 호출해야 하는 순서를 구성합니다.

Azure Multi-Factor Authentication 서버를 성공적으로 구성했습니다. 이제 서버는 구성된 클라이언트에서의 RADIUS 액세스 요청에 대해 구성된 포트에서 수신합니다.

## <a name="radius-client-configuration"></a>RADIUS 클라이언트 구성

RADIUS 클라이언트를 구성하려면 지침을 따르십시오.

* Radius 서버 역할을 하는 Azure Multi-Factor Authentication 서버의 IP 주소에 대 한 RADIUS를 통해 인증 하도록 어플라이언스/서버를 구성 합니다.
* 이전에 구성된 동일한 공유 암호를 사용합니다.
* 사용자 자격 증명의 유효성을 검사 하 고, 2 단계 인증을 수행 하 고, 응답을 수신 하 고, RADIUS 액세스 요청에 응답할 수 있도록 RADIUS 제한 시간을 30-60 초로 구성 합니다.

## <a name="next-steps"></a>다음 단계

클라우드에서 Azure Multi-Factor Authentication을 설치한 경우 [RADIUS 인증과 통합](howto-mfa-nps-extension.md)하는 방법을 알아봅니다. 
