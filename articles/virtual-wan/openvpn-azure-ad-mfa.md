---
title: 'VPN 사용자에 대해 MFA 사용: Azure AD 인증'
description: VPN 사용자에 대해 multi-factor authentication 사용
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: fcb60e80189da89b3f634c14582be606307536e6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166647"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN 사용자에 대해 MFA (Azure Multi-Factor Authentication) 사용

액세스 권한을 부여 하기 전에 사용자에 게 두 번째 인증 단계를 묻는 메시지가 표시 되도록 하려면 Azure AD 테넌트에 대해 MFA (Azure Multi-Factor Authentication)를 구성할 수 있습니다. 이 문서의 단계는 2 단계 인증에 대 한 요구 사항을 설정 하는 데 도움이 됩니다.

## <a name="prereq"></a>인지

이 구성에 대 한 필수 구성 요소는 [테넌트 구성](openvpn-azure-ad-tenant.md)의 단계를 사용 하 여 구성 된 Azure AD 테넌트입니다.

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>로그인 설정 구성

**AZURE VPN-속성** 페이지에서 로그인 설정을 구성 합니다.

1. **사용자가 로그인 할** 수 있도록 설정 하 시겠습니까? **예**로 설정 합니다. 이렇게 하면 AD 테넌트의 모든 사용자가 VPN에 성공적으로 연결할 수 있습니다.
2. Azure VPN에 대 한 권한이 있는 사용자로만 로그인을 제한 하려면 **사용자 할당 필요?** 를 **예** 로 설정 합니다.
3. 변경 내용을 저장합니다.

   ![권한](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결 하려면 VPN 클라이언트 프로필을 만들고 구성 해야 합니다. [Azure에 대 한 지점 및 사이트 간 연결에 대 한 AZURE AD 인증 구성을](virtual-wan-point-to-site-azure-ad.md)참조 하세요.
