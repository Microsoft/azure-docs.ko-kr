---
title: RADIUS-Azure Active Directory를 사용 하는 RDG 및 Azure MFA 서버
description: RADIUS를 사용하여 RD(Remote Desktop) 게이트웨이 및 Azure Multi-Factor Authentication을 배포하는 데 도움이 되는 Azure Multi-Factor Authentication 페이지입니다.
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
ms.openlocfilehash: 7ac4813e26d847f99f6a3bb7e3eb91bf06797d3c
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949340"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>RADIUS를 사용한 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버

일반적으로 RD (원격 데스크톱) 게이트웨이는 로컬 [NPS (네트워크 정책 서비스)](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) 를 사용 하 여 사용자를 인증 합니다. 이 문서에서는 원격 데스크톱 게이트웨이(로컬 NPS를 통해)에서 Multi-Factor Authentication 서버까지 RADIUS 요청을 라우팅하는 방법을 설명합니다. Azure MFA와 RD 게이트웨이를 함께 사용하면 사용자가 강력한 인증을 수행하면서 어디서든 자신의 작업 환경에 액세스할 수 있습니다.

터미널 서비스에 대한 Windows 인증이 Server 2012 R2에 대해 지원되지 않으므로 MFA 서버와 통합하려면 RD 게이트웨이 및 RADIUS를 사용합니다.

별도의 서버에 Multi-Factor Authentication 서버를 설치합니다. 이 서버는 원격 데스크톱 게이트웨이 서버의 NPS로 RADIUS 요청을 다시 프록시합니다. NPS에서 사용자 이름과 암호의 유효성을 검사한 후 Multi-Factor Authentication 서버로 응답을 반환합니다. 그런 다음 MFA 서버에서 두 번째 인증 단계를 수행하고 결과를 게이트웨이로 반환합니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 클라우드 기반 MFA를 사용 하는 경우 [Azure Multi-Factor Authentication에 대 한 RADIUS 인증과 통합](howto-mfa-nps-extension.md)하는 방법을 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 도메인에 가입된 Azure MFA 서버. 설치되어 있지 않은 경우 [Azure Multi-factor Authentication 서버 시작](howto-mfaserver-deploy.md)의 단계를 따릅니다.
- 기존 구성된 NPS 서버.
- 네트워크 정책 서비스를 인증하는 원격 데스크톱 게이트웨이.

> [!NOTE]
> 이 문서는 Azure MFA(클라우드 기반)가 아닌 MFA 서버 배포에만 해당합니다.

## <a name="configure-the-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이 구성

Azure Multi-Factor Authentication 서버에 RADIUS 인증을 보내도록 RD 게이트웨이를 구성합니다.

1. RD 게이트웨이 관리자에서 서버 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
2. **RD CAP 저장소** 탭으로 이동하고 **NPS를 실행하는 중앙 서버**를 선택합니다.
3. 각 서버의 이름 또는 IP 주소를 입력하여 하나 이상의 Azure Multi-Factor Authentication 서버를 RADIUS 서버로 추가합니다.
4. 각 서버에 대한 공유 암호를 만듭니다.

## <a name="configure-nps"></a>NPS 구성

RD 게이트웨이는 NPS를 사용하여 Azure Multi-Factor Authentication에 RADIUS 요청을 보냅니다. NPS를 구성하려면 먼저 RD 게이트웨이가 2단계 확인이 완료되기 전에 시간 초과되는 것을 방지하도록 시간 제한 설정을 변경합니다. 그런 다음 MFA 서버에서 RADIUS 인증을 받도록 NPS를 업데이트합니다. NPS를 구성하려면 다음 절차를 따르십시오.

### <a name="modify-the-timeout-policy"></a>시간 제한 정책 수정

