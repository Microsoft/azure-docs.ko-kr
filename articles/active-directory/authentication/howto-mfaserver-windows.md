---
title: Windows 인증 및 Azure MFA 서버-Azure Active Directory
description: Windows 인증 및 Azure Multi-Factor Authentication 서버 배포
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
ms.openlocfilehash: 8b945b984210dc3fc3338da8a5670a7813198e40
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919474"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows 인증 및 Azure Multi-Factor Authentication 서버

Azure Multi-Factor Authentication 서버의 Windows 인증 섹션을 사용하여 애플리케이션에 대한 Windows 인증을 사용하도록 설정하고 구성할 수 있습니다. Windows 인증을 설정하기 전에 다음 목록을 유념하세요.

* 설정한 후에는 터미널 서비스에 대한 Azure Multi-Factor Authentication을 다시 부팅하여 적용되도록 합니다.
* ' Azure Multi-Factor Authentication 사용자가 일치 해야 함 '이 선택 되어 있고 사용자 목록에 없는 경우 다시 부팅 한 후에는 컴퓨터에 로그인 할 수 없습니다.
* 신뢰할 수 있는 IP는 애플리케이션이 클라이언트 IP에 인증을 제공할 수 있는지 여부에 따라 달라집니다. 현재는 터미널 서비스만 지원됩니다.  

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

> [!NOTE]
> 이 기능은 Windows Server 2012 R2에서 보안 터미널 서비스를 보안하는 데 지원되지 않습니다.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Windows 인증을 사용 하 여 응용 프로그램을 보호 하려면 다음 절차를 따르십시오.

1. Azure Multi-Factor Authentication 서버에서 Windows 인증 아이콘을 클릭합니다.
   ![MFA 서버의 Windows 인증](./media/howto-mfaserver-windows/windowsauth.png)
2. **Windows 인증 사용** 확인란을 선택합니다. 이 상자는 기본적으로 선택되지 않습니다.
3. 애플리케이션 탭에서 관리자는 Windows 인증을 위해 하나 이상의 애플리케이션을 구성할 수 있습니다.
4. 서버 또는 애플리케이션을 선택합니다. 서버/애플리케이션이 사용되는지 여부를 지정합니다. **확인**을 클릭합니다.
5. **추가** ...를 클릭 합니다.
6. 신뢰할 수 있는 IP 탭에서 특정 IP에서 오는 Windows용 Azure Multi-Factor Authentication 세션을 건너뛸 수 있습니다. 예를 들어, 직원이 사무실과 집에서 애플리케이션을 사용하는 경우 사무실에 있는 동안 Azure Multi-Factor Authentication 확인용 전화를 원하지 않음을 결정할 수 있습니다. 이를 위해 사무실 서브넷을 신뢰할 수 있는 IP 항목으로 지정할 수 있습니다.
7. **추가** ...를 클릭 합니다.
8. 단일 ip 주소를 건너뛰려면 **단일 ip** 를 선택 합니다.
9. 전체 IP 범위를 건너뛰려면 **Ip 범위** 를 선택 합니다. 예 10.63.193.1-10.63.193.100.
10. 서브넷 표기법을 사용 하 여 Ip 범위를 지정 하려면 **서브넷** 을 선택 합니다. 시작 IP 서브넷을 입력하고 드롭다운 목록에서 적절한 네트워크 마스크를 선택합니다.
11. **확인**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure MFA 서버에 대한 타사 VPN 어플라이언스 구성](howto-mfaserver-nps-vpn.md)

- [Azure MFA에 대한 NPS 확장으로 기존 인증 인프라 보강](howto-mfa-nps-extension.md)
