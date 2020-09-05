---
title: RDG와 Azure MFA NPS 확장 통합-Azure Active Directory
description: Microsoft Azure용 네트워크 정책 서버 확장을 사용하여 원격 데스크톱 게이트웨이 인프라를 Azure MFA와 통합
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
ms.openlocfilehash: 4a75b6be3796a21e3f765ad69eee0578d5f2e9d0
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717849"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>NPS(네트워크 정책 서버) 확장 및 Azure AD를 사용하여 원격 데스크톱 게이트웨이 인프라 통합

이 문서에서는 Microsoft Azure용 NPS(네트워크 정책 서버) 확장을 사용하여 원격 데스크톱 게이트웨이 인프라를 Azure MFA(Multi-Factor Authentication)와 통합하는 방법에 대해 자세히 설명합니다.

Azure의 NPS (네트워크 정책 서버) 확장을 사용 하면 고객이 Azure의 [MFA (클라우드 기반 Multi-Factor Authentication)](./concept-mfa-howitworks.md)를 사용 하 여 RADIUS(REMOTE AUTHENTICATION DIAL-IN USER SERVICE) (RADIUS) 클라이언트 인증을 보호할 수 있습니다. 이 솔루션은 사용자 로그인 및 트랜잭션에 두 번째 보안 계층을 추가하는 2단계 인증을 제공합니다.

이 문서에서는 Azure용 NPS 확장을 사용하여 NPS 인프라를 Azure MFA와 통합하는 단계별 지침을 제공합니다. 이렇게 하면 원격 데스크톱 게이트웨이에 로그인하려고 시도하는 사용자를 안전하게 확인할 수 있습니다.

> [!NOTE]
> 이 문서는 MFA 서버 배포에 사용할 수 없으며 Azure MFA(클라우드 기반) 배포에만 사용해야 합니다.

NPS(네트워크 정책 및 액세스 서비스)는 조직에 다음과 같은 기능을 제공합니다.

* 연결할 수 있는 사용자, 연결이 허용되는 시간, 연결 기간 및 클라이언트에서 연결에 사용해야 하는 보안 수준 등을 지정하여 네트워크 요청을 관리하고 제어할 수 있는 중앙 위치를 정의합니다. 이러한 정책은 각 VPN 또는 RD(원격 데스크톱) 게이트웨이 서버에 지정하는 대신 중앙 위치에 한 번만 지정하면 됩니다. RADIUS 프로토콜은 중앙 집중식 AAA(인증, 권한 부여 및 계정 관리)를 제공합니다.
* 디바이스가 허용되거나 제한되지 않는지 또는 네트워크 리소스에 대한 액세스가 제한되는지 여부를 결정하는 NAP(네트워크 액세스 보호) 클라이언트 상태 정책을 설정하고 적용합니다.
* 802.1x 지원 무선 액세스 지점 및 이더넷 스위치에 액세스하기 위한 인증 및 권한 부여를 적용할 수 있는 방법을 제공합니다.

일반적으로 조직에서는 NPS (RADIUS)를 사용 하 여 VPN 정책의 관리를 간소화 하 고 중앙 집중화 합니다. 그러나 많은 조직에서는 NPS를 사용하여 RD CAP(원격 데스크톱 연결 권한 부여 정책) 관리를 단순화하고 중앙 집중화합니다.

또한 조직에서는 NPS를 Azure MFA와 통합하여 보안을 향상시키고 높은 수준의 규정을 제공할 수도 있습니다. 이렇게 하면 사용자가 원격 데스크톱 게이트웨이에 로그인하기 위한 2단계 인증을 설정할 수 있습니다. 사용자가 액세스 권한을 부여받으려면 자신이 제어할 수 있는 정보와 함께 사용자 이름/암호 조합을 제공해야 합니다. 이 정보는 신뢰할 수 있어야 하며, 휴대폰 번호, 유선 전화 번호, 모바일 디바이스의 애플리케이션 등과 같이 쉽게 복제할 수 없습니다. RDG는 현재 2FA 용 Microsoft authenticator 앱 메서드에서 전화 통화 및 푸시 알림을 지원 합니다. 지원되는 인증 방법에 대한 자세한 내용은 [사용자가 사용할 수 있는 인증 방법을 결정](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use) 섹션을 참조하세요.

Azure에 대 한 NPS 확장을 사용 하기 전에 통합 된 NPS 및 Azure MFA 환경에 대 한 2 단계 인증을 구현 하려는 고객은 [RADIUS를 사용 하 여 원격 데스크톱 게이트웨이 및 azure Multi-Factor Authentication 서버](howto-mfaserver-nps-rdg.md)에 설명 된 대로 온-프레미스 환경에서 별도의 MFA 서버를 구성 하 고 유지 관리 해야 했습니다.