1. NPS의 왼쪽 열에서 **RADIUS 클라이언트 및 서버** 메뉴를 열고 **원격 RADIUS 서버 그룹**을 선택합니다.
2. **TS 게이트웨이 서버 그룹**을 선택합니다.
3. **부하 분산** 탭으로 이동합니다.
4. **응답 없이 다음 시간(초)이 경과되면 요청이 손실된 것으로 간주** 및 **서버가 사용 불가능 상태로 표시된 경우 요청 사이의 시간(초)** 을 모두 30-60초 사이로 변경합니다. (인증하는 동안 여전히 서버가 시간 초과되는 경우 여기로 돌아와서 초 수를 늘립니다.)
5. **인증/계정** 탭으로 이동하고 지정된 RADIUS 포트가 Multi-Factor Authentication 서버에서 수신 대기하는 포트와 일치하는지 확인합니다.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>MFA 서버에서 인증을 받도록 NPS 준비

1. 왼쪽 열의 RADIUS 클라이언트 및 서버 아래에서 **RADIUS 클라이언트**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택합니다.
2. Azure Multi-Factor Authentication 서버를 RADIUS 클라이언트로 추가합니다. 친숙한 이름을 선택하고 공유 암호를 지정합니다.
3. 왼쪽 열에서 **정책** 메뉴를 열고 **연결 요청 정책**을 선택합니다. RD 게이트웨이를 구성할 때 만든 TS 게이트웨이 권한 부여 정책이라는 정책이 있어야 합니다. 이 정책은 Multi-Factor Authentication 서버에 RADIUS 요청을 전달합니다.
4. **TS 게이트웨이 권한 부여 정책**을 마우스 오른쪽 단추로 클릭하고 **복제 정책**을 선택합니다.
5. 새 정책을 열고 **조건** 탭으로 이동합니다.
6. Azure Multi-Factor Authentication 서버 RADIUS 클라이언트에 대한 2단계에서 친숙한 이름과 클라이언트 이름이 일치하는 조건을 추가합니다.
7. **설정** 탭으로 이동하고 **인증**을 선택합니다.
8. 인증 공급자를 **이 서버에서 요청 인증**으로 변경합니다. 이 정책은 NPS가 Azure MFA 서버에서 RADIUS 요청을 수신할 때 루프 조건이 될 수 있는 Azure Multi-Factor Authentication 서버에 RADIUS 요청을 전송하는 대신 로컬로 인증이 발생하도록 합니다.
9. 루프 조건을 방지하려면 새 정책이 **연결 요청 정책** 창의 원래 정책 위에 정렬되어 있는지 확인합니다.

## <a name="configure-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 구성

Azure Multi-Factor Authentication 서버는 RD 게이트웨이 및 NPS 사이의 RADIUS 프록시로 구성됩니다.  RD 게이트웨이 서버와 별개의 도메인에 가입된 서버에 설치해야 합니다. 다음 절차에 따라 Azure Multi-Factor Authentication 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버를 열고 RADIUS 인증 아이콘을 선택합니다.
2. **RADIUS 인증 사용** 확인란을 선택합니다.
3. 클라이언트 탭에서 포트가 NPS에 구성된 포트와 일치하는지 확인한 다음 **추가**를 선택합니다.
4. RD 게이트웨이 서버 IP 주소, 애플리케이션 이름(선택 사항) 및 공유 암호를 추가합니다. 공유 암호는 Azure Multi-Factor Authentication 서버 및 RD 게이트웨이 모두에서 동일해야 합니다.
3. **대상** 탭으로 이동하고 **RADIUS 서버** 라디오 단추를 선택합니다.
4. **추가**를 선택하고 NPS 서버의 IP 주소, 공유 암호 및 포트를 입력합니다. 중앙 NPS를 사용하지 않는 한 RADIUS 클라이언트와 RADIUS 대상은 동일합니다. 공유 암호는 NPS 서버에서 RADIUS 클라이언트 섹션의 설정 하나와 일치해야 합니다.

![MFA 서버의 Radius 인증](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>다음 단계

- Azure MFA 및 [IIS 웹앱](howto-mfaserver-iis.md) 통합

- [Azure Multi-Factor Authentication FAQ](multi-factor-authentication-faq.md)에서 답변 얻기