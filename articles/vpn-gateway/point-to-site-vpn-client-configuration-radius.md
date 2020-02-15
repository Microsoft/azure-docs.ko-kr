---
title: 'Azure VPN Gateway: VPN 클라이언트 구성 파일 만들기 & 설치-P2S RADIUS 연결'
description: RADIUS 인증을 사용하는 Windows, Mac OS X 및 Linux VPN 클라이언트 구성 파일을 만듭니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 6e1b162d17e2369b579fc1210c57e0409b0546bc
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148473"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>P2S RADIUS 인증용 VPN 클라이언트 구성 파일 만들기 및 설치

P2S(지점 및 사이트 간) 연결을 통해 가상 네트워크에 연결하려면 연결할 클라이언트 디바이스를 구성해야 합니다. Windows, Mac OS X 및 Linux 클라이언트 디바이스에서 P2S VPN 연결을 만들 수 있습니다. 

RADIUS 인증을 사용할 경우 사용자 이름/암호 인증, 인증서 인증 및 기타 인증 유형과 같은 여러 인증 옵션이 있습니다. VPN 클라이언트 구성은 각 인증 유형마다 다릅니다. VPN 클라이언트를 구성하려면 필요한 설정을 포함하는 클라이언트 구성 파일을 사용합니다. 이 아티클에서는 사용하려는 RADIUS 인증 유형에 대한 VPN 클라이언트 구성을 만들고 설치하는 데 도움이 됩니다.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

P2S RADIUS 인증에 대한 구성 워크플로는 다음과 같습니다.