이제 조직에서는 Azure용 NPS 확장을 통해 온-프레미스 MFA 솔루션 또는 클라우드 기반 MFA 솔루션을 배포하여 RADIUS 클라이언트 인증을 보호할 수 있습니다.

## <a name="authentication-flow"></a>인증 흐름

사용자가 원격 데스크톱 게이트웨이를 통해 네트워크 리소스에 대한 액세스 권한을 부여받으려면 RD CAP(RD 연결 권한 부여 정책) 및 RD RAP(RD 리소스 권한 부여 정책)에 지정된 조건을 충족해야 합니다. RD CAP는 RD 게이트웨이에 연결할 수 있는 권한이 있는 사용자를 지정합니다. RD RAP는 사용자가 RD 게이트웨이를 통해 연결할 수 있는 원격 데스크톱 또는 원격 앱과 같은 네트워크 리소스를 지정합니다.

RD 게이트웨이는 RD CAP용 중앙 정책 저장소를 사용하도록 구성할 수 있습니다. RD RAP는 RD 게이트웨이에서 처리되므로 중앙 정책을 사용할 수 없습니다. RD CAP용 중앙 정책 저장소를 사용하도록 구성된 RD 게이트웨이의 예는 중앙 정책 저장소로 사용되는 다른 NPS 서버에 대한 RADIUS 클라이언트입니다.

Azure용 NPS 확장을 NPS 및 원격 데스크톱 게이트웨이와 통합한 경우 성공적인 인증 흐름은 다음과 같습니다.

1. 원격 데스크톱 게이트웨이 서버에서 원격 데스크톱 세션과 같은 리소스에 연결하기 위해 원격 데스크톱 사용자로부터 인증 요청을 받습니다. RADIUS 클라이언트 역할을 하는 원격 데스크톱 게이트웨이 서버에서 해당 요청을 RADIUS 액세스 요청 메시지로 변환하고 NPS 확장이 설치된 RADIUS(NPS) 서버로 메시지를 보냅니다.
1. Active Directory에서 사용자 이름과 암호 조합이 확인되고 사용자가 인증됩니다.
1. NPS 연결 요청 및 네트워크 정책에 지정된 모든 조건(예: 시간 또는 그룹 멤버 자격 제한)이 충족되는 경우 NPS 확장에서 Azure MFA를 통한 보조 인증 요청을 트리거합니다.
1. Azure MFA는 Azure AD와 통신 하 고, 사용자의 세부 정보를 검색 하 고, 지원 되는 방법을 사용 하 여 보조 인증을 수행 합니다.
1. MFA 챌린지가 성공하면 Azure MFA에서 결과를 NPS 확장으로 전달합니다.
1. 확장이 설치된 NPS 서버에서 RD CAP 정책에 대한 RADIUS 액세스 허용 메시지를 원격 데스크톱 게이트웨이 서버로 보냅니다.
1. 사용자에게 RD 게이트웨이를 통해 요청된 네트워크 리소스에 대한 액세스 권한이 부여됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 섹션에서는 Azure MFA와 원격 데스크톱 게이트웨이를 통합하기 전에 필요한 전제 조건에 대해 자세히 설명합니다. 이 문서를 시작하기 전에 다음과 같은 필수 구성 요소가 있어야 합니다.  

* RDS(원격 데스크톱 서비스) 인프라
* Azure MFA 라이선스
* Windows Server 소프트웨어
* NPS(네트워크 정책 및 액세스 서비스) 역할
* 온-프레미스 Active Directory와 동기화되는 Azure Active Directory
* Azure Active Directory GUID ID

### <a name="remote-desktop-services-rds-infrastructure"></a>RDS(원격 데스크톱 서비스) 인프라

작동 중인 RDS(원격 데스크톱 서비스) 인프라가 있어야 합니다. 이렇게 하지 않으면 빠른 시작 템플릿: [원격 데스크톱 세션 컬렉션 배포 만들기](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)를 사용 하 여 Azure에서이 인프라를 빠르게 만들 수 있습니다.

테스트를 위해 온-프레미스 RDS 인프라를 수동으로 빨리 만들려면 다음 단계에 따라 배포합니다.
**자세한 정보**: Azure 빠른 시작 및 [기본 rds 인프라 배포](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure) [를 사용 하 여 rds 배포](/windows-server/remote/remote-desktop-services/rds-in-azure) .

### <a name="azure-mfa-license"></a>Azure MFA 라이선스

