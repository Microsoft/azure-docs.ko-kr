---
title: 온-프레미스 앱에 대 한 원격 액세스-Azure AD 응용 프로그램 프록시
description: Azure Active Directory 애플리케이션 프록시는 온-프레미스 웹 애플리케이션에 대한 보안 원격 액세스를 제공합니다. Azure AD에 Single Sign-On 되면 사용자는 외부 URL 또는 내부 애플리케이션 포털을 통해 클라우드와 온-프레미스 애플리케이션에 모두 액세스할 수 있습니다. 예를 들어 애플리케이션 프록시는 원격 데스크톱, SharePoint, Teams, Tableau, Qlik 및 LOB(기간 업무) 애플리케이션에 대한 원격 액세스와 Single Sign-On을 제공할 수 있습니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 96ebfecf01ff147a874674861c7cb64cb3725d52
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88164923"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시을 통해 온-프레미스 응용 프로그램에 원격으로 액세스

Azure Active Directory 애플리케이션 프록시는 온-프레미스 웹 애플리케이션에 대한 보안 원격 액세스를 제공합니다. Azure AD에 Single Sign-On 되면 사용자는 외부 URL 또는 내부 애플리케이션 포털을 통해 클라우드와 온-프레미스 애플리케이션에 모두 액세스할 수 있습니다. 예를 들어 애플리케이션 프록시는 원격 데스크톱, SharePoint, Teams, Tableau, Qlik 및 LOB(기간 업무) 애플리케이션에 대한 원격 액세스와 Single Sign-On을 제공할 수 있습니다.

다음은 Azure AD 애플리케이션 프록시의 특징입니다.

- **간편한 사용**. 사용자는 O365 및 Azure AD와 통합된 다른 SaaS 앱에 액세스할 때와 같은 방식으로 온-프레미스 애플리케이션에 액세스할 수 있습니다. 애플리케이션 프록시를 사용하도록 애플리케이션을 변경하거나 업데이트할 필요가 없습니다.

- **보안**. 온-프레미스 애플리케이션은 Azure의 권한 부여 컨트롤 및 보안 분석을 사용할 수 있습니다. 예를 들어 온-프레미스 응용 프로그램은 조건부 액세스 및 2 단계 인증을 사용할 수 있습니다. 애플리케이션 프록시는 방화벽을 통해 인바운드 연결을 열 필요가 없습니다.

- **비용 효율적**입니다. 온-프레미스 솔루션을 사용하려면 일반적으로 DMZ(완충 영역), 에지 서버 또는 기타 복잡한 인프라를 설정하고 유지 관리해야 합니다. 애플리케이션 프록시는 클라우드에서 실행되므로 쉽게 사용할 수 있습니다. 애플리케이션 프록시를 사용하기 위해 네트워크 인프라를 변경하거나 온-프레미스 환경에 추가 어플라이언스를 설치할 필요가 없습니다.

## <a name="what-is-application-proxy"></a>애플리케이션 프록시란?
애플리케이션 프록시는 사용자가 원격 클라이언트에서 온-프레미스 웹 애플리케이션에 액세스할 수 있게 해주는 Azure AD의 기능입니다. 애플리케이션 프록시는 클라우드에서 실행되는 애플리케이션 프록시 서비스와 온-프레미스 서버에서 실행되는 애플리케이션 프록시 커넥터를 모두 포함하고 있습니다. Azure AD, 애플리케이션 프록시 및 애플리케이션 프록시 커넥터가 함께 작동하여 Azure AD에서 웹 애플리케이션으로 사용자 로그온 토큰을 안전하게 전달합니다.

애플리케이션 프록시는 다음 항목과 함께 작동합니다.

* 인증을 위해 [Windows 통합 인증](application-proxy-configure-single-sign-on-with-kcd.md)을 사용하는 웹 애플리케이션
* 폼 기반 또는 [헤더 기반](application-proxy-configure-single-sign-on-with-ping-access.md) 액세스를 사용하는 웹 애플리케이션
* 여러 디바이스에서 다양한 애플리케이션을 표시하려는 웹 API
* [원격 데스크톱 게이트웨이](application-proxy-integrate-with-remote-desktop-services.md) 뒤에 호스트 되는 응용 프로그램
* MSAL(Microsoft Authentication Library)과 통합되는 리치 클라이언트 앱

