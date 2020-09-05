---
title: Azure MFA 서버를 시작 하는 중-Azure Active Directory
description: Azure MFA 서버 온-프레미스 단계별 시작
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe8b4176e33ff04ff8033af3b50cbdd0d882ae2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919576"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버로 시작하기

<center>

![온-프레미스에서 MFA 서버 시작](./media/howto-mfaserver-deploy/server2.png)</center>

이 페이지에서는 서버를 새롭게 설치하고 이를 온-프레미스 Active Directory를 사용하여 설정하는 것을 다룹니다. MFA 서버가 이미 설치되어 있고 업그레이드를 고려하는 경우 [최신 Azure Multi-Factor Authentication 서버로 업그레이드](howto-mfaserver-deploy-upgrade.md)를 참조하세요. 웹 서비스만 설치하는 정보는 [Azure Multi-Factor Authentication 서버 모바일 앱 웹 서비스 배포](howto-mfaserver-deploy-mobileapp.md)를 참조하세요.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="plan-your-deployment"></a>배포 계획

Azure Multi-Factor Authentication 서버를 다운로드하기 전에 로드 및 고가용성 요구 사항에 대해 알아봅니다. 이 정보를 사용하여 배포 방법 및 위치를 결정합니다.

필요한 메모리 양에 대한 올바른 지침은 정기적으로 인증해야 하는 사용자의 수입니다.

| 사용자 | RAM |
| ----- | --- |
| 1-10,000 | 4GB |
| 10,001-50,000 | 8GB |
| 50,001-100,000 | 12GB |
| 100,000-200,001 | 16GB |
| 200,001+ | 32GB |

고가용성 또는 부하 분산을 위해 여러 개의 서버를 설정해야 하는가요? Azure MFA 서버로 이 구성을 설정하는 방법은 여러 가지가 있습니다. 첫 번째로 설치하는 Azure MFA 서버는 마스터 서버가 됩니다. 추가되는 서버는 모두 하위 서버가 되며 사용자와 구성을 마스터 서버와 자동으로 동기화합니다. 그런 다음 하나의 주 서버를 구성하고 나머지 서버를 백업으로 사용하거나 모든 서버 간에 부하 분산을 설정할 수 있습니다.

마스터 Azure MFA 서버가 오프라인 상태가 되면 하위 서버에서 2단계 인증 요청을 계속 처리할 수 있습니다. 그러나 새 사용자를 추가할 수 없으며, 마스터가 다시 온라인 상태가 되거나 하위 서버가 승격될 때까지 기존 사용자는 해당 설정을 업데이트할 수 없습니다.

### <a name="prepare-your-environment"></a>환경 준비

Azure Multi-Factor Authentication에 사용 중인 서버가 다음 요구 사항을 충족하는지 확인합니다.

