---
title: 'VPN 사용자에 대해 MFA 사용: Azure AD 인증'
description: VPN 사용자에 대해 multi-factor authentication 사용
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 771dea2d9ae2979bc71880368ed3a9a538e8a803
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964729"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>VPN 사용자에 대해 MFA (Azure Multi-Factor Authentication) 사용

액세스 권한을 부여 하기 전에 사용자에 게 두 번째 인증 단계를 묻는 메시지가 표시 되도록 하려면 사용자별로 Azure Multi-Factor Authentication (MFA)를 구성 하거나 [조건부 액세스](../active-directory/conditional-access/overview.md) 를 통해 mfa (Multi-Factor Authentication)를 활용 하 여 보다 세분화 된 제어를 수행할 수 있습니다. 사용자 당 Multi-Factor Authentication 구성은 추가 비용 없이 사용 하도록 설정할 수 있지만 사용자 당 MFA를 사용 하도록 설정 하는 경우 사용자에 게 Azure AD 테넌트에 연결 된 모든 응용 프로그램에 대 한 두 번째 단계 인증을 요구 하는 메시지가 표시 됩니다. 조건부 액세스를 사용 하면 두 번째 단계를 승격 하는 방법을 보다 세밀 하 게 제어할 수 있으며, Azure AD 테넌트에 연결 된 다른 응용 프로그램이 아닌 VPN에만 MFA 할당을 허용할 수 있습니다.

## <a name="enableauth"></a>인증 사용

1. **Azure Active Directory-> Enterprise 응용 프로그램-모든 응용 프로그램 >** 으로 이동 합니다.
2. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지에서 **Azure VPN**을 선택 합니다.

   ![디렉터리 ID](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>로그인 설정 구성

**AZURE VPN-속성** 페이지에서 로그인 설정을 구성 합니다.

1. **사용자가 로그인 할** 수 있도록 설정 하 시겠습니까? **예**로 설정 합니다. 이렇게 하면 AD 테넌트의 모든 사용자가 VPN에 성공적으로 연결할 수 있습니다.
2. Azure VPN에 대 한 권한이 있는 사용자로만 로그인을 제한 하려면 **사용자 할당 필요?** 를 **예** 로 설정 합니다.
3. 변경 내용을 저장합니다.

   ![권한](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="option-1---enable-multi-factor-authentication-mfa-via-conditional-access"></a>옵션 1-조건부 액세스를 통해 Multi-Factor Authentication (MFA) 사용

조건부 액세스를 통해 응용 프로그램 별로 세분화 된 액세스 제어를 사용할 수 있습니다.  조건부 액세스를 활용 하려면 조건부 액세스 규칙에 적용 되는 사용자에 게 하나 이상의 라이선스가 적용 Azure AD Premium 해야 합니다.

1. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지에서 **Azure VPN**을 선택 하 고 **조건부 액세스**를 선택한 다음 **새 정책**을 클릭 합니다.
2. 사용자 및 그룹의 *포함* 탭에서 **사용자 및**그룹을 선택 하 고, **사용자 및 그룹**을 선택 하 고, MFA의 대상이 되는 그룹 또는 사용자 집합을 선택 합니다.  **Done**을 클릭합니다.
![할당](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. **권한 부여**에서 **액세스 권한 부여**를 선택 하 고 **multi-factor authentication 필요**를 선택 하 고 선택 **된 모든 컨트롤 필요**를 선택 하 고 **선택** 단추를 클릭 합니다.
액세스 허용 ![-MFA](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. **정책 사용** **아래에서 확인을 선택** 하 고 **만들기** 단추를 클릭 합니다.
정책 사용 ![](../../includes/media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)

## <a name="option-2---enable-multi-factor-authentication-mfa-per-user"></a>옵션 2-사용자 당 MFA (Multi-Factor Authentication) 사용

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결 하려면 VPN 클라이언트 프로필을 만들고 구성 해야 합니다. [P2S vpn 연결에 대 한 vpn 클라이언트 구성](openvpn-azure-ad-client.md)을 참조 하세요.
