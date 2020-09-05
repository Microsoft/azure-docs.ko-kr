---
title: FortiGate 배포 가이드 | Microsoft Docs
description: Azure Active Directory와 FortiGate SSL VPN 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657162"
---
# <a name="fortigate-deployment-guide"></a>FortiGate 배포 가이드

## <a name="contents"></a>콘텐츠

- FortiGate 라이선스 교환
- 펌웨어 다운로드
- FortiGate VM 배포
   - 고정 공용 IP 주소 설정 및 정규화된 도메인 이름 할당
   - TCP 포트에 대한 새 인바운드 네트워크 보안 그룹 규칙 만들기
- FortiGate용 사용자 지정 Azure 앱 만들기
- 그룹 일치 준비
   - 사용자 그룹 만들기
- FortiGate VM 구성
   - 라이선스 설치
   - 펌웨어 업데이트
   - 관리 포트를 TCP로 변경
   - Azure Active Directory SAML 서명 인증서 업로드
   - 사용자 지정 SSL 인증서 업로드 및 구성
   - 명령줄 구성 수행
   - VPN 포털 및 방화벽 정책 만들기
- Azure를 사용하여 로그인 테스트

## <a name="redeeming-the-fortigate-license"></a>FortiGate 라이선스 교환

Fortinet FortiGate 차세대 방화벽 제품은 Azure IaaS에서 가상 머신으로 사용할 수 있습니다. 이 가상 머신에는 다음 두 가지 라이선스 모드가 있습니다.

- PAYG(종량제)
- BYOL(사용자 라이선스 필요)

Fortinet과 협력하여 SHA(Secure Hybrid Access) 지침을 제공하는 동안 Fortinet에서 Azure AD Get to Production SHA 팀의 멤버에게 라이선스를 제공할 수 있습니다. 라이선스가 제공되지 않은 경우 PAYG 배포도 작동합니다.

라이선스가 발급된 경우 Fortinet에서 온라인으로 교환해야 하는 등록 코드를 제공합니다.

![Fortigate SSL VPN](registration-code.png)

1. https://support.fortinet.com/ 에 등록합니다.
2. 등록되면 https://support.fortinet.com/ 에서 로그인합니다.
3. **자산** - > **등록/활성화**로 차례로 이동합니다.
4. Fortinet에서 제공하는 등록 코드를 입력합니다.
5. 등록 코드를 지정하고, **비정부 사용자가 제품을 사용합니다.** 를 선택하고, **다음**을 클릭합니다.
6. 제품 설명(예: FortiGate)을 입력하고, Fortinet 파트너를 **기타** - > **Microsoft**로 차례로 설정하고, **다음**을 클릭합니다.
7. **Fortinet 제품 등록 계약**을 수락하고, **다음**을 클릭합니다.
8. **사용 약관**에 동의하고, **확인**을 클릭합니다.
9. **라이선스 파일 다운로드**를 클릭하고, 나중에 사용할 수 있도록 해당 라이선스를 저장합니다.


## <a name="download-firmware"></a>펌웨어 다운로드

이 문서를 작성할 당시 Fortinet FortiGate Azure VM은 SAML 인증에 필요한 펌웨어 버전과 함께 제공되지 않았습니다. Fortinet에서 최신 버전을 가져와야 합니다.

1. https://support.fortinet.com/ 에서 로그인합니다.
2. **다운로드** - > **펌웨어 이미지**로 차례로 이동합니다.
3. **릴리스 정보**의 오른쪽에 있는 **다운로드**를 클릭합니다.
4. **v6**을 클릭합니다.
5. **6**을 클릭합니다.
6. **6.4**를 클릭합니다.
7. 동일한 행에서 **HTTPS** 링크를 클릭하여 **FGT_VM64_AZURE-v6-build1637-FORTINET.out**을 다운로드합니다.
8. 나중에 사용할 수 있도록 해당 파일을 저장합니다.


## <a name="deploy-the-fortigate-vm"></a>FortiGate VM 배포

