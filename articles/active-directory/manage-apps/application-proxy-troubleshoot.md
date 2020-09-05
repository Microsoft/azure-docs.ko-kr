---
title: 애플리케이션 프록시 문제 해결 | Microsoft Docs
description: Azure AD 애플리케이션 프록시에서 오류를 해결하는 방법을 설명합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 413cfe4f3aed446ad26a210b4faa452c4f624685
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640857"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>애플리케이션 프록시 문제 및 오류 메시지 문제 해결

응용 프로그램 프록시 문제를 해결할 때 응용 프로그램 프록시 커넥터가 올바르게 구성 되었는지 확인 하기 위해 문제 해결 흐름, [응용 프로그램 프록시 커넥터 문제 디버깅](application-proxy-debug-connectors.md)을 검토 하는 것으로 시작 하는 것이 좋습니다. 응용 프로그램에 연결 하는 데 문제가 여전히 발생 하는 경우 [응용 프로그램 프록시 응용 프로그램 디버그 문제](application-proxy-debug-apps.md)에서 문제 해결 흐름을 따릅니다.

게시된 애플리케이션에 액세스할 때나 애플리케이션을 게시할 때 오류가 발생한다면 다음 옵션을 확인하여 Microsoft Azure AD 애플리케이션 프록시가 올바르게 작동하는지 확인합니다.

* Windows 서비스 콘솔을 엽니다. **MICROSOFT AAD 응용 프로그램 프록시 커넥터** 서비스가 사용 하도록 설정 되어 있고 실행 중인지 확인 합니다. 또한 다음 그림에 표시된 것처럼 애플리케이션 프록시 서비스 속성 페이지에서 확인할 수도 있습니다.  
  ![Microsoft AAD 애플리케이션 프록시 커넥터 속성 창 스크린샷](./media/application-proxy-troubleshoot/connectorproperties.png)
