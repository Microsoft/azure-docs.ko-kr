---
title: Azure MFA 서버 업그레이드-Azure Active Directory
description: Azure Multi-factor Authentication 서버를 최신 버전으로 업그레이드하는 단계 및 지침입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 455df5d2bf6003dd06a11f93d3e0d70ab9e11ee2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919525"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>최신 Azure Multi-Factor Authentication 서버로 업그레이드

이 문서에서는 Azure MFA(Multi-factor Authentication) 서버 v6.0 이상을 업그레이드하는 프로세스를 단계별로 안내합니다. 이전 버전의 PhoneFactor Agent를 업그레이드해야 하는 경우 [PhoneFactor Agent를 Azure Multi-factor Authentication 서버로 업그레이드](howto-mfaserver-deploy-upgrade-pf.md)를 참조하세요.

v6.x 이상에서 v7.x 또는 최신 버전으로 업그레이드하는 경우 모든 구성 요소가 .NET 2.0에서 .NET 4.5로 변경됩니다. 모든 구성 요소에는 Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트 1 이상이 필요합니다. 이러한 구성 요소의 x86 및 x64 버전이 아직 설치되지 않았으면 MFA 서버 설치 관리자가 설치합니다. 사용자 포털 및 모바일 앱 웹 서비스가 별도 서버에서 실행되는 경우 해당 구성 요소를 업그레이드하기 전에 해당 패키지를 설치해야 합니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/)에서 최신 Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트를 검색할 수 있습니다. 

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

업그레이드 단계 한 눈에 보기:

* Azure MFA 서버 업그레이드(하위 다음에 마스터)
* 사용자 포털 인스턴스 업그레이드
* AD FS 어댑터 인스턴스 업그레이드

## <a name="upgrade-azure-mfa-server"></a>Azure MFA 서버 업그레이드

1. [Azure Multi-Factor Authentication 서버 다운로드](howto-mfaserver-deploy.md#download-the-mfa-server)의 지침을 사용하여 최신 버전의 Azure MFA 서버 설치 관리자를 다운로드합니다.
2. C:\Program Files\multi-factor Authentication Server\Data\PhoneFactor.pfdata(기본 설치 위치로 가정)에 있는 MFA 서버 데이터 파일의 백업을 마스터 MFA 서버에 만듭니다.
3. 고가용성을 위해 여러 서버를 실행하는 경우 업그레이드하려는 MFA 서버에 트래픽을 더 이상 전송하지 않도록 MFA 서버에서 인증을 받는 클라이언트 시스템을 변경합니다. 부하 분산 장치를 사용하는 경우 부하 분산 장치에서 하위 MFA 서버를 제거하고 업그레이드한 다음, 해당 서버를 팜에 다시 추가합니다.
4. 각 MFA 서버에서 새 설치 관리자를 실행합니다. 하위 서버는 마스터에 의해 복제되는 이전 데이터 파일을 읽을 수 있으므로 먼저 업그레이드합니다.

   > [!NOTE]
   > 서버를 업그레이드 하는 경우 다른 MFA 서버와의 부하 분산 또는 트래픽 공유에서 제거 해야 합니다.
   >
   > 설치 관리자를 실행하기 전에 현재 MFA 서버를 제거할 필요는 없습니다. 설치 관리자는 전체 업그레이드를 수행합니다. 설치 경로는 이전 설치의 레지스트리에서 선택되므로 동일한 위치(예: C:\Program Files\PhoneFactor)에 설치됩니다.
  
5. Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트 패키지를 설치할 것인지 묻는 메시지가 표시되면 수락합니다. x86 및 x64 버전 패키지가 모두 설치됩니다.
6. 웹 서비스 SDK를 사용하는 경우 새 웹 서비스 SDK를 설치하라는 메시지가 표시됩니다. 새 웹 서비스 SDK를 설치하면 가상 디렉터리 이름이 이전에 설치된 가상 디렉터리(예: MultiFactorAuthWebServiceSdk)와 일치하는지 확인합니다.
7. 모든 하위 서버에서 이 단계를 반복합니다. 하위 서버 중 하나를 새 마스터 서버로 승격한 다음 이전 마스터 서버를 업그레이드합니다.

## <a name="upgrade-the-user-portal"></a>사용자 포털 업그레이드

이 섹션으로 이동하려면 MFA 서버 업그레이드를 완료해야 합니다.

1. 사용자 포털 설치 위치의 가상 디렉터리(예: C:\inetpub\wwwroot\MultiFactorAuth)에 있는 web.config 파일의 백업을 만듭니다. 기본 테마를 변경한 경우 App_Themes\Default 폴더 백업도 만듭니다. 기본 테마를 변경하는 것보다는 기본 폴더의 복사본을 만든 후 새 테마를 만드는 것이 더 좋습니다.
2. 사용자 포털이 다른 MFA 서버 구성 요소가 있는 동일한 서버에서 실행되는 경우 MFA 서버 설치는 사용자 포털을 업데이트할 것인지 묻는 메시지를 표시합니다. 메시지를 수락하고 사용자 포털 업데이트를 설치합니다. 가상 디렉터리 이름이 이전에 설치한 가상 디렉터리(예: MultiFactorAuth)와 일치하는지 확인합니다.
3. 사용자 포털이 자체 서버에 있는 경우 MFA 서버 중 하나의 설치 위치에서 MultiFactorAuthenticationUserPortalSetup64.msi 파일을 복사한 후 사용자 포털 웹 서버에 저장합니다. 설치 관리자를 실행합니다.

   “Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트 1 이상이 필요하다”는 오류가 발생하는 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/download/)에서 최신 업데이트 패키지를 다운로드하여 설치합니다. x86 및 x64 버전을 둘 다 설치합니다.

