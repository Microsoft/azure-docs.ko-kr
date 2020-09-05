---
title: Windows Server에서 AD FS를 설치한 Azure MFA 서버 - Azure Active Directory
description: 이 문서에서는 Windows Server 2012 R2 및 2016에서 Azure Multi-Factor Authentication 및 AD FS로 시작하는 방법을 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a57f42400468d69970c7c0b5d05d99a21b2d6b9
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949561"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Windows Server에서 AD FS와 작동하도록 Azure Multi-Factor Authentication 서버 구성

AD FS(Active Directory Federation Services)를 사용하고 클라우드 또는 온-프레미스 리소스 보안을 유지하려는 경우, AD FS와 작동하도록 Azure Multi-Factor Authentication 서버를 구성할 수 있습니다. 이 구성은 높은 값의 엔드포인트에 대해 2단계 인증을 트리거합니다.

이 문서에서는 Windows Server 2012 R2 또는 Windows Server 2016에서 AD FS와 함께 Azure Multi-Factor Authentication 서버를 사용하는 방법을 설명합니다. 자세한 내용은 [AD FS 2.0과 함께 Azure Multi-Factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안을 유지](howto-mfaserver-adfs-2.md)하는 방법에 대해 읽어보세요.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 클라우드 기반 MFA를 사용 하는 경우 [Azure Multi-Factor Authentication 및 AD FS를 사용 하 여 클라우드 리소스 보안 유지](howto-mfa-adfs.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버를 사용하여 Windows Server AD FS 보안 유지

Azure Multi-Factor Authentication 서버를 설치하는 경우 다음과 같은 옵션을 사용할 수 있습니다.

* AD FS와 동일한 서버에 로컬로 Azure Multi-factor Authentication 서버 설치
* AD FS 서버에서 로컬로 Azure Multi-Factor Authentication 어댑터를 설치하고 다른 컴퓨터에 Multi-Factor Authentication 서버 설치

이 작업을 시작하기 전에 다음 정보를 고려하세요.

* AD FS 서버에 Azure Multi-Factor Authentication 서버를 설치할 필요가 없습니다. 그러나 AD FS를 실행하는 Windows Server 2012 R2 또한 Windows Server 2016에서 AD FS용 Multi-Factor Authentication 어댑터를 설치해야 합니다. AD FS 어댑터를 AD FS 페더레이션 서버에 따로 설치하는 경우 다른 컴퓨터에 서버를 설치할 수 있습니다. 어댑터를 별도로 설치하는 방법에 알아보려면 다음 절차를 참조하세요.
* 조직이 문자 메시지 또는 모바일 앱 확인 방법을 사용하는 경우, 회사 설정에서 정의된 문자열에는 <$*application_name*$> 자리 표시자가 있습니다. MFA 서버 v7.1에서 이 자리 표시자를 대체하는 애플리케이션 이름을 제공할 수 있습니다. v7.0 이상에서 이 자리 표시자는 AD FS 어댑터를 사용하는 경우 자동으로 대체되지 않습니다. 이전 버전의 경우 AD FS 보안을 유지하면 적절 한 문자열에서 자리 표시자를 제거합니다.
* 로그인에 사용하는 계정에는 Active Directory 서비스에서 보안 그룹을 만드는 사용자 권한이 있어야 합니다.
* Multi-Factor Authentication AD FS 어댑터 설치 마법사는 Active Directory의 인스턴스에서 PhoneFactor Admins이라고 하는 보안 그룹을 만듭니다. 그런 다음 페더레이션 서비스의 AD FS 서비스 계정을 이 그룹에 추가합니다. PhoneFactor Admins 그룹이 도메인 컨트롤러에 생성되고 AD FS 서비스 계정이 이 그룹의 멤버인지 확인합니다. 필요한 경우 도메인 컨트롤러의 PhoneFactor Admins 그룹에 AD FS 서비스 계정을 수동으로 추가합니다.
* 사용자 포털을 사용하여 웹 서비스 SDK를 설치하는 방법에 대한 내용은 [Azure Multi-Factor Authentication 서버에 사용자 포털 배포](howto-mfaserver-deploy-userportal.md)를 참조하세요.

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>AD FS 서버에 로컬로 Azure Multi-Factor Authentication 서버 설치

1. AD FS 서버에 Azure Multi-Factor Authentication 서버를 다운로드하여 설치합니다. 설치에 대한 내용은 [Azure Multi-factor Authentication 서버로 시작](howto-mfaserver-deploy.md)을 읽어보세요.
2. Azure Multi-Factor Authentication 서버 관리 콘솔에서 **AD FS** 아이콘을 클릭합니다. **사용자 등록 허용** 및 **사용자가 메서드를 선택하도록 허용** 옵션을 선택합니다.
3. 조직에 대해 지정하려는 추가 옵션을 선택합니다.
4. **AD FS 어댑터 설치**를 클릭합니다.

   ![MFA 서버 콘솔에서 ADFS 어댑터 설치](./media/howto-mfaserver-adfs-2012/server.png)

5. Active Directory 창이 표시되는 경우 다음 두 가지를 의미합니다. 컴퓨터가 도메인에 연결되어 있고 AD FS 어댑터와 Multi-Factor Authentication 서비스 간의 통신 보안 유지를 위한 Active Directory 구성이 완료되지 않습니다. **다음**을 클릭하여 이 구성을 자동으로 완료하거나, **자동 Active Directory 구성을 건너뛰고 수동으로 설정 구성** 확인란을 선택합니다. **다음**을 클릭합니다.
6. 로컬 그룹 창이 표시되는 경우 두 가지를 의미합니다. 컴퓨터가 도메인에 가입되지 않고 AD FS 어댑터와 Multi-Factor Authentication 서비스 간의 통신 보안 유지를 위한 로컬 그룹 구성이 완료되지 않습니다. **다음**을 클릭하여 이 구성을 자동으로 완료하거나, **자동 로컬 그룹 구성을 건너뛰고 수동으로 설정 구성** 확인란을 선택합니다. **다음**을 클릭합니다.
7. 설치 마법사에서 **다음**을 클릭합니다. Azure Multi-Factor Authentication 서버는 PhoneFactor Admins 그룹을 만들고 AD FS 서비스 계정을 PhoneFactor Admins 그룹에 추가합니다.
8. **설치 관리자 시작** 페이지에서 **다음**을 클릭합니다.
9. Multi-Factor Authentication AD FS 어댑터 설치 관리자에서 **다음**을 클릭합니다.
10. 설치가 완료되면 **닫기** 를 클릭합니다.
11. 어댑터가 설치되면 이를 AD FS에 등록해야 합니다. Windows PowerShell을 열고 다음 명령을 실행합니다.

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. 새로 등록된 어댑터를 사용하려면 AD FS에서 전역 인증 정책을 편집합니다. AD FS 관리 콘솔에서 **인증 정책** 노드로 이동합니다. **Multi-factor Authentication** 섹션에서 **전역 설정** 섹션 옆에 있는 **편집** 링크를 클릭합니다. **전역 인증 정책 편집** 창에서 추가 인증 방법으로 **Multi-Factor Authentication**을 선택하고 **확인**을 클릭합니다. 어댑터는 WindowsAzureMultiFactorAuthentication으로 등록됩니다. 등록이 적용되려면 AD FS 서비스를 다시 시작합니다.

![글로벌 인증 정책 편집](./media/howto-mfaserver-adfs-2012/global.png)

이제 Multi-Factor Authentication 서버가 AD FS와 함께 사용할 수 있도록 추가 인증 공급자로 설정됩니다.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>웹 서비스 SDK를 사용하여 AD FS 어댑터의 독립 실행형 인스턴스를 설치합니다.

1. Multi-Factor Authentication 서버를 실행하는 서버에 웹 서비스 SDK를 설치합니다.
2. \Program Files\Multi-Factor Authentication Server 디렉터리에서 다음 파일을 AD FS 어댑터를 설치하려는 서버에 복사합니다.
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. MultiFactorAuthenticationAdfsAdapterSetup64.msi 설치 파일을 실행합니다.
4. Multi-Factor Authentication AD FS 어댑터 설치 관리자에서 **다음** 을 클릭하여 설치를 시작합니다.
5. 설치가 완료되면 **닫기** 를 클릭합니다.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>MultiFactorAuthenticationAdfsAdapter.config 파일을 편집합니다.

다음 단계를 따라 MultiFactorAuthenticationAdfsAdapter.config 파일을 편집합니다.

1. **UseWebServiceSdk** 노드를 **true**로 설정합니다.  
2. **WebServiceSdkUrl** 의 값을 Multi-Factor Authentication 웹 서비스 SDK의 URL로 설정합니다. 예: *https: \/ \/ Contoso.com/ \<certificatename> /multifactorauthwebservicesdk/pfwssdk.asmx가 있으며*, 여기서 *\<certificatename>* 은 인증서의 이름입니다.  
3. Register-MultiFactorAuthenticationAdfsAdapter.ps1 스크립트를 편집하고 `Register-AdfsAuthenticationProvider` 명령 끝에 `-ConfigurationFilePath &lt;path&gt;`를 추가합니다. 여기서 *&lt;path&gt;* 는 MultiFactorAuthenticationAdfsAdapter.config 파일의 전체 경로입니다.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>사용자 이름 및 암호를 사용하여 Web Service SDK 구성

Web Service SDK를 구성하는 데는 두 가지 옵션이 있습니다. 첫 번째 옵션은 사용자 이름 및 암호를 사용하고 두 번째 옵션은 클라이언트 인증서를 사용합니다. 첫 번째 옵션의 경우 다음 단계를 수행하거나 두 번째 옵션으로 건너 뛰세요.  

1. **WebServiceSdkUsername** 의 값을 PhoneFactor Admins 보안 그룹의 구성원인 계정으로 설정합니다. &lt;도메인&gt;&#92;&lt;사용자 이름&gt; 형식을 사용합니다.  
2. **WebServiceSdkPassword** 값을 적절한 계정 암호로 설정합니다.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>클라이언트 인증서를 사용하여 Web Service SDK 구성

사용자 이름 및 암호를 사용하지 않으려는 경우 다음 단계를 따라 클라이언트 인증서를 사용하는 Web Service SDK를 구성합니다.

1. 인증 기관에서 웹 서비스 SDK를 실행하는 서버에 대한 클라이언트 인증서를 가져옵니다. [클라이언트 인증서를 가져오는](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770328(v=ws.10))방법을 알아봅니다.  
2. 웹 서비스 SDK를 실행하는 서버의 로컬 컴퓨터 개인 인증서 저장소로 클라이언트 인증서를 가져옵니다. 신뢰할 수 있는 루트 인증서 인증서 저장소에 인증 기관의 공용 인증서가 있는지 확인합니다.  
3. 클라이언트 인증서의 공개 키 및 프라이빗 키를 .pfx 파일로 내보냅니다.  
4. Base64 형식인 공개 키를 .cer 파일로 내보냅니다.  
5. Server Manager에서 Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication 기능이 설치되어 있는지 확인합니다. 이 기능이 설치되어 있지 않은 경우 **역할 및 기능 추가** 를 선택하여 이 기능을 추가합니다.  
6. IIS Manager에서 Web Service SDK 가상 디렉터리가 포함된 웹 사이트의 **구성 편집기** 를 두 번 클릭합니다. 가상 디렉터리가 아니라 웹 사이트를 선택하는 것이 중요합니다.  
7. **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** 섹션으로 이동합니다.  
8. 사용을 **true**로 설정합니다.  
9. oneToOneCertificateMappingsEnabled를 **true**로 설정합니다.  
10. oneToOneMappings 옆에 있는 **...** 단추를 클릭한 다음 **추가** 링크를 클릭합니다.  
11. 앞에서 내보낸 Base64 .cer 파일을 엽니다. *-----BEGIN CERTIFICATE-----* , *-----END CERTIFICATE-----* 및 줄바꿈을 제거합니다. 결과 문자열을 복사합니다.  
12. 인증서를 이전 단계에서 복사한 문자열로 설정합니다.  
13. 사용을 **true**로 설정합니다.  
14. userName을 PhoneFactor Admins 보안 그룹의 구성원인 계정으로 설정합니다. &lt;도메인&gt;&#92;&lt;사용자 이름&gt; 형식을 사용합니다.  
15. 적절한 계정 암호를 설정하고 Configuration Editor를 닫습니다.  
16. **적용** 단추를 클릭합니다.  
17. Web Service SDK 가상 디렉터리에서 **인증**을 두 번 클릭합니다.  
18. ASP.NET 가장 및 기본 인증을 **사용**으로 설정하고 다른 모든 항목을 **사용 안 함**으로 설정했는지 확인합니다.  
19. Web Service SDK 가상 디렉터리에서 **SSL 설정**을 두 번 클릭합니다.  
20. 클라이언트 인증서를 **허용**으로 설정한 다음 **적용**을 클릭합니다.  
21. AD FS 어댑터를 실행하는 서버에 앞서 내보낸 .pfx 파일을 복사합니다.  
22. .pfx 파일을 로컬 컴퓨터 개인 인증서 저장소로 가져옵니다.  
23. **프라이빗 키 관리**를 마우스 오른쪽 단추로 클릭하고 선택한 다음, AD FS 서비스에 로그인할 때 사용한 계정에 읽기 액세스 권한을 부여합니다.  
24. 클라이언트 인증서를 열고 **세부 정보** 탭에서 지문을 복사합니다.  
25. MultiFactorAuthenticationAdfsAdapter.config 파일에서 **WebServiceSdkCertificateThumbprint** 를 이전 단계에서 복사한 문자열로 설정합니다.  

마지막으로 어댑터를 등록하려면 PowerShell에서 \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 스크립트를 실행합니다. 어댑터는 WindowsAzureMultiFactorAuthentication으로 등록됩니다. 등록이 적용되려면 AD FS 서비스를 다시 시작합니다.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>AD FS를 사용하여 Azure AD 리소스 보안 유지

클라우드 리소스를 보호하려면 사용자가 두 단계 인증을 성공적으로 수행했을 때 Active Directory Federation Services가 multipleauthn 클레임을 내보내도록 클레임 규칙을 설정합니다. 이 클레임은 Azure AD에 전달됩니다. 다음 단계를 수행하려면 이 절차를 따르세요.

1. AD FS 관리를 엽니다.
2. 왼쪽에서 **신뢰 당사자 트러스트**를 선택합니다.
3. **Microsoft Office 365 ID 플랫폼**을 마우스 오른쪽 단추로 클릭하고 **클레임 규칙 편집...** 을 선택합니다.

   ![ADFS 콘솔에서 클레임 규칙 편집](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. 발급 변환 규칙에서 **규칙 추가**를 클릭합니다.

   ![ADFS 콘솔에서 변환 규칙 편집](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. 변환 클레임 규칙 추가 마법사의 드롭다운 목록에서 **들어오는 클레임 통과 또는 필터링**을 선택하고 **다음**을 클릭합니다.

   ![변환 클레임 규칙 추가 마법사](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. 규칙의 이름을 지정합니다.
7. 들어오는 클레임 유형으로 **인증 방법 참조**를 선택합니다.
8. **모든 클레임 값 통과**를 선택합니다.

    ![변환 클레임 규칙 추가 마법사](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. **Finish**를 클릭합니다. AD FS 관리 콘솔을 닫습니다.

## <a name="troubleshooting-logs"></a>문제 해결 로그

MFA 서버 AD FS 어댑터 문제를 해결하는 데 도움이 되도록 추가 로깅을 사용하려면 다음 단계를 사용합니다.

1. MFA 서버 인터페이스에서 AD FS 섹션을 열고 **로깅 사용** 확인란을 선택합니다.
2. 각 AD FS 서버에서 **regedit.exe**를 사용하여 `C:\Program Files\Multi-Factor Authentication Server\` 값(또는 원하는 다른 디렉터리)이 있는 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` 문자열 값 레지스트리 키를 만듭니다.  **단, 후행 백슬래시가 중요합니다.**
3. `C:\Program Files\Multi-Factor Authentication Server\Logs` 디렉터리(또는 **2단계**에서 참조된 다른 디렉터리)를 만듭니다.
4. Logs 디렉터리에 대한 수정 액세스 권한을 AD FS 서비스 계정에 부여합니다.
5. AD FS 서비스를 다시 시작합니다.
6. `MultiFactorAuthAdfsAdapter.log` 파일이 Logs 디렉터리에 만들어졌는지 확인합니다.

## <a name="related-topics"></a>관련 항목

문제 해결 도움말을 보려면 [Azure Multi-Factor Authentication FAQ](multi-factor-authentication-faq.md)