1. https://portal.azure.com 으로 이동하여 FortiGate Virtual Machine을 배포하려는 구독에 로그인합니다.
2. 새 리소스 그룹을 만들거나 FortiGate Virtual Machine을 배포하려는 리소스 그룹을 엽니다.
3. **추가**를 클릭합니다.
4. **Marketplace 검색** 대화 상자에서 "Forti"를 입력하고, **Fortinet FortiGate** **차세대 방화벽**을 선택합니다.
5. 소프트웨어 계획(라이선스가 있는 경우 BYOL, 그렇지 않은 경우 PAYG)을 선택하고, **만들기**를 클릭합니다.
6. VM 구성을 채웁니다.

    ![Fortigate SSL VPN](virtual-machine.png)

7. [인증 유형]을 **암호**로 설정하고, VM에 대한 관리 자격 증명을 제공합니다.
8. **검토 + 만들기** 클릭
9. **만들기**를 클릭합니다.
10. VM 배포가 완료될 때까지 기다립니다.


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>고정 공용 IP 주소 설정 및 정규화된 도메인 이름 할당

일관된 사용자 환경을 위해 FortiGate VM에 할당된 공용 IP 주소를 정적으로 할당하도록 설정하는 것이 좋습니다. 또한 정규화된 도메인 이름에 매핑하는 것도 동일한 이유로 유용합니다.

_고정 공용 IP 주소 설정_

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. **개요** 화면에서 공용 IP 주소를 클릭합니다.

    ![Fortigate SSL VPN](public-ip-address.png)

3. **고정**, **저장**을 차례로 클릭합니다.

_정규화된 도메인 이름 할당_

FortiGate VM이 배포되는 환경에 대해 공개적으로 라우팅할 수 있는 도메인 이름을 소유하고 있는 경우, 위에서 정적으로 할당된 공용 IP 주소에 매핑되는 VM에 대한 호스트(A) 레코드를 만듭니다.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>TCP 포트에 대한 새 인바운드 네트워크 보안 그룹 규칙 만들기

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **네트워킹**을 클릭합니다. 네트워크 인터페이스가 나열되고, 인바운드 포트 규칙이 표시됩니다.
3. **인바운드 포트 규칙 추가**를 클릭합니다.
4. 8443 TCP에 대한 새 인바운드 포트 규칙을 만듭니다.

    ![Fortigate SSL VPN](port-rule.png)

5. **추가**를 클릭합니다.


## <a name="create-a-custom-azure-app-for-fortigate"></a>FortiGate용 사용자 지정 Azure 앱 만들기

1. https://portal.azure.com 으로 이동하여 FortiGate 로그인에 대한 ID를 제공할 테넌트의 Azure Active Directory 블레이드를 엽니다.
2. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.
3. **새 애플리케이션**을 클릭합니다.
4. **비갤러리 애플리케이션**을 클릭합니다.
5. 이름(예: FortiGate)을 입력하고, **추가**를 클릭합니다.
6. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.
7. 로그인할 수 있는 사용자를 추가하고, **할당**을 클릭합니다.
8. 왼쪽 메뉴에서 **Single Sign-On**을 클릭합니다.
9. **SAML**을 클릭합니다.
10. **기본 SAML 구성** 아래에서 연필 아이콘을 클릭하여 구성을 편집합니다.
11. 구성
    - 식별자(엔터티 ID) - `https://<address>/remote/saml/metadata`
    - 회신 URL(Assertion Consumer Service URL) - `https://<address>/remote/saml/login`
    - 로그아웃 URL - `https://<address>/remote/saml/logout`

    여기서 `address`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

    나중에 사용할 수 있도록 이러한 URL을 각각 기록합니다.

    - 엔터티 ID
    - 회신 URL
    - 로그아웃 URL
12. 페이지 맨 아래에 있는 **저장**
13. [기본 SAML 구성]을 닫습니다.
14. **3 – SAML 서명 인증서** 아래에서 **인증서(Base64)** 를 다운로드하고, 나중에 사용할 수 있도록 저장합니다.
15. **4 – 설정(앱 이름)** 아래에서 Azure 로그인 URL, Azure AD 식별자 및 Azure 로그아웃 URL을 복사하고, 나중에 사용할 수 있도록 저장합니다.
    - Azure 로그인 URL
    - Azure AD 식별자
    - Azure 로그아웃 URL