Azure AD Premium 또는 이를 포함하는 다른 번들을 통해 제공되는 Azure MFA의 라이선스가 필요합니다. 사용자 기준 또는 인증 기준 라이선스와 같은 Azure MFA에 대한 사용량 기반 라이선스는 NPS 확장과 호환되지 않습니다. 자세한 내용은 [Azure Multi-Factor Authentication 획득 방법](concept-mfa-licensing.md)을 참조하세요. 테스트를 위해 평가판 구독을 사용할 수 있습니다.

### <a name="windows-server-software"></a>Windows Server 소프트웨어

NPS 확장을 사용하려면 NPS 역할 서비스가 설치된 Windows Server 2008 R2 SP1 이상이 필요합니다. 이 섹션의 모든 단계는 Windows Server 2016에서 수행되었습니다.

### <a name="network-policy-and-access-services-nps-role"></a>NPS(네트워크 정책 및 액세스 서비스) 역할

NPS 역할 서비스는 네트워크 액세스 정책 상태 서비스뿐만 아니라 RADIUS 서버 및 클라이언트 기능도 제공합니다. 이 역할은 인프라에서 적어도 두 대의 컴퓨터, 즉 원격 데스크톱 게이트웨이와 다른 구성원 서버 또는 도메인 컨트롤러에 설치해야 합니다. 기본적으로 이 역할은 원격 데스크톱 게이트웨이로 구성된 컴퓨터에 이미 있습니다.  또한 도메인 컨트롤러 또는 구성원 서버와 같은 다른 컴퓨터에도 NPS 역할을 설치해야 합니다.

NPS 역할 서비스 Windows Server 2012 또는 이전 버전 설치에 대한 자세한 내용은 [NAP 상태 정책 서버 설치(영문)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10))를 참조하세요. 도메인 컨트롤러에 NPS를 설치 하기 위한 권장 사항을 비롯 하 여 NPS에 대 한 모범 사례에 대 한 설명은 [nps에 대 한 모범 사례](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10))를 참조 하세요.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>온-프레미스 Active Directory와 동기화되는 Azure Active Directory

NPS 확장을 사용하려면 온-프레미스 사용자가 Azure AD와 동기화되고 MFA를 사용하도록 설정되어야 합니다. 이 섹션에서는 온-프레미스 사용자가 AD Connect를 사용하여 Azure AD와 동기화된다고 가정합니다. Azure AD 연결에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID ID

NPS 확장을 설치하려면 Azure AD의 GUID를 알고 있어야 합니다. Azure AD의 GUID를 찾기 위한 지침은 아래에 나와 있습니다.

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication 구성

이 섹션에서는 Azure MFA와 원격 데스크톱 게이트웨이를 통합하기 위한 지침에 대해 설명합니다. 사용자는 관리자로서 다단계 인증 디바이스 또는 애플리케이션을 직접 등록하기 전에 먼저 Azure MFA 서비스를 구성해야 합니다.

Azure AD 사용자가 MFA를 사용하도록 설정하려면 [클라우드에서 Azure Multi-Factor Authentication 시작](howto-mfa-getstarted.md)의 단계를 수행하세요.

### <a name="configure-accounts-for-two-step-verification"></a>2단계 인증에 대한 계정 구성

계정에서 MFA를 사용하도록 설정하면 2단계 인증을 사용하여 인증한 두 번째 인증 요소에 사용할 신뢰할 수 있는 디바이스를 성공적으로 구성할 때까지는 MFA 정책이 적용되는 리소스에 로그인할 수 없습니다.

[Azure Multi-Factor Authentication은 무엇을 의미하나요?](../user-help/multi-factor-authentication-end-user-first-time.md)의 단계에 따라 사용자 계정으로 MFA용 디바이스를 이해하고 제대로 구성하세요.

> [!IMPORTANT]
> 원격 데스크톱 게이트웨이에 대 한 로그인 동작은 Azure Multi-Factor Authentication에서 확인 코드를 입력 하는 옵션을 제공 하지 않습니다. 휴대폰 확인 또는 푸시 알림이 있는 Microsoft Authenticator 앱에 대해 사용자 계정을 구성 해야 합니다.
>
> 이러한 두 인증 방법 중 하나가 사용자에 대해 구성 되지 않은 경우 Azure Multi-Factor Authentication 챌린지를 완료 하 고 원격 데스크톱 게이트웨이에 로그인 할 수 없습니다.

## <a name="install-and-configure-nps-extension"></a>NPS 확장 설치 및 구성

이 섹션에서는 원격 데스크톱 게이트웨이에서 클라이언트 인증에 Azure MFA를 사용하도록 RDS 인프라를 구성하는 방법에 대해 설명합니다.

### <a name="acquire-azure-active-directory-tenant-id"></a>Azure Active Directory 테 넌 트 ID 가져오기