애플리케이션 프록시는 Single Sign-On을 지원합니다. 지원되는 방법에 대한 자세한 내용은 [Single Sign-On 방법 선택](sso-options.md#choosing-a-single-sign-on-method)을 참조하세요.

원격 사용자에 게 내부 리소스에 대 한 액세스를 제공 하기 위해 응용 프로그램 프록시가 권장 됩니다. 응용 프로그램 프록시는 VPN 또는 역방향 프록시에 대 한 필요성을 대체 합니다. 회사 네트워크의 내부 사용자에 게 적합 하지 않습니다.  응용 프로그램 프록시를 불필요 하 게 사용 하는 이러한 사용자는 예기치 않은 성능 문제를 일으킬 수 있습니다.

## <a name="how-application-proxy-works"></a>애플리케이션 프록시의 작동 원리

다음 다이어그램은 Azure AD와 애플리케이션 프록시가 함께 작동하여 온-프레미스 애플리케이션에 대한 Single Sign-On을 제공하는 방법을 보여줍니다.

![Azure Ad 애플리케이션 프록시 다이어그램](./media/application-proxy/azureappproxxy.png)

1. 사용자는 엔드포인트를 통해 애플리케이션에 액세스한 후에는 Azure AD 로그인 페이지로 리디렉션됩니다.
2. 로그인이 성공하면 Azure AD가 사용자의 클라이언트 디바이스에 토큰을 전송합니다.
3. 클라이언트가 애플리케이션 프록시 서비스로 토큰을 보내면 서비스는 토큰에서 UPN(사용자 주체 이름) 및 SPN(보안 주체 이름)을 검색합니다. 그러면 애플리케이션 프록시가 애플리케이션 프록시 커넥터에 요청을 보냅니다.
4. Single Sign-On을 구성한 경우 커넥터는 사용자를 대신하는 데 필요한 모든 추가 인증을 수행합니다.
5. 커넥터는 온-프레미스 애플리케이션에 요청을 보냅니다.
6. 응답은 커넥터 및 애플리케이션 프록시 서비스를 통해 사용자에게 전송됩니다.

| 구성 요소 | Description |
| --------- | ----------- |
| 엔드포인트  | 엔드포인트는 URL 또는 [최종 사용자 포털](end-user-experiences.md)입니다. 사용자는 외부 URL에 액세스하여 네트워크 외부에서 애플리케이션에 연결할 수 있습니다. 네트워크 내 사용자는 URL 또는 최종 사용자 포털을 통해 애플리케이션에 액세스할 수 있습니다. 사용자가 이러한 엔드포인트 중 하나로 이동하면 Azure AD에서 인증한 다음 커넥터를 통해 온-프레미스 애플리케이션에 라우팅됩니다.|
| Azure AD | Azure AD는 클라우드에 저장된 테넌트 디렉터리를 사용하여 인증을 수행합니다. |
| 애플리케이션 프록시 서비스 | 이 애플리케이션 프록시 서비스는 클라우드에서 Azure AD의 일부로 실행됩니다. 사용자의 로그온 토큰을 애플리케이션 프록시 커넥터에 전달합니다. 애플리케이션 프록시는 요청이 있을 시 액세스 가능한 모든 헤더를 클라이언트 IP 주소에 전달하고 프로토콜에 따라 헤더를 설정합니다. 들어오는 프록시 요청에 이미 헤더가 있는 경우 헤더의 값인 쉼표로 구분된 목록의 끝에 클라이언트 IP 주소가 추가됩니다.|
| 애플리케이션 프록시 커넥터 | 커넥터는 네트워크 내부의 Windows Server에서 실행되는 간단한 에이전트입니다. 커넥터는 클라우드 애플리케이션과 온-프레미스 애플리케이션의 애플리케이션 프록시 서비스 사이에서 이루어지는 통신을 관리합니다. 커넥터가 아웃바운드 연결만 사용하므로 인바운드 포트를 열거나 DMZ에 아무 것도 배치할 필요가 없습니다. 커넥터는 상태를 저장하지 않으며 필요에 따라 클라우드에서 정보를 가져옵니다. 커넥터에 대한 정보 및 부하 분산 및 인증하는 방법은 [Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)를 참조하세요.|
| AD(Active Directory) | Active Directory는 온-프레미스에서 실행되어 도메인 계정에 대한 인증을 수행합니다. Single Sign-On이 구성되면 커넥터가 AD와 통신하여 필요한 추가 인증을 수행합니다.
| 온-프레미스 애플리케이션 | 마지막으로, 사용자는 온-프레미스 애플리케이션에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
응용 프로그램 프록시 사용을 시작 하려면 [자습서: 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md)를 참조 하세요.