16. **2 – 사용자 특성 및 클레임** 아래에서 연필 아이콘을 클릭하여 구성을 편집합니다.
17. **새 클레임 추가**를 클릭합니다.
18. [이름]을 **username**으로 설정합니다.
19. [원본 특성]을 **user.userprincipalname**으로 설정합니다.
20. 페이지 맨 아래에 있는 **저장**
21. **그룹 클레임 추가**를 클릭합니다.
22. **모든 그룹**을 선택합니다.
23. **그룹 클레임 이름 사용자 지정**을 클릭합니다.
24. [이름]을 **group**으로 설정합니다.
25. 페이지 맨 아래에 있는 **저장**


## <a name="prepare-for-group-matching"></a>그룹 일치 준비

FortiGate는 로그인 후에 그룹 멤버 자격에 따라 다양한 사용자 포털 환경을 허용합니다. 예를 들어 마케팅 그룹과 재무 그룹에 대해 각각의 서로 다른 환경이 있을 수 있습니다.

다음과 같이 구성합니다.

### <a name="create-groups-for-users"></a>사용자 그룹 만들기

1. https://portal.azure.com 으로 이동하여 FortiGate 로그인에 대한 ID를 제공할 테넌트의 Azure Active Directory 블레이드를 엽니다.
2. **그룹**을 클릭합니다.
3. **새 그룹**을 클릭합니다.
4. 다음과 같은 그룹을 만듭니다.
    - 그룹 유형 = 보안
    - 그룹 이름 = `a meaningful name`
    - 그룹 설명 = `a meaningful description for the group`
    - 멤버 자격 유형 = 할당됨
    - 멤버 = `users for the user experience that will map to this group`
5. 추가 사용자 환경에 대해 3단계와 4단계를 반복합니다.
6. 그룹이 만들어지면 각 그룹을 선택하여 각 그룹에 대한 개체 ID를 기록합니다.
7. 나중에 사용할 수 있도록 이러한 개체 ID 및 그룹 이름을 저장합니다.


## <a name="configure-the-fortigate-vm"></a>FortiGate VM 구성

### <a name="install-the-license"></a>라이선스 설치

1. `https://<address>` 로 이동합니다.

    여기서 `address`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 배포에서 BYOL 모델을 사용하는 경우 라이선스를 업로드하라는 메시지가 표시됩니다. 이전에 만든 라이선스 파일을 선택하여 업로드하고, **확인**을 클릭하고, FortiGate VM을 다시 시작합니다.

    ![Fortigate SSL VPN](license.png)

5. 다시 부팅되면 관리자 자격 증명으로 다시 로그인하여 라이선스의 유효성을 검사합니다.

### <a name="update-firmware"></a>펌웨어 업데이트

1. `https://<address>` 로 이동합니다.

    여기서 `address`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템**을 클릭합니다.
5. [시스템] 아래의 왼쪽 메뉴에서 **펌웨어**를 클릭합니다.
6. [펌웨어 관리] 페이지에서 **브라우저**를 클릭하고, 이전에 다운로드한 펌웨어 파일을 선택합니다.
7. 경고를 무시하고, **백업 구성 및 업그레이드**를 클릭합니다.

    ![Fortigate SSL VPN](backup-configure-upgrade.png)

8. **계속**을 클릭합니다.
9. FortiGate 구성을 .conf 파일로 저장하라는 메시지가 표시되면 **저장**을 클릭합니다.
10. 펌웨어가 업로드되어 적용되고 FortiGate VM이 다시 부팅될 때까지 기다립니다.
11. FortiGate VM이 다시 부팅되면 관리자 자격 증명으로 다시 로그인합니다.
12. 대시보드 설정을 수행하라는 메시지가 표시되면 **나중에**를 클릭합니다.
13. 자습서 비디오가 시작되면 **확인**을 클릭합니다.

### <a name="change-the-management-port-to-tcp"></a>관리 포트를 TCP로 변경

1. `https://<address>` 로 이동합니다.

    여기서 `address`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템**을 클릭합니다.