NPS 확장 구성의 일환으로 Azure AD 테넌트에 대한 관리자 자격 증명과 Azure AD ID를 제공해야 합니다. 테 넌 트 ID를 가져오려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 Azure 테넌트의 전역 관리자로 로그인합니다.
1. Azure Portal 메뉴에서 **Azure Active Directory**를 선택하거나 모든 페이지에서 **Azure Active Directory**를 검색하여 선택합니다.
1. **개요** 페이지에서 *테 넌 트 정보가* 표시 됩니다. 다음 예제 스크린샷에 표시 된 것 처럼 *테 넌 트 ID*옆에 있는 **복사** 아이콘을 선택 합니다.

   ![Azure Portal에서 테 넌 트 ID 가져오기](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>NPS 확장 설치

NPS(네트워크 정책 및 액세스 서비스) 역할이 설치된 서버에 NPS 확장을 설치합니다. 이 확장은 설계 목적에 맞는 RADIUS 서버로 작동합니다.

> [!IMPORTANT]
> 원격 데스크톱 게이트웨이 (RDG) 서버에 NPS 확장을 설치 하지 마세요. RDG 서버는 해당 클라이언트와 RADIUS 프로토콜을 사용 하지 않으므로 확장을 해석 하 고 MFA를 수행할 수 없습니다.
>
> RG 서버와 NPS 확장을 사용 하는 NPS 서버가 서로 다른 서버인 경우 RDG는 내부적으로 NPS를 사용 하 여 다른 NPS 서버와 통신 하 고, RADIUS를 사용 하 여 올바르게 통신 합니다.

1. [NPS 확장](https://aka.ms/npsmfa)을 다운로드합니다.
1. 설치 실행 파일(NpsExtnForAzureMfaInstaller.exe)을 NPS 서버에 복사합니다.
1. NPS 서버에서 **NpsExtnForAzureMfaInstaller.exe**를 두 번 클릭합니다. 메시지가 표시되면 **실행**을 클릭합니다.
1. Azure MFA용 NPS 확장 설치 대화 상자에서 소프트웨어 사용 조건을 검토하고, **사용 약관에 동의함**을 선택한 다음, **설치**를 클릭합니다.
1. Azure MFA용 NPS 확장 설치 대화 상자에서 **닫기**를 클릭합니다.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>PowerShell 스크립트를 사용하여 NPS 확장에서 사용할 인증서 구성

다음으로 보안 통신 및 보증을 보장하기 위해 NPS 확장에서 사용할 인증서를 구성해야 합니다. NPS 구성 요소에는 NPS에서 사용할 자체 서명된 인증서를 구성하는 Windows PowerShell 스크립트가 포함되어 있습니다.

이 스크립트에서는 다음 작업을 수행합니다.

* 자체 서명된 인증서 만들기
* 인증서의 공개 키를 Azure AD의 서비스 사용자에 연결
* 로컬 컴퓨터 인증서 저장소에 인증서 저장
* 네트워크 사용자에 게 인증서의 개인 키에 대 한 액세스 권한을 부여 합니다.
* 네트워크 정책 서버 서비스 다시 시작

사용자 고유의 인증서를 사용하려면 인증서의 공개 키를 Azure AD의 서비스 주체 등에 연결해야 합니다.

스크립트를 사용하려면 이전에 복사한 Azure AD 관리자 자격 증명과 Azure AD 테넌트 ID를 확장에 제공합니다. NPS 확장을 설치한 각 NPS 서버에서 스크립트를 실행합니다. 그런 다음 아래 작업을 수행합니다.

1. 관리 Windows PowerShell 프롬프트를 엽니다.
1. PowerShell 프롬프트에서 `cd 'c:\Program Files\Microsoft\AzureMfa\Config'`를 입력하고 **Enter** 키를 누릅니다.
1. `.\AzureMfaNpsExtnConfigSetup.ps1`을 입력하고 **ENTER** 키를 누릅니다. 스크립트에서 Azure Active Directory PowerShell 모듈이 설치되어 있는지 확인합니다. 설치되어 있지 않으면 스크립트에서 해당 모듈을 설치합니다.

   ![Azure AD PowerShell에서 AzureMfaNpsExtnConfigSetup.ps1 실행](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. 스크립트에서 PowerShell 모듈 설치를 확인한 후에 Azure Active Directory PowerShell 모듈 대화 상자를 표시합니다. 대화 상자에서 Azure AD 관리자 자격 증명 및 암호를 입력 하 고 **로그인**을 클릭 합니다.

   ![PowerShell에서 Azure AD 인증](./media/howto-mfa-nps-extension-rdg/image5.png)

1. 메시지가 표시 되 면 앞에서 클립보드에 복사한 *테 넌 트 ID* 를 붙여넣고 **enter**키를 누릅니다.

   ![PowerShell에서 테 넌 트 ID 입력](./media/howto-mfa-nps-extension-rdg/image6.png)

1. 스크립트에서 자체 서명된 인증서를 만들고 다른 구성 변경 작업을 수행합니다. 출력은 아래에 표시된 이미지와 같아야 합니다.

   ![자체 서명 된 인증서를 보여 주는 PowerShell 출력](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이에서 NPS 구성 요소 구성

이 섹션에서는 원격 데스크톱 게이트웨이 연결 권한 부여 정책 및 기타 RADIUS 설정을 구성합니다.

인증 흐름을 사용 하려면 NPS 확장이 설치 된 원격 데스크톱 게이트웨이와 NPS 서버 간에 RADIUS 메시지를 교환 해야 합니다. 즉 원격 데스크톱 게이트웨이와 NPS 확장이 설치된 NPS 서버 모두에서 RADIUS 클라이언트 설정을 구성해야 합니다.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>중앙 저장소를 사용하도록 원격 데스크톱 게이트웨이 연결 권한 부여 정책 구성

RD CAP(원격 데스크톱 연결 권한 부여 정책)는 원격 데스크톱 게이트웨이 서버에 연결하기 위한 요구 사항을 지정합니다. RD CAP는 로컬로(기본값) 저장하거나 NPS를 실행하는 중앙 RD CAP 저장소에 저장할 수 있습니다. Azure MFA와 RDS를 통합하도록 구성하려면 중앙 저장소의 사용을 지정해야 합니다.

1. RD 게이트웨이 서버에서 **서버 관리자**를 엽니다.
1. 메뉴에서 **도구**를 클릭하고 **원격 데스크톱 서비스**를 가리킨 다음 **원격 데스크톱 게이트웨이 관리자**를 클릭합니다.
1. RD 게이트웨이 관리자에서 ** \[ 서버 이름 \] (로컬)** 을 마우스 오른쪽 단추로 클릭 하 고 **속성**을 클릭 합니다.
1. 속성 대화 상자에서 **RD CAP 저장소** 탭을 선택 합니다.
1. RD CAP 저장소 탭에서 NPS를 실행 하는 **중앙 서버**를 선택 합니다. 
1. **NPS를 실행하는 서버의 이름 또는 IP 주소 입력** 필드에서 NPS 확장을 설치한 서버의 IP 주소 또는 서버 이름을 입력합니다.

   ![NPS 서버의 이름 또는 IP 주소 입력](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. **추가**를 클릭합니다.
1. **공유 비밀** 대화 상자에서 공유 비밀을 입력한 다음 **확인**을 클릭합니다. 이 공유 비밀을 기록하여 안전하게 저장해 둡니다.

   >[!NOTE]
   >공유 비밀은 RADIUS 서버와 클라이언트 간의 신뢰 관계를 설정하는 데 사용됩니다. 길고 복잡한 비밀을 만드세요.
   >

   ![트러스트를 설정 하기 위해 공유 암호 만들기](./media/howto-mfa-nps-extension-rdg/image11.png)

1. **확인** 을 클릭하여 대화 상자를 닫습니다.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>원격 데스크톱 게이트웨이 NPS에서 RADIUS 시간 제한 값 구성

사용자 자격 증명의 유효성을 검사 하 고, 2 단계 인증을 수행 하 고, 응답을 수신 하 고, RADIUS 메시지에 응답 하는 시간이 있는지 확인 하려면 RADIUS 시간 제한 값을 조정 해야 합니다.

1. RD 게이트웨이 서버에서 서버 관리자를 엽니다. 메뉴에서 **도구**를 클릭한 다음 **네트워크 정책 서버**를 클릭합니다.
1. **NPS(로컬)** 콘솔에서 **RADIUS 클라이언트 및 서버**를 펼치고 **원격 RADIUS 서버**를 선택합니다.

   ![원격 RADIUS 서버를 표시 하는 네트워크 정책 서버 관리 콘솔](./media/howto-mfa-nps-extension-rdg/image12.png)

1. 세부 정보 창에서 **TS 게이트웨이 서버 그룹**을 두 번 클릭합니다.

   >[!NOTE]
   >이 RADIUS 서버 그룹은 NPS 정책에 대한 중앙 서버를 구성할 때 만들어졌습니다. 그룹에 하나 이상의 서버가 있는 경우 RD 게이트웨이에서는 RADIUS 메시지를 이러한 서버 또는 서버 그룹으로 전달합니다.
   >

1. **TS 게이트웨이 서버 그룹 속성** 대화 상자에서 RD CAP를 저장하도록 구성된 NPS 서버의 IP 주소 또는 이름을 선택한 다음 **편집**을 클릭합니다.

   ![이전에 구성 된 NPS 서버의 IP 또는 이름 선택](./media/howto-mfa-nps-extension-rdg/image13.png)

1. **RADIUS 서버 편집** 대화 상자에서 **부하 분산** 탭을 선택합니다.
1. **부하 분산** 탭의 **응답 없이 다음 시간(초)이 경과되면 요청이 손실된 것으로 간주** 필드에서 3인 기본값을 30초에서 60초 사이의 값으로 변경합니다.
1. **서버가 사용 불가능 상태로 표시된 경우 요청 사이의 시간(초)** 필드에서 30초인 기본값을 이전 단계에서 지정한 값보다 크거나 같은 값으로 변경합니다.

   ![부하 분산 탭에서 Radius 서버 제한 시간 설정 편집](./media/howto-mfa-nps-extension-rdg/image14.png)

1. **확인**을 두 번 클릭하여 대화 상자를 닫습니다.

### <a name="verify-connection-request-policies"></a>연결 요청 정책 확인

기본적으로 연결 권한 부여 정책에 중앙 정책 저장소를 사용하도록 RD 게이트웨이를 구성하면 RD 게이트웨이가 CAP 요청을 NPS 서버로 전달하도록 구성됩니다. Azure MFA 확장이 설치된 NPS 서버에서 RADIUS 액세스 요청을 처리합니다. 다음 단계에서는 기본 연결 요청 정책을 확인하는 방법을 보여 줍니다.  

1. RD 게이트웨이의 [NPS(로컬)] 콘솔에서 **정책**을 펼치고 **연결 요청 정책**을 선택합니다.
1. **TS 게이트웨이 권한 부여 정책**을 두 번 클릭합니다.
1. **TS 게이트웨이 권한 부여 정책 속성** 대화 상자에서 **설정** 탭을 클릭합니다.
1. **설정** 탭의 [연결 요청 전달]에서 **인증**을 클릭합니다. RADIUS 클라이언트에서 인증 요청을 전달하도록 구성됩니다.

   ![서버 그룹을 지정 하는 인증 설정 구성](./media/howto-mfa-nps-extension-rdg/image15.png)

1. **취소**를 클릭합니다.

>[!NOTE]
> 연결 요청 정책을 만드는 방법에 대 한 자세한 내용은 동일한 항목에 대 한 [연결 요청 정책 구성](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) 문서를 참조 하세요. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS 확장이 설치된 서버에 NPS 구성

NPS 확장이 설치된 NPS 서버에서는 원격 데스크톱 게이트웨이의 NPS 서버와 RADIUS 메시지를 교환할 수 있어야 합니다. 이 메시지 교환을 사용하도록 설정하려면 NPS 확장 서비스가 설치된 서버에 NPS 구성 요소를 구성해야 합니다.

### <a name="register-server-in-active-directory"></a>Active Directory에 서버 등록

이 시나리오에서 제대로 작동하려면 NPS 서버를 Active Directory에 등록해야 합니다.

1. NPS 서버에서 **서버 관리자**를 엽니다.
1. [서버 관리자]에서 **도구**를 클릭한 다음 **네트워크 정책 서버**를 클릭합니다.
1. [네트워크 정책 서버] 콘솔에서 **NPS(로컬)** 를 마우스 오른쪽 단추로 클릭한 다음 **Active Directory에 서버 등록**을 클릭합니다.
1. **확인**을 두 번 클릭합니다.

   ![Active Directory에 NPS 서버 등록](./media/howto-mfa-nps-extension-rdg/image16.png)

1. 다음 절차를 위해 콘솔을 열어 둡니다.

### <a name="create-and-configure-radius-client"></a>RADIUS 클라이언트 만들기 및 구성

원격 데스크톱 게이트웨이를 NPS 서버에 대한 RADIUS 클라이언트로 구성해야 합니다.

1. NPS 확장이 설치된 NPS 서버의 **NPS(로컬)** 콘솔에서 **RADIUS 클라이언트**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 클릭합니다.

   ![NPS 콘솔에서 새 RADIUS 클라이언트 만들기](./media/howto-mfa-nps-extension-rdg/image17.png)

1. **새 RADIUS 클라이언트** 대화 상자에서 _게이트웨이_와 같은 친숙 한 이름과 원격 데스크톱 게이트웨이 서버의 IP 주소 또는 DNS 이름을 제공 합니다.
1. **공유 비밀** 및 **공유 비밀 확인** 필드에서 이전에 사용한 것과 동일한 비밀을 입력합니다.

   ![친숙 한 이름 및 IP 또는 DNS 주소 구성](./media/howto-mfa-nps-extension-rdg/image18.png)

1. **확인** 을 클릭 하 여 새 RADIUS 클라이언트 대화 상자를 닫습니다.

### <a name="configure-network-policy"></a>네트워크 정책 구성

Azure MFA 확장이 있는 NPS 서버는 CAP(연결 권한 부여 정책)에 지정된 중앙 정책 저장소입니다. 따라서 유효한 연결 요청에 대한 권한을 부여하도록 NPS 서버에 CAP를 구현해야 합니다.  

1. NPS 서버에서 NPS(로컬) 콘솔을 열고 **정책**을 펼쳐서 **네트워크 정책**을 클릭합니다.
1. **다른 액세스 서버에 대 한 연결**을 마우스 오른쪽 단추로 클릭 하 고 **정책 복제**를 클릭 합니다.

   ![다른 액세스 서버 정책에 대 한 연결 복제](./media/howto-mfa-nps-extension-rdg/image19.png)

1. **Copy of Connections to other access servers(다른 액세스 서버 연결 복사본)** 를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
1. **다른 액세스 서버에 대 한 연결 복사** 대화 상자의 **정책 이름**에 _RDG_CAP_와 같은 적절 한 이름을 입력 합니다. **정책 사용**을 선택 하 고 **액세스 허용**을 선택 합니다. 필요에 따라 **네트워크 액세스 서버 형식**에서 **원격 데스크톱 게이트웨이**를 선택하거나 **지정되지 않음**으로 그대로 둘 수 있습니다.

   ![정책 이름, 활성화 및 액세스 권한 부여](./media/howto-mfa-nps-extension-rdg/image21.png)

1. **제약 조건** 탭을 클릭하고 **인증 방법을 협상하지 않고 클라이언트가 연결할 수 있음**을 선택합니다.

   ![클라이언트의 연결을 허용 하도록 인증 방법 수정](./media/howto-mfa-nps-extension-rdg/image22.png)

1. 필요에 따라 **조건** 탭을 클릭하고 특정 Windows 그룹의 멤버 자격과 같이 연결 권한을 부여받기 위해 충족해야 하는 조건을 추가합니다.

   ![선택적으로 연결 조건 지정](./media/howto-mfa-nps-extension-rdg/image23.png)

1. **확인**을 클릭합니다. 해당 도움말 항목을 볼 것인지 묻는 메시지가 표시되면 **아니요**를 클릭합니다.
1. 새 정책이 목록의 맨 위에 있는지, 정책을 사용하도록 설정되었는지 및 액세스 권한을 부여하는지를 확인합니다.

   ![정책을 목록 맨 위로 이동](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>구성 확인

구성을 확인하려면 적합한 RDP 클라이언트를 사용하여 원격 데스크톱 게이트웨이에 로그인해야 합니다. 연결 권한 부여 정책에서 허용하고 Azure MFA를 사용하도록 설정된 계정을 사용해야 합니다.

아래 이미지와 같이 **원격 데스크톱 웹 액세스** 페이지를 사용할 수 있습니다.

![원격 데스크톱에서 테스트 웹 액세스](./media/howto-mfa-nps-extension-rdg/image25.png)

기본 인증에 대한 자격 증명을 성공적으로 입력하면 [원격 데스크톱 연결] 대화 상자에서 아래와 같이 원격 연결 시작 중 상태가 표시됩니다. 

Azure MFA에서 이전에 구성한 보조 인증 방법으로 성공적으로 인증하면 해당 리소스에 연결됩니다. 그러나 보조 인증이 실패하면 리소스에 대한 액세스가 거부됩니다. 

![원격 연결을 시작 원격 데스크톱 연결](./media/howto-mfa-nps-extension-rdg/image26.png)

아래 예에서 Windows Phone의 Authenticator 앱이 보조 인증을 제공하는 데 사용됩니다.

![확인을 보여 주는 예제 Windows Phone Authenticator 앱](./media/howto-mfa-nps-extension-rdg/image27.png)

보조 인증 방법을 사용하여 성공적으로 인증하면 원격 데스크톱 게이트웨이에 정상적으로 로그인됩니다. 그러나 신뢰할 수 있는 장치에서 모바일 앱을 사용 하 여 보조 인증 방법을 사용 해야 하므로 로그인 프로세스는 그렇지 않은 경우 보다 안전 합니다.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>성공적인 로그온 이벤트에 대한 이벤트 뷰어 로그 보기

Windows 이벤트 뷰어 로그에서 성공적인 로그인 이벤트를 확인하려면 다음 Windows PowerShell 명령을 실행하여 Windows 터미널 서비스 및 Windows 보안 로그를 쿼리할 수 있습니다.

_Event Viewer\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational_ 게이트웨이 작업 로그에서 성공적인 로그인 이벤트를 쿼리하려면 다음 PowerShell 명령을 사용합니다.

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* 이 명령에서는 사용자가 RD RAP(리소스 권한 부여 정책 요구 사항)를 충족하고 액세스 권한이 부여되었음을 보여 주는 Windows 이벤트가 표시됩니다.

![PowerShell을 사용 하 여 이벤트 보기](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* 이 명령에서는 사용자가 연결 권한 부여 정책 요구 사항을 충족한 경우를 보여 주는 이벤트가 표시됩니다.

![PowerShell을 사용 하 여 연결 권한 부여 정책 보기](./media/howto-mfa-nps-extension-rdg/image29.png)

또한 이 로그를 보고 300 및 200 이벤트 ID에서 필터링할 수도 있습니다. 보안 이벤트 뷰어 로그에서 성공적인 로그온 이벤트를 쿼리하려면 다음 명령을 사용합니다.

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* 이 명령은 중앙의 NPS 또는 RD 게이트웨이 서버에서 실행할 수 있습니다.

![성공적인 로그온 이벤트 샘플링](./media/howto-mfa-nps-extension-rdg/image30.png)

또한 아래와 같이 보안 로그 또는 네트워크 정책 및 액세스 서비스 사용자 지정 보기를 볼 수도 있습니다.

![네트워크 정책 및 액세스 서비스 이벤트 뷰어](./media/howto-mfa-nps-extension-rdg/image31.png)

Azure MFA용 NPS 확장을 설치한 서버에서 _Application and Services Logs\Microsoft\AzureMfa_에 있는 확장과 관련된 이벤트 뷰어 애플리케이션 로그를 찾을 수 있습니다.

![이벤트 뷰어 인증 응용 프로그램 로그](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>문제 해결 가이드

구성이 예상대로 작동하지 않는 경우 문제 해결을 시작할 첫 번째 작업은 사용자가 Azure MFA를 사용하도록 구성되어 있는지 확인하는 것입니다. 사용자가 [Azure Portal](https://portal.azure.com)에 연결하도록 합니다. 사용자가 보조 인증을 요청받았고 성공적으로 인증할 수 있으면 Azure MFA의 잘못된 구성을 제거할 수 있습니다.

Azure MFA가 사용자에 대해 작동하는 경우 관련 이벤트 로그를 검토해야 합니다. 여기에는 이전 섹션에서 설명한 보안 이벤트, 게이트웨이 작업 및 Azure MFA 로그가 포함됩니다.

다음은 실패한 로그온 이벤트(6273 이벤트 ID)를 보여 주는 보안 로그의 출력 예입니다.

![실패 한 로그온 이벤트 샘플](./media/howto-mfa-nps-extension-rdg/image33.png)

다음은 AzureMFA 로그와 관련된 이벤트입니다.

![샘플 Azure MFA 로그인 이벤트 뷰어](./media/howto-mfa-nps-extension-rdg/image34.png)

고급 문제 해결 옵션을 수행하려면 NPS 서비스가 설치된 NPS 데이터베이스 형식 로그 파일을 참조하세요. 이러한 로그 파일은 _%SystemRoot%\System32\Logs_ 폴더에 쉼표로 구분된 텍스트 파일로 만들어집니다.

이러한 로그 파일에 대한 자세한 내용은 [NPS 데이터베이스 형식 로그 파일 해석(영문)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10))을 참조하세요. 이러한 로그 파일의 항목은 스프레드시트 또는 데이터베이스로 가져오지 않고는 해석하기가 어려울 수 있습니다. 로그 파일을 해석하는 데 도움이 되는 여러 IAS 파서를 온라인으로 찾을 수 있습니다.

아래 이미지는 다운로드할 수 있는 [셰어웨어 애플리케이션](https://www.deepsoftware.com/iasviewer)의 출력을 보여 줍니다.

![샘플 셰어웨어 앱 IAS 파서](./media/howto-mfa-nps-extension-rdg/image35.png)

마지막으로 추가적인 문제 해결 옵션을 위해 [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)와 같은 프로토콜 분석기를 사용할 수 있습니다.

아래의 Microsoft Message Analyzer 이미지에서는 **CONTOSO\AliceC** 사용자 이름이 포함된 RADIUS 프로토콜에서 필터링된 네트워크 트래픽을 보여줍니다.

![필터링된 트래픽을 표시하는 Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>다음 단계

[Azure Multi-Factor Authentication 획득 방법](concept-mfa-licensing.md)

[RADIUS를 사용한 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버](howto-mfaserver-nps-rdg.md)

[Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)