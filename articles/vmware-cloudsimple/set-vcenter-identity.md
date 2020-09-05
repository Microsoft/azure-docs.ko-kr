---
title: CloudSimple의 Azure VMware 솔루션-사설 클라우드에서 vCenter id 원본 설정
description: VMware 관리자가 vCenter에 액세스할 수 있도록 Active Directory 인증 하도록 사설 클라우드 vCenter를 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f6f3b10219775adb02d47a91da2573ea99f30ac0
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212253"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Active Directory 사용할 vCenter id 원본 설정

## <a name="about-vmware-vcenter-identity-sources"></a>VMware vCenter id 원본 정보

VMware vCenter는 vCenter에 액세스 하는 사용자를 인증 하는 데 다양 한 id 원본을 지원 합니다.  CloudSimple 사설 클라우드 vCenter를 설정 하 여 VMware 관리자가 vCenter에 액세스할 Active Directory를 인증할 수 있습니다. 설치가 완료 되 면 **cloudowner** 사용자가 id 원본에서 vCenter에 사용자를 추가할 수 있습니다.  

다음과 같은 방법으로 Active Directory 도메인 및 도메인 컨트롤러를 설정할 수 있습니다.

* 온-프레미스에서 실행 되는 도메인 컨트롤러 및 도메인 컨트롤러 Active Directory
* Azure 구독에서 가상 머신으로 Azure에서 실행 되는 도메인 및 도메인 컨트롤러 Active Directory
* 사설 클라우드에서 실행 중인 새 Active Directory 도메인 및 도메인 컨트롤러
* Azure Active Directory 서비스

이 가이드에서는 구독에서 온-프레미스 또는 가상 머신으로 실행 되는 Active Directory 도메인 및 도메인 컨트롤러를 설정 하는 작업에 대해 설명 합니다.  Azure AD를 id 원본으로 사용 하려는 경우 id 원본 설정에 대 한 자세한 지침을 보려면 [CloudSimple 사설 클라우드에서 vCenter 용 id 공급자로 AZURE Ad 사용](azure-ad.md) 을 참조 하세요.

