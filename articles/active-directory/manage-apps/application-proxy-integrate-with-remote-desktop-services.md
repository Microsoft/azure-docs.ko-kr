---
title: Azure AD 앱 프록시를 사용하여 원격 데스크톱 게시 | Microsoft 문서
description: Azure AD 애플리케이션 프록시 커넥터에 대한 기본 사항을 제공합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108456"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 사용하여 원격 데스크톱 게시

원격 데스크톱 서비스와 Azure AD 애플리케이션 프록시를 함께 작동하여 회사 네트워크에서 멀리 떨어져 있는 작업자의 생산성을 높일 수 있습니다. 

이 문서의 대상은 다음과 같습니다.
- 원격 데스크톱 서비스를 통해 온-프레미스 애플리케이션을 게시하여 최종 사용자에게 더 많은 애플리케이션을 제공하려고 하는 현재 애플리케이션 프록시 고객.
- Azure AD 애플리케이션 프록시를 사용하여 배포의 공격에 대한 취약성을 줄이려고 하는 현재 원격 데스크톱 서비스 고객. 이 시나리오는 rds.에 제한 된 집합만 2 단계 인증 및 조건부 액세스 제어 제공

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>애플리케이션 프록시를 표준 RDS 배포에 맞추는 방법

표준 RDS 배포에는 Windows Server에서 실행되는 다양한 원격 데스크톱 역할 서비스가 포함됩니다. [Remote Desktop Services architecture](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)(원격 데스크톱 서비스 아키텍처)에는 다양한 배포 옵션이 있습니다. 다른 배포 옵션과 달리, [RDS deployment with Azure AD Application Proxy(Azure AD 애플리케이션 프록시를 사용한 RDS 배포)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)(다음 다이어그램에 표시)는 커넥터 서비스를 실행하는 서버에서 영구 아웃바운드 연결이 있다는 것입니다. 기타 배포에서는 부하 분산 장치를 통해 열린 인바운드 연결을 유지합니다.