5. [관리 설정] 아래에서 HTTPS 포트를 **8443**으로 변경합니다.
6. **적용**을 클릭합니다.
7. 변경 내용이 적용되면 브라우저에서 [관리] 페이지를 다시 로드하려고 하지만 실패합니다. 지금부터 관리 페이지 주소는 `https://<address>`가 됩니다.

    ![Fortigate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Azure Active Directory SAML 서명 인증서 업로드

1. `https://<address>` 로 이동합니다.

    여기서 `address`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템**을 클릭합니다.
5. [시스템] 아래에서 **인증서**를 클릭합니다.
6. **가져오기** - > **원격 인증서**를 차례로 클릭합니다.
7. Azure 테넌트의 FortiGate 사용자 지정 앱 배포에서 다운로드한 인증서를 찾아서 선택하고, **확인**을 클릭합니다.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>사용자 지정 SSL 인증서 업로드 및 구성

사용 중인 FQDN을 지원하는 사용자 고유의 SSL 인증서를 사용하여 FortiGate VM을 구성할 수 있습니다. 프라이빗 키와 함께 .PFX 형식으로 패키지된 SSL 인증서에 액세스할 수 있는 경우 해당 인증서를 이 용도로 사용할 수 있습니다.

1. `https://<address>` 로 이동합니다.

    여기서 `address`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템**을 클릭합니다.
5. [시스템] 아래에서 **인증서**를 클릭합니다.
6. **가져오기** - > **로컬 인증서**를 차례로 클릭합니다.
7. **PKCS #12 인증서**를 클릭합니다.
8. SSL 인증서 및 프라이빗 키가 포함된 .PFX 파일로 이동합니다.
9. .PFX 암호를 제공합니다.
10. 인증서에 대한 의미 있는 이름을 입력합니다.
11. **확인**을 클릭합니다.
12. 왼쪽 메뉴에서 **시스템**을 클릭합니다.
13. [시스템] 아래에서 **설정**을 클릭합니다.
14. [관리 설정] 아래에서 HTTPS 서버 인증서 옆의 드롭다운을 펼치고, 위에서 가져온 SSL 인증서를 선택합니다.
15. **적용**을 클릭합니다.
16. 브라우저 창을 닫은 다음, `https://<address>`로 다시 이동합니다.
17. FortiGate 관리자 자격 증명으로 로그인하고, 사용 중인 올바른 SSL 인증서를 확인합니다.


### <a name="perform-command-line-configuration"></a>명령줄 구성 수행

_SAML 인증에 대한 명령줄 구성 수행_

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **직렬 콘솔**을 클릭합니다.
3. [직렬 콘솔]에서 FortiGate VM 관리자 자격 증명으로 로그인합니다.

    다음 단계에서는 앞에서 기록한 URL이 필요합니다. 구체적으로 다음과 같습니다.

    - 엔터티 ID
    - 회신 URL
    - 로그아웃 URL
    - Azure 로그인 URL
    - Azure AD 식별자
    - Azure 로그아웃 URL
4. [직렬 콘솔]에서 다음 명령을 실행합니다.

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Azure 로그아웃 URL에는 ?(물음표) 문자가 포함되어 있습니다. 이 경우 FortiGate 직렬 콘솔에 올바르게 제공되려면 특수한 키 시퀀스가 필요합니다. URL은 일반적으로 다음과 같습니다.

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    이를 [직렬 콘솔]에 제공하려면 다음을 입력하여 계속합니다.

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    그런 다음, CTRL+V를 입력합니다.

    그런 다음, 나머지 URL을 붙여넣어 줄을 완성합니다.

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. 구성을 확인하려면 다음을 실행합니다.

    ```
    show user saml
    ```

_그룹 일치에 대한 명령줄 구성 수행_

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **직렬 콘솔**을 클릭합니다.
3. [직렬 콘솔]에서 FortiGate VM 관리자 자격 증명으로 로그인합니다.
4. [직렬 콘솔]에서 다음 명령을 실행합니다.

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    `group 1 name` 편집에서 이러한 명령을 반복하지만, FortiGate에서 다른 포털 환경을 사용할 추가 그룹 각각에 대해 이를 반복합니다.

_인증 시간 제한에 대한 명령줄 구성 수행_

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **직렬 콘솔**을 클릭합니다.
3. [직렬 콘솔]에서 FortiGate VM 관리자 자격 증명으로 로그인합니다.
4. [직렬 콘솔]에서 다음 명령을 실행합니다.

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN 포털 및 방화벽 정책 만들기

1. `https://<address>` 로 이동합니다.

    여기서 `address`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
3. 왼쪽 메뉴에서 **VPN**을 클릭합니다.
4. [VPN] 아래에서 **SSL-VPN 포털**을 클릭합니다.
5. **새로 만들기**를 클릭합니다.
6. 이름을 제공합니다(일반적으로 사용자 지정 포털 환경을 제공하는 데 사용되는 Azure 그룹과 일치함).
7. [원본 IP 풀] 옆에 있는 더하기 기호( **+** )를 클릭하고, 기본 풀을 선택하고, **닫기**를 클릭합니다.
8. 이 그룹에 대한 환경을 사용자 지정합니다. 테스트를 위해 [포털 메시지] 및 [테마]를 사용자 지정할 수 있습니다. 또한 사용자를 내부 리소스로 안내하는 사용자 지정 책갈피를 만들 수도 있습니다.
9. **확인**을 클릭합니다.
10. 사용자 지정 포털 환경을 사용할 각 Azure 그룹에 대해 5~9단계를 반복합니다.
11. [VPN] 아래에서 **SSL-VPN 설정**을 클릭합니다.
12. [인터페이스에서 수신 대기] 옆에 있는 더하기 기호( **+** )를 클릭합니다.
13. **Port1**을 선택하고, **닫기**를 클릭합니다.


14. 사용자 지정 SSL 인증서가 이전에 설치된 경우 드롭다운 메뉴에서 사용자 지정 SSL 인증서를 사용하도록 서버 인증서를 변경합니다.
15. [인증/포털 매핑] 아래에서 **새로 만들기**를 클릭합니다.
16. 첫 번째 Azure 그룹을 선택하고, 동일한 이름의 포털과 일치시킵니다.
17. **확인**을 클릭합니다.
18. 각 Azure 그룹/포털 쌍에 대해 15~17단계를 반복합니다.
19. [인증/포털 매핑] 아래에서 **다른 모든 사용자/그룹**을 편집합니다.
20. 포털을 **전체 액세스**로 설정합니다.
21. **확인**을 클릭합니다.
22. **적용**을 클릭합니다.
23. [SSL-VPN 설정] 페이지의 위쪽으로 스크롤하여 **SSL-VPN 정책이 없습니다**
     **. 이러한 설정을 사용하여 새 SSL-VPN 정책을 만들려면 여기를 클릭하세요.** 라는 경고를 클릭합니다.
24. 이름을 입력합니다(예: **VPN Grp**).
25. [발신 인터페이스]를 **포트**로 설정합니다.
26. **원본**을 클릭합니다.
27. [주소] 아래에서 **모두**를 선택합니다.
28. [사용자] 아래에서 첫 번째 Azure 그룹을 선택합니다.
29. **닫기**를 클릭합니다.
30. **대상**을 클릭합니다.
31. [주소] 아래에서 이는 일반적으로 내부 네트워크입니다. 테스트를 위해 login.microsoft.com을 선택합니다.
32. **닫기**를 클릭합니다.
33. **서비스**를 클릭합니다.
34. **모두**를 클릭합니다.
35. **닫기**를 클릭합니다.
36. **확인**을 클릭합니다.
37. 왼쪽 메뉴에서 **정책 및 개체**를 클릭합니다.
38. [정책 및 개체] 아래에서 **방화벽 정책**을 클릭합니다.
39. **SSL-VPN 터널 인터페이스(ssl.root) - > 포트**를 차례로 펼칩니다.
40. 마우스 오른쪽 단추로 이전에 만든 VPN 정책(**VPN Grp 1**)을 클릭하고, **복사**를 선택합니다.
41. [VPN 정책] 아래에서 마우스 오른쪽 단추를 클릭하고 **붙여넣기** - > **아래**를 차례로 선택합니다.
42. 새 정책을 편집하여 다른 이름(예: **VPN Grp2**)을 제공합니다. 그룹이 변경되면 다른 Azure 그룹에 적용됩니다.
43. 마우스 오른쪽 단추로 새 정책을 클릭하고, 상태를 **사용**으로 설정합니다.


## <a name="test-sign-in-using-azure"></a>Azure를 사용하여 로그인 테스트

1. 개인 브라우저 세션을 사용하여 `https://<address>`로 이동합니다. 
2. 로그인은 로그인을 위해 Azure Active Directory로 리디렉션되어야 합니다.
3. Azure 테넌트에서 FortiGate 앱에 할당된 사용자에게 자격 증명이 제공되면 적절한 사용자 포털이 표시됩니다.

    ![Fortigate SSL VPN](test-sign-in.png)
