---
title: 'VPN Gateway: P2S VPN 연결에 대 한 Azure AD 테넌트: Azure AD 인증'
description: Azure AD 인증을 사용 하 여 VNet에 연결 하는 데 P2S VPN을 사용할 수 있습니다.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: alzam
ms.openlocfilehash: 1f7cf97e38bf201679593819cce814249f9625b0
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930411"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결에 대 한 Azure Active Directory 테넌트 만들기

VNet에 연결 하는 경우 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 오픈 VPN 프로토콜을 사용 하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 이 문서는 P2S 오픈 VPN 인증을 위해 Azure AD 테넌트를 설정 하는 데 도움이 됩니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

## <a name="tenant"></a>1. Azure AD 테넌트 만들기

[새 테넌트 만들기](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용 하 여 Azure AD 테넌트를 만듭니다.

* 조직 이름
* 초기 도메인 이름

예:

   ![새 Azure AD 테넌트](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. Azure AD 테넌트 사용자 만들기

다음으로 두 개의 사용자 계정을 만듭니다. 전역 관리자 계정과 마스터 사용자 계정 하나를 만듭니다. 마스터 사용자 계정은 마스터 포함 계정 (서비스 계정)으로 사용 됩니다. Azure AD 테넌트 사용자 계정을 만들 때 만들려는 사용자의 유형에 대 한 디렉터리 역할을 조정 합니다.

[이 문서의](../active-directory/fundamentals/add-users-azure-active-directory.md) 단계를 사용 하 여 Azure AD 테넌트에 대해 두 명 이상의 사용자를 만듭니다. 계정 유형을 만들려면 **디렉터리 역할** 을 변경 해야 합니다.

* 전역 관리자
* 사용자

## <a name="enable-authentication"></a>3. VPN 게이트웨이에서 Azure AD 인증을 사용 하도록 설정

1. 인증에 사용할 디렉터리의 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열 됩니다.

    ![디렉터리 ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. 디렉터리 ID를 복사 합니다.

3. **전역 관리자** 역할이 할당 된 사용자로 Azure Portal에 로그인 합니다.

4. 다음으로, 관리자 동의를 제공 합니다. 브라우저의 주소 표시줄에서 배포 위치와 관련 된 URL을 복사 하 여 붙여 넣습니다.

    공용

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud 독일

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 메시지가 표시 되 면 **전역 관리자** 계정을 선택 합니다.

    ![디렉터리 ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. 메시지가 표시 되 면 **동의** 를 선택 합니다.

    ![수락](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Azure AD의 **엔터프라이즈 응용 프로그램**에는 나열 된 **azure VPN** 이 표시 됩니다.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Azure에 대 한 [지점 및 사이트 간 연결에 대 한 AZURE ad 인증 구성](virtual-wan-point-to-site-azure-ad.md) 의 단계에 따라 사용자 VPN에 대 한 azure ad 인증을 구성 하 고 가상 허브에 할당 합니다.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결 하려면 VPN 클라이언트 프로필을 만들어 구성 하 고 가상 허브에 연결 해야 합니다. [Azure에 대 한 지점 및 사이트 간 연결에 대 한 AZURE AD 인증 구성을](virtual-wan-point-to-site-azure-ad.md)참조 하세요.
