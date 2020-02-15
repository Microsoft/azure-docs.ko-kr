---
title: 'VPN Gateway: OpenVPN 프로토콜 P2S 연결에 대 한 VPN 클라이언트: Azure AD 인증'
description: Azure AD 인증을 사용 하 여 VNet에 연결 하는 데 P2S VPN을 사용할 수 있습니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: alzam
ms.openlocfilehash: 4b9678f72dd69db24b105d4b1d708928e29a09ba
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134508"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>P2S OpenVPN 프로토콜 연결용 VPN 클라이언트 구성: Azure AD 인증

이 문서에서는 지점 및 사이트 간 VPN 및 Azure Active Directory 인증을 사용 하 여 가상 네트워크에 연결 하도록 VPN 클라이언트를 구성 하는 방법을 설명 합니다. Azure AD를 사용 하 여 연결 하 고 인증 하려면 먼저 Azure AD 테 넌 트를 구성 해야 합니다. 자세한 내용은 [AZURE AD 테 넌 트 구성](openvpn-azure-ad-tenant.md)을 참조 하세요.

> [!NOTE]
> Azure AD 인증은 OpenVPN® 프로토콜 연결에만 지원됩니다.
>

## <a name="profile"></a>클라이언트 프로필 작업

연결 하려면 Azure VPN 클라이언트를 다운로드 하 고 VNet에 연결 하려는 모든 컴퓨터에서 VPN 클라이언트 프로필을 구성 해야 합니다. 컴퓨터에서 클라이언트 프로필을 만들어 내보낸 다음 추가 컴퓨터에 가져올 수 있습니다.

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

이 [링크](https://go.microsoft.com/fwlink/?linkid=2117554) 를 사용 하 여 Azure VPN 클라이언트를 다운로드 합니다.

### <a name="cert"></a>인증서 기반 클라이언트 프로필을 만들려면

인증서 기반 프로필을 사용 하는 경우 적절 한 인증서가 클라이언트 컴퓨터에 설치 되어 있는지 확인 합니다. 인증서에 대 한 자세한 내용은 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조 하세요.

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>RADIUS 클라이언트 프로필을 만들려면

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> P2S VPN 클라이언트 프로필에서 서버 암호를 내보낼 수 있습니다.  클라이언트 프로필을 내보내는 방법에 대 한 지침은 [여기](about-vpn-profile-download.md)에서 찾을 수 있습니다.
>

### <a name="export"></a>클라이언트 프로필을 내보내고 배포 하려면

작업 프로필을 사용 하 고 다른 사용자에 게 배포 해야 하는 경우 다음 단계를 사용 하 여 내보낼 수 있습니다.

1. 내보내려는 VPN 클라이언트 프로필을 강조 표시 하 고 **...** 를 선택한 다음 **내보내기**를 선택 합니다.

    ![export](./media/openvpn-azure-ad-client/export/export1.jpg)

2. 이 프로필을 저장 하려는 위치를 선택 하 고 파일 이름을 그대로 두고 **저장** 을 선택 하 여 xml 파일을 저장 합니다.

    ![export](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기**를 선택합니다.

    ![가져오기](./media/openvpn-azure-ad-client/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기**를 선택합니다.

    ![가져오기](./media/openvpn-azure-ad-client/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장**을 선택합니다.

    ![가져오기](./media/openvpn-azure-ad-client/import/import3.jpg)

4. **연결**을 선택하여 VPN에 연결합니다.

    ![가져오기](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨**으로 표시됩니다.

    ![가져오기](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제할 클라이언트 프로필 옆에 있는 줄임표를 선택 합니다. 그런 다음, **제거**를 선택합니다.

    ![삭제](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. **제거**를 선택하여 삭제합니다.

    ![삭제](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>연결 만들기

1. 페이지에서 **+** 를 선택 하 고 **+ 추가**를 선택 합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 연결 정보를 입력 합니다. 값이 확실 하지 않은 경우 관리자에 게 문의 하십시오. 값을 입력 한 후 **저장**을 선택 합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create2.jpg)

3. **연결**을 선택하여 VPN에 연결합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 적절 한 자격 증명을 선택한 다음 **계속**을 선택 합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 성공적으로 연결 되 면 아이콘은 녹색으로 바뀌고 **연결 된**것을 말합니다.

    ![연결](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>자동으로 연결 하려면

이러한 단계를 통해 연결을 자동으로 on으로 연결 하도록 구성할 수 있습니다.

1. VPN 클라이언트의 홈 페이지에서 **Vpn 설정**을 선택 합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. 앱 전환 대화 상자에서 **예** 를 선택 합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 설정 하려는 연결이 아직 연결 되지 않았는지 확인 한 다음 프로필을 강조 표시 하 고 **자동으로 연결** 확인란을 선택 합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. **연결** 을 선택 하 여 VPN 연결을 시작 합니다.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>연결 문제 진단

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단 하려는 VPN 연결 옆 **에 있는 ...** 를 선택 하 여 메뉴를 표시 합니다. 그런 다음, **진단**을 선택합니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행**을 선택합니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![진단](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>VPN 클라이언트에 DNS 접미사를 추가 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 **dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes >** 태그를 추가할 수 있습니다.\<

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>VPN 클라이언트에 사용자 지정 경로를 추가 어떻게 할까요??

다운로드 한 프로필 XML 파일을 수정 하 고 **\<경로 >\<includeroutes >\<** >\<mask > \<>\<>\<>\<> 태그를 추가할 수 있습니다.

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [AZURE AD 인증을 사용 하는 P2S OPEN VPN 연결에 대 한 Azure Active Directory 테 넌 트 만들기](openvpn-azure-ad-tenant.md)를 참조 하세요.
