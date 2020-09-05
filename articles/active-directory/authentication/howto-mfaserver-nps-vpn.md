---
title: Azure MFA 서버 및 타사 Vpn-Azure Active Directory
description: Cisco, Citrix 및 곱 향나무와 통합 하기 위한 Azure MFA 서버에 대 한 단계별 구성 가이드입니다.
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
ms.openlocfilehash: 116446c2ca6512ac39b907e95a2c1ccdf70befda
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949289"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Azure MFA 서버 및 타사 VPN 솔루션을 사용한 고급 시나리오

Azure Multi-Factor Authentication 서버 (Azure MFA 서버)를 사용 하 여 다양 한 타사 VPN 솔루션과 원활 하 게 연결할 수 있습니다. 이 문서에서는 Cisco의 &reg; vpn 어플라이언스, Citrix NETSCALER SSL vpn 어플라이언스 및 곱 향나무 네트워크 보안 액세스/Pulse Secure Connect SECURE SSL VPN 어플라이언스에 대해 집중적으로 설명 합니다. 이러한 세 가지 일반적인 어플라이언스를 해결하기 위해 구성 지침을 작성했습니다. Azure MFA 서버는 RADIUS, LDAP, IIS 또는 클레임 기반 인증을 사용 하 여 AD FS 하는 대부분의 다른 시스템과 통합할 수도 있습니다. 자세한 내용은 [AZURE MFA 서버 구성](howto-mfaserver-deploy.md#next-steps)에서 찾을 수 있습니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 클라우드 기반 MFA를 사용 하는 경우 [VPN 인프라를 AZURE MFA와 통합](howto-mfa-nps-extension-vpn.md)을 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco GLOBAL.ASA VPN 어플라이언스 및 Azure MFA 서버
Azure MFA 서버는 cisco의 VPN 어플라이언스와 통합 되어 &reg; Cisco AnyConnect &reg; vpn 로그인 및 포털 액세스에 대 한 추가 보안을 제공 합니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Anyconnect VPN을 사용한 Cisco ASA 및 LDAP를 위한 Azure MFA 구성](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Cisco ASA VPN 어플라이언스 통합 |
| [Anyconnect VPN을 사용한 Cisco ASA 및 RADIUS를 위한 Azure MFA 구성](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Cisco ASA VPN 어플라이언스 통합 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN 및 Azure MFA 서버
Azure MFA 서버는 citrix NetScaler SSL VPN 어플라이언스와 통합 되어 Citrix NetScaler SSL VPN 로그인 및 포털 액세스에 대 한 추가 보안을 제공 합니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Citrix NetScaler SSL VPN 및 LDAP를 위한 Azure MFA 구성](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Citrix NetScaler SSL VPN 어플라이언스 통합 |
| [Citrix NetScaler SSL VPN 및 RADIUS를 위한 Azure MFA 구성](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Citrix NetScaler SSL VPN 어플라이언스 통합 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>곱 향나무/펄스 보안 SSL VPN 어플라이언스 및 Azure MFA 서버
Azure MFA 서버는 곱 향나무/펄스 보안 ssl vpn 어플라이언스와 통합 되어, 곱/펄스 보안 SSL VPN 로그인 및 포털 액세스에 대 한 추가 보안을 제공 합니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 및 LDAP를 위한 Azure MFA 구성](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Juniper/Pulse Secure SSL VPN 어플라이언스 통합 |
| [Juniper/Pulse Secure SSL VPN 및 RADIUS를 위한 Azure MFA 구성](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Juniper/Pulse Secure SSL VPN 어플라이언스 통합 |

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication에 대한 NPS 확장을 사용하여 기존 인증 인프라 보강](howto-mfa-nps-extension.md)

- [Azure Multi-Factor Authentication 구성 설정](howto-mfa-mfasettings.md)