1. [P2S 연결을 위한 Azure VPN 게이트웨이를 설정합니다](point-to-site-how-to-radius-ps.md).
2. [인증을 위한 RADIUS 서버를 설정합니다](point-to-site-how-to-radius-ps.md#radius). 
3. **선택한 인증 옵션에 대한 VPN 클라이언트 구성을 가져오고 이 구성을 사용하여 VPN 클라이언트를 설정합니다**(이 문서).
4. [P2S 구성을 완료하고 연결합니다](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>VPN 프로토콜 유형 또는 인증 유형 등 VPN 클라이언트 구성 프로필을 생성한 후 지점 및 사이트 간 구성을 변경한 경우, 새 VPN 클라이언트 구성을 생성하고 사용자 디바이스에 설치해야 합니다.
>
>

이 아티클의 섹션을 사용하려면 먼저 사용자 이름/암호, 인증서 또는 다른 인증 유형 중에서 사용하려는 인증 유형을 결정하세요. 각 섹션에는 Windows, Mac OS X, Linux에 대한 단계가 있습니다(현재는 제한된 단계 제공).


## <a name="adeap"></a>사용자 이름/암호 인증

Active Directory를 사용하거나 Active Directory를 사용하지 않도록 사용자 이름/암호 인증을 구성할 수 있습니다. 각 시나리오에서 모든 연결 사용자는 RADIUS를 통해 인증할 수 있는 사용자 이름/암호 자격 증명을 가지고 있어야 합니다.

사용자 이름/암호 인증을 구성할 때 EAP-MSCHAPv2 사용자 이름/암호 인증 프로토콜용 구성만 만들 수 있습니다. 명령에서 `-AuthenticationMethod`는 `EapMSChapv2`입니다.

### <a name="usernamefiles"></a>1. VPN 클라이언트 구성 파일 생성

Azure Portal 또는 Azure PowerShell를 사용 하 여 VPN 클라이언트 구성 파일을 생성할 수 있습니다.

#### <a name="azure-portal"></a>Azure 포털

1. 가상 네트워크 게이트웨이로 이동 합니다.
2. **지점 및 사이트 간 구성을**클릭 합니다.
3. **VPN 클라이언트 다운로드**를 클릭 합니다.
4. 클라이언트를 선택 하 고 요청 된 정보를 입력 합니다.
5. **다운로드** 를 클릭 하 여 .zip 파일을 생성 합니다.
6. .Zip 파일은 일반적으로 다운로드 폴더에 다운로드 됩니다.

#### <a name="azure-powershell"></a>Azure PowerShell

사용자 이름/암호 인증에 사용할 VPN 클라이언트 구성 파일을 생성합니다. 다음 명령을 사용하여 VPN 클라이언트 구성 파일을 생성할 수 있습니다.

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
명령을 실행하면 링크가 반환됩니다. 링크를 복사하여 웹 브라우저에 붙여넣고 **VpnClientConfiguration.zip**을 다운로드합니다. 파일의 압축을 풀어 다음 폴더를 확인합니다. 
 
* **WindowsAmd64** 및 **WindowsX86**: 이 폴더에는 Windows 64비트 및 32비트 설치 관리자 패키지가 각각 포함되어 있습니다. 
* **Generic**: 이 폴더는 자체 VPN 클라이언트 구성을 만드는 데 사용하는 일반 정보를 포함합니다. 사용자 이름/암호 인증 구성에는 이 폴더가 필요하지 않습니다.
* **Mac**: 가상 네트워크 게이트웨이를 만들 때 IKEv2를 구성한 경우, **mobileconfig** 파일을 포함하는 **Mac**이라는 폴더가 보입니다. 이 파일을 사용하여 Mac 클라이언트를 구성합니다.

클라이언트 구성 파일을 이미 만든 경우 `Get-AzVpnClientConfiguration` cmdlet을 사용하여 검색할 수 있습니다. 단, VPN 프로토콜 유형 또는 인증 유형 등 P2S VPN 구성을 변경하면 구성이 자동으로 업데이트되지 않습니다.  `New-AzVpnClientConfiguration` cmdlet을 실행하여 새 구성 다운로드를 만들어야 합니다.

이전에 생성된 클라이언트 구성 파일을 검색하려면 다음 명령을 사용합니다.

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a>2. VPN 클라이언트 구성

다음 VPN 클라이언트를 구성할 수 있습니다.

* [Windows](#adwincli)
* [Mac(OS X)](#admaccli)
* [strongSwan을 사용하는 Linux](#adlinuxcli)
 
#### <a name="adwincli"></a>Windows VPN 클라이언트 설정

클라이언트의 아키텍처와 일치하는 버전이면 각 Windows 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 [FAQ](vpn-gateway-vpn-faq.md#P2S)를 참조하세요.

다음 단계에 따라 인증서 인증을 위한 Windows VPN 기본 클라이언트를 구성합니다.

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 **VpnClientSetupAmd64** 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 **VpnClientSetupX86** 설치 관리자 패키지를 선택합니다. 
2. 패키지를 설치하려면 두 번 클릭합니다. SmartScreen 팝업이 표시되면 **자세한 정보** > **실행**을 선택합니다.
3. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 선택합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다. 

#### <a name="admaccli"></a>Mac(OS X) VPN 클라이언트 설정

1. **VpnClientSetup mobileconfig** 파일을 선택하고 각 사용자에게 보냅니다. 이메일 또는 다른 방법을 사용할 수 있습니다.

2. Mac에서 **mobileconfig** 파일을 찾습니다.

   ![mobileconfig 파일의 위치](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. 선택적 단계 - 사용자 지정 DNS를 지정하려면 다음 줄을 **mobileconfig** 파일에 추가합니다.

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        <array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. 프로필을 두 번 클릭하여 설치하고 **계속**을 선택합니다. 프로필 이름은 가상 네트워크의 이름과 같습니다.

   ![설치 메시지](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. **계속**을 선택하여 프로필의 받는 사람을 신뢰하고 설치를 계속합니다.

   ![확인 메시지](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. 프로필을 설치하는 동안 VPN 인증을 위한 사용자 이름 및 암호를 지정하는 옵션이 제공됩니다. 이 정보를 입력하는 것은 필수가 아닙니다. 입력하는 경우, 정보가 저장되고 연결을 시작할 때 자동으로 사용됩니다. **설치** 를 선택 하 여 계속 합니다.

   ![VPN의 사용자 이름 및 암호 상자](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. 컴퓨터에 프로필을 설치하는 데 필요한 권한에 대한 사용자 이름 및 암호를 입력합니다. **확인**을 선택합니다.

   ![프로필 설치를 위한 사용자 이름과 암호 상자](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. 프로필이 설치되면 **프로필** 대화 상자에서 볼 수 있습니다. 이 대화 상자는 나중에 **시스템 기본 설정**에서 열 수도 있습니다.

   ![“프로필” 대화 상자](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. VPN 연결에 액세스하려면 **시스템 기본 설정**에서 **네트워크** 대화 상자를 엽니다.

   ![시스템 기본 설정의 아이콘](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. VPN 연결은 **IkeV2-VPN**로 표시됩니다. **mobileconfig** 파일을 업데이트하여 이름을 변경할 수 있습니다.

    ![VPN 연결에 대한 세부 정보](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. **인증 설정**을 선택합니다. 목록에서 **사용자 이름**을 선택하고 자격 증명을 입력합니다. 이전에 자격 증명을 입력한 경우 **사용자 이름**이 목록에서 자동으로 선택되며 사용자 이름 및 암호가 미리 채워집니다. **확인**을 선택하여 설정을 저장합니다.

    ![인증 설정](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. **네트워크** 대화 상자로 돌아가서 **적용**을 선택하여 변경 내용을 저장합니다. 연결을 시작하려면 **연결**을 선택합니다.

#### <a name="adlinuxcli"></a>strongSwan을 통해 Linux VPN 클라이언트 설정

다음 지침은 Ubuntu 17.0.4에서 strongSwan 5.5.1을 통해 작성되었습니다. 실제 화면은 Linux와 strongSwan의 버전에 따라 다를 수 있습니다.

1. 이 예의 명령을 실행하여 **터미널**을 열고 **strongSwan**과 네트워크 관리자를 설치합니다. `libcharon-extra-plugins`와 관련된 오류가 표시되면 `strongswan-plugin-eap-mschapv2`로 바꿉니다.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. **네트워크 관리자** 아이콘(위쪽 화살표/아래쪽 화살표)을 선택하고 **연결 편집**을 선택합니다.

   ![네트워크 관리자에서 "연결 편집"선택](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. **추가** 단추를 선택하여 새 연결을 만듭니다.

   ![연결에 대한 "추가" 버튼](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. 드롭다운 메뉴에서 **IPsec/IKEv2(strongswan)** 를 선택한 후 **만들기**를 선택합니다. 이 단계에서 연결의 이름을 바꿀 수 있습니다.

   ![연결 형식 선택](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. 다운로드한 클라이언트 구성 파일의 **일반** 폴더에서 **VpnSettings.xml** 파일을 엽니다. `VpnServer`라는 태그를 찾아서 `azuregateway`로 시작하여 `.cloudapp.net`으로 끝나는 이름을 복사합니다.

   ![VpnSettings.xml 파일의 내용](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. **게이트웨이** 섹션에서 새 VPN 연결의 **주소** 필드에 이 이름을 붙여넣습니다. 다음으로, **인증서** 필드 끝에 있는 폴더 아이콘을 클릭하고 **Generic** 폴더로 이동하여 **VpnServerRoot** 파일을 선택합니다.
7. 연결의 **클라이언트** 섹션에서 **인증**에 **EAP**를 선택하고 사용자 이름과 암호를 입력합니다. 이 정보를 저장하려면 오른쪽에 있는 자물쇠 아이콘을 선택해야 할 수도 있습니다. 그런 다음 **저장**을 선택합니다.

   ![연결 설정 편집](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. **네트워크 관리자** 아이콘(위쪽 화살표/아래쪽 화살표)을 선택하고 **VPN 연결**을 마우스로 가리킵니다. 만든 VPN 연결이 표시됩니다. 연결을 시작하려면 선택합니다.

   ![네트워크 관리자의 "VPN Radius" 연결](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>인증서 인증
 
EAP-TLS 프로토콜을 사용하는 RADIUS 인증서 인증을 위해 VPN 클라이언트 구성 파일을 만들 수 있습니다. 일반적으로 VPN에 대해 사용자를 인증할 때는 엔터프라이즈에서 발급한 인증서를 사용합니다. 연결하는 모든 사용자의 디바이스에 인증서가 설치되어 있고 RADIUS 서버가 인증서의 유효성을 검사할 수 있는지 확인합니다.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

명령에서 `-AuthenticationMethod`는 `EapTls`입니다. 인증서를 인증하는 동안 클라이언트가 인증서의 유효성을 검사하여 RADIUS 서버의 유효성을 검사합니다. `-RadiusRootCert`는 RADIUS 서버의 유효성을 검사하는 데 사용하는 루트 인증서를 포함하는 .cer 파일입니다.

각 VPN 클라이언트 디바이스는 설치된 클라이언트 인증서를 요구합니다. Windows 디바이스가 클라이언트 인증서 여러 개를 포함하는 경우도 있습니다. 인증하는 동안 이 때문에 모든 인증서를 나열하는 팝업 대화 상자가 표시될 수 있습니다. 이때 사용자는 사용할 인증서를 선택해야 합니다. 클라이언트 인증서가 연결해야 하는 루트 인증서를 지정하여 올바른 인증서를 필터링할 수 있습니다. 

`-ClientRootCert`는 루트 인증서를 포함하는 .cer 파일입니다. 이는 선택적 매개 변수입니다. 연결하려는 디바이스에 클라이언트 인증서가 하나만 있으면 이 매개 변수를 지정할 필요가 없습니다.

### <a name="certfiles"></a>1. VPN 클라이언트 구성 파일 생성

인증서 인증에 사용할 VPN 클라이언트 구성 파일을 생성합니다. 다음 명령을 사용하여 VPN 클라이언트 구성 파일을 생성할 수 있습니다.
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

명령을 실행하면 링크가 반환됩니다. 링크를 복사하여 웹 브라우저에 붙여넣고 VpnClientConfiguration.zip을 다운로드합니다. 파일의 압축을 풀어 다음 폴더를 확인합니다.

* **WindowsAmd64** 및 **WindowsX86**: 이 폴더에는 Windows 64비트 및 32비트 설치 관리자 패키지가 각각 포함되어 있습니다. 
* **GenericDevice**: 이 폴더에는 자체 VPN 클라이언트 구성을 만드는 데 사용되는 일반 정보가 포함되어 있습니다.

클라이언트 구성 파일을 이미 만든 경우 `Get-AzVpnClientConfiguration` cmdlet을 사용하여 검색할 수 있습니다. 단, VPN 프로토콜 유형 또는 인증 유형 등 P2S VPN 구성을 변경하면 구성이 자동으로 업데이트되지 않습니다.  `New-AzVpnClientConfiguration` cmdlet을 실행하여 새 구성 다운로드를 만들어야 합니다.

이전에 생성된 클라이언트 구성 파일을 검색하려면 다음 명령을 사용합니다.

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a>2. VPN 클라이언트 구성

다음 VPN 클라이언트를 구성할 수 있습니다.

* [Windows](#certwincli)
* [Mac(OS X)](#certmaccli)
* Linux(지원됨, 아직 아티클 단계 없음)

#### <a name="certwincli"></a>Windows VPN 클라이언트 설정

1. 구성 패키지를 선택하고 클라이언트 디바이스에 설치합니다. 64비트 프로세서 아키텍처의 경우 **VpnClientSetupAmd64** 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 **VpnClientSetupX86** 설치 관리자 패키지를 선택합니다. SmartScreen 팝업이 표시되면 **자세한 정보** > **실행**을 선택합니다. 다른 클라이언트 컴퓨터에 설치하기 위해 패키지를 저장할 수도 있습니다.
2. 인증을 위해 각 클라이언트에 클라이언트 인증서가 필요합니다. 클라이언트 인증서를 설치합니다. 클라이언트 인증서에 대한 자세한 내용은 [지점 및 사이트 간 연결을 위한 클라이언트 인증서](vpn-gateway-certificates-point-to-site.md)를 참조하세요. 생성된 인증서를 설치하려면 [Windows 클라이언트에 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.
3. 클라이언트 컴퓨터에서 **네트워크 설정**으로 이동하고 **VPN**을 선택합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.

#### <a name="certmaccli"></a>Mac(OS X) VPN 클라이언트 설정

Azure 가상 네트워크에 연결하는 모든 Mac 디바이스에 별도의 프로파일을 만들어야 합니다. 이러한 디바이스를 프로필에 지정하려면 인증을 위한 사용자 인증서가 필요하기 때문입니다. **Generic** 폴더에는 프로필을 만드는 데 필요한 모든 정보가 들어 있습니다.

* **VpnSettings.xml**은 서버 주소 및 터널 유형 같은 중요한 설정을 포함합니다.
* **VpnServerRoot.cer**은 P2S 연결 설정 중에 VPN Gateway 유효성을 검사하는 데 필요한 루트 인증서를 포함합니다.
* **RadiusServerRoot.cer**은 인증하는 동안 RADIUS 서버 유효성을 검사하는 데 필요한 루트 인증서를 포함합니다.

다음 단계에 따라 인증서 인증을 위해 Mac에서 기본 VPN 클라이언트를 구성합니다.

1. **VpnServerRoot** 및 **RadiusServerRoot** 루트 인증서를 Mac으로 가져옵니다. 각 파일을 Mac에 복사하고 두 번 클릭한 다음, **추가**를 선택합니다.

   ![VpnServerRoot 인증서 추가](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![RadiusServerRoot 인증서 추가](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. 인증을 위해 각 클라이언트에 클라이언트 인증서가 필요합니다. 클라이언트 디바이스에 클라이언트 인증서를 설치합니다.
3. **네트워크 기본 설정**에서 **네트워크** 대화 상자를 엽니다. **+** 를 선택하여 Azure 가상 네트워크에 P2S 연결을 위해 새 VPN 클라이언트 연결 프로필을 만듭니다.

   **인터페이스** 값은 **VPN**이고, **VPN 유형** 값은 **IKEv2**입니다. **서비스 이름** 상자에 프로필 이름을 지정한 다음, **만들기**를 선택하여 VPN 클라이언트 연결 프로필을 만듭니다.

   ![인터페이스 및 서비스 이름 정보](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. **Generic** 폴더의 **VpnSettings.xml** 파일에서 **VpnServer** 태그 값을 복사합니다. 이 값을 프로필의 **서버 주소** 및 **원격 ID** 상자에 붙여넣습니다. **로컬 ID** 상자는 비워둡니다.

   ![서버 정보](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. **인증 설정**을 선택하고 **인증서**를 선택합니다. 

   ![인증 설정](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. **선택**을 클릭하여 인증에 사용할 인증서를 선택합니다.

   ![인증에 사용할 인증서 선택](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **ID 선택**은 선택할 수 있는 인증서의 목록을 표시합니다. 적절한 인증서를 선택한 다음, **계속**을 선택합니다.

   !["ID 선택" 목록](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. **로컬 ID** 상자에 인증서 이름(6단계에서 사용한 이름)을 지정합니다. 이 예에서는 **ikev2Client.com**입니다. 그런 다음, **적용** 단추를 선택하여 변경 내용을 저장합니다.

   !["로컬 ID" 상자](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. **네트워크** 대화 상자에서 **적용**을 선택하여 변경 내용을 모두 저장합니다. 그런 다음, **연결**을 선택하여 Azure 가상 네트워크에 대한 P2S 연결을 시작합니다.

## <a name="otherauth"></a>다른 인증 유형 또는 프로토콜 작업

다른 인증 유형(예: OTP)를 사용하거나 다른 인증 프로토콜을 사용하려면(예: EAP-MSCHAPv2 대신 PEAP-MSCHAPv2) 자체 VPN 클라이언트 구성 프로필을 만들어야 합니다. 프로필을 만들려면 가상 네트워크 게이트웨이 IP 주소, 터널 유형 및 분할 터널 경로 등과 같은 정보가 필요합니다. 다음 단계를 사용하여 이 정보를 얻을 수 있습니다.

1. `Get-AzVpnClientConfiguration` cmdlet을 사용하여 EapMSChapv2에 대한 VPN 클라이언트 구성을 생성합니다.

2. VpnClientConfiguration.zip 파일의 압축을 풀고 **GenericDevice** 폴더를 찾습니다. 64비트 및 32비트 아키텍처에 대한 Windows Installer를 포함하는 폴더를 무시합니다.
 
3. **GenericDevice** 폴더는 **VpnSettings**라는 XML 파일을 포함합니다. 이 파일은 모든 필요한 정보를 포함합니다.

   * **VpnServer**: Azure VPN 게이트웨이의 FQDN입니다. 이는 클라이언트가 연결하는 대상 주소입니다.
   * **VpnType**: 연결하는 데 사용하는 터널 유형입니다.
   * **Routes**: P2S 터널을 통해 Azure 가상 네트워크에 바인딩된 트래픽만 전송되도록 프로필에 구성해야 하는 경로입니다.
   
   **GenericDevice** 폴더에는 **VpnServerRoot**라는 .cer 파일도 있습니다. 이 파일은 P2S 연결 설정 중에 Azure VPN 게이트웨이 유효성을 검사하는 데 필요한 루트 인증서를 포함합니다. Azure 가상 네트워크에 연결하는 모든 디바이스에 인증서를 설치합니다.

## <a name="next-steps"></a>다음 단계

P2S 구성을 완료하려면 [P2S 구성 완료](point-to-site-how-to-radius-ps.md) 문서로 돌아갑니다.

P2S 문제 해결 정보는 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.
