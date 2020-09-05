---
title: NPS 확장을 사용한 Azure MFA가 포함된 VPN - Azure Active Directory
description: Microsoft Azure용 네트워크 정책 서버 확장을 사용하여 VPN 인프라를 Azure MFA와 통합합니다.
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
ms.openlocfilehash: 13ed87903845d9f8295e56f187b643d73fbfb04e
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717883"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Azure용 네트워크 정책 서버 확장을 사용하여 VPN 인프라를 Azure MFA와 통합

Azure용 NPS(네트워크 정책 서비스) 확장을 사용하면 조직에서 2단계 인증을 제공하는 클라우드 기반 [Azure MFA(Multi-Factor Authentication)](howto-mfaserver-nps-rdg.md)를 사용하여 RADIUS(Remote Authentication Dial-In User Service) 클라이언트 인증을 보호할 수 있습니다.

이 문서에서는 Azure용 NPS 확장을 사용하여 NPS 인프라를 MFA와 통합하기 위한 지침을 제공합니다. 이 프로세스에서는 VPN을 사용하여 네트워크에 연결하려고 하는 사용자를 위해 2단계 인증을 설정합니다.

네트워크 정책 및 액세스 서비스는 조직에 다음과 같은 기능을 제공합니다.

* 네트워크 요청을 관리 및 제어하여 다음을 지정할 수 있는 중앙 위치를 할당합니다.

  * 연결할 수 있는 사용자

  * 1일에 허용되는 연결 횟수

  * 연결 기간

  * 클라이언트가 연결에 사용해야 하는 보안 수준

    각 VPN 또는 원격 데스크톱 게이트웨이 서버에 대해 정책을 지정하지 않고, 이러한 항목이 중앙 위치에 있을 때 지정합니다. RADIUS 프로토콜은 중앙 집중식 AAA(인증, 권한 부여 및 계정 관리)를 제공하는 데 사용됩니다.

* 디바이스가 허용되거나 제한되지 않는지 또는 네트워크 리소스에 대한 액세스가 제한되는지 여부를 결정하는 NAP(네트워크 액세스 보호) 클라이언트 상태 정책을 설정하고 적용합니다.

* 802.1x 지원 무선 액세스 지점 및 이더넷 스위치에 액세스하기 위한 인증 및 권한 부여를 적용할 수 있는 방법을 제공합니다.
  자세한 내용은 [네트워크 정책 서버](/windows-server/networking/technologies/nps/nps-top)를 참조하세요.

보안을 강화하고 높은 수준의 규정 준수를 제공하기 위해 조직에서는 NPS를 Azure Multi-Factor Authentication과 통합하여 사용자가 2단계 인증을 통해 VPN 서버의 가상 포트에 연결하도록 할 수 있습니다. 사용자가 액세스 권한을 부여받으려면 자신이 제어할 수 있는 기타 정보와 함께 사용자 이름 및 암호 조합을 제공해야 합니다. 이 정보는 신뢰할 수 있어야 하고 복제하기 어려워야 합니다. 휴대폰 번호, 유선 전화 번호 또는 모바일 디바이스의 애플리케이션이 여기에 포함될 수 있습니다.

Azure용 NPS 확장을 사용하기 전에 통합된 NPS 및 MFA 환경에 대한 2단계 인증을 구현하려는 고객은 온-프레미스 환경에서 별도의 MFA 서버를 구성하고 유지 관리해야 했습니다. 이 유형의 인증은 RADIUS를 사용하는 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버에서 제공합니다.

조직에서는 Azure용 NPS 확장을 통해 온-프레미스 기반 MFA 솔루션 또는 클라우드 기반 MFA 솔루션을 배포하여 RADIUS 클라이언트 인증을 보호할 수 있습니다.

## <a name="authentication-flow"></a>인증 흐름

사용자는 VPN 서버의 가상 포트에 연결할 때 먼저 다양한 프로토콜을 사용하여 인증을 받아야 합니다. 프로토콜은 사용자 이름 및 암호와 인증서 기반 인증 방법을 조합해서 사용하도록 허용합니다.

ID 인증 및 확인 외에도 사용자는 적절한 전화 접속 권한을 가지고 있어야 합니다. 간단한 구현에서 액세스를 허용하는 전화 접속 권한은 Active Directory 사용자 개체에 직접 설정됩니다.

![Active Directory 사용자 및 컴퓨터 사용자 속성의 전화 접속 탭](./media/howto-mfa-nps-extension-vpn/image1.png)

간단한 구현에서 각 VPN 서버에서 각 로컬 VPN 서버에 정의된 정책에 따라 액세스를 허용하거나 거부합니다.

