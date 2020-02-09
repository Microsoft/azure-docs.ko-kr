---
title: 'VPN Gateway: 다른 사용자 그룹에 대 한 Azure AD 테넌트: Azure AD 인증'
description: Azure AD 인증을 사용 하 여 VNet에 연결 하는 데 P2S VPN을 사용할 수 있습니다.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985648"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN 프로토콜 연결에 대 한 Azure Active Directory 테넌트 만들기

VNet에 연결 하는 경우 인증서 기반 인증 또는 RADIUS 인증을 사용할 수 있습니다. 그러나 오픈 VPN 프로토콜을 사용 하는 경우 Azure Active Directory 인증을 사용할 수도 있습니다. 다른 사용자가 다른 VPN gateway에 연결할 수 있도록 하려면 AD에 여러 앱을 등록 하 고 다른 VPN 게이트웨이에 연결할 수 있습니다. 이 문서는 P2S OpenVPN 인증을 위해 Azure AD 테넌트를 설정 하 고 여러 사용자 및 그룹에 대해 서로 다른 액세스를 허용 하기 위해 Azure AD에서 여러 앱을 만들고 등록 하는 데 도움이 됩니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

## <a name="tenant"></a>1. Azure AD 테넌트 만들기

[새 테넌트 만들기](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서의 단계를 사용 하 여 Azure AD 테넌트를 만듭니다.

* 조직 이름
* 초기 도메인 이름

예:

   ![새 Azure AD 테넌트](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. Azure AD 테넌트 사용자 만들기

다음으로 두 개의 사용자 계정을 만듭니다. 전역 관리자 계정과 마스터 사용자 계정 하나를 만듭니다. 마스터 사용자 계정은 마스터 포함 계정 (서비스 계정)으로 사용 됩니다. Azure AD 테넌트 사용자 계정을 만들 때 만들려는 사용자의 유형에 대 한 디렉터리 역할을 조정 합니다.

[이 문서의](../active-directory/fundamentals/add-users-azure-active-directory.md) 단계를 사용 하 여 Azure AD 테넌트에 대해 두 명 이상의 사용자를 만듭니다. 계정 유형을 만들려면 **디렉터리 역할** 을 변경 해야 합니다.

* 전역 관리자
* 사용자

## <a name="enable-authentication"></a>3. azure AD 테넌트에서 Azure VPN 클라이언트 등록

1. 인증에 사용할 디렉터리의 디렉터리 ID를 찾습니다. Active Directory 페이지의 속성 섹션에 나열 됩니다.

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![디렉터리 ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 메시지가 표시 되 면 **동의** 를 선택 합니다.

    ![수락](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Azure AD의 **엔터프라이즈 응용 프로그램**에는 **azure VPN** 나열이 표시 됩니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. 다양 한 사용자/그룹에 대 한 추가 응용 프로그램 등록

1. Azure Active Directory에서 **앱 등록** 를 클릭 하 고 **+ 새로 등록** 을 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. **응용 프로그램 등록** 블레이드에서 **이름을** 입력 하 고 원하는 **지원 계정 유형을** 선택한 후 **등록** 을 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 새 앱이 등록 되 면 앱 블레이드에서 **API 노출** 을 클릭 합니다.

4. **+ 범위 추가** 를 클릭 합니다.
5. 기본 **응용 프로그램 ID URI** 를 그대로 두고 **저장 후 계속을** 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. 필수 필드를 입력 하 고 **상태가** **사용으로 설정**되어 있는지 확인 합니다. **범위 추가** 클릭

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. **API 노출** 를 클릭 한 다음 **클라이언트 응용 프로그램 추가**를 클릭 합니다.  **클라이언트 ID**에 대해 클라우드에 따라 다음 값을 입력 합니다.
    -   **41b23e61-6c1e-4545-b367-cd054e0ed4b4** For Azure **Public** 을 입력 합니다.
    -   Azure **정부** 에 **51bb15d4-3a4f-4ebf-9dca-40096fe32426** 입력
    -   Azure **독일** 에 **538ee9e6-310a-468d-afef-ea97365856a9** 입력
    -   Azure **중국 21vianet** 에 대해 **49f817b6-84ae-4cc0-928c-73f27289b3aa** 입력


8. **응용 프로그램 추가** 를 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. **개요** 페이지에서 **응용 프로그램 (클라이언트) ID** 를 복사 합니다. VPN gateway를 구성 하는 데 필요 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 이 섹션 (4)의 단계를 반복 하 여 보안 요구 사항에 필요한 만큼의 응용 프로그램을 만듭니다. 각 응용 프로그램은 VPN gateway에 연결 되며 다른 사용자 집합을 가질 수 있습니다. 하나의 응용 프로그램만 게이트웨이에 연결할 수 있습니다.

## <a name="enable-authentication"></a>5. 응용 프로그램에 사용자 할당

1. Azure AD, **엔터프라이즈 응용 프로그램**에서 새로 등록 된 응용 프로그램을 선택 하 고 **속성**을 클릭 합니다. **사용자 할당 필요 여부** 를 **예**로 설정 합니다. 페이지 맨 아래에 있는 **저장**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 앱 페이지에서 **사용자 및 그룹** 을 클릭 한 다음 **사용자 추가** 를 클릭 합니다.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. **할당 추가**에서 **사용자 및 그룹**을 클릭 합니다. 이 VPN 응용 프로그램에 액세스 하려는 사용자를 선택 합니다. **선택** 클릭

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="site"></a>6. 새 P2S 구성 만들기

P2S 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

1. 환경에 필요한 대로 값을 대체하여 다음 변수를 설정합니다.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 다음 명령을 실행하여 구성을 만듭니다.

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

> [!NOTE]
> 모든 사용자에 게 VPN gateway에 대 한 액세스 권한을 부여 하므로 위의 명령에서 Azure VPN 클라이언트의 응용 프로그램 ID를 사용 하지 마세요. 등록 한 응용 프로그램의 ID를 사용 합니다.

## <a name="hub"></a>7. 허브 할당 편집

1. 가상 WAN 아래의 **허브** 블레이드로 이동합니다.
2. vpn 서버 구성을 연결할 허브를 선택하고, 줄임표(...)를 클릭합니다.

   ![새 사이트](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. **가상 허브 편집**을 클릭합니다.
4. **지점 및 사이트 간 게이트웨이 포함** 확인란을 선택하고 원하는 **게이트웨이 배율 단위**를 선택합니다.

   ![새 사이트](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. VPN 클라이언트에 IP 주소를 할당할 **주소 풀**를 입력합니다.
6. **확인**을 클릭합니다.
7. 작업이 완료될 때까지 최대 30분이 걸릴 수 있습니다.

## <a name="device"></a>8. VPN 프로필 다운로드

VPN 프로필을 사용하여 클라이언트를 구성합니다.

1. 가상 WAN에 대한 페이지에서 **사용자 VPN 구성**을 클릭합니다.
2. 페이지 맨 위에서 **사용자 VPN 구성 다운로드**를 클릭합니다.
3. 파일 만들기가 끝나면 링크를 클릭하여 다운로드할 수 있습니다.
4. 프로필 파일을 사용하여 VPN 클라이언트를 구성합니다.

4. 다운로드 한 zip 파일을 추출 합니다.

5. 압축을 푼 "AzureVPN" 폴더로 이동 합니다.

6. "Azurevpnconfig" 파일의 위치를 적어둡니다. Azurevpnconfig에는 VPN 연결에 대 한 설정이 포함 되어 있으며, Azure VPN 클라이언트 응용 프로그램으로 직접 가져올 수 있습니다. 또한 전자 메일 또는 다른 방법을 통해 연결 해야 하는 모든 사용자에 게이 파일을 배포할 수 있습니다. 성공적으로 연결 하려면 사용자에 게 유효한 Azure AD 자격 증명이 필요 합니다.
## <a name="configure-user-vpn-clients"></a>사용자 VPN 클라이언트 구성

연결하려면 Azure VPN Client(미리 보기)를 다운로드하고, 이전 단계에서 VNet에 연결하려는 모든 컴퓨터에 다운로드한 VPN 클라이언트 프로필을 가져와야 합니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

#### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

이 [링크](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)를 사용하여 Azure VPN Client(미리 보기)를 다운로드합니다.

#### <a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기**를 선택합니다.

    ![수입](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기**를 선택합니다.

    ![수입](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장**을 선택합니다.

    ![수입](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. **연결**을 선택하여 VPN에 연결합니다.

    ![수입](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨**으로 표시됩니다.

    ![수입](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제하려는 클라이언트 프로필 옆의 줄임표(...)를 선택합니다. 그런 다음, **제거**를 선택합니다.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. **제거**를 선택하여 삭제합니다.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>연결 문제 진단

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단하려는 VPN 연결 옆에 있는 줄임표(...)를 선택하여 메뉴를 표시합니다. 그런 다음, **진단**을 선택합니다.

    ![진단](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행**을 선택합니다.

    ![진단](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    ![진단](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![진단](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다. 점 위로 마우스를 가져가면 허브 상태 요약을 볼 수 있습니다.
3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="viewhealth"></a>리소스 상태 보기

1. WAN으로 이동합니다.
2. WAN 페이지의 **지원 및 문제 해결** 섹션에서 **상태**를 클릭하고 리소스를 봅니다.


## <a name="cleanup"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