4. 업데이트된 사용자 포털 소프트웨어를 설치한 후에 1단계에서 백업한 web.config 파일을 새 web.config 파일과 비교합니다. 새 web.config에 새 특성이 없으면 백업 web.config를 가상 디렉터리에 복사하여 새 파일을 덮어씁니다. 또 다른 옵션은 백업 파일에서 appSettings 값 및 웹 서비스 SDK URL을 복사한 후 새 web.config에 붙여 넣습니다.

여러 서버에 사용자 포털에 있는 경우 모든 서버에서 설치를 반복합니다.

## <a name="upgrade-the-mobile-app-web-service"></a>모바일 앱 웹 서비스 업그레이드

> [!NOTE]
> 8.0 보다 오래 된 버전의 Azure MFA 서버에서 8.0 이상으로 업그레이드 하는 경우 업그레이드 후 모바일 앱 웹 서비스를 제거할 수 있습니다.

## <a name="upgrade-the-ad-fs-adapters"></a>AD FS 어댑터 업그레이드

이 섹션으로 이동하려면 MFA 서버 및 사용자 포털의 업그레이드를 완료해야 합니다.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>MFA가 AD FS 이외의 다른 서버에서 실행되는 경우

다음 지침은 AD FS 서버와는 별도로 Multi-factor Authentication 서버를 실행하는 경우에만 적용됩니다. 두 서비스가 동일한 서버에서 실행되는 경우 이 섹션을 건너뛰고 설치 단계로 이동합니다. 

1. AD FS에서 등록된 MultiFactorAuthenticationAdfsAdapter.config 파일의 복사본을 저장하거나 PowerShell 명령 `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`을 사용하여 구성을 내보냅니다. 어댑터 이름은 이전에 설치한 버전에 따라 "WindowsAzureMultiFactorAuthentication" 또는 "AzureMfaServerAuthentication" 중 하나입니다.
2. MFA 서버 설치 위치의 다음 파일을 AD FS 서버로 복사합니다.

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. `Register-AdfsAuthenticationProvider` 명령의 끝에 `-ConfigurationFilePath [path]`를 추가하여 Register-MultiFactorAuthenticationAdfsAdapter.ps1 스크립트를 편집합니다. *[path]* 를 MultiFactorAuthenticationAdfsAdapter.config 파일에 대한 전체 경로 또는 이전 단계에서 내보낸 구성 파일로 바꿉니다.

   새 MultiFactorAuthenticationAdfsAdapter.config의 특성이 이전 구성 파일과 일치하는지 확인합니다. 새 버전에서 특성이 추가되었거나 제거된 경우 이전 구성 파일에서 새 파일로 특성 값을 복사하거나 새 구성과 일치하도록 이전 구성 파일을 수정합니다.