![애플리케이션 프록시는 RDS VM과 공용 인터넷 간에 놓입니다.](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

RDS 배포에서 RD 웹 역할 및 RD 게이트웨이 역할은 인터넷 연결 컴퓨터에서 실행됩니다. 이러한 엔드포인트는 다음 이유로 표시됩니다.
- RD 웹에서는 사용자에게 액세스할 수 있는 다양한 온-프레미스 애플리케이션 및 데스크톱에 로그인하고 이를 볼 수 있는 공용 엔드포인트를 제공합니다. 리소스 선택 시 OS에서 네이티브 앱을 사용하여 RDP 연결이 생성됩니다.
- 사용자가 RDP 연결을 시작하면 RD 게이트웨이가 중요해집니다. RD 게이트웨이는 인터넷을 통해 나오는 암호화된 RDP 트래픽을 처리하고 사용자가 연결되어 있는 온-프레미스 서버로 전환합니다. 이 시나리오에서 RD 게이트웨이가 수신하는 트래픽은 Azure AD 애플리케이션 프록시에서 나옵니다.

>[!TIP]
>이전에 RDS를 배포하지 않았거나 시작하기 전에 추가 정보가 필요한 경우 [seamlessly deploy RDS with Azure Resource Manager and Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)(Azure Resource Manager 및 Azure Marketplace를 사용하여 원활하게 RDS 배포)를 수행하는 방법을 알아보세요.

## <a name="requirements"></a>요구 사항

- 웹 클라이언트가 애플리케이션 프록시를 지원하지 않으므로 원격 데스크톱 웹 클라이언트 이외의 클라이언트를 사용합니다.

- RD 웹 및 RD 게이트웨이 엔드포인트는 둘 다 같은 컴퓨터에 있고 공통 루트를 사용해야 합니다. RD 웹 및 RD 게이트웨이는 애플리케이션 프록시와 함께 단일 애플리케이션으로 게시되므로 두 애플리케이션 간에 Single Sign-On 환경이 있을 수 있습니다.

- 이미 [RDS를 배포](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)하고 [애플리케이션 프록시를 사용하도록 설정](application-proxy-add-on-premises-application.md)했어야 합니다.

- 이 시나리오에서는 최종 사용자가 RD 웹 페이지를 통해 연결하는 Windows 7 또는 Windows 10 데스크톱에서 Internet Explorer를 수행한다고 가정합니다. 다른 운영 체제를 지원해야 하는 경우 [다른 클라이언트 구성 지원](#support-for-other-client-configurations)을 참조하세요.

- RD 웹을 게시할 경우 동일한 내부 및 외부 FQDN을 사용하는 것이 좋습니다. 내부 및 외부 FQDN 서로 다른 경우 클라이언트가 잘못된 링크를 받는 것을 방지하려면 요청 헤더 변환을 사용하지 않도록 설정해야 합니다. 

- Internet Explorer에서 RDS ActiveX 추가 기능을 사용하도록 설정합니다.

- Azure AD 사전 인증 흐름에 대 한 사용자만 연결할 수 있습니다에 게시 된 리소스를 **RemoteApp 및 데스크톱** 창입니다. 사용자가 데스크톱을 연결할 수 없습니다는 **원격 PC에 연결** 창입니다.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>공동 RDS 및 애플리케이션 프록시 시나리오 배포

환경에 대해 RDS 및 Azure AD 애플리케이션 프록시를 설정한 후 두 가지 솔루션을 결합하는 단계를 따릅니다. 이러한 단계에서는 두 개의 웹 연결 RDS 엔드포인트(RD 웹 및 RD 게이트웨이)를 애플리케이션으로 게시하고 나서 애플리케이션 프록시를 통과하도록 RDS의 트래픽을 전달하는 작업을 안내합니다.

### <a name="publish-the-rd-host-endpoint"></a>RD 호스트 엔드포인트 게시

1. 다음 값을 사용하여 [새 애플리케이션 프록시 애플리케이션을 게시](application-proxy-add-on-premises-application.md)합니다.
   - 내부 URL: `https://\<rdhost\>.com/`, 여기서 `\<rdhost\>`는 RD 웹과 RD 게이트웨이에서 공유하는 공통 루트입니다.
   - 외부 URL: 이 필드는 애플리케이션 이름에 따라 자동으로 채워지지만 수정할 수 있습니다. 사용자는 RDS에 액세스하면 이 URL로 이동합니다.
   - 사전 인증 방법: Azure Active Directory
   - URL 헤더 변환: 아닙니다.
2. 게시된 RD 애플리케이션에 사용자를 할당합니다. 모든 사용자가 RDS에도 액세스할 수 있는지 확인합니다.
3. 애플리케이션에 대한 Single Sign-On 방법을 **Azure AD Single Sign-On 사용 안 함**으로 유지합니다. 사용자에게는 Azure AD 및 RD 웹에 대해 한 번씩 인증하도록 요청되지만 RD 게이트웨이에 대한 Single Sign-On이 제공됩니다.
4. 선택 **Azure Active Directory**를 차례로 **앱 등록**합니다. 목록에서 앱을 선택 합니다.
5. 아래 **관리**를 선택 **브랜드**합니다.
6. 업데이트를 **홈 페이지 URL** RD 웹 엔드포인트을 가리키도록 필드 (같은 `https://\<rdhost\>.com/RDWeb`).

### <a name="direct-rds-traffic-to-application-proxy"></a>애플리케이션 프록시에 대한 직접 RDS 트래픽

관리자로 RDS 배포에 연결하고 배포에 대한 RD 게이트웨이 서버 이름을 변경합니다. 이렇게 구성하면 연결이 Azure AD 애플리케이션 프록시 서비스를 통과합니다.

1. RD 연결 브로커 역할을 실행하는 RDS 서버에 연결합니다.
2. **서버 관리자**를 시작합니다.
3. 왼쪽 창에서 **원격 데스크톱 서비스**를 선택합니다.
4. **개요**를 선택합니다.
5. 배포 개요 섹션에서 드롭다운 메뉴를 선택하고 **배포 속성 편집**을 선택합니다.
6. [RD 게이트웨이] 탭에서 **서버 이름** 필드를 애플리케이션 프록시에서 RD 호스트 엔드포인트에 대해 설정한 외부 URL로 변경합니다.
7. **로그온 방법** 필드를 **암호 인증**으로 변경합니다.

   ![RDS의 배포 속성 화면](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. 각 컬렉션에 대해 이 명령을 실행합니다. *\<yourcollectionname\>* 및 *\<proxyfrontendurl\>* 을 사용자 고유의 정보로 바꿉니다. 이 명령은 RD 웹과 RD 게이트웨이 간에 Single Sign-On을 사용하도록 설정하고 성능을 최적화합니다.

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **예:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >위의 명령은 “`nrequire”에서 억음 악센트 기호를 사용합니다.

9. 이 컬렉션의 RDWeb에서 다운로드할 RDP 파일 콘텐츠를 보고 사용자 지정 RDP 속성의 수정 내용을 확인하려면 다음 명령을 실행합니다.
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

이제 원격 데스크톱을 구성했으므로 Azure AD 애플리케이션 프록시가 RDS의 인터넷 연결 구성 요소로 대체되었습니다. RD 웹 및 RD 게이트웨이 엔드포인트에서 다른 공용 인터넷 연결 엔드포인트를 제거할 수 있습니다.

## <a name="test-the-scenario"></a>시나리오 테스트

Windows 7 또는 10 컴퓨터에서 Internet Explorer를 사용하여 시나리오를 테스트합니다.

1. 설정한 외부 URL로 이동하거나 [MyApps](https://myapps.microsoft.com) 패널에서 애플리케이션을 찾습니다.
2. Azure Active Directory에 대해 인증할지 묻는 메시지가 표시됩니다. 애플리케이션에 할당한 계정을 사용합니다.
3. RD 웹에 대해 인증할지 묻는 메시지가 표시됩니다.
4. RDS 인증이 성공하면 원하는 데스크톱 또는 애플리케이션을 선택하고 작동을 시작할 수 있습니다.

## <a name="support-for-other-client-configurations"></a>다른 클라이언트 구성에 대한 지원

이 문서에 요약된 구성은 Internet Explorer와 RDS ActiveX 추가 기능을 사용하는 Windows 7 또는 10 사용자를 대상으로 합니다. 그러나 필요한 경우 다른 운영 체제 또는 브라우저를 지원할 수 있습니다. 차이점은 사용하는 인증 방법입니다.

| 인증 방법 | 지원되는 클라이언트 구성 |
| --------------------- | ------------------------------ |
| 사전 인증    | Internet Explorer + RDS ActiveX 추가 기능을 사용하는 Windows 7/10 |
| 통과 | Microsoft 원격 데스크톱 애플리케이션을 지원하는 다른 운영 체제 |

사전 인증 흐름은 통과 흐름보다 더 많은 보안 이점을 제공합니다. 사전 인증을 사용 하 여 온-프레미스 리소스에 대 한 single sign-on, 조건부 액세스 및 2 단계 확인 같은 Azure AD 인증 기능을 사용할 수 있습니다. 또한 인증된 트래픽만 네트워크에 도달하도록합니다.

통과 인증을 사용하려면 이 문서에 나열된 단계를 두 번만 수정하면 됩니다.
1. [RD 호스트 엔드포인트 게시](#publish-the-rd-host-endpoint)의 1단계에서 사전 인증 방법을 **통과**로 설정합니다.
2. [애플리케이션 프록시에 대한 직접 RDS 트래픽](#direct-rds-traffic-to-application-proxy)에서 8단계 전체를 건너뜁니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 애플리케이션 프록시를 사용하여 SharePoint에 원격 액세스 사용하도록 설정](application-proxy-integrate-with-sharepoint-server.md)  
[Azure AD 애플리케이션 프록시를 사용하여 앱에 원격으로 액세스하는 경우 보안 고려 사항](application-proxy-security.md)
