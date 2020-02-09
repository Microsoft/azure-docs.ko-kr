---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: ba1bdd60a3363cfab694bae9b8ee3cf63e24b054
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907579"
---
## <a name="steps-to-enable-single-sign-on"></a>Single Sign-on을 사용 하도록 설정 하는 단계
클라우드 프로 비전은 Single Sign-on과 함께 작동 합니다.  현재는 에이전트가 설치 될 때 SSO를 사용 하도록 설정 하는 옵션이 없지만 아래 단계를 사용 하여 SSO를 사용 하도록 설정 하고 사용할 수 있습니다. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>1 단계: Azure AD Connect 파일 다운로드 및 추출
1.  먼저 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)의 최신 버전을 다운로드 합니다.
2.  관리 권한을 사용 하여 명령 프롬프트를 열고 방금 다운로드 한 MSI로 이동 합니다.
3.  다음을 실행 합니다. `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4.  파일 경로 및 추출 폴더의 이름과 일치 하도록 filepath 및 extractfolder를 변경 합니다. 이제 콘텐츠는 추출 폴더에 있습니다.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>2 단계: 원활한 SSO PowerShell 모듈 가져오기

1. [AZURE AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)을 다운로드 하고 설치 합니다.
2. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
3. `Import-Module .\AzureADSSO.psd1` 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>3 단계: 원활한 SSO가 사용 하도록 설정 된 Active Directory 포리스트 목록 가져오기

1. PowerShell을 관리자 권한으로 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.
2. `Get-AzureADSSOStatus`을 호출합니다. 이 명령은 기능이 사용하도록 설정된 Active Directory 포리스트 목록("도메인" 목록에 표시됨)을 제공합니다.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4단계: 각 Active Directory 포리스트에 대한 Seamless SSO 사용 설정

1. `Enable-AzureADSSOForest`을 호출합니다. 메시지가 표시되면 원하는 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

   > [!NOTE]
   >도메인 관리자 자격 증명 사용자 이름은 SAM 계정 형식 (예 contoso\johndoe 또는 com\johndoe)으로 입력 해야 합니다. 사용자 이름의 도메인 부분을 사용 하여 DNS를 사용 하는 도메인 관리자의 도메인 컨트롤러를 찾습니다.

   >[!NOTE]
   >사용 되는 도메인 관리자 계정은 보호 된 사용자 그룹의 구성원이 아니어야 합니다. 이 경우 작업이 실패 합니다.

2. 기능을 설정하려는 각 Active Directory 포리스트에 대해 앞의 단계를 반복합니다.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5단계. 테넌트에서 기능 활성화

테넌트에서 기능을 켜려면 `Enable-AzureADSSO -Enable $true`를 호출하세요.