### <a name="install-new-ad-fs-adapters"></a>새 AD FS 어댑터 설치

> [!IMPORTANT]
> 사용자는 이 섹션의 3-8단계 동안 2단계 확인을 수행할 필요가 없습니다. 여러 개의 클러스터에 AD FS가 구성되어 있으면 가동 중단을 피하기 위해 다른 클러스터와는 별도로 팜의 각 클러스터를 제거, 업그레이드 및 복원할 수 있습니다.

1. 팜에서 일부 AD FS 서버를 제거합니다. 다른 서버가 여전히 실행되는 동안 이러한 서버를 업데이트합니다.
2. AD FS 팜에서 제거된 각 서버에 새 AD FS 어댑터를 설치합니다. MFA 서버가 각 AD FS 서버에 설치된 경우 MFA 서버 관리자 UX를 통해 업데이트할 수 있습니다. 그렇지 않으면 MultiFactorAuthenticationAdfsAdapterSetup64.msi를 실행하여 업데이트합니다.

   “Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트 1 이상이 필요하다”는 오류가 발생하는 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/download/)에서 최신 업데이트 패키지를 다운로드하여 설치합니다. x86 및 x64 버전을 둘 다 설치합니다.

3. **AD FS**  >  **인증 정책**  >  **전역 다단계 인증 정책 편집**으로 이동 합니다. **WindowsAzureMultiFactorAuthentication** 또는 **AzureMFAServerAuthentication**(현재 설치된 버전에 따라 다름)을 선택 취소합니다.

   이 단계가 완료되면 8단계를 완료할 때까지 이 AD FS 클러스터에서 MFA 서버를 통한 2단계 인증을 사용할 수 없습니다.

4. Unregister-multifactorauthenticationadfsadapter.ps1 PowerShell 스크립트를 실행하여 이전 버전의 AD FS 어댑터를 등록 취소합니다. *-Name* 매개 변수 ("WindowsAzureMultiFactorAuthentication" 또는 "AzureMFAServerAuthentication")가 3 단계에 표시 된 이름과 일치 하는지 확인 합니다. 하나의 중앙 구성이 있으므로 동일한 AD FS 클러스터의 모든 서버도 마찬가지입니다.
5. Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell 스크립트를 실행하여 새 AD FS 어댑터를 등록합니다. 하나의 중앙 구성이 있으므로 동일한 AD FS 클러스터의 모든 서버도 마찬가지입니다.
6. AD FS 팜에서 제거된 각 서버에서 AD FS 서비스를 다시 시작합니다.
7. 업데이트된 서버를 AD FS 팜에 다시 추가하고 팜에서 다른 서버를 제거합니다.
8. **AD FS**  >  **인증 정책**  >  **전역 다단계 인증 정책 편집**으로 이동 합니다. **AzureMfaServerAuthentication**을 선택합니다.
9. 2단계를 반복하여 AD FS 팜에서 제거된 서버를 업데이트하고 해당 서버에서 AD FS 서비스를 다시 시작합니다.
10. 해당 서버를 AD FS 팜에 다시 추가합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Multi-Factor Authentication 및 타사 VPN을 사용한 고급 시나리오](howto-mfaserver-nps-vpn.md) 예제 보기

* [MFA 서버를 Windows Server Active Directory와 동기화](howto-mfaserver-dir-ad.md)

* 응용 프로그램에 대 한 [Windows 인증 구성](howto-mfaserver-windows.md)
