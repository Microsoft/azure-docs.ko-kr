---
title: 'Azure AD Connect: 필수 조건 및 하드웨어 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect에 대 한 필수 구성 요소 및 하드웨어 요구 사항을 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1f77d6189e5b32ca771d17ae9902341bcaa1871
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688133"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect에 대한 필수 조건
이 문서에서는 Azure AD (Azure Active Directory) 연결에 대 한 필수 구성 요소 및 하드웨어 요구 사항을 설명 합니다.

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.

### <a name="azure-ad"></a>Azure AD
* Azure AD 테 넌 트가 필요 합니다. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)에서 사용할 수 있습니다. 다음 포털 중 하나를 사용하여 Azure AD Connect를 관리할 수 있습니다.
  * [Azure Portal](https://portal.azure.com)입니다.
  * [Office 포털](https://portal.office.com)
* [도메인을 추가하고 확인합니다](../active-directory-domains-add-azure-portal.md) . 예를 들어 사용자에 대해 contoso.com를 사용 하려는 경우이 도메인이 확인 되 고 contoso.onmicrosoft.com 기본 도메인만 사용 하 고 있지 않은지 확인 합니다.
* Azure AD 테 넌 트는 기본적으로 5만 개체를 허용 합니다. 도메인을 확인 하면 30만 개체로 제한 됩니다. Azure AD에서 더 많은 개체가 필요한 경우 제한을 더 증가 시키려면 지원 사례를 여세요. 50만 개 이상의 개체가 필요한 경우 Office 365, Azure AD Premium 또는 Enterprise Mobility + Security와 같은 라이선스가 필요 합니다.

### <a name="prepare-your-on-premises-data"></a>온-프레미스 데이터 준비
* Azure AD 및 Office 365로 동기화하기 전에 [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac)를 사용하여 디렉터리의 중복 및 서식 문제 등의 오류가 있는지 식별합니다.
* [AZURE AD에서 사용 하도록 설정할 수 있는 선택적 동기화 기능](how-to-connect-syncservice-features.md)을 검토 하 고 사용 하도록 설정 해야 하는 기능을 평가 합니다.

### <a name="on-premises-active-directory"></a>온-프레미스 Active Directory
* Active Directory 스키마 버전 및 포리스트 기능 수준은 Windows Server 2003 이상 이어야 합니다. 도메인 컨트롤러는 스키마 버전 및 포리스트 수준 요구 사항이 충족 되는 한 모든 버전을 실행할 수 있습니다.
* *비밀 번호 쓰기 저장*기능을 사용 하려는 경우 도메인 컨트롤러는 Windows Server 2008 R2 이상에 있어야 합니다.
* Azure AD에서 사용되는 도메인 컨트롤러는 쓰기 가능해야 합니다. RODC (읽기 전용 도메인 컨트롤러)를 사용 하는 것은 *지원 되지*않으며 Azure AD Connect는 쓰기 리디렉션을 따르지 않습니다.
* "점으로 구분 된" (이름에 마침표 ".")를 사용 하 여 온-프레미스 포리스트 또는 도메인을 사용 합니다. NetBIOS 이름은 *지원 되지 않습니다*.
* [Active Directory 휴지통을 사용 하도록 설정](how-to-connect-sync-recycle-bin.md)하는 것이 좋습니다.

### <a name="azure-ad-connect-server"></a>Azure AD Connect 서버
Azure AD Connect 서버에는 중요 한 id 데이터가 포함 되어 있습니다. 이 서버에 대 한 관리자 액세스 권한이 적절 하 게 보호 되는 것이 중요 합니다. [권한 있는 액세스 보안에 대](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)한 지침을 따릅니다. 

[Active Directory 관리 계층 모델](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 에 설명 된 대로 Azure AD Connect 서버는 계층 0 구성 요소로 처리 되어야 합니다. 

Active Directory 환경을 보호 하는 방법에 대 한 자세한 내용은 [Active Directory 보안 설정에 대 한 모범 사례](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)를 참조 하세요.

#### <a name="installation-prerequisites"></a>설치 필수 구성 요소

- Azure AD Connect 도메인에 가입 된 Windows Server 2012 이상에 설치 해야 합니다. 
- Azure AD Connect는 Small Business Server 또는 Windows Server Essentials 2019 이전에 설치할 수 없습니다 (Windows Server Essentials 2019이 지원 됨). 서버는 Windows Server Standard 이상을 사용해야 합니다. 
- Azure AD Connect 서버에는 전체 GUI가 설치되어 있어야 합니다. Windows Server Core에 Azure AD Connect를 설치 하는 것은 지원 되지 않습니다. 
- Azure AD Connect 마법사를 사용 하 여 Active Directory Federation Services (AD FS) 구성을 관리 하는 경우에는 Azure AD Connect 서버에서 PowerShell 기록을 사용 하도록 설정 그룹 정책 하지 않아야 합니다. Azure AD Connect 마법사를 사용 하 여 동기화 구성을 관리 하는 경우 PowerShell 기록을 사용 하도록 설정할 수 있습니다. 
- AD FS 배포 되는 경우: 
    - AD FS 또는 웹 응용 프로그램 프록시가 설치 되는 서버는 Windows Server 2012 R2 이상 이어야 합니다. Windows 원격 관리 를 사용할 수 있어야 합니다. 
    - TLS/SSL 인증서를 구성 해야 합니다. 자세한 내용은 AD FS 및 [AD FS에서 ssl 인증서 관리](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)를 [위한 ssl/TLS 프로토콜 및 암호 그룹 관리](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) 를 참조 하세요.
    - 이름 확인을 구성 해야 합니다. 
- 전역 관리자가 MFA를 사용 하도록 설정한 경우 URL은 신뢰할 수 있는 https://secure.aadcdn.microsoftonline-p.com 사이트 목록에 *있어야* 합니다. MFA 챌린지를 묻는 메시지가 표시 되 고 이전에 추가 되지 않은 경우 신뢰할 수 있는 사이트 목록에이 사이트를 추가 하 라는 메시지가 표시 됩니다. Internet Explorer를 사용하여 신뢰할 수 있는 사이트에 추가할 수 있습니다.

#### <a name="harden-your-azure-ad-connect-server"></a>Azure AD Connect 서버 강화 
IT 환경의이 중요 한 구성 요소에 대 한 보안 공격 노출 영역을 줄이기 위해 Azure AD Connect 서버를 강화 하는 것이 좋습니다. 이러한 권장 사항을 따르면 조직에 대 한 몇 가지 보안 위험을 완화 하는 데 도움이 됩니다.

- Azure AD Connect 도메인 컨트롤러 및 다른 계층 0 리소스와 동일 하 게 처리 합니다. 자세한 내용은 [Active Directory 관리 계층 모델](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)을 참조 하세요.
- Azure AD Connect 서버에 대 한 관리 액세스를 도메인 관리자 또는 긴밀 하 게 제어 되는 다른 보안 그룹 으로만 제한 합니다.
- [권한 있는 액세스 권한이 있는 모든 직원에 대 한 전용 계정을](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)만듭니다. 관리자는 웹을 탐색 하 고, 전자 메일을 확인 하 고, 높은 권한의 계정을 사용 하 여 일상적인 생산성 작업을 수행 하지 않아야 합니다.
- [권한 있는 액세스 보안](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)에 제공 된 지침을 따릅니다. 
- AADConnect 서버에서 NTLM 인증 사용을 거부 합니다. 이 작업을 수행 하는 몇 가지 방법은 다음과 같습니다. [AADConnect 서버에서 ntlm을 제한](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) 하 고 [도메인에서 ntlm을 제한](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain) 합니다.
- 모든 컴퓨터에 고유한 로컬 관리자 암호가 있는지 확인 합니다. 자세한 내용은 [LAPS (로컬 관리자 암호 솔루션)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) 에서 각 워크스테이션에 고유한 임의의 암호를 구성 하 고 ACL로 보호 되 Active Directory에 저장할 수 있습니다. 권한 있는 적격 사용자만이 이러한 로컬 관리자 계정 암호를 읽거나 재설정을 요청할 수 있습니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.)에서 워크스테이션 및 서버에 사용할 LAPS를 가져올 수 있습니다. LAPS 및 Paw (권한 있는 액세스 워크스테이션)를 사용 하 여 환경을 운영 하는 방법에 대 한 추가 지침은 [클린 소스 원칙을 기반으로 하는 운영 표준](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)에서 찾을 수 있습니다. 
- 조직의 정보 시스템에 대 한 권한 있는 액세스 권한이 있는 모든 직원에 대 한 전용 [권한 있는 액세스 워크스테이션](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) 을 구현 합니다. 
- 이러한 [추가 지침](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) 에 따라 Active Directory 환경의 공격 노출 영역을 줄입니다.


### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect에서 사용하는 SQL Server
* Azure AD Connect는 ID 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. 기본적으로 SQL Server 2012 Express LocalDB (SQL Server Express의 라이트 버전)가 설치 되어 있습니다. SQL Server Express은 약 10만 개체를 관리할 수 있도록 10gb 크기 제한이 있습니다. 더 큰 볼륨의 디렉터리 개체를 관리 해야 하는 경우 설치 마법사에서 다른 SQL Server 설치를 가리키도록 합니다. SQL Server 설치의 유형은 [Azure AD Connect의 성능](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)에 영향을 줄 수 있습니다.
* 다른 SQL Server 설치를 사용 하는 경우 다음 요구 사항이 적용 됩니다.
  * Azure AD Connect는 2012 (최신 Service Pack)의 모든 버전 SQL Server를 SQL Server 2019으로 지원 합니다. Azure SQL Database는 데이터베이스로 *지원 되지 않습니다* .
  * 대/소문자를 구분하지 않는 SQL 데이터 정렬을 사용해야 합니다. 이러한 데이터 정렬은 이름에 \_CI_를 사용하여 식별됩니다. 이름에 CS_으로 식별 되는 대/소문자 구분 데이터 정렬을 사용 하는 것은 \_ *지원 되지 않습니다*.
  * SQL 인스턴스당 동기화 엔진을 하나만 사용할 수 있습니다. FIM/MIM 동기화, DirSync 또는 Azure AD Sync를 사용 하 여 SQL 인스턴스를 공유 하는 것은 *지원 되지 않습니다*.

### <a name="accounts"></a>계정
* 통합 하려는 Azure AD 테 넌 트에 대 한 Azure AD 전역 관리자 계정이 있어야 합니다. 이 계정은 *학교 또는 조직 계정* 이어야 하며 *Microsoft 계정*수 없습니다.
* [Express 설정을](reference-connect-accounts-permissions.md#express-settings-installation) 사용 하거나 DirSync에서 업그레이드 하는 경우 온-프레미스 Active Directory에 대 한 엔터프라이즈 관리자 계정이 있어야 합니다.
* 사용자 지정 설정 설치 경로를 사용 하는 경우에는 더 많은 옵션을 사용할 수 있습니다. 자세한 내용은 [사용자 지정 설치 설정](reference-connect-accounts-permissions.md#custom-installation-settings)을 참조 하세요.

### <a name="connectivity"></a>연결
* Azure AD Connect 서버는 인트라넷 및 인터넷에 대해 DNS 확인을 해야 합니다. DNS 서버는 온-프레미스 Active Directory와 Azure AD 엔드포인트 모두에 대해 이름을 확인할 수 있어야 합니다.
* 인트라넷에 방화벽이 있고 Azure AD Connect 서버와 도메인 컨트롤러 사이에서 포트를 열어야 하는 경우 자세한 내용은 [Azure AD Connect 포트](reference-connect-ports.md) 를 참조 하세요.
* 프록시 또는 방화벽에 액세스할 수 있는 Url을 제한 하는 경우 [Office 365 url 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 에 설명 된 url을 열어야 합니다.
  * 독일 또는 Microsoft Azure Government 클라우드에서 Microsoft 클라우드를 사용 하는 경우 Url에 대 한 [동기화 서비스 인스턴스 고려 사항 Azure AD Connect](reference-connect-instances.md) 을 참조 하세요.
* Azure AD Connect(1.1.614.0 버전 이상)는 기본적으로 TLS 1.2를 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. 기본 운영 체제에서 TLS 1.2를 사용할 수 없는 경우 Azure AD Connect에 이전 프로토콜(TLS 1.1 및 TLS 1.0)이 점차적으로 다시 적용됩니다.
* 1.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. TLS 1.2로 변경하려면 [Azure AD Connect에 TLS 1.2 사용](#enable-tls-12-for-azure-ad-connect)의 단계를 수행합니다.
* 인터넷에 연결 하기 위해 아웃 바운드 프록시를 사용 하는 경우 설치 마법사에 **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** 파일의 다음 설정을 추가 하 고 인터넷 및 Azure AD에 연결할 수 있도록 동기화를 Azure AD Connect 해야 합니다. 이 텍스트는 파일의 맨 아래에 입력해야 합니다. 이 코드에서 * &lt; proxyaddress &gt; * 는 실제 프록시 IP 주소 또는 호스트 이름을 나타냅니다.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* 프록시 서버에 인증이 필요한 경우 [서비스 계정은](reference-connect-accounts-permissions.md#adsync-service-account) 도메인에 위치 해야 합니다. 사용자 지정 설정 설치 경로를 사용 하 여 [사용자 지정 서비스 계정을](how-to-connect-install-custom.md#install-required-components)지정 합니다. machine.config 다른 변경도 필요 합니다. 이러한 변경 내용으로 machine.config 설치 마법사와 동기화 엔진이 프록시 서버의 인증 요청에 응답 합니다. **구성** 페이지를 제외 하 고 모든 설치 마법사 페이지에서 로그인 한 사용자의 자격 증명이 사용 됩니다. 설치 마법사의 마지막 부분에 있는 **구성** 페이지에서 컨텍스트는 사용자가 만든 [서비스 계정](reference-connect-accounts-permissions.md#adsync-service-account) 으로 전환 됩니다. machine.config 섹션은 다음과 같습니다.

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* 프록시 구성이 기존 설정에서 수행 되는 경우 Azure AD Connect에서 프록시 구성을 읽고 behviour을 업데이트 하려면 **Microsoft Azure AD 동기화 서비스** 를 한 번 다시 시작 해야 합니다. 
* Azure AD Connect에서 디렉터리 동기화의 일부로 웹 요청을 Azure AD로 보내면 Azure AD에서 응답하는 데 최대 5분이 걸릴 수 있습니다. 프록시 서버에서 연결 유휴 시간 제한 구성을 설정 하는 것이 일반적입니다. 구성이 6 분 이상으로 설정 되어 있는지 확인 합니다.

자세한 내용은 MSDN의 [기본 프록시 요소](https://msdn.microsoft.com/library/kd3cf2ex.aspx)에 대 한 정보를 참조 하세요.
연결에 문제가 있는 경우 [연결 문제 해결](tshoot-connect-connectivity.md)을 참조하세요.

### <a name="other"></a>기타
선택 사항: 동기화를 확인 하려면 테스트 사용자 계정을 사용 합니다.

## <a name="component-prerequisites"></a>구성 요소 필수 조건
### <a name="powershell-and-net-framework"></a>PowerShell 및 .NET Framework
Azure AD Connect는 Microsoft PowerShell 및 .NET Framework 4.5.1에 따라 다릅니다. 서버에 이 버전 이상을 설치해야 합니다. Windows Server 버전에 따라 다음 작업을 수행 합니다.

* Windows Server 2012 R2
  * Microsoft PowerShell은 기본적으로 설치되므로 사용자가 조치할 필요는 없습니다.
  * .NET Framework 4.5.1 이후 릴리스는 Windows 업데이트를 통해 제공됩니다. 제어판에서 Windows Server에 대 한 최신 업데이트를 설치 했는지 확인 합니다.
* Windows Server 2012
  * 최신 버전의 Microsoft PowerShell은 [Microsoft 다운로드 센터](https://www.microsoft.com/downloads)에서 다운로드할 수 있는 Windows Management Framework 4.0에서 다운로드할 수 있습니다.
  * .NET Framework 4.5.1 이상 릴리스는 [Microsoft 다운로드 센터](https://www.microsoft.com/downloads)에서 다운로드할 수 있습니다.


### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect에 TLS 1.2 사용
1.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진 서버와 Azure AD 간의 통신을 암호화합니다. 서버에서 기본적으로 TLS 1.2를 사용 하도록 .NET 응용 프로그램을 구성할 수 있습니다. TLS 1.2에 대 한 자세한 내용은 [Microsoft 보안 공지 2960358](https://technet.microsoft.com/security/advisory/2960358)를 참조 하세요.

1. 운영 체제에 대해 .NET 4.5.1 핫픽스를 설치 했는지 확인 합니다. 자세한 내용은 [Microsoft 보안 공지 2960358](https://technet.microsoft.com/security/advisory/2960358)을 참조 하세요. 이 핫픽스 또는 이후 릴리스를 서버에 이미 설치했을 수 있습니다.

1. 모든 운영 체제에 대해 이 레지스트리 키를 설정하고 서버를 다시 시작합니다.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. 또한 동기화 엔진 서버와 원격 SQL Server 간에 TLS 1.2을 사용 하도록 설정 하려면 [Microsoft SQL Server에 대 한 tls 1.2 지원](https://support.microsoft.com/kb/3135244)에 필요한 버전이 설치 되어 있는지 확인 합니다.

## <a name="prerequisites-for-federation-installation-and-configuration"></a>페더레이션 설치 및 구성을 위한 필수 조건
### <a name="windows-remote-management"></a>Windows 원격 관리
Azure AD Connect를 사용 하 여 AD FS 또는 WAP (웹 응용 프로그램 프록시)를 배포 하는 경우 다음 요구 사항을 확인 하세요.

* 대상 서버가 도메인에 가입 된 경우 Windows 원격 관리를 사용 하도록 설정 되어 있는지 확인 합니다.
  * 관리자 권한 PowerShell 명령 창에서 명령을 사용 `Enable-PSRemoting –force` 합니다.
* 대상 서버가 도메인에 가입 되지 않은 WAP 컴퓨터 이면 다음과 같은 몇 가지 추가 요구 사항이 있습니다.
  * 대상 컴퓨터(WAP 컴퓨터):
    * 서비스 스냅인을 통해 WinRM (Windows 원격 관리/WS-MANAGEMENT) 서비스가 실행 중인지 확인 합니다.
    * 관리자 권한 PowerShell 명령 창에서 명령을 사용 `Enable-PSRemoting –force` 합니다.
  * 대상 컴퓨터가 도메인에 가입 되어 있지 않거나 신뢰할 수 없는 도메인에 있는 경우 마법사가 실행 되 고 있는 컴퓨터에서 다음을 수행 합니다.
    * 관리자 권한 PowerShell 명령 창에서 명령을 사용 `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` 합니다.
    * 서버 관리자에서 다음을 수행 합니다.
      * 컴퓨터 풀에 DMZ WAP 호스트를 추가 합니다. 서버 관리자에서 **관리**  >  **서버 추가**를 선택 하 고 **DNS** 탭을 사용 합니다.
      * **모든 서버 서버 관리자** 탭에서 WAP 서버를 마우스 오른쪽 단추로 클릭 하 고 **다른 이름으로 관리**를 선택 합니다. WAP 컴퓨터에 대 한 로컬 (도메인 아님) 자격 증명을 입력 합니다.
      * 원격 PowerShell 연결의 유효성을 검사 하려면 **모든 서버 서버 관리자** 탭에서 WAP 서버를 마우스 오른쪽 단추로 클릭 하 고 **Windows PowerShell**을 선택 합니다. 원격 powershell 세션을 설정할 수 있도록 원격 PowerShell 세션이 열립니다.

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 인증서 요구 사항
* AD FS 팜과 모든 웹 응용 프로그램 프록시 서버의 모든 노드에 대해 동일한 TLS/SSL 인증서를 사용 하는 것이 좋습니다.
* 인증서는 X509 인증서여야 합니다.
* 테스트 랩 환경의 페더레이션 서버에서 자체 서명된 인증서를 사용할 수 있습니다. 프로덕션 환경의 경우에는 공용 인증 기관에서 인증서를 가져오는 것이 좋습니다.
  * 공개적으로 신뢰할 수 없는 인증서를 사용 하는 경우 각 웹 응용 프로그램 프록시 서버에 설치 된 인증서를 로컬 서버와 모든 페더레이션 서버에서 신뢰할 수 있는지 확인 합니다.
* 인증서의 ID는 페더레이션 서비스 이름(예: sts.contoso.com)과 일치해야 합니다.
  * Id는 dNSName 형식의 SAN (주체 대체 이름) 확장 이거나, SAN 항목이 없는 경우 주체 이름이 일반 이름으로 지정 됩니다.
  * 인증서 중 하나가 페더레이션 서비스 이름과 일치 하는 경우 여러 SAN 항목이 인증서에 제공 될 수 있습니다.
  * Workplace Join를 사용 하려는 경우 enterpriseregistration 값을 사용 하 여 추가 SAN이 필요 **합니다.** 그런 다음 조직의 UPN (사용자 계정 이름) 접미사 (예: enterpriseregistration.contoso.com)를 사용 합니다.
* 이 CNG (CryptoAPI next generation) 키 및 KSPs (키 저장소 공급자)를 기반으로 하는 인증서는 지원 되지 않습니다. 따라서 KSP가 아닌 CSP (암호화 서비스 공급자)를 기반으로 하는 인증서를 사용 해야 합니다.
* 와일드카드 인증서가 지원됩니다.

### <a name="name-resolution-for-federation-servers"></a>페더레이션 서버에 대한 이름 확인
* 인트라넷 (내부 DNS 서버) 및 엑스트라넷 (도메인 등록자를 통해 공용 DNS) 모두에 대 한 AD FS 이름 (예: sts.contoso.com)에 대 한 DNS 레코드를 설정 합니다. 인트라넷 DNS 레코드의 경우, CNAME 레코드가 아닌 A 레코드를 사용해야 합니다. 도메인에 가입 된 컴퓨터에서 Windows 인증이 제대로 작동 하려면 레코드를 사용 해야 합니다.
* 둘 이상의 AD FS 서버 또는 웹 응용 프로그램 프록시 서버를 배포 하는 경우 부하 분산 장치를 구성 하 고 AD FS 이름 (예: sts.contoso.com)에 대 한 DNS 레코드가 부하 분산 장치를 가리키도록 해야 합니다.
* 인트라넷에서 Internet Explorer를 사용 하 여 브라우저 응용 프로그램에 대해 Windows 통합 인증을 사용 하려면 AD FS 이름 (예: sts.contoso.com)을 Internet Explorer의 인트라넷 영역에 추가 해야 합니다. 이 요구 사항은 그룹 정책를 통해 제어 하 고 도메인에 가입 된 모든 컴퓨터에 배포할 수 있습니다.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect 지원 구성 요소
Azure AD Connect는 Azure AD Connect가 설치 된 서버에서 다음 구성 요소를 설치 합니다. 이 목록은 기본 Express 설치에 해당합니다. **동기화 서비스 설치** 페이지에서 다른 SQL Server를 사용 하도록 선택 하면 SQL Express LocalDB가 로컬에 설치 되지 않습니다.

* Azure AD Connect Health
* Microsoft SQL Server 2012 명령줄 유틸리티
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 재배포 패키지

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect의 하드웨어 요구 사항
다음 표에서는 Azure AD Connect 동기화 컴퓨터의 최소 요구 사항을 보여 줍니다.

| Active Directory의 개체 수 | CPU | 메모리 | 하드 드라이브 크기 |
| --- | --- | --- | --- |
| 10,000개 미만 |1.6GHz |4GB |70GB |
| 10,000–50,000개 |1.6GHz |4GB |70GB |
| 50,000–100,000개 |1.6GHz |16GB |100GB |
| 10만 이상의 개체에는 SQL Server의 전체 버전이 필요 합니다. | | | |
| 100,000–300,000개 |1.6GHz |32GB |300GB |
| 300,000–600,000개 |1.6GHz |32GB |450GB |
| 600,000개 초과 |1.6GHz |32GB |500GB |

AD FS 또는 웹 응용 프로그램 프록시 서버를 실행 하는 컴퓨터의 최소 요구 사항은 다음과 같습니다.

* CPU: 듀얼 코어 1.6GHz 이상
* 메모리: 2gb 이상
* Azure VM: A2 구성 이상

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