[Id 원본을 추가](#add-an-identity-source-on-vcenter)하기 전에 [vCenter 권한을 임시로 에스컬레이션](escalate-private-cloud-privileges.md)합니다.

> [!CAUTION]
> 새 사용자는 *클라우드-소유자-그룹*, *클라우드-글로벌-* 관리-그룹, 클라우드- *네트워크* -관리자- *그룹, 클라우드*-글로벌- *v m*i-관리 그룹에만 추가 해야 합니다.  *Administrators* 그룹에 추가 된 사용자는 자동으로 제거 됩니다.  서비스 계정만 *관리자* 그룹에 추가 해야 하며, 서비스 계정은 vsphere 웹 UI에 로그인 하는 데 사용 하면 안 됩니다.   


## <a name="identity-source-options"></a>Id 원본 옵션

* [온-프레미스 Active Directory Single Sign-On id 원본으로 추가](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [사설 클라우드에서 새 Active Directory 설정](#set-up-new-active-directory-on-a-private-cloud)
* [Azure에서 Active Directory 설정](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **Active Directory (Windows 통합 인증)은 지원 되지 않습니다.** LDAP 옵션만 Active Directory id 원본으로 지원 됩니다.

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>온-프레미스 Active Directory을 Single Sign-on Id 원본으로 추가

온-프레미스 Active Directory Single Sign-on id 원본으로 설정 하려면 다음이 필요 합니다.

* 온-프레미스 데이터 센터에서 사설 클라우드로의 [사이트 간 VPN 연결](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) .
* VCenter 및 플랫폼 서비스 컨트롤러 (PSC)에 추가 된 온-프레미스 DNS 서버 IP

Active Directory 도메인을 설정할 때 다음 표의 정보를 사용 합니다.

| **옵션** | **설명** |
|------------|-----------------|
| **이름** | Id 원본의 이름입니다. |
| **사용자의 기본 DN** | 사용자의 기본 고유 이름입니다. |
| **도메인 이름** | 도메인의 FQDN (예: example.com)입니다. 이 텍스트 상자에 IP 주소를 제공 하지 마십시오. |
| **도메인 별칭** | 도메인 NetBIOS 이름입니다. SSPI 인증을 사용 하는 경우 Active Directory 도메인의 NetBIOS 이름을 id 원본의 별칭으로 추가 합니다. |
| **그룹의 기본 DN** | 그룹에 대 한 기본 고유 이름입니다. |
| **주 서버 URL** | 도메인에 대 한 주 도메인 컨트롤러 LDAP 서버입니다.<br><br>또는 형식을 사용  `ldap://hostname:port`    `ldaps://hostname:port` 합니다. 포트는 일반적으로 LDAP 연결의 경우 389이 고, LDAPS 연결의 경우 636입니다. Active Directory 다중 도메인 컨트롤러 배포의 경우 포트는 일반적으로 LDAP의 경우 3268, LDAPS의 경우 3269입니다.<br><br> `ldaps://`   주 또는 보조 LDAP URL에서를 사용 하는 경우 ACTIVE DIRECTORY 서버의 LDAPS 끝점에 대 한 신뢰를 설정 하는 인증서가 필요 합니다. |
| **보조 서버 URL** | 장애 조치 (failover)에 사용 되는 보조 도메인 컨트롤러 LDAP 서버의 주소입니다. |
| **인증서 선택** | Active Directory LDAP 서버 또는 OpenLDAP 서버 id 원본에서 LDAPS를 사용 하려는 경우  `ldaps://`   URL 텍스트 상자에를 입력 하면 인증서 선택 단추가 표시 됩니다. 보조 URL은 필요 하지 않습니다. |
| **사용자 이름** | 도메인에서 사용자 및 그룹의 기본 DN에 대 한 읽기 전용 액세스 권한이 있는 사용자의 ID입니다. |
| **암호** | 사용자 이름으로 지정 된 사용자의 암호입니다. |

위의 표에 나와 있는 정보를 사용할 경우 온-프레미스 Active Directory를 vCenter의 Single Sign-on id 원본으로 추가할 수 있습니다.

> [!TIP]
> [VMware 설명서 페이지](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)에서 Single sign-on id 소스에 대 한 자세한 내용을 확인할 수 있습니다.

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>사설 클라우드에서 새 Active Directory 설정

사설 클라우드에서 새 Active Directory 도메인을 설정 하 고 Single Sign-on에 대 한 id 원본으로 사용할 수 있습니다.  Active Directory 도메인은 기존 Active Directory 포리스트에 속할 수도 있고 독립 포리스트로 설정할 수도 있습니다.

### <a name="new-active-directory-forest-and-domain"></a>새 Active Directory 포리스트 및 도메인

새 Active Directory 포리스트와 도메인을 설정 하려면 다음이 필요 합니다.

* 새 Active Directory 포리스트와 도메인의 도메인 컨트롤러로 사용할 Microsoft Windows Server를 실행 하는 하나 이상의 가상 컴퓨터입니다.
* 이름 확인을 위해 DNS 서비스를 실행 하는 하나 이상의 가상 컴퓨터.

자세한 단계는 [새 Windows Server 2012 Active Directory 포리스트 설치를](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) 참조 하세요.

> [!TIP]
> 서비스의 고가용성을 위해 여러 도메인 컨트롤러와 DNS 서버를 설정 하는 것이 좋습니다.

Active Directory 포리스트와 도메인을 설정한 후 새 Active Directory에 대 한 [id 원본을 vCenter에 추가할](#add-an-identity-source-on-vcenter) 수 있습니다.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>기존 Active Directory 포리스트의 새 Active Directory 도메인

기존 Active Directory 포리스트에 새 Active Directory 도메인을 설정 하려면 다음이 필요 합니다.

* Active Directory 포리스트 위치에 대 한 사이트 간 VPN 연결.
* 기존 Active Directory 포리스트의 이름을 확인 하는 DNS 서버입니다.

자세한 단계 [는 새 Windows Server 2012 Active Directory 자식 또는 트리 도메인 설치를](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) 참조 하세요.

Active Directory 도메인을 설정한 후 새 Active Directory의 [vCenter에 id 원본을 추가할](#add-an-identity-source-on-vcenter) 수 있습니다.

## <a name="set-up-active-directory-on-azure"></a>Azure에서 Active Directory 설정

Azure에서 실행 되는 Active Directory 온-프레미스에서 실행 되는 Active Directory와 비슷합니다.  VCenter의 Single Sign-on id 원본으로 Azure에서 실행 되는 Active Directory을 설정 하려면 vCenter 서버와 PSC에 Active Directory 서비스가 실행 되는 Azure Virtual Network에 대 한 네트워크 연결이 있어야 합니다.  Active Directory 서비스가 CloudSimple 사설 클라우드를 실행 하는 Azure 가상 네트워크에서 Express 경로를 [사용 하 여 azure Virtual Network 연결](azure-expressroute-connection.md) 을 사용 하 여이 연결을 설정할 수 있습니다.

네트워크 연결이 설정 된 후 [온-프레미스 Active Directory을 Single Sign-on Id 원본으로 추가](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) 의 단계에 따라 id 원본으로 추가 합니다.  

## <a name="add-an-identity-source-on-vcenter"></a>VCenter에서 id 원본 추가

1. 사설 클라우드에 대 한 [권한을 에스컬레이션](escalate-private-cloud-privileges.md) 합니다.

2. 사설 클라우드의 vCenter에 로그인 합니다.

3. **홈 > 관리**를 선택 합니다.

    ![관리](media/OnPremAD01.png)

4. **Single Sign On > 구성**을 선택 합니다.

    ![Single Sign On](media/OnPremAD02.png)

5. **Id** 원본 탭을 열고를 클릭 **+** 하 여 새 id 원본을 추가 합니다.

    ![Id 원본](media/OnPremAD03.png)

6. **LDAP 서버로 Active Directory** 를 선택 하 고 **다음**을 클릭 합니다.

    ![Active Directory](media/OnPremAD04.png)

7. 사용자 환경에 대 한 id 원본 매개 변수를 지정 하 고 **다음**을 클릭 합니다.

    ![Active Directory](media/OnPremAD05.png)

8. 설정을 검토하고 **마침**을 클릭합니다.