더 크고 확장성 있는 구현에서는 VPN 액세스를 허용하거나 거부하는 정책이 RADIUS 서버에 중앙 집중화됩니다. 이러한 경우 VPN 서버는 연결 요청 및 계정 메시지를 RADIUS 서버로 전달하는 액세스 서버(RADIUS 클라이언트) 역할을 합니다. VPN 서버의 가상 포트에 연결하려면 사용자를 인증하고 RADIUS 서버에서 중앙 집중식으로 정의된 조건을 충족해야 합니다.

Azure용 NPS 확장을 NPS와 통합한 경우 성공적인 인증 흐름 결과는 다음과 같습니다.

1. VPN 서버에서 원격 데스크톱 세션과 같은 리소스에 연결하기 위해 VPN 사용자로부터 사용자 이름과 암호가 포함된 인증 요청을 받습니다.
2. RADIUS 클라이언트 역할을 하는 VPN 서버에서 해당 요청을 RADIUS *액세스 요청* 메시지로 변환하고 NPS 확장이 설치된 RADIUS 서버로 이 메시지(암호가 암호화됨)를 보냅니다.
3. Active Directory에서 사용자 이름과 암호 조합이 확인됩니다. 사용자 이름이나 암호가 올바르지 않으면 RADIUS 서버에서 *액세스 거부* 메시지를 보냅니다.
4. NPS 연결 요청 및 네트워크 정책에 지정된 모든 조건(예: 시간 또는 그룹 멤버 자격 제한)이 충족되는 경우 NPS 확장에서 Azure Multi-Factor Authentication을 통한 보조 인증 요청을 트리거합니다.
5. Azure Multi-Factor Authentication에서 Azure Active Directory와 통신하여 사용자의 세부 정보를 검색하고 사용자가 구성한 방법(휴대폰 통화, 문자 메시지 또는 모바일 앱)을 사용하여 보조 인증을 수행합니다.
6. MFA 챌린지가 성공할 경우 Azure Multi-Factor Authentication은 NPS 확장에 해당 결과를 전달합니다.
7. 연결 시도가 인증되고 권한이 부여된 후에 확장이 설치된 NPS에서 RADIUS *액세스 허용* 메시지를 VPN 서버(RADIUS 클라이언트)로 보냅니다.
8. 사용자가 VPN 서버의 가상 포트에 대한 액세스 권한을 부여받고 암호화된 VPN 터널을 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 섹션에서는 MFA를 VPN과 통합하기 전에 완료해야 하는 필수 구성 요소에 대해 자세히 설명합니다. 이 문서를 시작하기 전에 다음과 같은 필수 조건이 있어야 합니다.

* VPN 인프라
* 네트워크 정책 및 액세스 서비스 역할
* Azure Multi-Factor Authentication 라이선스
* Windows Server 소프트웨어
* 라이브러리
* 온-프레미스 Active Directory와 동기화되는 Azure AD(Azure Active Directory)
* Azure Active Directory GUID ID

### <a name="vpn-infrastructure"></a>VPN 인프라

이 문서에서는 Microsoft Windows Server 2016을 사용하여 작동 중인 VPN 인프라가 있으며 현재 VPN 서버가 연결 요청을 RADIUS 서버로 전달하도록 구성되지 않았다고 가정합니다. 이 문서에서는 중앙 RADIUS 서버를 사용하도록 VPN 인프라를 구성합니다.

작동 중인 VPN 인프라가 없는 경우 Microsoft 및 타사 사이트에서 찾을 수 있는 다양한 VPN 설정 자습서의 지침에 따라 이 인프라를 빠르게 만들 수 있습니다.

### <a name="the-network-policy-and-access-services-role"></a>네트워크 정책 및 액세스 서비스 역할

네트워크 정책 및 액세스 서비스는 RADIUS 서버 및 클라이언트 기능을 제공합니다. 이 문서에서는 사용자 환경의 구성원 서버 또는 도메인 컨트롤러에 네트워크 정책 및 액세스 서비스 역할을 설치했다고 가정합니다. 이 가이드에서는 VPN 구성을 위해 RADIUS를 구성합니다. VPN 서버 *이외의* 서버에 네트워크 정책 및 액세스 서비스 역할을 설치합니다.

Windows Server 2012 이상에서 네트워크 정책 및 액세스 서비스 역할 서비스를 설치하는 방법에 대한 자세한 내용은 [NAP 상태 정책 서버 설치](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10))를 참조하세요. Windows Server 2016에서는 NAP가 더 이상 사용되지 않습니다. 도메인 컨트롤러에 NPS를 설치하기 위한 권장 사항을 포함하여 NPS에 대한 모범 사례에 대한 자세한 내용은 [NPS 모범 사례](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10))를 참조하세요.