* 이벤트 뷰어를 열고 응용 프로그램 **및 서비스 로그**  >  **Microsoft**  >  **AadApplicationProxy**  >  **connector**  >  **Admin**에서 응용 프로그램 프록시 커넥터 이벤트를 찾습니다.
* 필요한 경우 [애플리케이션 프록시 커넥터 세션 로그를 켜면](application-proxy-connectors.md#under-the-hood) 더 자세한 로그를 볼 수 있습니다.

## <a name="the-page-is-not-rendered-correctly"></a>페이지가 제대로 렌더링되지 않습니다.
특정 오류 메시지를 수신하지 않고도 애플리케이션 렌더링 또는 기능이 제대로 이뤄지지 않는 문제가 있을 수 있습니다. 문서 경로를 게시했지만 애플리케이션에 해당 경로 밖에 있는 콘텐츠가 필요한 경우에 이 문제가 발생할 수 있습니다.

예를 들어, `https://yourapp/app` 경로를 게시하는 한편 애플리케이션에서 `https://yourapp/media`의 이미지를 호출하는 경우 이미지가 렌더링되지 않습니다. 모든 관련 콘텐츠를 포함해야 하는 가장 높은 수준의 경로를 사용하여 애플리케이션을 게시하는지 확인합니다. 이 예에서는 `http://yourapp/`입니다.

## <a name="connector-errors"></a>커넥터 오류

커넥터 마법사를 설치하는 동안 등록에 실패하는 경우 실패한 이유를 확인하는 두 가지 방법이 있습니다. **Applications and Services Logs\Microsoft\AadApplicationProxy\Connector\Admin**에서 이벤트 로그를 확인하거나 다음 Windows PowerShell 명령을 실행합니다.

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

이벤트 로그에서 커넥터 오류를 찾으면 일반적인 오류 테이블을 사용하여 문제를 해결합니다.

| Error | 권장되는 단계 |
| ----- | ----------------- |
| 커넥터를 등록하지 못함: Azure 관리 포털에서 애플리케이션 프록시를 활성화했으며 Active Directory 사용자 이름과 암호를 올바르게 입력했는지 확인합니다. 오류: '하나 이상의 오류가 발생했습니다.' | Azure AD에 로그인하지 않고 등록 창을 닫은 경우 커넥터 마법사를 다시 실행하고 커넥터를 등록합니다. <br><br> 등록 창이 열리고 로그인을 허용 하지 않고 즉시 닫히면이 오류가 발생할 수 있습니다. 시스템에 네트워킹 오류가 있을 때 이 오류가 발생합니다. 브라우저에서 공용 웹 사이트로 연결할 수 있는지, 그리고 [응용 프로그램 프록시 필수 구성 요소](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)에 지정 된 대로 포트가 열려 있는지 확인 합니다. |
| 등록 창에 명확한 오류가 표시됩니다. 설치를 진행할 수 없습니다. | 이 오류가 표시되고 창이 닫힌 경우 잘못된 사용자 이름 또는 암호를 입력했습니다. 다시 시도하세요. |
| 커넥터를 등록하지 못함: Azure 관리 포털에서 애플리케이션 프록시를 활성화했으며 Active Directory 사용자 이름과 암호를 올바르게 입력했는지 확인합니다. 오류: 'AADSTS50059: 테넌트를 식별하는 정보가 요청에서 찾을 수 없거나 제공된 자격 증명으로 암시되지 않으며, 서비스 주체 URI에 의한 검색이 실패했습니다. | 액세스 하려는 디렉터리의 조직 ID에 속하는 도메인이 아닌 Microsoft 계정을 사용 하 여 로그인 하려고 합니다. 관리자가 테넌트 도메인과 동일한 도메인 이름의 일부인지 확인하세요. 예를 들어, Azure AD 도메인이 contoso.com이라면 관리자는 admin@contoso.com여야 합니다. |
| PowerShell 스크립트의 실행을 위한 현재 실행 정책을 검색하지 못했습니다. | 커넥터 설치에 실패 하는 경우 PowerShell 실행 정책이 비활성화 되지 않았는지 확인 합니다. <br><br>1. 그룹 정책 편집기를 엽니다.<br>2. **컴퓨터 구성**  >  **관리 템플릿**  >  **windows 구성 요소**  >  **windows PowerShell** 로 이동 하 고 **스크립트 실행 켜기**를 두 번 클릭 합니다.<br>3. 실행 정책은 **구성 안 함** 또는 **사용**으로 설정할 수 있습니다. **사용**으로 설정했다면 옵션에 있는 실행 정책을 **로컬 스크립트 및 원격 서명된 스크립트 허용** 또는 **모든 스크립트 허용**으로 설정했는지 확인합니다. |
| 커넥터에서 구성을 다운로드하지 못했습니다. | 인증에 사용되는 커넥터의 클라이언트 인증서가 만료되었습니다. 이것은 커넥터가 프록시 뒤에 설치되어 있는 경우 발생할 수도 있습니다. 이 경우 커넥터는 인터넷에 액세스할 수 없으며 원격 사용자에게 애플리케이션을 제공할 수 없게 됩니다. `Register-AppProxyConnector` Windows PowerShell에서 cmdlet을 사용하여 트러스트를 수동으로 갱신합니다. 커넥터가 프록시 뒤에 있는 경우 커넥터 계정 "네트워크 서비스" 및 "로컬 시스템"에 인터넷 액세스 권한을 부여해야 합니다. 이것은 프록시에 대한 액세스 권한을 부여하거나 프록시를 우회하도록 설정하여 수행할 수 있습니다. |
| 커넥터 등록 실패: 커넥터를 등록 하려면 Active Directory의 응용 프로그램 관리자 여야 합니다. 오류: '등록 요청이 거부되었습니다.' | 로그인하고자 하는 별칭이 이 도메인에서 관리자가 아닙니다. 커넥터는 항상 사용자의 도메인을 소유하는 디렉터리에 대해 설치됩니다. 로그인을 시도 하는 관리자 계정에 Azure AD 테 넌 트에 대 한 응용 프로그램 관리자 권한이 있는지 확인 합니다. |
| 네트워크 문제로 인해 커넥터에서 서비스에 연결할 수 없습니다. 커넥터에서 다음 URL에 액세스 하려고 했습니다. | 커넥터에서 응용 프로그램 프록시 클라우드 서비스에 연결할 수 없습니다. 이는 연결을 차단 하는 방화벽 규칙이 있는 경우에 발생할 수 있습니다. [응용 프로그램 프록시 필수 구성 요소](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)에 나열 된 올바른 포트와 url에 대 한 액세스를 허용 했는지 확인 합니다. |

## <a name="kerberos-errors"></a>Kerberos 오류

이 테이블은 Kerberos 설정 및 구성에서 발생한 일반적인 오류에 대해 설명하고 해결하기 위한 제안을 포함합니다.

| Error | 권장되는 단계 |
| ----- | ----------------- |
| PowerShell 스크립트의 실행을 위한 현재 실행 정책을 검색하지 못했습니다. | 커넥터 설치에 실패한다면 PowerShell 실행 정책이 비활성화되어 있지 않은지 확인하세요.<br><br>1. 그룹 정책 편집기를 엽니다.<br>2. **컴퓨터 구성**  >  **관리 템플릿**  >  **windows 구성 요소**  >  **windows PowerShell** 로 이동 하 고 **스크립트 실행 켜기**를 두 번 클릭 합니다.<br>3. 실행 정책은 **구성 안 함** 또는 **사용**으로 설정할 수 있습니다. **사용**으로 설정했다면 옵션에 있는 실행 정책을 **로컬 스크립트 및 원격 서명된 스크립트 허용** 또는 **모든 스크립트 허용**으로 설정했는지 확인합니다. |
| 12008-Azure AD가 백 엔드 서버에 허용되는 Kerberos 인증 시도의 최대 횟수를 초과합니다. | 이 오류는 Azure AD와 백 엔드 애플리케이션 서버 간의 잘못된 구성 또는 두 머신 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 백 엔드 서버가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 애플리케이션 서버가 올바르게 구성되어 있는지 확인합니다. Azure AD 및 백 엔드 애플리케이션 서버상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. |
| 13016 - 에지 토큰이나 액세스 토큰에 UPN이 없기 때문에 Azure AD에서 사용자를 대신해서 Kerberos 티켓을 검색할 수 없습니다. | STS 구성에 문제가 있습니다. STS에서 UPN 클레임 구성을 수정합니다. |
| 13019 - 다음과 같은 일반적인 API 오류로 인해 Azure AD에서 사용자를 대신해서 Kerberos 티켓을 검색할 수 없습니다. | 이 이벤트는 Azure AD와 도메인 컨트롤러 서버 간의 잘못된 구성 또는 두 컴퓨터 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 도메인 컨트롤러가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 애플리케이션 서버, 특히 SPN 구성이 올바르게 구성되어 있는지 확인합니다. Azure AD가 도메인 컨트롤러와 동일한 도메인에 도메인 가입되어 있는지 확인하여 도메인 컨트롤러가 Azure AD와 신뢰를 구축할 수 있게 하세요. Azure AD 및 도메인 컨트롤러상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. |
| 13020 - 백 엔드 서버 SPN이 정의되어 있지 않기 때문에 Azure AD에서 사용자를 대신해서 Kerberos 티켓을 검색할 수 없습니다. | 이 이벤트는 Azure AD와 도메인 컨트롤러 서버 간의 잘못된 구성 또는 두 컴퓨터 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 도메인 컨트롤러가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 애플리케이션 서버, 특히 SPN 구성이 올바르게 구성되어 있는지 확인합니다. Azure AD가 도메인 컨트롤러와 동일한 도메인에 도메인 가입되어 있는지 확인하여 도메인 컨트롤러가 Azure AD와 신뢰를 구축할 수 있게 하세요. Azure AD 및 도메인 컨트롤러상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. |
| 13022 - 백 엔드 서버가 HTTP 401 오류와 함께 Kerberos 인증 시도에 응답하기 때문에 Azure AD에서 사용자를 인증할 수 없습니다. | 이 이벤트는 Azure AD와 백 엔드 애플리케이션 서버 간의 잘못된 구성 또는 두 머신 모두에서 날짜 및 시간 구성에 문제가 있음을 나타냅니다. 백 엔드 서버가 Azure AD에서 생성한 Kerberos 티켓을 거부했습니다. Azure AD 및 백 엔드 애플리케이션 서버가 올바르게 구성되어 있는지 확인합니다. Azure AD 및 백 엔드 애플리케이션 서버상의 시간 및 날짜 구성이 동기화되었는지 확인합니다. 자세한 내용은 [애플리케이션 프록시에 대한 Kerberos 제한 위임 구성 문제 해결](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)을 참조하세요.  |

## <a name="end-user-errors"></a>최종 사용자 오류

이 목록에서는 최종 사용자가 앱에 액세스하는 데 실패한 경우 발생할 수 있는 오류에 대해 설명합니다. 

| Error | 권장되는 단계 |
| ----- | ----------------- |
| 웹 사이트에서 페이지를 표시할 수 없습니다. | 애플리케이션이 IWA 애플리케이션인 경우 게시된 앱에 사용자가 액세스를 시도하는 경우 이 오류가 발생할 수 있습니다. 이 애플리케이션에 대해 정의된 SPN이 올바르지 않을 수 있습니다. IWA 앱의 경우 이 애플리케이션에 대해 구성된 SPN이 올바른지 확인합니다. |
| 웹 사이트에서 페이지를 표시할 수 없습니다. | 애플리케이션이 OWA 애플리케이션인 경우 게시된 앱에 사용자가 액세스를 시도하는 경우 이 오류가 발생할 수 있습니다. 이는 다음 중 하나 때문일 수 있습니다.<br><li>이 애플리케이션에 대해 정의된 SPN이 올바르지 않습니다. 이 애플리케이션에 대해 구성된 SPN이 올바른지 확인합니다.</li><li>이 애플리케이션에 액세스하려고 하는 사용자가 적절한 기업 계정이 아닌 Microsoft 계정을 사용하거나 사용자가 게스트 사용자입니다. 사용자가 게시된 애플리케이션의 도메인과 일치하는 기업 계정을 사용하여 로그인하는지 확인합니다. Microsoft 계정 사용자 및 게스트가 IWA 애플리케이션에 액세스할 수 없습니다.</li><li>응용 프로그램에 액세스 하려는 사용자가 온-프레미스 쪽에서이 응용 프로그램에 대해 제대로 정의 되지 않았습니다. 이 사용자에 게 온-프레미스 컴퓨터에서이 백 엔드 응용 프로그램에 대해 정의 된 적절 한 사용 권한이 있는지 확인 합니다. |
| 이 회사 앱에 액세스할 수 없습니다. 이 애플리케이션에 액세스할 수 있는 권한이 없습니다. 권한 부여에 실패했습니다. 이 애플리케이션에 액세스할 수 있는 사용자를 할당할 수 있는지 확인합니다. | 사용자가 회사 계정 대신 Microsoft 계정을 사용 하 여 로그인 하는 경우 게시 한 앱에 액세스 하려고 하면이 오류가 발생할 수 있습니다. 게스트 사용자에게 이 오류가 발생할 수도 있습니다. Microsoft 계정 사용자 및 게스트가 IWA 애플리케이션에 액세스할 수 없습니다. 사용자가 게시된 애플리케이션의 도메인과 일치하는 기업 계정을 사용하여 로그인하는지 확인합니다.<br><br>이 애플리케이션에 사용자를 할당하지 않았을 수 있습니다. **애플리케이션** 탭으로 이동하고 **사용자 및 그룹**에서 이 사용자나 사용자 그룹을 이 애플리케이션에 할당합니다. |
| 현재 이 회사 앱에 액세스할 수 없습니다. 나중에 다시 시도하세요. 커넥터 제한 시간이 초과되었습니다. | 온-프레미스 쪽에서이 응용 프로그램에 대해 올바르게 정의 되지 않은 경우 사용자가 게시 한 앱에 액세스 하려고 하면이 오류가 발생할 수 있습니다. 사용자에 게 온-프레미스 컴퓨터에서이 백 엔드 응용 프로그램에 대해 정의 된 적절 한 사용 권한이 있는지 확인 합니다. |
| 이 회사 앱에 액세스할 수 없습니다. 이 애플리케이션에 액세스할 수 있는 권한이 없습니다. 권한 부여에 실패했습니다. 사용자에 게 Azure Active Directory Premium에 대 한 라이선스가 있는지 확인 합니다. | 구독자의 관리자가 프리미엄 라이선스를 사용 하 여 명시적으로 할당 하지 않은 경우 사용자가 게시 한 앱에 액세스 하려고 하면이 오류가 발생할 수 있습니다. 구독자의 Active Directory **라이선스** 탭으로 이동 하 여이 사용자 또는 사용자 그룹에 프리미엄 라이선스가 할당 되었는지 확인 합니다. |
| 지정 된 호스트 이름을 가진 서버를 찾을 수 없습니다. | 응용 프로그램의 사용자 지정 도메인이 올바르게 구성 되지 않은 경우 사용자가 게시 한 앱에 액세스 하려고 하면이 오류가 발생할 수 있습니다. [Azure에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md) 의 단계를 수행 하 여 도메인에 대 한 인증서를 업로드 하 고 DNS 레코드를 올바르게 구성 했는지 확인 AD 응용 프로그램 프록시 |
|사용 권한 없음:이 회사 앱에 액세스할 수 없거나 사용자에 게 권한을 부여할 수 없습니다. 사용자가 온-프레미스 AD에 정의 되어 있고 사용자가 온-프레미스 AD에서 앱에 액세스할 수 있는지 확인 합니다. | 권한 부여 정보에 액세스 하는 데 문제가 있을 수 있습니다. [일부 응용 프로그램 및 api는 계정 개체에 대 한 권한 부여 정보에 액세스 해야]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information)합니다 .를 참조 하세요. 간단히 말해서 해결할 앱 프록시 커넥터 컴퓨터 계정을 "Windows 인증 액세스 그룹" builtin 도메인 그룹에 추가 합니다. |

## <a name="my-error-wasnt-listed-here"></a>내 오류가 여기에 나열되지 않았습니다.

Azure AD 애플리케이션 프록시에 이 문제 해결 가이드에 나열되지 않은 오류 또는 문제가 발생한 경우 알려주시기 바랍니다. 발생한 오류의 세부 정보를 [피드백 팀](mailto:aadapfeedback@microsoft.com)에게 전자 메일로 보내 주세요.

## <a name="see-also"></a>참고 항목
* [Azure Active Directory에 대한 애플리케이션 프록시 사용](application-proxy-add-on-premises-application.md)
* [애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)
* [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
* [조건부 액세스 사용](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