| Azure Multi-Factor Authentication 서버 요구 사항 | Description |
|:--- |:--- |
| 하드웨어 |<li>200MB의 하드 디스크 공간</li><li>x32 또는 x64 지원 프로세서</li><li>1GB 이상 RAM</li> |
| 소프트웨어 |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 ( [Esu](https://docs.microsoft.com/lifecycle/faq/extended-security-updates) 만)</li><li>Windows 10</li><li>Windows 8.1, 모든 버전</li><li>Windows 8, 모든 버전</li><li>Windows 7, 모든 버전 ( [Esu](https://docs.microsoft.com/lifecycle/faq/extended-security-updates) 만 있음)</li><li>Microsoft .NET 4.0 Framework</li><li>사용자 포털 또는 웹 서비스 SDK를 설치하는 경우 IIS 7.0 이상</li> |
| 사용 권한 | Active Directory에 등록할 도메인 관리자 또는 엔터프라이즈 관리자 계정 |

### <a name="azure-mfa-server-components"></a>Azure MFA 서버 구성 요소

Azure MFA 서버를 구성하는 세 가지 웹 구성 요소가 있습니다.

* 웹 서비스 SDK - 다른 구성 요소와 통신을 활성화하고 Azure MFA 애플리케이션 서버에 설치됩니다.
* 사용자 포털 - 사용자가 Azure MFA(Multi-Factor Authentication)에 등록하고 해당 계정을 유지 관리할 수 있는 IIS 웹 사이트입니다.
* 모바일 앱 웹 서비스 - 2단계 인증에 대해 Microsoft Authenticator 앱과 같은 모바일 앱을 사용할 수 있습니다.

서버를 인터넷에 연결하는 경우 세 가지 구성 요소를 모두 동일한 서버에 설치할 수 있습니다. 구성 요소를 나누는 경우 웹 서비스 SDK는 Azure MFA 애플리케이션 서버에 설치되고 사용자 포털 및 모바일 앱 웹 서비스는 인터넷 연결 서버에 설치됩니다.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication 방화벽 요구 사항

각 MFA 서버는 다음 주소로 포트 443 아웃바운드에서 통신할 수 있어야 합니다.

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

아웃바운드 방화벽이 포트 443에서 제한되는 경우 다음 IP 주소 범위를 엽니다.

| IP 서브넷 | 네트워크 마스크 | IP 범위 |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

이벤트 확인 기능을 사용하지 않고 사용자가 모바일 앱을 사용하여 회사 네트워크의 디바이스에서 확인하지 않는 경우 다음 범위만 필요합니다.

| IP 서브넷 | 네트워크 마스크 | IP 범위 |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201 – 70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>MFA 서버를 다운로드합니다.

다음 단계를 따라 Azure Portal에서 Azure Multi-Factor Authentication 서버를 다운로드합니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 사용자 로부터 MFA (multi-factor authentication)를 요구 하려는 새 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다. 다음 단계는 기존 MFA 서버 고객 인 경우에만 작동 합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. *Azure Active Directory*를 검색하고 선택합니다. **보안**  >  **MFA**를 선택 합니다.
3. **관리자 MFA 서버**에서 **서버 설정**을 선택 합니다.
4. **다운로드**를 선택하고 다운로드 페이지의 지침을 따라 설치 관리자를 저장합니다. 

   ![Azure Portal에서 MFA 서버 다운로드](./media/howto-mfaserver-deploy/downloadportal.png)

5. 설치 관리자를 실행한 후 참조할 수 있도록 이 페이지를 열어둡니다.

## <a name="install-and-configure-the-mfa-server"></a>MFA 서버 설치 및 구성

서버를 다운로드했으므로 이제 서버를 설치하고 구성할 수 있습니다. 설치하려는 서버가 계획 섹션에 나열된 요구 사항을 충족하는지 확인합니다.

1. 실행 파일을 두 번 클릭합니다.
2. 설치 폴더 선택 화면에서 해당 폴더가 올바른지 확인 하 고 **다음**을 클릭 합니다.
3. 설치가 완료되면 **마침**을 클릭합니다. 구성 마법사가 시작됩니다.
4. 구성 마법사 시작 화면에서 **인증 구성 마법사를 사용하여 건너뛰기**에 체크 표시하고 **다음**을 클릭합니다. 마법사가 닫히고 서버가 시작됩니다.

   ![인증 구성 마법사 사용 건너뛰기](./media/howto-mfaserver-deploy/skip2.png)

5. 서버를 다운로드한 페이지로 돌아가서 **정품 인증 자격 증명 생성** 버튼을 클릭합니다. 이 정보를 제공된 상자의 Azure MFA 서버에 복사하고 **활성화**를 클릭합니다.

> [!NOTE]
> 전역 관리자만 Azure Portal에서 활성화 자격 증명을 생성할 수 있습니다.

## <a name="send-users-an-email"></a>사용자에게 전자 메일 보내기

출시를 용이하게 하려면 MFA 서버가 사용자와 통신하도록 합니다. MFA 서버는 2단계 인증에 등록되었음을 알리는 전자 메일을 보낼 수 있습니다.

전자 메일을 보내는 작업은 2단계 인증에 사용자를 구성하는 방법으로 결정되어야 합니다. 예를 들어, 회사 디렉터리에서 전화 번호를 가져올 수 있었다면 사용자가 예상하는 것을 알 수 있도록 전자 메일에 기본 전화 번호가 포함되어 있습니다. 전화 번호를 가져오지 않았거나 사용자가 모바일 앱을 사용하려는 경우 해당 계정 등록을 완료하도록 지시하는 이메일을 보냅니다. 전자 메일에 Azure Multi-factor Authentication 사용자 포털에 대한 하이퍼링크를 포함합니다.

또한 전자 메일의 내용은 사용자에 대해 설정된 인증 방법(전화 통화, SMS, 모바일 앱)에 따라 달라집니다. 예를 들어 사용자가 인증할 때 PIN을 사용해야 하는 경우 전자 메일은 초기 PIN 설정 내용을 알려줍니다. 사용자는 처음 인증할 때 PIN을 변경해야 합니다.

### <a name="configure-email-and-email-templates"></a>전자 메일 및 전자 메일 템플릿 구성

왼쪽에 있는 전자 메일 아이콘을 클릭하여 이 전자 메일 보내기에 대한 설정을 설정합니다. 이 페이지에서는 메일 서버의 SMTP 정보를 입력할 수 있으며 **전자 메일을 사용자에게 보내기** 확인란을 선택하여 전자 메일을 보낼 수 있습니다.

![MFA 서버 전자 메일 구성](./media/howto-mfaserver-deploy/email1.png)

전자 메일 내용 탭에서 선택할 수 있는 전자 메일 템플릿을 확인할 수 있습니다. 사용자가 2단계 인증을 수행하도록 구성한 방법에 따라 가장 적합한 템플릿을 선택합니다.

![콘솔의 MFA 서버 전자 메일 템플릿](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Active Directory에서 사용자 가져오기

이제 서버가 설치되었으므로 사용자를 추가하려고 합니다. 사용자를 수동으로 만들거나, Active Directory에서 사용자를 가져오거나, Active Directory와 자동 동기화를 구성할 수 있습니다.

### <a name="manual-import-from-active-directory"></a>Active Directory에서 수동 가져오기

1. Azure MFA 서버의 왼쪽에서 **사용자**를 선택합니다.
2. 아래쪽에서 **Active Directory에서 가져오기**를 선택합니다.
3. 이제 개별 사용자를 검색하거나 해당 사용자로 OU에 대한 AD 디렉터리를 검색할 수 있습니다. 이 경우 사용자 OU를 지정합니다.
4. 오른쪽의 모든 사용자를 강조 표시하고 **가져오기**를 클릭합니다. 성공했음을 알려주는 팝업 메시지가 나타납니다. 가져오기 창을 닫습니다.

   ![Active Directory에서 MFA 서버 사용자 가져오기](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Active Directory와 자동 동기화

1. Azure MFA 서버의 왼쪽에서 **디렉터리 통합**을 선택합니다.
2. **동기화** 탭으로 이동합니다.
3. 아래에서 **추가**를 선택합니다.
4. 나타나는 **동기화 항목 추가** 상자에서 도메인, OU **또는** 보안 그룹, 설정, 메서드 기본값 및 이 동기화 작업의 기본 언어를 선택하고 **추가**를 클릭합니다.
5. 레이블이 지정된 **Active Directory와 동기화 사용** 확인란을 선택하고 **동기화 간격**을 1분에서 24시간 사이로 선택합니다.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Azure Multi-Factor Authentication 서버에서 사용자 데이터를 처리하는 방법

온-프레미스에서 MFA (Multi-Factor Authentication) 서버를 사용 하는 경우 사용자의 데이터는 온-프레미스 서버에 저장 됩니다. 영구 사용자 데이터는 클라우드에 저장되지 않습니다. 사용자가 2단계 인증을 수행하면 MFA 서버가 인증을 수행할 Azure MFA 클라우드 서비스에 데이터를 보냅니다. 이러한 인증 요청이 클라우드 서비스에 전송되면 다음 필드가 요청 및 로그에 전송되어 고객의 인증/사용 보고서에서 사용할 수 있게 됩니다. 일부 필드는 선택 사항이므로 Multi-Factor Authentication 서버 내에서 사용하거나 사용하지 않도록 설정할 수 있습니다. MFA 서버에서 MFA 클라우드 서비스로의 통신은 포트 443 아웃바운드를 통해 연결된 SSL/TLS를 사용합니다. 이러한 필드는 다음과 같습니다.

* 고유 ID - 사용자 이름 또는 내부 MFA 서버 ID
* 이름과 성(선택 사항)
* 메일 주소(선택 사항)
* 전화 번호 - 음성 통화 또는 SMS 인증을 수행할 때
* 디바이스 토큰 - 모바일 앱 인증을 수행할 때
* 인증 모드
* 인증 결과
* MFA 서버 이름
* MFA 서버 IP
* 클라이언트 IP - 사용 가능한 경우

위의 필드 외에도 인증 결과(성공/거부) 및 모든 거부 사유는 인증 데이터와 함께 저장되어 인증/사용 보고서를 통해 사용할 수 있습니다.

> [!IMPORTANT]
> 2019년 3월부터 무료/평가판 Azure AD 테넌트의 MFA 서버 사용자는 전화 통화 옵션을 사용할 수 없습니다. SMS 메시지는 이 변경의 영향을 받지 않습니다. 유료 Azure AD 테넌트의 사용자는 전화 통화를 계속 사용할 수 있습니다. 이 변경 내용은 무료/평가판 Azure AD 테넌트에만 영향을 줍니다.

## <a name="back-up-and-restore-azure-mfa-server"></a>Azure MFA 서버 백업 및 복원

백업을 설정하는 것은 모든 시스템에서 수행할 중요한 단계입니다.

Azure MFA 서버를 백업하려면 **PhoneFactor.pfdata** 파일을 포함한 **C:\Program Files\Multi-Factor Authentication Server\Data** 폴더의 복사본을 가지고 있는지 확인합니다. 

복원이 필요한 경우에 다음 단계를 완료합니다.

1. 새 서버에 Azure MFA 서버를 다시 설치합니다.
2. 새 Azure MFA 서버를 활성화합니다.
3. **MultiFactorAuth** 서비스를 중지합니다.
4. **PhoneFactor.pfdata**를 백업된 복사본으로 덮어씁니다.
5. **MultiFactorAuth** 서비스를 시작합니다.

이제 원래 백업된 구성 및 사용자 데이터를 사용하여 새 서버가 실행됩니다.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>TLS/SSL 프로토콜 및 암호 그룹 관리

MFA 서버 버전 8.x 이상으로 업그레이드하거나 해당 버전을 설치하면 조직에서 필요하지 않는 경우 오래되고 약한 암호 그룹을 사용하지 않도록 설정하거나 제거하는 것이 좋습니다. 이 작업을 완료하는 방법에 대한 정보는 [AD FS에 대한 SSL/TLS 프로토콜 및 암호 그룹 관리](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) 문서에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 사용자 셀프 서비스를 위해 [사용자 포털](howto-mfaserver-deploy-userportal.md)을 설정 및 구성합니다.
- [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md), [RADIUS 인증](howto-mfaserver-dir-radius.md) 또는 [LDAP 인증](howto-mfaserver-dir-ldap.md)을 사용하여 Azure MFA 서버를 설정하고 구성합니다.
- [RADIUS를 사용하여 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버](howto-mfaserver-nps-rdg.md)를 설정 및 구성합니다.
- [Azure Multi-Factor Authentication 서버 모바일 앱 웹 서비스를 배포](howto-mfaserver-deploy-mobileapp.md)합니다.
- [Azure Multi-Factor Authentication 및 타사 VPN을 사용한 고급 시나리오](howto-mfaserver-nps-vpn.md)