### <a name="azure-mfa-license"></a>Azure MFA 라이선스

Azure Multi-Factor Authentication에는 라이선스가 필요하며 이 라이선스는 Azure AD Premium, Enterprise Mobility + Security 또는 Multi-Factor Authentication 독립 실행형 라이선스를 통해 사용할 수 있습니다. 사용자 기준 또는 인증 기준 라이선스와 같은 Azure MFA에 대한 사용량 기반 라이선스는 NPS 확장과 호환되지 않습니다. 자세한 내용은 [Azure Multi-Factor Authentication 획득 방법](concept-mfa-licensing.md)을 참조하세요. 테스트를 위해 평가판 구독을 사용할 수 있습니다.

### <a name="windows-server-software"></a>Windows Server 소프트웨어

NPS 확장을 사용하려면 네트워크 정책 및 액세스 서비스 역할이 설치된 Windows Server 2008 R2 SP1 이상이 필요합니다. 이 가이드의 모든 단계는 Windows Server 2016을 사용하여 수행되었습니다.

### <a name="libraries"></a>라이브러리

다음 라이브러리는 NPS 확장과 함께 자동으로 설치됩니다.

-    [Visual Studio 2013(X64)용 Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=40784)
-    [Windows PowerShell용 Microsoft Azure Active Directory 모듈 버전 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Microsoft Azure Active Directory PowerShell 모듈이 아직 없는 경우 설치 프로세스의 일부로 실행되는 구성 스크립트를 통해 설치됩니다. 이 모듈은 아직 설치하지 않았다고 해서 미리 설치할 필요가 없습니다.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>온-프레미스 Active Directory와 동기화되는 Azure Active Directory

NPS 확장을 사용하려면 온-프레미스 사용자가 Azure Active Directory와 동기화되고 MFA를 사용하도록 설정되어야 합니다. 이 가이드에서는 온-프레미스 사용자가 AD Connect를 통해 Azure Active Directory와 동기화된다고 가정합니다. 사용자가 MFA를 사용하도록 설정하기 위한 지침은 다음과 같습니다.

Azure AD Connect에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID

NPS 확장을 설치하려면 Azure Active Directory의 GUID를 알고 있어야 합니다. Azure Active Directory의 GUID를 찾기 위한 지침은 다음 섹션에서 제공됩니다.

## <a name="configure-radius-for-vpn-connections"></a>VPN 연결을 위한 RADIUS 구성

구성원 서버에 NPS 역할을 설치한 경우 VPN 연결을 요청하는 VPN 클라이언트를 인증하고 권한을 부여하도록 구성해야 합니다. 

이 섹션에서는 네트워크 정책 및 액세스 서비스 역할을 설치했지만 인프라에서 사용할 수 있도록 구성하지 않았다고 가정합니다.

> [!NOTE]
> 인증을 위해 중앙 집중식 RADIUS 서버를 통해 작동하는 VPN 서버가 이미 있는 경우 이 섹션을 건너뛸 수 있습니다.
>

### <a name="register-server-in-active-directory"></a>Active Directory에 서버 등록

이 시나리오에서 제대로 작동하려면 NPS 서버를 Active Directory에 등록해야 합니다.

1. [서버 관리자]를 엽니다.

2. 서버 관리자에서 **도구**를 선택하고 **네트워크 정책 서버**를 선택합니다.

3. 네트워크 정책 서버 콘솔에서 **NPS(로컬)** 를 마우스 오른쪽 단추로 클릭한 다음 **Active Directory에 서버 등록**을 선택합니다. **확인**을 두 번 선택합니다.

    ![Active Directory 메뉴 옵션에서 서버 등록](./media/howto-mfa-nps-extension-vpn/image2.png)

4. 다음 절차를 위해 콘솔을 열어 둡니다.

### <a name="use-wizard-to-configure-the-radius-server"></a>마법사를 사용하여 RADIUS 서버 구성

표준(마법사 기반) 또는 고급 구성 옵션을 사용하여 RADIUS 서버를 구성할 수 있습니다. 이 섹션에서는 마법사 기반 표준 구성 옵션을 사용한다고 가정합니다.

1. 네트워크 정책 서버 콘솔에서 **NPS(로컬)** 를 선택합니다.

2. **표준 구성** 아래에서 **전화 접속 또는 VPN 연결을 위한 RADIUS 서버**를 선택한 다음 **VPN 또는 전화 접속 구성**을 선택합니다.

    ![전화 접속 또는 VPN 연결용 RADIUS 서버 구성](./media/howto-mfa-nps-extension-vpn/image3.png)

3. **전화 접속 또는 가상 사설망 연결 형식 선택** 창에서 **가상 사설망 연결**을 선택한 후, **다음**을 선택합니다.

    ![가상 사설망 연결 구성](./media/howto-mfa-nps-extension-vpn/image4.png)

4. **전화 접속 또는 VPN 서버 지정** 창에서 **추가**를 선택합니다.

5. **새 RADIUS 클라이언트** 창에서 친숙한 이름을 제공하고, VPN 서버의 확인할 수 있는 이름 또는 IP 주소를 입력한 다음, 공유 비밀 암호를 입력합니다. 이 공유 비밀 암호는 길고 복잡하게 만들고, 다음 섹션에서 필요하므로 적어둡니다.

    ![새 RADIUS 클라이언트 창 만들기](./media/howto-mfa-nps-extension-vpn/image5.png)

6. **확인**선택하고 **다음**을 선택합니다.

7. **인증 방법 구성** 창에서 기본 선택 사항(**Microsoft 암호화 인증 버전 2(MS-CHAPv2)** )을 그대로 적용하거나 다른 옵션을 선택하고 **다음**을 선택합니다.

    > [!NOTE]
    > EAP(Extensible Authentication Protocol)를 구성하는 경우 Microsoft CHAPv2(Challenge Handshake Authentication Protocol) 또는 PEAP(Protected Extensible Authentication Protocol)를 사용해야 합니다. 다른 EAP는 지원되지 않습니다.

8. **사용자 그룹 지정** 창에서 **추가**를 선택하고 해당 그룹을 선택합니다. 그룹이 없으면 선택 사항을 비워 두어 모든 사용자에게 액세스 권한을 부여합니다.

    ![액세스를 허용하거나 거부할 사용자 그룹 창 지정](./media/howto-mfa-nps-extension-vpn/image7.png)

9. **다음**을 선택합니다.

10. **IP 필터 지정** 창에서 **다음**을 선택합니다.

11. **암호화 설정 지정** 창에서 기본 설정을 적용하고 **다음**을 선택합니다.

    ![암호화 설정 지정 창](./media/howto-mfa-nps-extension-vpn/image8.png)

12. **영역 이름 지정** 창에서 이름을 비워 두고, 기본 설정을 적용하고, **다음**을 선택합니다.

    ![영역 이름 지정 창](./media/howto-mfa-nps-extension-vpn/image9.png)

13. **새 전화 접속 또는 가상 사설망 연결 및 RADIUS 클라이언트 완료** 창에서 **마침**을 선택합니다.

    ![완료된 구성 창](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>RADIUS 구성 확인

이 섹션에서는 마법사를 사용하여 만든 구성에 대해 자세히 설명합니다.

1. 네트워크 정책 서버의 NPS(로컬) 콘솔에서 **RADIUS 클라이언트**를 펼치고 **RADIUS 클라이언트**를 선택합니다.

2. 세부 정보 창에서 만든 RADIUS 클라이언트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. RADIUS 클라이언트(VPN 서버)의 속성은 아래와 같습니다.

    ![VPN 속성 및 구성 확인](./media/howto-mfa-nps-extension-vpn/image11.png)

3. **취소**를 선택합니다.

4. 네트워크 정책 서버의 NPS(로컬) 콘솔에서 **정책**을 펼치고 **연결 요청 정책**을 선택합니다. 아래 이미지와 같이 VPN 연결 정책이 표시됩니다.

    ![VPN 연결 정책을 보여주는 연결 요청 정책](./media/howto-mfa-nps-extension-vpn/image12.png)

5. **정책** 아래에서 **네트워크 정책**을 선택합니다. 아래 이미지의 정책과 비슷한 VPN(가상 사설망) 연결 정책이 표시됩니다.

    ![가상 사설망 연결 정책을 보여주는 네트워크 정책](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>RADIUS 인증을 사용하도록 VPN 서버 구성

이 섹션에서는 VPN 서버에서 RADIUS 인증을 사용하도록 구성합니다. 지침에서는 VPN 서버의 작동하는 구성이 있지만 RADIUS 인증을 사용하도록 구성하지 않았다고 가정합니다. VPN 서버를 구성한 후에 구성이 예상대로 작동하는지 확인합니다.

> [!NOTE]
> RADIUS 인증을 통해 작동하는 VPN 서버 구성이 이미 있는 경우 이 섹션을 건너뛸 수 있습니다.
>

### <a name="configure-authentication-provider"></a>인증 공급자 구성

1. VPN 서버에서 [서버 관리자]를 엽니다.

2. 서버 관리자에서 **도구**, **라우팅 및 원격 액세스**를 차례로 선택합니다.

3. **라우팅 및 원격 액세스** 창에서 ** \<server name> (로컬)** 을 마우스 오른쪽 단추로 클릭 한 다음 **속성**을 선택 합니다.

4. ** \<server name> (로컬) 속성** 창에서 **보안** 탭을 선택 합니다.

5. **보안** 탭의 **인증 공급자** 아래에서 **RADIUS 인증**, **구성**을 차례로 선택합니다.

    ![RADIUS 인증 공급자 구성](./media/howto-mfa-nps-extension-vpn/image15.png)

6. **RADIUS 인증** 창에서 **추가**를 선택합니다.

7. **RADIUS 서버 추가** 창에서 다음을 수행합니다.

    a. **서버 이름** 상자에서 이전 섹션에서 구성한 RADIUS 서버의 이름 또는 IP 주소를 입력합니다.

    b. **공유 비밀**에서 **변경**을 선택하고 이전에 만들어 기록해 둔 공유 비밀 암호를 입력합니다.

    다. **시간 제한(초)** 상자에 **30** 값을 입력합니다.  
    이 시간 제한 값은 두 번째 인증 요소를 완료할 수 있을 만큼 충분한 시간을 허용하는 데 필요합니다. 일부 VPN 또는 지역에서는 사용자가 여러 번 전화를 받지 못하도록 30초보다 큰 시간 제한 설정이 필요합니다. 사용자에게 이 문제가 발생하는 경우 문제가 다시 발생하지 않을 때까지 **시간 제한(초)** 값을 30초 단위로 늘립니다.

    ![시간 제한을 구성하는 RADIUS 서버 추가 창](./media/howto-mfa-nps-extension-vpn/image16.png) 

8. **확인**을 선택합니다.

### <a name="test-vpn-connectivity"></a>VPN 연결 테스트

이 섹션에서는 VPN 가상 포트에 연결하려고 할 때 VPN 클라이언트가 RADIUS 서버에서 인증되고 권한을 부여받았는지 확인합니다. 지침에서는 Windows 10을 VPN 클라이언트로 사용하고 있다고 가정합니다.

> [!NOTE]
> 이미 VPN 서버에 연결하도록 VPN 클라이언트를 구성하고 해당 설정을 저장한 경우 VPN 연결 개체의 구성 및 저장과 관련된 단계를 건너뛸 수 있습니다.
>

1. VPN 클라이언트 컴퓨터에서 **시작** 단추를 선택하고 **설정** 단추를 선택합니다.

2. **Windows 설정** 창에서 **네트워크 및 인터넷**을 선택합니다.

3. **VPN**을 선택합니다.

4. **VPN 연결 추가**를 선택합니다.

5. **VPN 연결 추가** 창의 **VPN 공급자** 상자에서 **Windows(기본 제공)** 를 선택한 다음 나머지 필드를 적절히 완료하고 **저장**을 선택합니다.

    ![“VPN 연결 추가” 창](./media/howto-mfa-nps-extension-vpn/image17.png)

6. **제어판**으로 이동한 후 **네트워크 및 공유 센터**를 선택합니다.

7. **어댑터 설정 변경**을 선택합니다.

    ![네트워크 및 공유 센터 - 어댑터 설정 변경](./media/howto-mfa-nps-extension-vpn/image18.png)

8. VPN 네트워크 연결을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

9. VPN 속성 창에서 **보안** 탭을 선택합니다.

10. **보안** 탭에서 **Microsoft CHAP 버전 2(MS-CHAP v2)** 만 선택했는지 확인하고 **확인**을 선택합니다.

    !["다음 프로토콜 허용" 옵션](./media/howto-mfa-nps-extension-vpn/image20.png)

11. VPN 연결을 마우스 오른쪽 단추로 클릭하고 **연결**을 선택합니다.

12. **설정** 창에서 **연결**을 선택합니다.  
    성공적인 연결은 다음과 같이 RADIUS 서버의 보안 로그에 6272 이벤트 ID로 표시됩니다.

    ![성공적인 연결을 보여주는 이벤트 속성 창](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>RADIUS 문제 해결

인증 및 권한 부여를 위해 중앙 집중식 RADIUS 서버를 사용하도록 VPN 서버를 구성하기 전에 VPN 구성이 작동하고 있다고 가정합니다. 구성이 작동하는 경우 RADIUS 서버의 잘못된 구성 또는 잘못된 사용자 이름 또는 암호의 사용으로 인해 문제가 발생한 것일 수 있습니다. 예를 들어 사용자 이름에 대체 UPN 접미사를 사용하는 경우 로그인 시도가 실패할 수 있습니다. 최상의 결과를 얻으려면 동일한 계정 이름을 사용합니다.

이러한 문제를 해결하려면 RADIUS 서버에서 보안 이벤트 로그를 검사하는 것이 가장 이상적입니다. 이벤트 검색 시간을 줄이기 위해 아래와 같이 이벤트 뷰어에서 역할 기반 네트워크 정책 및 액세스 서버 사용자 지정 보기를 사용할 수 있습니다. 6273 이벤트 ID는 NPS에서 사용자에 대한 액세스를 거부한 이벤트를 나타냅니다.

![NPAS 이벤트를 보여주는 이벤트 뷰어](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication 구성

Multi-Factor Authentication에 대한 사용자를 구성할 때 도움이 필요한 경우 [클라우드 기반 Multi-Factor Authentication 배포 계획](howto-mfa-getstarted.md#create-conditional-access-policy) 및 [2단계 인증에 내 계정 설정](../user-help/multi-factor-authentication-end-user-first-time.md) 문서를 참조하세요.

## <a name="install-and-configure-the-nps-extension"></a>NPS 확장 설치 및 구성

이 섹션에서는 VPN 서버에서 클라이언트 인증에 MFA를 사용하도록 VPN을 구성하는 방법에 대해 설명합니다.

> [!NOTE]
> REQUIRE_USER_MATCH 레지스트리 키는 대/소문자를 구분 합니다. 모든 값은 대문자 형식으로 설정 해야 합니다.
>

NPS 확장을 설치하고 구성한 후에 MFA를 사용하려면 이 서버에서 모든 RADIUS 기반 클라이언트 인증을 처리해야 합니다. 모든 VPN 사용자가 Azure Multi-Factor Authentication에 등록되지는 않은 경우 다음 중 하나를 수행할 수 있습니다.

* MFA를 사용하도록 구성되지 않은 사용자를 인증하도록 다른 RADIUS 서버를 설정합니다.

* Microsoft Azure Multi-Factor Authentication에 등록된 경우에만 문제가 있는 사용자가 두 번째 인증 요소를 제공할 수 있도록 레지스트리 항목을 만듭니다.

_만들고이에 REQUIRE_USER_MATCH_라는 새 문자열 값을 만들고이 값을 *TRUE* 또는 *FALSE*로 설정 합니다.

![“사용자 일치 필요” 설정](./media/howto-mfa-nps-extension-vpn/image34.png)

값이 *TRUE* 로 설정 되거나 비어 있으면 모든 인증 요청은 MFA 챌린지의 영향을 받습니다. 값이 *FALSE*로 설정 된 경우 MFA 문제는 Azure Multi-Factor Authentication에 등록 된 사용자 에게만 발급 됩니다. 등록 기간 동안에는 테스트 또는 프로덕션 환경 에서만 *FALSE* 설정을 사용 합니다.



### <a name="obtain-the-azure-active-directory-tenant-id"></a>Azure Active Directory 테 넌 트 ID 가져오기

NPS 확장 구성의 일환으로 Azure AD 테넌트의 관리자 자격 증명과 ID를 제공해야 합니다. 테 넌 트 ID를 가져오려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 Azure 테넌트의 전역 관리자로 로그인합니다.
1. Azure Portal 메뉴에서 **Azure Active Directory**를 선택하거나 모든 페이지에서 **Azure Active Directory**를 검색하여 선택합니다.
1. **개요** 페이지에서 *테 넌 트 정보가* 표시 됩니다. 다음 예제 스크린샷에 표시 된 것 처럼 *테 넌 트 ID*옆에 있는 **복사** 아이콘을 선택 합니다.

   ![Azure Portal에서 테 넌 트 ID 가져오기](./media/howto-mfa-nps-extension-vpn/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>NPS 확장 설치

NPS 확장은 네트워크 정책 및 액세스 서비스 역할이 설치되고 설계상 RADIUS 서버로 작동하는 서버에 설치해야 합니다. VPN 서버에 NPS 확장을 설치하지 *마세요*.

1. [Microsoft 다운로드 센터](https://aka.ms/npsmfa)에서 NPS 확장을 다운로드합니다.

2. 설치 실행 파일(*NpsExtnForAzureMfaInstaller.exe*)을 NPS 서버에 복사합니다.

3. NPS 서버에서 **NpsExtnForAzureMfaInstaller.exe**를 두 번 클릭하고 확인 메시지가 표시되면 **실행**을 선택합니다.

4. **Azure MFA용 NPS 확장 설치** 창에서 소프트웨어 사용 조건을 검토하고, **사용 약관에 동의함** 확인란을 선택한 다음, **설치**를 선택합니다.

    ![“Azure MFA용 NPS 확장 설치” 창](./media/howto-mfa-nps-extension-vpn/image36.png)

5. **Azure MFA용 NPS 확장 설치** 창에서 **닫기**를 선택합니다.  

    !["설치 완료" 확인 창](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>PowerShell 스크립트를 사용하여 NPS 확장에서 사용할 인증서 구성

보안 통신 및 보증을 보장하려면 NPS 확장에서 사용할 인증서를 구성합니다. NPS 구성 요소에는 NPS에서 사용할 자체 서명된 인증서를 구성하는 Windows PowerShell 스크립트가 포함되어 있습니다.

이 스크립트에서는 다음 작업을 수행합니다.

* 자체 서명된 인증서를 만듭니다.
* 인증서의 공개 키를 Azure AD의 서비스 주체에 연결합니다.
* 로컬 컴퓨터 저장소에 인증서를 저장합니다.
* 네트워크 사용자에게 인증서의 프라이빗 키에 대한 액세스 권한을 부여합니다.
* NPS 서비스를 다시 시작합니다.

사용자 고유의 인증서를 사용하려면 인증서의 공개 키를 Azure AD의 서비스 주체 등에 연결해야 합니다.

스크립트를 사용하려면 이전에 복사한 Azure Active Directory 관리자 자격 증명과 Azure Active Directory 테넌트 ID를 확장에 제공합니다. 계정은 확장을 사용하도록 설정하려는 것과 동일한 Azure AD 테넌트에 있어야 합니다. NPS 확장을 설치하는 각 NPS 서버에서 스크립트를 실행합니다.

1. 관리자 권한으로 Windows PowerShell을 실행합니다.

2. PowerShell 명령 프롬프트에서 **cd "c:\Program Files\Microsoft\AzureMfa\Config"** 를 입력하고 Enter 키를 누릅니다.

3. 다음 명령 프롬프트에서 **.\AzureMfaNpsExtnConfigSetup.ps1**을 입력한 다음, Enter를 선택합니다. 스크립트에서 Azure AD PowerShell 모듈이 설치되어 있는지 확인합니다. 설치되어 있지 않으면 스크립트에서 해당 모듈을 설치합니다.

    ![AzureMfsNpsExtnConfigSetup.ps1 구성 스크립트 실행](./media/howto-mfa-nps-extension-vpn/image38.png)

    TLS로 인해 보안 오류가 발생하면 PowerShell 프롬프트에서 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 명령을 사용하여 TLS 1.2를 활성화합니다.
    
    스크립트에서 PowerShell 모듈 설치를 확인한 후에 Azure Active Directory PowerShell 모듈 로그인 창을 표시합니다.

4. Azure AD 관리자 자격 증명 및 암호를 입력하고 **로그인**을 선택합니다.

    ![Azure AD PowerShell에 인증](./media/howto-mfa-nps-extension-vpn/image39.png)

5. 명령 프롬프트에서 이전에 복사한 테넌트 ID를 붙여넣고 Enter 키를 선택합니다.

    ![이전에 복사 된 Azure AD 테 넌 트 ID 입력](./media/howto-mfa-nps-extension-vpn/image40.png)

    스크립트에서 자체 서명된 인증서를 만들고 다른 구성 변경 작업을 수행합니다. 다음 이미지와 비슷한 출력이 표시됩니다.

    ![자체 서명된 인증서를 표시하는 PowerShell 창](./media/howto-mfa-nps-extension-vpn/image41.png)

6. 서버를 다시 부팅합니다.

### <a name="verify-the-configuration"></a>구성 확인

구성을 확인하려면 VPN 서버와의 새 VPN 연결을 설정해야 합니다. 기본 인증을 위한 자격 증명을 성공적으로 입력하면 아래와 같이 VPN 연결에서 연결이 설정되기 전에 보조 인증이 성공할 때까지 대기합니다.

![Windows 설정 VPN 창](./media/howto-mfa-nps-extension-vpn/image42.png)

Azure MFA에서 이전에 구성한 보조 인증 방법으로 성공적으로 인증하면 해당 리소스에 연결됩니다. 그러나 보조 인증이 실패하면 리소스에 대한 액세스가 거부됩니다.

다음 예제에서 Windows Phone의 Microsoft Authenticator 앱은 보조 인증을 제공합니다.

![Windows Phone의 MFA 프롬프트 예](./media/howto-mfa-nps-extension-vpn/image43.png)

보조 인증 방법을 사용하여 성공적으로 인증되면 VPN 서버의 가상 포트에 대한 액세스 권한이 부여됩니다. 신뢰할 수 있는 디바이스에서 모바일 앱을 사용하여 보조 인증 방법을 사용해야 하므로 로그인 프로세스가 사용자 이름 및 암호 조합을 사용하는 경우보다 더 안전합니다.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>성공적인 로그인 이벤트에 대한 이벤트 뷰어 로그 보기

Windows 이벤트 뷰어 로그에서 성공적인 로그인 이벤트를 확인하려면 다음 PowerShell 명령을 실행하여 NPS 서버에서 Windows 보안 로그를 쿼리합니다.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![PowerShell 보안 이벤트 뷰어](./media/howto-mfa-nps-extension-vpn/image44.png)

또한 아래와 같이 보안 로그 또는 네트워크 정책 및 액세스 서비스 사용자 지정 보기를 볼 수도 있습니다.

![네트워크 정책 서버 로그 예](./media/howto-mfa-nps-extension-vpn/image45.png)

Azure Multi-Factor Authentication용 NPS 확장을 설치한 서버에서 *Application and Services Logs\Microsoft\AzureMfa*에 있는 확장과 관련된 이벤트 뷰어 애플리케이션 로그를 찾을 수 있습니다.

```powershell
Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL
```

![이벤트 뷰어 AuthZ 로그 창 예](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>문제 해결 가이드

구성이 예상대로 작동하지 않는 경우 사용자가 MFA를 사용하도록 구성되어 있는지 확인하여 문제 해결을 시작합니다. 사용자가 [Azure Portal](https://portal.azure.com)에 연결하도록 합니다. 보조 인증을 요청받았고 성공적으로 인증할 수 있으면 MFA의 잘못된 구성을 제거할 수 있습니다.

MFA가 해당 사용자에 적용되는 경우 관련 이벤트 뷰어 로그를 검토합니다. 로그에는 이전 섹션에서 설명한 보안 이벤트, 게이트웨이 작업 및 Azure Multi-Factor Authentication 로그가 포함됩니다.

실패한 로그인 이벤트(이벤트 ID 6273)를 표시하는 보안 로그 예제는 다음과 같습니다.

![실패한 로그인 이벤트를 표시하는 보안 로그](./media/howto-mfa-nps-extension-vpn/image47.png)

Azure Multi-factor Authentication 로그의 관련 이벤트는 다음과 같습니다.

![Azure Multi-Factor Authentication 로그](./media/howto-mfa-nps-extension-vpn/image48.png)

고급 문제 해결을 수행하려면 NPS 서비스가 설치된 NPS 데이터베이스 형식 로그 파일을 참조하세요. 이 로그 파일은 _%SystemRoot%\System32\Logs_ 폴더에 쉼표로 구분된 텍스트 파일로 만들어집니다. 로그 파일에 대한 자세한 내용은 [NPS 데이터베이스 형식 로그 파일 해석](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10))을 참조하세요.

이러한 로그 파일의 항목은 스프레드시트 또는 데이터베이스로 내보내지 않으면 해석하기 어렵습니다. 온라인에서 로그 파일을 해석하는 데 도움이 되는 많은 IAS(인터넷 인증 서비스) 구문 분석 도구를 찾을 수 있습니다. 다운로드할 수 있는 [셰어웨어 애플리케이션](https://www.deepsoftware.com/iasviewer)의 출력은 다음과 같습니다.

![샘플 셰어웨어 앱 IAS 파서](./media/howto-mfa-nps-extension-vpn/image49.png)

추가적인 문제 해결을 수행하려면 Wireshark 또는 [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)와 같은 프로토콜 분석기를 사용할 수 있습니다. 다음의 Wireshark 이미지에서는 VPN 서버와 NPS 간의 RADIUS 메시지를 보여줍니다.

![필터링된 트래픽을 표시하는 Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

자세한 내용은 [기존 NPS 인프라를 Azure Multi-Factor Authentication과 통합](howto-mfa-nps-extension.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Multi-Factor Authentication 다운로드](concept-mfa-licensing.md)

[RADIUS를 사용한 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버](howto-mfaserver-nps-rdg.md)

[Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)