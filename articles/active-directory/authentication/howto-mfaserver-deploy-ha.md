---
title: Azure MFA 서버의 고가용성 - Azure Active Directory
description: 고가용성을 제공하는 구성에서 Azure Multi-Factor Authentication 서버의 여러 인스턴스를 배포합니다.
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
ms.openlocfilehash: 8f5f1e123447bfff53a603bad23b0444a93e4a83
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919627"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>고가용성을 위한 Azure Multi-Factor Authentication 서버 구성

Azure 서버 MFA 배포로 고가용성을 달성하려면 여러 MFA 서버를 배포해야 합니다. 이 섹션에서는 Azure MFS 서버 배포에서 고가용성 목표를 달성하기 위한 부하가 분산된 디자인에 대한 정보를 제공합니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="mfa-server-overview"></a>MFA 서버 개요

다음 다이어그램에 표시된 것처럼 Azure MFA 서버 서비스 아키텍처는 여러 가지 구성 요소로 이루어집니다.

 ![MFA 서버 아키텍처 구성 요소](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

MFA 서버는 Azure Multi-Factor Authentication 소프트웨어가 설치되어 있는 Windows Server입니다. MFA 서버 인스턴스가 작동하려면 Azure에서 MFA 서비스에 의해 활성화되어야 합니다. 온-프레미스에는 둘 이상의 MFA 서버를 설치할 수 있습니다.

설치된 첫 번째 MFA 서버는 기본적으로 Azure MFA 서비스에 의해 활성화되었을 경우 마스터 MFA 서버입니다. 마스터 MFA 서버에는 쓰기 가능한 PhoneFactor.pfdata 데이터베이스 복사본이 있습니다. MFA 서버 인스턴스의 후속 설치는 하위라고 합니다. MFA 하위가 PhoneFactor.pfdata 데이터베이스의 복제된 읽기 전용 복사본을 포함합니다. MFA 서버는 원격 프로시저 호출(RPC)을 사용하여 정보를 복제합니다. 모든 MFA 서버는 정보를 복제하기 위해 전체적으로 도메인에 가입되거나 독립 실행형이어야 합니다.

MFA 마스터와 하위 MFA 서버는 모두 2단계 인증이 필요한 경우 MFA 서비스와 통신합니다. 예를 들어 사용자가 2단계 인증을 요구하는 애플리케이션에 액세스하려고 하는 경우 사용자는 AD(Active Directory)와 같은 ID 공급자에 의해 먼저 인증됩니다.

AD와 성공적으로 인증된 후 MFA 서버는 MFA 서비스와 통신합니다. MFA 서버는 애플리케이션에 대한 사용자 액세스를 허용하거나 거부하는 MFA 서비스 알림을 기다립니다.

MFA 마스터 서버가 오프라인인 경우 인증은 계속 처리될 수 있지만 MFA 데이터베이스에 대한 변경이 요구되는 작업은 처리할 수 없습니다. (예를 들어 사용자 추가, 셀프 서비스 PIN 변경 및 사용자 정보 변경 또는 사용자 포털 액세스 등)

## <a name="deployment"></a>배포

Azure MFA 서버 및 관련 구성 요소 간 부하 분산을 위해서는 다음 중요 사항을 고려합니다.

* **RADIUS 표준을 사용하여 고가용성을 달성**합니다. Azure MFA 서버를 RADIUS 서버로 사용하는 경우, 한 MFA 서버를 기본 RADIUS 인증 대상으로, 다른 Azure MFA 서버를 보조 인증 에이전트로 구성할 수 있습니다. 그러나 보조 인증 대상에 대해 인증될 수 있으려면 기본 인증 대상에서 인증이 실패할 경우 시간 제한 기간이 발생할 때까지 기다려야 하므로 고가용성을 달성하는 데 이 방법은 실용적이지 않을 수 있습니다. RADIUS 클라이언트와 RADIUS 서버(이 경우 Azure MFA 서버는 RADIUS 서버 역할로 작동함) 간에 RADIUS 트래픽을 부하 분산시키는 것이 더욱 효율적이므로 클라이언트가 가리킬 수 있는 단일 URL로 RADIUS 클라이언트를 구성할 수 있습니다.
* **수동으로 MFA 하위의 수준을 올려야** 합니다. 마스터 Azure MFA 서버가 오프라인 상태가 되면 보조 Azure MFA 서버는 MFA 요청을 계속해서 처리합니다. 그러나 마스터 MFA 서버가 사용 가능해질 때까지 관리자는 사용자를 추가하거나 MFA 설정을 수정할 수 없으며 사용자는 사용자 포털을 사용하여 변경할 수 없습니다. MFA 하위의 수준을 마스터 역할로 올리는 것은 항상 수동 프로세스입니다.
* **구성 요소의 분리성**. Azure MFA 서버는 동일한 Windows Server 인스턴스 또는 다른 인스턴스에 설치할 수 있는 여러 구성 요소로 이루어집니다. 이러한 구성 요소에는 사용자 포털, 모바일 앱 웹 서비스 및 ADFS 어댑터(에이전트)가 있습니다. 이 분리성을 통해 웹 애플리케이션 프록시를 사용하여 경계 네트워크로부터 사용자 포털 및 모바일 앱 웹 서버를 게시할 수 있습니다. 다음 다이어그램에 나와 있는 것처럼 이러한 구성이 디자인의 전반적인 보안에 추가됩니다. MFA 사용자 포털 및 모바일 앱 웹 서버는 HA 부하 분산된 구성에도 배포될 수 있습니다.

   ![경계 네트워크와 MFA 서버](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **SMS(즉, 단방향 SMS)를 통한 OTP(일회용 암호)를 사용하려면 트래픽이 부하 분산된 경우 고정 세션을 사용해야 합니다**. 단방향 SMS는 MFA 서버가 사용자에게 OTP가 포함된 문자 메시지를 보내도록 하는 인증 옵션입니다. 사용자는 프롬프트 창에서 OTP를 입력하고 MFA 챌린지를 완료합니다. Azure MFA 서버를 부하 분산하는 경우 초기 인증 요청을 제공한 동일한 서버는 사용자로부터 OTP 메시지를 받는 서버여야 하며 다른 MFA 서버가 OTP 응답을 수신하는 경우 인증 챌린지는 실패합니다. 자세한 내용은 [SMS를 통한 일회용 암호가 Azure MFA 서버에 추가됨](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server)을 참조하세요.
* **사용자 포털 및 모바일 앱 웹 서비스의 부하 분산 배포에는 고정 세션이 필요**합니다. MFA 사용자 포털 및 모바일 앱 웹 서비스를 부하 분산하는 경우 각 세션은 동일한 서버에 유지되어야 합니다.

## <a name="high-availability-deployment"></a>고가용성 배포

다음 다이어그램은 참조를 위해 ADFS와 함께 Azure MFA 및 해당 구성 요소의 전체 HA 부하 분산된 구현을 보여 줍니다.

 ![Azure MFA 서버 HA 구현](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

위 다이어그램의 해당 번호가 매겨진 영역에 대해 다음 항목을 유의합니다.

1. 두 Azure MFA 서버(MFA1 및 MFA2)는 부하 분산(mfaapp.contoso.com)되고 정적 포트(4443)를 사용하여 PhoneFactor.pfdata 데이터베이스를 복제하도록 구성됩니다. 웹 서비스 SDK는 TCP 포트 443을 통해 ADFS 서버와 통신할 수 있도록 MFA 서버 각각에 설치됩니다. MFA 서버는 상태 비저장 부하 분산된 구성에 배포됩니다. 그러나 SMS를 통한 OTP를 사용하려는 경우 상태 저장 부하 분산을 사용해야 합니다.
   ![Azure MFA 서버 - 앱 서버 HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > RPC는 동적 포트를 사용하므로 RPC에서 잠재적으로 사용할 수 있는 동적 포트 범위까지 방화벽을 열어 놓는 것은 좋지 않습니다. MFA 애플리케이션 서버 **사이에** 방화벽이 있는 경우 하위 및 마스터 서버 간의 복제 트래픽에 대한 정적 포트에서 통신하고 방화벽에서 해당 포트를 열도록 MFA 서버를 구성해야 합니다. ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor```에서 ```Pfsvc_ncan_ip_tcp_port```라는 DWORD 레지스트리 값을 만들고 값을 사용 가능한 정적 포트로 설정하여 정적 포트를 강제 적용할 수 있습니다. 연결은 항상 하위 MFA 서버에 의해 마스터로 시작되고 정적 포트는 마스터에서만 필요하지만 언제든지 하위를 마스터가 되도록 수준을 올릴 수 있으므로 모든 MFA 서버에서 정적 포트를 설정해야 합니다.

2. 두 명의 사용자 포털/MFA 모바일 앱 서버(MFA-UP-MAS1 및 MFA-UP-MAS2)는 **상태 저장** 구성으로 부하 분산됩니다(mfa.contoso.com). 고정 세션은 MFA 사용자 포털과 Mobile App Service의 부하 분산을 위한 필수 조건임에 유의하세요.
   ![Azure MFA 서버 - 사용자 포털 및 Mobile App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. ADFS 서버 팜은 경계 네트워크에 있는 부하 분산된 ADFS 프록시를 통해 부하 분산되어 인터넷에 게시됩니다. 각 ADFS 서버는 ADFS 에이전트를 사용하여 TCP 포트 443을 통해 단일 부하 분산된 URL(mfaapp.contoso.com)을 사용하는 Azure MFA 서버와 통신합니다.

## <a name="next-steps"></a>다음 단계

* [Azure MFA 서버 설치 및 구성](howto-mfaserver-deploy.md)
