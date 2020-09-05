---
title: Azure AD Connect Health Agent 설치 | Microsoft Docs
description: AD FS 및 동기화를 위한 에이전트 설치에 관해 설명하는 Azure AD Connect Health 페이지입니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b51eb7e59e32985363d83c3d515fa7f54babac1f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179457"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health Agent 설치

이 문서는 Azure AD Connect Health Agent를 설치하고 구성하는 단계를 안내합니다. [여기](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)에서 에이전트를 다운로드할 수 있습니다.

## <a name="requirements"></a>요구 사항

다음 표는 Azure AD Connect Health를 사용하기 위한 요구 사항 목록입니다.

| 요구 사항 | Description |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health는 Azure AD Premium 기능이기 때문에 Azure AD Premium이 필요합니다. <br /><br />자세한 내용은 시작 하기를 참조 하십시오 [Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) <br />30일 무료 평가판을 시작하려면 [평가판 시작](https://azure.microsoft.com/trial/get-started-active-directory/)을 참조하세요. |
| 사용자는 Azure AD Connect Health를 시작할 수 있는 Azure AD의 전역 관리자여야 합니다. |기본적으로 전역 관리자만 Health Agent를 설치할 수 있고, 이것을 시작하고 포털에 액세스하고 Azure AD Connect Health 내에서 작업을 수행하도록 구성할 수 있습니다. 자세한 내용은 [Azure AD 디렉터리 관리](../fundamentals/active-directory-administer.md)를 참조하세요. <br /><br /> Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 조직의 다른 사용자에 게 Azure AD Connect Health에 대 한 액세스를 허용할 수 있습니다. 자세한 내용은 [Azure AD Connect Health에 대 한 AZURE RBAC (역할 기반 액세스 제어)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) 를 참조 하세요. <br /><br />**중요:** 에이전트를 설치할 때 사용하는 계정은 직장 또는 학교 계정이어야 합니다. Microsoft 계정은 사용할 수 없습니다. 자세한 내용은 [조직으로 Azure 등록](../fundamentals/sign-up-organization.md) 을 참조 하세요. |
| Azure AD Connect Health Agent는 각 대상 서버에 설치됩니다. | Azure AD Connect Health가 데이터를 수신하고 모니터링 및 분석 기능을 제공하려면 Health Agents가 대상 서버에 설치되고 구성되어야 합니다. <br /><br />예를 들어, AD FS 인프라에서 데이터를 가져오려면 AD FS 및 웹 애플리케이션 프록시 서버에 에이전트가 설치되어야 합니다. 마찬가지로 온-프레미스 AD DS 인프라에 대한 데이터를 가져오려면 에이전트는 도메인 컨트롤러에 설치되어야 합니다. <br /><br /> |
| Azure 서비스 엔드포인트에 대한 아웃바운드 연결 | 에이전트는 설치 및 런타임 중에 Azure AD Connect Health 서비스 엔드포인트에 연결되어야 합니다. 방화벽을 사용하여 아웃바운드 연결을 차단하는 경우 다음 엔드포인트가 허용 목록에 추가되어 있는지 확인합니다. [아웃바운드 연결 엔드포인트](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)를 참조하세요. |
|IP 주소를 기반으로 하는 아웃바운드 연결 | 방화벽의 IP 주소 기반 필터링은 [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.|
| 아웃 바운드 트래픽에 대 한 TLS 검사를 필터링 하거나 사용 하지 않도록 설정 | 네트워크 계층에서 아웃 바운드 트래픽에 대 한 TLS 검사 또는 종료가 있으면 에이전트 등록 단계 또는 데이터 업로드 작업이 실패할 수 있습니다. [TLS 검사를 설정 하는 방법](https://technet.microsoft.com/library/ee796230.aspx) 에 대해 자세히 알아보세요. |
| 에이전트를 실행하는 서버의 방화벽 포트 |에이전트가 Azure AD Health 서비스 엔드포인트와 통신하기 위해 다음 방화벽 포트를 열어놓아야 합니다.<br /><br /><li>TCP 포트 443</li><li>TCP 포트 5671</li> <br />포트 5671은 최신 버전의 에이전트에 더 이상 필요하지 않습니다. 포트 443만 필요하도록 최신 버전으로 업그레이드합니다. [방화벽 포트 사용](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx)에 대해 자세히 알아보기 |
| IE 보안 강화를 사용하는 경우 다음 웹 사이트 허용 |에이전트가 설치될 서버에서 IE 보안 강화를 사용하도록 설정되어 있는 경우 다음 웹 사이트를 허용해야 합니다.<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>Azure Active Directory에 의해 신뢰할 수 있는 조직의 페더레이션 서버입니다. 예: https:\//sts.contoso.com</li> [IE를 구성 하는 방법](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)에 대해 자세히 알아보세요. 네트워크 내에 프록시가 있는 경우 아래 참고를 참조 하세요.|
| PowerShell v4.0 이상이 설치되어 있는지 확인합니다. | <li>Windows Server 2008 R2는 에이전트에 충분하지 않은 PowerShell v2.0과 함께 제공됩니다. [Windows Server 2008 R2 서버에 에이전트 설치](#agent-installation-on-windows-server-2008-r2-servers)에 설명된 대로 PowerShell을 업데이트합니다.</li><li>Windows Server 2012는 에이전트에 충분하지 않은 PowerShell v3.0과 함께 제공됩니다.</li><li>Windows Server 2012 R2 이상은 PowerShell 최신 버전과 함께 제공됩니다.</li>|
|FIPS 사용 안 함|FIPS는 Azure AD Connect Health 에이전트에서 지원되지 않습니다.|


> [!NOTE]
> 매우 잠금 수준이 매우 제한 된 환경이 있는 경우 위의 IE 보안 강화 구성에 나열 된 것 외에 아래 서비스 끝점 목록에서 언급 한 Url을 허용 목록 해야 합니다. 
>

### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure 서비스 엔드포인트에 대한 아웃바운드 연결

 에이전트는 설치 및 런타임 중에 Azure AD Connect Health 서비스 엔드포인트에 연결되어야 합니다. 방화벽을 사용하여 아웃바운드 연결을 차단하는 경우 다음 URL이 기본적으로 차단되지 않는지 확인합니다. 이러한 URL의 보안 모니터링 또는 검사를 사용 안 함으로 설정하지 말고 다른 인터넷 트래픽처럼 허용합니다. 해당 URL은 Azure AD Connect Health 서비스 엔드포인트와의 통신을 허용합니다. [AzureADConnectHealthConnectivity를 사용 하 여 아웃 바운드 연결을 확인](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install#test-connectivity-to-azure-ad-connect-health-service)하는 방법을 알아봅니다.

| 도메인 환경 | 필수 Azure 서비스 엔드포인트 |
| --- | --- |
| 일반 공개 | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - Port: 5671 </li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *이 엔드포인트는 등록하는 동안 검색 용도로만 사용합니다.</li> |
| Azure 독일 | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de *이 엔드포인트는 등록하는 동안 검색 용도로만 사용합니다.</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *이 엔드포인트는 등록하는 동안 검색 용도로만 사용합니다.</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Azure AD Connect Health Agent 다운로드 및 설치

* Azure AD Connect Health에 대한 [요구 사항을 충족](how-to-connect-health-agent-install.md#requirements)해야 합니다.
* AD FS용 Azure AD Connect Health 사용 시작
    * [AD FS용 Azure AD Connect Health Agent 다운로드.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [설치 지침을 참조](#installing-the-azure-ad-connect-health-agent-for-ad-fs)하세요.
* 동기화용 Azure AD Connect Health 사용 시작
    * [최신 버전의 Azure AD Connect 다운로드 및 설치](https://go.microsoft.com/fwlink/?linkid=615771). 동기화용 Health 에이전트는 Azure AD Connect의 일부로 설치됩니다(버전 1.0.9125.0 이상).
* AD DS용 Azure AD Connect Health 사용 시작
    * [AD DS에 대 한 Azure AD Connect Health 에이전트를 다운로드](https://go.microsoft.com/fwlink/?LinkID=820540)합니다.
    * [설치 지침을 참조](#installing-the-azure-ad-connect-health-agent-for-ad-ds)하세요.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>AD FS용 Azure AD Connect Health Agent 설치

> [!NOTE]
> AD FS 서버는 Sync 서버와 다릅니다. Sync 서버에 AD FS 에이전트를 설치하지 마세요.
>

설치하기 전에 AD FS 서버 호스트 이름이 고유하고 AD FS 서비스에 없는지 확인합니다.
에이전트 설치를 시작하려면 다운로드한 .exe 파일을 두 번 클릭합니다. 첫 번째 화면에서 설치를 클릭합니다.

![Azure AD Connect Health AD FS 설치 시작](./media/how-to-connect-health-agent-install/install1.png)

설치가 완료되면 지금 구성을 클릭합니다.

![Azure AD Connect Health AD FS 설치 완료](./media/how-to-connect-health-agent-install/install2.png)

PowerShell 창을 열고 에이전트 등록 프로세스를 시작합니다. 대화 상자가 나타나면 에이전트 등록을 수행할 권한이 있는 Azure AD 계정으로 로그인합니다. 기본적으로 전역 관리자 계정은 액세스 권한이 있습니다.

![Azure AD Connect Health AD FS 로그인 구성](./media/how-to-connect-health-agent-install/install3.png)

로그인한 후 PowerShell이 계속됩니다. 완료된 후에 PowerShell을 닫으면 구성이 완료됩니다.

이 때, 에이전트 서비스가 자동으로 시작되고 에이전트가 필요한 정보를 클라우드 서비스에 안전하게 업로드하도록 허용해야 합니다.

이전 섹션에 설명된 필수 조건이 모두 충족되지 않으면 PowerShell 창에 경고가 표시됩니다. 에이전트를 설치하기 전에 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 완료해야 합니다. 다음 스크린샷은 이러한 오류의 예입니다.

![Azure AD Connect Health AD FS 스크립트 구성](./media/how-to-connect-health-agent-install/install4.png)

에이전트가 설치되었는지 확인하려면 서버에서 다음 서비스를 살펴봅니다. 구성을 완료했다면 해당 서비스가 실행 중이어야 합니다. 그렇지 않으면 구성이 완료될 때까지 해당 서비스가 중지됩니다.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Azure AD Connect Health AD FS 서비스](./media/how-to-connect-health-agent-install/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Windows Server 2008 R2 서버에서 에이전트 설치

Windows Server 2008 R2 서버에 대한 단계:

1. 서버가 서비스 팩 1 이상에서 실행되고 있는지 확인합니다.
2. 에이전트 설치에 대해 IE ESC를 끕니다.
3. AD Health Agent를 설치하기 전에 각 서버에 Windows PowerShell 4.0을 설치합니다. Windows PowerShell 4.0을 설치하려면
   * 오프라인 설치 관리자를 다운로드하려면 다음 링크를 사용하여 [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779)를 설치합니다.
   * Windows 기능에서 PowerShell ISE를 설치합니다.
   * 서버에서 Internet Explorer 버전 10 이상을 설치합니다. (Health Service에서 Azure 관리자 자격 증명을 사용하여 인증하는 데 필요합니다.)
4. Windows Server 2008 R2에서 Windows PowerShell 4.0을 설치하는 방법에 대한 자세한 내용은 [여기](https://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)에서 wiki 문서를 참조하세요.

### <a name="enable-auditing-for-ad-fs"></a>AD FS 감사 사용

> [!NOTE]
> 이 섹션은 AD FS 서버에만 해당됩니다. 웹 애플리케이션 프록시 서버에서는 이 단계를 수행할 필요가 없습니다.
>

사용량 현황 분석 기능을 통해 데이터를 수집하고 분석하려면 Azure AD Connect Health Agent에 AD FS 감사 로그의 정보가 필요합니다. 이러한 로그는 기본적으로 사용하지 않도록 설정됩니다. AD FS 서버에서 AD FS 감사를 사용하도록 설정하고 AD FS 감사 로그를 찾으려면 다음 절차에 따르세요.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Windows Server 2008 R2에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. **시작**을 클릭하고 **프로그램**, **관리 도구**를 차례로 가리킨 다음 **로컬 보안 정책**을 클릭합니다.
2. **Security Settings\Local Policies\User Rights Assignment** 폴더로 이동한 후 **보안 감사 생성**을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 2.0 서비스 계정이 목록에 있는지 확인합니다. 나열되어 있지 않으면 **사용자 또는 그룹 추가**를 클릭하여 해당 계정을 목록에 추가하고 **확인**을 클릭합니다.
4. 감사를 사용하려면 상승된 권한으로 명령 프롬프트를 열고 <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code> 명령을 실행합니다.
5. **로컬 보안 정책**을 닫습니다.
<br />   -- **다음 단계는 기본 AD FS 서버에만 필요합니다.** -- <br />
6. **AD FS 관리** 스냅인을 엽니다. AD FS 관리 스냅인을 열려면 **시작**을 클릭하고 **프로그램**, **관리 도구** 순으로 가리킨 다음 **AD FS 2.0 관리**를 클릭합니다.
7. **작업** 창에서 **페더레이션 서비스 속성 편집**을 클릭합니다.
8. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 클릭합니다.
9. **성공 감사** 및 **실패 감사** 확인란을 선택합니다.
10. **확인**을 클릭합니다.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Windows Server 2012 R2에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자**를 열거나 바탕 화면 작업 표시줄에서 서버 관리자를 열어 **로컬 보안 정책**을 연 다음 **도구/로컬 보안 정책**을 클릭합니다.
2. **Security Settings\Local Policies\User Rights Assignment** 폴더로 이동한 후 **보안 감사 생성**을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 목록에 있는지 확인합니다. 나열되어 있지 않으면 **사용자 또는 그룹 추가**를 클릭하여 해당 계정을 목록에 추가하고 **확인**을 클릭합니다.
4. 감사를 사용하려면 상승된 권한으로 명령 프롬프트를 열고 ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable``` 명령을 실행합니다.
5. **로컬 보안 정책**을 닫습니다.
<br />   -- **다음 단계는 기본 AD FS 서버에만 필요합니다.** -- <br />
6. **AD FS 관리** 스냅인을 엽니다(서버 관리자에서 도구를 클릭한 다음 AD FS 관리 선택).
7. **작업** 창에서 **페더레이션 서비스 속성 편집**을 클릭합니다.
8. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 클릭합니다.
9. **성공 감사 및 실패 감사** 확인란을 선택하고 **확인**을 클릭합니다.
10. 명령을 사용 하 여 powershell을 통해 자세한 정보 로깅을 사용 하도록 설정할 수 있습니다 ```Set-AdfsProperties -LOGLevel Verbose``` .

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Windows Server 2016에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자**를 열거나 바탕 화면 작업 표시줄에서 서버 관리자를 열어 **로컬 보안 정책**을 연 다음 **도구/로컬 보안 정책**을 클릭합니다.
2. **Security Settings\Local Policies\User Rights Assignment** 폴더로 이동한 후 **보안 감사 생성**을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 목록에 있는지 확인합니다. 없는 경우 **사용자 또는 그룹 추가**를 클릭하여 AD FS 서비스 계정을 목록에 추가한 다음 **확인**을 클릭합니다.
4. 감사를 사용하려면 상승된 권한으로 명령 프롬프트를 열고 <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code> 명령을 실행합니다.
5. **로컬 보안 정책**을 닫습니다.
<br />   -- **다음 단계는 기본 AD FS 서버에만 필요합니다.** -- <br />
6. **AD FS 관리** 스냅인을 엽니다(서버 관리자에서 도구를 클릭한 다음 AD FS 관리 선택).
7. **작업** 창에서 **페더레이션 서비스 속성 편집**을 클릭합니다.
8. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 클릭합니다.
9. **성공 감사 및 실패 감사** 확인란을 선택하고 **확인**을 클릭합니다. 기본적으로 사용하도록 설정되어 있습니다.
10. PowerShell 창을 열고 다음 명령을 실행합니다. ```Set-AdfsProperties -AuditLevel Verbose```.

기본적으로 "기본" 감사 수준을 사용하도록 설정되어 있습니다. [Windows Server 2016의 AD FS 감사 기능 향상](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)에 대해 자세히 알아보세요.


#### <a name="to-locate-the-ad-fs-audit-logs"></a>AD FS 감사 로그를 찾으려면

1. **이벤트 뷰어**를 엽니다.
2. Windows 로그로 이동하여 **보안**을 선택합니다.
3. 오른쪽에서 **현재 로그 필터링**을 클릭합니다.
4. 이벤트 소스에서 **AD FS 감사**를 선택합니다.

    감사 로그에 대한 빠른 [FAQ 참고](reference-connect-health-faq.md#operations-questions)도 살펴보세요.

![AD FS 감사 로그](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> 그룹 정책은 AD FS 감사를 사용하지 않도록 설정할 수 있습니다. AD FS 감사를 사용하지 않도록 설정하면 로그인 활동에 대한 사용량 현황 분석을 사용할 수 없습니다. AD FS 감사를 사용하지 않도록 설정하는 그룹 정책이 없는지 확인해야 합니다.
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>동기화용 Azure AD Connect Health Agent 설치

동기화용 Azure AD Connect Health Agent는 Azure AD Connect의 최신 빌드에 자동으로 설치됩니다. Azure AD Connect를 동기화에 사용하려면 Azure AD Connect의 최신 버전을 다운로드하여 설치해야 합니다. 최신 버전은 [여기](https://www.microsoft.com/download/details.aspx?id=47594)에서 다운로드할 수 있습니다.

에이전트가 설치되었는지 확인하려면 서버에서 다음 서비스를 살펴봅니다. 구성을 완료했다면 해당 서비스가 실행 중이어야 합니다. 그렇지 않으면 구성이 완료될 때까지 해당 서비스가 중지됩니다.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![동기화에 대한 Azure AD Connect Health 확인](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Azure AD Connect Health를 사용하려면 Azure AD Premium이 필요합니다. Azure AD Premium이 없으면 Azure Portal에서 구성을 완료할 수 없습니다. 자세한 내용은 [요구 사항 페이지](how-to-connect-health-agent-install.md#requirements)를 참조하세요.
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>동기화에 대한 Azure AD Connect Health 수동 등록

Azure AD Connect를 성공적으로 설치한 후 동기화에 대한 Azure AD Connect Health 등록이 실패할 경우 다음 PowerShell 명령을 사용하여 수동으로 에이전트를 등록할 수 있습니다.

> [!IMPORTANT]
> 이 PowerShell 명령은 Azure AD Connect를 설치한 후 에이전트 등록에 실패하는 경우에만 필요합니다.
>
>

다음 PowerShell 명령은 Azure AD Connect를 성공적으로 설치 및 구성한 후에도 Health Agent 등록이 실패하는 경우에만 필요합니다. Azure AD Connect Health 서비스는 에이전트가 성공적으로 등록된 후에 시작됩니다.

다음 PowerShell 명령을 사용하여 동기화에 대한 Azure AD Connect Health 에이전트를 수동으로 등록할 수 있습니다.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

이 명령은 다음 매개 변수를 사용합니다.

* AttributeFiltering: Azure AD Connect가 기본 특성 집합을 동기화하지 않으며 필터링된 특성 집합을 사용하도록 사용자 지정된 경우에는 $true(기본값)입니다. 그렇지 않으면 $false입니다.
* StagingMode: Azure AD Connect 서버가 준비 모드에 있지 않으면 $false(기본값)이고, 서버가 준비 모드에 있도록 구성되면 $true입니다.

인증을 요구하는 메시지가 표시되면 Azure AD Connect 구성에 사용한 것과 동일한 전역 관리자 계정(예: admin@domain.onmicrosoft.com)을 사용해야 합니다.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>AD DS용 Azure AD Connect Health 에이전트 설치

에이전트 설치를 시작하려면 다운로드한 .exe 파일을 두 번 클릭합니다. 첫 번째 화면에서 설치를 클릭합니다.

![AD DS 설치를 위한 Azure AD Connect Health 에이전트 시작](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

설치가 완료되면 지금 구성을 클릭합니다.

![AD DS 설치를 위한 Azure AD Connect Health 에이전트 마침](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

명령 프롬프트가 실행된 다음 PowerShell에서 Register-AzureADConnectHealthADDSAgent가 실행됩니다. Azure에 로그인하라는 메시지가 표시되면, 로그인을 진행합니다.

![Azure AD Connect Health 에이전트 AD DS 로그인 구성](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

로그인한 후 PowerShell이 계속됩니다. 완료된 후에 PowerShell을 닫으면 구성이 완료됩니다.

이때 서비스가 자동으로 시작되면서 에이전트에 모니터링과 데이터 수집이 허용됩니다. 이전 섹션에 설명된 필수 조건이 모두 충족되지 않으면 PowerShell 창에 경고가 표시됩니다. 에이전트를 설치하기 전에 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 완료해야 합니다. 다음 스크린샷은 이러한 오류의 예입니다.

![AD DS 스크립트 구성 Azure AD Connect Health 에이전트](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

에이전트가 설치되었는지 확인하려면 도메인 컨트롤러에서 다음 서비스를 살펴봅니다.

* Azure AD Connect Health AD DS Insights 서비스
* Azure AD Connect Health AD DS 모니터링 서비스

구성을 완료했다면 이러한 서비스가 이미 실행 중이어야 합니다. 그렇지 않으면 구성이 완료될 때까지 해당 서비스가 중지됩니다.

![AD DS 서비스용 Azure AD Connect Health 에이전트](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>여러 서버에 빠른 에이전트 설치

1. Azure AD에서 암호를 사용 하 여 사용자 계정을 만듭니다.
2. 포털을 통해 Azure AD Connect Health에서이 로컬 AAD 계정의 **소유자** 역할을 할당 합니다. [여기](how-to-connect-health-operations.md#manage-access-with-azure-rbac)의 단계를 따르세요. 모든 서비스 인스턴스에 역할을 할당 합니다. 
3. 설치를 위해 로컬 도메인 컨트롤러에서 .exe MSI 파일을 다운로드 합니다.
4. 다음 스크립트를 실행 하 여 등록 합니다. 매개 변수를 만든 새 사용자 계정 및 암호를 바꿉니다. 

```powershell
AdHealthAddsAgentSetup.exe /quiet
Start-Sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -Credential $myCreds

```

1. 완료 되 면 다음 중 하나 이상을 수행 하 여 로컬 계정에 대 한 액세스 권한을 제거할 수 있습니다. 
    * AAD Connect Health에 대 한 로컬 계정에 대 한 역할 할당을 제거 합니다.
    * 로컬 계정에 대 한 암호를 회전 합니다. 
    * AAD 로컬 계정 사용 안 함
    * AAD 로컬 계정 삭제  

## <a name="agent-registration-using-powershell"></a>PowerShell을 사용한 에이전트 등록

적합한 에이전트 setup.exe를 설치한 후 역할에 따라 다음 PowerShell 명령을 사용하여 에이전트 등록 단계를 수행할 수 있습니다. PowerShell 창을 열고 적합한 명령을 실행합니다.

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

이 명령은 비대화형 방식으로 또는 서버-코어 시스템에서 등록을 완료하기 위해 "자격 증명"을 매개 변수로 수락합니다.
* 자격 증명은 매개 변수로 전달되는 PowerShell 변수에서 캡처할 수 있습니다.
* 에이전트를 등록할 권한이 있고 MFA를 사용하도록 설정되어 있지 않은 Azure AD ID를 제공할 수 있습니다.
* 기본적으로 전역 관리자는 에이전트 등록을 수행할 권한이 있습니다. 기타 낮은 권한의 ID가 이 단계를 수행하도록 허용할 수도 있습니다. Azure [RBAC (역할 기반 액세스 제어)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)에 대해 자세히 알아보세요.

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>HTTP 프록시를 사용하도록 Azure AD Connect Health Agent 구성

HTTP 프록시와 작동하도록 Azure AD Connect Health Agent를 구성할 수 있습니다.

> [!NOTE]
> * 에이전트가 Microsoft Windows HTTP Services 대신 System.Net을 사용하여 웹 요청을 수행하므로 “Netsh WinHttp set ProxyServerAddress” 사용이 지원되지 않습니다.
> * 구성된 Http 프록시 주소는 암호화된 Https 메시지를 통과하는 데 사용됩니다.
> * 인증된 프록시(HTTPBasic 사용)는 지원되지 않습니다.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Health Agent 프록시 구성 변경

HTTP 프록시를 사용하도록 Azure AD Connect Health Agent를 구성하는 옵션은 다음과 같습니다.

> [!NOTE]
> 프록시 설정이 업데이트되도록 하려면 모든 Azure AD Connect Health Agent 서비스를 다시 시작해야 합니다. 다음 명령을 실행합니다.<br />
> 서비스 다시 시작 AzureADConnectHealth *
>
>

#### <a name="import-existing-proxy-settings"></a>기존 프록시 설정 가져오기

##### <a name="import-from-internet-explorer"></a>Internet Explorer에서 가져오기

Internet Explorer HTTP 프록시 설정을 가져와서 Azure AD Connect Health Agent에서 사용되도록 할 수 있습니다. Health Agent를 실행하는 각 서버에 다음 PowerShell 명령을 실행합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

##### <a name="import-from-winhttp"></a>WinHTTP에서 가져오기

WinHTTP 프록시 설정을 가져와서 Azure AD Connect Health Agent에서 사용되도록 할 수 있습니다. Health Agent를 실행하는 각 서버에 다음 PowerShell 명령을 실행합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>수동으로 프록시 주소 지정

다음 PowerShell 명령을 실행하면 Health Agent를 실행하는 각 서버에 프록시 서버를 수동으로 설정할 수 있습니다.

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

예: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443*

* "address"는 DNS 확인 가능 서버 이름 또는 IPv4 주소일 수 있습니다.
* "port"는 생략할 수 있습니다. 생략하면 443이 기본 포트로 선택됩니다.

#### <a name="clear-existing-proxy-configuration"></a>기존 프록시 구성 지우기

다음 명령을 실행하여 기존 프록시 구성을 지울 수 있습니다.

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>현재 프록시 설정 읽기

다음 명령을 실행하여 현재 구성된 프록시 설정을 읽을 수 있습니다.

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health 서비스에 대한 테스트 연결

Azure AD Connect Health 에이전트에서 Azure AD Connect Health 서비스와의 연결이 끊어지게 하는 문제가 발생할 수 있습니다. 여기에는 네트워크 문제, 권한 문제 또는 기타 다양한 이유가 포함됩니다.

에이전트에서 2시간 넘게 Azure AD Connect Health 서비스에 데이터를 보낼 수 없으면 "상태 서비스 데이터가 최신 상태가 아닙니다."라는 경고가 포털에 표시됩니다. 다음 PowerShell 명령을 실행하면 영향을 받는 Azure AD Connect Health Agent가 Azure AD Connect Health 서비스에 데이터를 업로드할 수 있는지 확인할 수 있습니다.

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

역할 매개 변수가 현재 다음 값을 사용합니다.

* ADFS
* 동기화
* ADDS

> [!NOTE]
> 연결 도구를 사용하려면 먼저 에이전트 등록을 완료해야 합니다. 에이전트 등록을 완료할 수 없는 경우 Azure AD Connect Health에 대한 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 모두 충족했는지 확인합니다. 이 연결 테스트는 에이전트를 등록하는 동안 기본적으로 수행됩니다.
>
>

## <a name="related-links"></a>관련 링크

* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 작업](how-to-connect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)
* [동기화에 Azure AD Connect Health 사용](how-to-connect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health 버전 기록](reference-connect-health-version-history.md)
