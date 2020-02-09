---
title: Azure AD 암호 보호 배포-Azure Active Directory
description: Azure AD 암호 보호를 배포 하 여 온-프레미스에 잘못 된 암호를 금지
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61ab87a3eb1bd4b81a8e67a182a4cb6a09aa069
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888950"
---
# <a name="deploy-azure-ad-password-protection"></a>Azure AD 암호 보호 배포

이제 [Windows Server Active Directory에 대해 AZURE AD 암호 보호를 적용 하는 방법을](concept-password-ban-bad-on-premises.md)이해 했으므로 다음 단계는 배포를 계획 하 고 실행 하는 것입니다.

## <a name="deployment-strategy"></a>배포 전략

감사 모드에서 배포를 시작 하는 것이 좋습니다. 감사 모드는 암호를 계속 설정할 수 있는 기본 초기 설정입니다. 차단 되는 암호는 이벤트 로그에 기록 됩니다. 감사 모드에서 프록시 서버 및 DC 에이전트를 배포한 후 정책이 적용 될 때 사용자와 환경에 대 한 암호 정책에 미치는 영향을 모니터링 해야 합니다.

감사 단계 중에 많은 조직에서 다음을 찾습니다.

* 더 안전한 암호를 사용하도록 기존 운영 프로세스를 개선해야 합니다.
* 사용자는 보안 되지 않은 암호를 사용 하는 경우가 많습니다.
* 사용자에 게 보안 적용의 예정 된 변경 내용, 영향을 받는 보안 암호를 선택 하는 방법에 대 한 정보를 알려 주어 야 합니다.

기존 Active Directory 도메인 컨트롤러 배포 자동화에 영향을 주는 더 강력한 암호 유효성 검사를 수행할 수도 있습니다. 이러한 문제를 미리 파악 하기 위해 감사 기간 평가 중에 하나 이상의 DC 수준 올리기 및 DC 수준 내리기를 수행 하는 것이 좋습니다.  자세한 내용은 다음을 참조하세요.

* [Ntdsutil.exe에서 weak 디렉터리 서비스 복구 모드 암호를 설정할 수 없습니다.](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [취약 한 디렉터리 서비스 복구 모드 암호로 인해 도메인 컨트롤러 복제본 수준을 올리지 못했습니다.](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [취약 한 로컬 관리자 암호로 인해 도메인 컨트롤러 강등을 실패 합니다.](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

적절 한 기간 동안 기능이 감사 모드에서 실행 된 후에 *감사* 에서 *적용* 으로 구성을 전환 하 여 더 안전한 암호를 요구 하도록 전환할 수 있습니다. 이 시간 동안 집중적으로 모니터링하는 것이 좋습니다.

## <a name="deployment-requirements"></a>배포 요구 사항

* Azure AD 암호 보호에 대 한 라이선스 요구 사항은 [조직에서 잘못 된 암호 제거](concept-password-ban-bad.md#license-requirements)문서에서 찾을 수 있습니다.
* Azure AD 암호 보호 DC 에이전트 소프트웨어가 설치 되는 모든 컴퓨터는 Windows Server 2012 이상을 실행 해야 합니다. 이 요구 사항은 Active Directory 도메인 또는 포리스트가 Windows Server 2012 도메인 또는 포리스트 기능 수준에 있어야 한다는 의미는 아닙니다. [디자인 원칙](concept-password-ban-bad-on-premises.md#design-principles)에 설명 된 대로 DC 에이전트 또는 프록시 소프트웨어를 실행 하는 데 필요한 최소 dfl 또는 ffl은 없습니다.
* DC 에이전트 서비스를 설치 하는 모든 컴퓨터에는 .NET 4.5이 설치 되어 있어야 합니다.
* Azure AD 암호 보호 프록시 서비스가 설치 되는 모든 컴퓨터는 Windows Server 2012 R2 이상을 실행 해야 합니다.
   > [!NOTE]
   > 프록시 서비스 배포는 도메인 컨트롤러에서 아웃 바운드 직접 인터넷 연결을 사용할 수 있는 경우에도 Azure AD 암호 보호를 배포 하기 위한 필수 요구 사항입니다. 
   >
* Azure AD 암호 보호 프록시 서비스가 설치 될 모든 컴퓨터에는 .NET 4.7이 설치 되어 있어야 합니다.
  .NET 4.7은 완전히 업데이트 된 Windows Server에 이미 설치 되어 있어야 합니다. 필요한 경우 [Windows 용 .NET Framework 4.7 오프 라인 설치 관리자](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)에서 찾은 설치 관리자를 다운로드 하 여 실행 합니다.
* Azure AD 암호 보호 구성 요소가 설치 된 도메인 컨트롤러를 비롯 한 모든 컴퓨터에는 유니버설 C 런타임이 설치 되어 있어야 합니다. Windows 업데이트의 모든 업데이트가 있는지 확인 하 여 런타임을 가져올 수 있습니다. 또는 OS 특정 업데이트 패키지에서 가져올 수 있습니다. 자세한 내용은 [Windows에서 유니버설 C 런타임 업데이트](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)를 참조 하세요.
* 각 도메인에 있는 하나 이상의 도메인 컨트롤러와 암호 보호를 위해 프록시 서비스를 호스팅하는 하나 이상의 서버 간에 네트워크 연결이 존재 해야 합니다. 이 연결을 통해 도메인 컨트롤러에서 RPC 엔드포인트 매퍼 포트 135 및 프록시 서비스의 RPC 서버 포트에 액세스할 수 있어야 합니다. 기본적으로 RPC 서버 포트는 동적 RPC 포트 이지만 [정적 포트를 사용](#static)하도록 구성할 수 있습니다.
* Azure AD 암호 보호 프록시 서비스가 설치 될 모든 컴퓨터에는 다음 엔드포인트에 대 한 네트워크 액세스 권한이 있어야 합니다.

    |**엔드포인트**|**용도**|
    | --- | --- |
    |`https://login.microsoftonline.com`|인증 요청|
    |`https://enterpriseregistration.windows.net`|Azure AD 암호 보호 기능|
 
* Microsoft Azure AD 연결 에이전트 업데이트 프로그램 필수 구성 요소

  Microsoft Azure AD Connect Agent 업데이트 서비스는 Azure AD 암호 보호 프록시 서비스와 나란히 설치 됩니다. Microsoft Azure AD Connect Agent 업데이트 서비스에서 기능을 사용할 수 있도록 하려면 추가 구성이 필요 합니다.

  사용자 환경에서 http 프록시 서버를 사용 하는 경우 [기존 온-프레미스 프록시 서버 작업](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)에 지정 된 지침을 따라야 합니다.

  Microsoft Azure AD Connect Agent 업데이트 서비스에는 [tls 요구 사항](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)에 지정 된 tls 1.2 단계도 필요 합니다.

  [응용 프로그램 프록시 환경 설정 절차](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)에 지정 된 포트 및 url 집합에 대해 네트워크 액세스를 사용 하도록 설정 해야 합니다.

  > [!WARNING]
  > Azure AD 암호 보호 프록시와 응용 프로그램 프록시는 다양 한 버전의 Microsoft Azure AD Connect Agent 업데이트 서비스를 설치 합니다. 즉, 지침이 응용 프로그램 프록시 콘텐츠를 참조 합니다. 이러한 다른 버전은 함께 설치 될 때 호환 되지 않으므로 동일한 컴퓨터에 Azure AD 암호 보호 프록시와 응용 프로그램 프록시를 함께 설치 하는 것은 권장 되지 않습니다.

* 도메인 컨트롤러에 프록시 서비스에 로그온 할 수 있는 권한을 부여 하려면 암호 보호를 위해 프록시 서비스를 호스팅하는 모든 컴퓨터를 구성 해야 합니다. 이 기능은 "네트워크에서이 컴퓨터 액세스" 권한 할당을 통해 제어 됩니다.
* 암호 보호를 위해 프록시 서비스를 호스트 하는 모든 컴퓨터는 아웃 바운드 TLS 1.2 HTTP 트래픽을 허용 하도록 구성 되어야 합니다.
* Azure AD를 사용 하 여 암호 보호 및 포리스트에 대 한 프록시 서비스를 등록 하는 전역 관리자 계정.
* Windows Server Active Directory 포리스트를 Azure AD에 등록할 수 있는 포리스트 루트 도메인의 도메인 관리자 권한이 있는 계정 Active Directory 합니다.
* DC 에이전트 서비스 소프트웨어를 실행 하는 모든 Active Directory 도메인은 sysvol 복제를 위해 DFSR (분산 파일 시스템 복제)을 사용 해야 합니다.

  도메인이 아직 DFSR을 사용 하지 않는 경우 Azure AD 암호 보호를 설치 하기 전에 DFSR을 사용 하도록 마이그레이션해야 합니다. 자세한 내용은 다음 링크를 참조 하세요.

  [SYSVOL 복제 마이그레이션 가이드: FRS에서 DFS 복제으로](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Azure AD 암호 보호 DC 에이전트 소프트웨어는 현재 sysvol (DFSR에 대 한 선행 기술)을 사용 하는 도메인의 도메인 컨트롤러에 설치 되며,이 환경에서는 소프트웨어가 제대로 작동 하지 않습니다. 추가 부정적 부작용에는 복제에 실패 한 개별 파일이 포함 되며 sysvol 복원 절차는 성공 하는 것 처럼 보이지만 자동으로 모든 파일을 복제 하지 못합니다. DFSR의 내재 된 혜택을 제공 하 고 Azure AD 암호 보호 배포의 차단을 해제 하기 위해 가능한 한 빨리 DFSR을 사용 하려면 도메인을 마이그레이션해야 합니다. 이후 버전의 소프트웨어는 여전히 FRS를 사용 하는 도메인에서 실행 되는 경우 자동으로 사용 하지 않도록 설정 됩니다.

* Windows Server 2012를 실행 하는 도메인의 모든 도메인 컨트롤러에서 키 배포 서비스를 사용 하도록 설정 해야 합니다. 기본적으로이 서비스는 수동 트리거 시작을 통해 사용 하도록 설정 됩니다.

## <a name="single-forest-deployment"></a>단일 포리스트 배포

다음 다이어그램에서는 온-프레미스 Active Directory 환경에서 Azure AD 암호 보호의 기본 구성 요소가 함께 작동 하는 방식을 보여 줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

소프트웨어를 배포 하기 전에 소프트웨어의 작동 방식을 검토 하는 것이 좋습니다. [AZURE AD 암호 보호의 개념적 개요를](concept-password-ban-bad-on-premises.md)참조 하세요.

### <a name="download-the-software"></a>소프트웨어 다운로드

Azure AD 암호 보호에는 두 가지 필수 설치 관리자가 있습니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 사용할 수 있습니다.

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>암호 보호를 위한 프록시 서비스 설치 및 구성

1. 암호 보호를 위해 프록시 서비스를 호스트할 서버를 하나 이상 선택 합니다.
   * 이러한 각 서비스는 단일 포리스트에 대 한 암호 정책만 제공할 수 있습니다. 호스트 컴퓨터는 해당 포리스트의 도메인에 가입 되어 있어야 합니다. 루트 및 자식 도메인이 둘 다 지원 됩니다. 포리스트의 각 도메인에 있는 하나 이상의 DC와 암호 보호 컴퓨터 간에 네트워크 연결이 필요 합니다.
   * 테스트를 위해 도메인 컨트롤러에서 프록시 서비스를 실행할 수 있습니다. 그러나이 도메인 컨트롤러에는 인터넷 연결이 필요 하며,이는 보안에 문제가 될 수 있습니다. 이 구성은 테스트용 으로만 권장 됩니다.
   * 중복성을 위해 두 개 이상의 프록시 서버를 권장 합니다. [고가용성](howto-password-ban-bad-on-premises-deploy.md#high-availability)을 참조 하세요.

1. `AzureADPasswordProtectionProxySetup.exe` 소프트웨어 설치 관리자를 사용 하 여 Azure AD 암호 보호 프록시 서비스를 설치 합니다.
   * 소프트웨어가 설치되면 다시 부팅할 필요가 없습니다. 소프트웨어 설치는 표준 MSI 절차를 사용하여 자동화할 수 있습니다. 예를 들면 다음과 같습니다.

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > 설치 오류를 방지 하려면 AzureADPasswordProtectionProxySetup 패키지를 설치 하기 전에 Windows 방화벽 서비스가 실행 중 이어야 합니다. Windows 방화벽을 실행 하지 않도록 구성 된 경우 설치 하는 동안 일시적으로 방화벽 서비스를 사용 하도록 설정 하 고 실행 하는 것이 해결 방법입니다. 프록시 소프트웨어는 설치 후 Windows 방화벽에 대 한 특정 종속성이 없습니다. 타사 방화벽을 사용 하는 경우 배포 요구 사항을 충족 하도록 구성 해야 합니다. 여기에는 포트 135 및 프록시 RPC 서버 포트에 대 한 인바운드 액세스를 허용 하는 작업이 포함 됩니다. [배포 요구 사항](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)을 참조 하세요.

1. 관리자 권한으로 PowerShell 창을 엽니다.
   * 암호 보호 프록시 소프트웨어에는 새 PowerShell 모듈인 *AzureADPasswordProtection*이 포함 되어 있습니다. 다음 단계는이 PowerShell 모듈에서 다양 한 cmdlet을 실행 합니다. 다음과 같이 새 모듈을 가져옵니다.

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * 서비스가 실행 되 고 있는지 확인 하려면 다음 PowerShell 명령을 사용 합니다.

      `Get-Service AzureADPasswordProtectionProxy | fl`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다.

     결과는 "실행 중" **상태** 를 표시 합니다.

1. 프록시를 등록합니다.
   * 3 단계를 완료 한 후에는 프록시 서비스가 컴퓨터에서 실행 되지만 Azure AD와 통신 하는 데 필요한 자격 증명은 아직 없습니다. Azure AD에 등록 해야 합니다.

     `Register-AzureADPasswordProtectionProxy`

     이 cmdlet에는 Azure 테넌트에 대 한 전역 관리자 자격 증명이 필요 합니다. 또한 포리스트 루트 도메인에서 온-프레미스 Active Directory 도메인 관리자 권한이 필요 합니다. 또한 로컬 관리자 권한이 있는 계정을 사용 하 여이 cmdlet을 실행 해야 합니다.

     프록시 서비스에 대해이 명령을 한 번 성공한 후에는 추가 호출이 성공 하지만 필요 하지 않습니다.

      `Register-AzureADPasswordProtectionProxy` cmdlet은 다음과 같은 세 가지 인증 모드를 지원 합니다. 처음 두 모드는 Azure Multi-Factor Authentication을 지원 하지만 세 번째 모드는 지원 하지 않습니다. 자세한 내용은 아래 설명을 참조 하세요.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 이 모드는 Server Core 운영 체제에서 작동 하지 않습니다. 대신 다음 인증 모드 중 하나를 사용 합니다. 또한 Internet Explorer 보안 강화 구성을 사용 하는 경우이 모드가 실패할 수 있습니다. 해결 방법은 해당 구성을 사용 하지 않도록 설정 하 고 프록시를 등록 한 다음 다시 사용 하도록 설정 하는 것입니다.

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        그런 다음 다른 장치에 표시 된 지침에 따라 인증을 완료 합니다.

     * 자동(암호 기반) 인증 모드:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 계정에 Azure Multi-Factor Authentication가 필요한 경우이 모드는 실패 합니다. 이 경우 이전 두 인증 모드 중 하나를 사용 하거나 대신 MFA를 요구 하지 않는 다른 계정을 사용 합니다.
        >
        > Azure AD 암호 보호에서 다루는 Azure 장치 등록이 MFA를 전역적으로 요구 하도록 구성 된 경우 MFA가 필요할 수도 있습니다. 이 문제를 해결 하려면 이전 두 인증 모드 중 하나를 사용 하 여 MFA를 지 원하는 다른 계정을 사용 하거나, Azure 장치 등록 MFA 요구 사항을 일시적으로 완화 시킬 수도 있습니다. 이렇게 하려면 Azure 관리 포털로 이동한 다음 Azure Active Directory, 장치, 장치 설정으로 이동한 후 "Multi-factor Auth를 통해 장치를 연결 하도록 요구"를 아니요로 설정 합니다. 등록이 완료 되 면이 설정을 다시 [예]로 다시 구성 해야 합니다.
        >
        > MFA 요구 사항은 테스트 목적 으로만 무시 하는 것이 좋습니다.

       현재는 이후 기능을 위해 예약 된 *-ForestCredential* 매개 변수를 지정할 필요가 없습니다.

   암호 보호를 위한 프록시 서비스 등록은 서비스의 수명 동안 한 번만 필요 합니다. 그런 다음 프록시 서비스는 다른 필요한 유지 관리 작업을 자동으로 수행 합니다.

   > [!TIP]
   > 특정 Azure 테넌트에 대해이 cmdlet이 처음으로 실행 될 때 완료 되기 전에 매우 많은 지연이 발생할 수 있습니다. 오류가 보고 되지 않으면 이러한 지연에 대해 걱정 하지 마세요.

1. 포리스트를 등록합니다.
   * `Register-AzureADPasswordProtectionForest` PowerShell cmdlet을 사용 하 여 Azure와 통신 하는 데 필요한 자격 증명을 사용 하 여 온-프레미스 Active Directory 포리스트를 초기화 해야 합니다.

      Cmdlet에는 Azure 테넌트에 대 한 전역 관리자 자격 증명이 필요 합니다.  또한 로컬 관리자 권한이 있는 계정을 사용 하 여이 cmdlet을 실행 해야 합니다. 또한 온-프레미스 Active Directory 엔터프라이즈 관리자 권한이 필요 합니다. 이 단계는 포리스트마다 한 번씩 실행됩니다.

      `Register-AzureADPasswordProtectionForest` cmdlet은 다음과 같은 세 가지 인증 모드를 지원 합니다. 처음 두 모드는 Azure Multi-Factor Authentication을 지원 하지만 세 번째 모드는 지원 하지 않습니다. 자세한 내용은 아래 설명을 참조 하세요.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 이 모드는 Server Core 운영 체제에서 작동 하지 않습니다. 대신 다음 두 가지 인증 모드 중 하나를 사용 합니다. 또한 Internet Explorer 보안 강화 구성을 사용 하는 경우이 모드가 실패할 수 있습니다. 해결 방법은 해당 구성을 사용 하지 않도록 설정 하 고 포리스트를 등록 한 다음 다시 사용 하도록 설정 하는 것입니다.  

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        그런 다음 다른 장치에 표시 된 지침에 따라 인증을 완료 합니다.

     * 자동(암호 기반) 인증 모드:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 계정에 Azure Multi-Factor Authentication가 필요한 경우이 모드는 실패 합니다. 이 경우 이전 두 인증 모드 중 하나를 사용 하거나 대신 MFA를 요구 하지 않는 다른 계정을 사용 합니다.
        >
        > Azure AD 암호 보호에서 다루는 Azure 장치 등록이 MFA를 전역적으로 요구 하도록 구성 된 경우 MFA가 필요할 수도 있습니다. 이 문제를 해결 하려면 이전 두 인증 모드 중 하나를 사용 하 여 MFA를 지 원하는 다른 계정을 사용 하거나, Azure 장치 등록 MFA 요구 사항을 일시적으로 완화 시킬 수도 있습니다. 이렇게 하려면 Azure 관리 포털로 이동한 다음 Azure Active Directory, 장치, 장치 설정으로 이동한 후 "Multi-factor Auth를 통해 장치를 연결 하도록 요구"를 아니요로 설정 합니다. 등록이 완료 되 면이 설정을 다시 [예]로 다시 구성 해야 합니다.
        >
        > MFA 요구 사항은 테스트 목적 으로만 무시 하는 것이 좋습니다.

       이러한 예제는 현재 로그인 한 사용자가 루트 도메인의 Active Directory 도메인 관리자 이기도 한 경우에만 성공 합니다. 그렇지 않은 경우 *-ForestCredential* 매개 변수를 통해 대체 도메인 자격 증명을 제공할 수 있습니다.

   > [!NOTE]
   > 환경에 여러 프록시 서버가 설치 되어 있으면 포리스트를 등록 하는 데 사용 하는 프록시 서버에 상관 없습니다.
   >
   > [!TIP]
   > 특정 Azure 테넌트에 대해이 cmdlet이 처음으로 실행 될 때 완료 되기 전에 매우 많은 지연이 발생할 수 있습니다. 오류가 보고 되지 않으면 이러한 지연에 대해 걱정 하지 마세요.

   Active Directory 포리스트 등록은 포리스트의 수명 동안 한 번만 필요 합니다. 그런 다음 포리스트의 도메인 컨트롤러 에이전트가 다른 필요한 유지 관리 작업을 자동으로 수행 합니다. 포리스트에 대 한 `Register-AzureADPasswordProtectionForest` 성공적으로 실행 된 후에는 cmdlet의 추가 호출이 성공 하지만 필요 하지 않습니다.

   `Register-AzureADPasswordProtectionForest` 성공 하려면 Windows Server 2012 이상을 실행 하는 하나 이상의 도메인 컨트롤러를 프록시 서버의 도메인에서 사용할 수 있어야 합니다. 이 단계를 수행 하기 전에는 DC 에이전트 소프트웨어를 도메인 컨트롤러에 설치할 필요가 없습니다.

1. HTTP 프록시를 통해 통신 하도록 암호 보호를 위한 프록시 서비스를 구성 합니다.

   사용자 환경에서 특정 HTTP 프록시를 사용 하 여 Azure와 통신 해야 하는 경우이 메서드를 사용 합니다 .%ProgramFiles%\Azure AD Password Protection Proxy\Service 폴더에 *AzureADPasswordProtectionProxy* 파일을 만듭니다. 다음 내용을 포함 합니다.

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   HTTP 프록시에 인증이 필요한 경우 *useDefaultCredentials* 태그를 추가 합니다.

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   두 경우 모두 `http://yourhttpproxy.com:8080`를 특정 HTTP 프록시 서버의 주소 및 포트로 바꿉니다.

   HTTP 프록시가 권한 부여 정책을 사용 하도록 구성 된 경우에는 암호 보호를 위해 프록시 서비스를 호스팅하는 컴퓨터의 Active Directory 컴퓨터 계정에 액세스 권한을 부여 해야 합니다.

   *AzureADPasswordProtectionProxy* 파일을 만들거나 업데이트 한 후 프록시 서비스를 중지 하 고 다시 시작 하는 것이 좋습니다.

   프록시 서비스는 HTTP 프록시에 연결 하는 데 특정 자격 증명을 사용 하는 것을 지원 하지 않습니다.

1. 선택 사항: 특정 포트에서 수신 하도록 암호 보호를 위한 프록시 서비스를 구성 합니다.
   * 도메인 컨트롤러에서 암호를 보호 하기 위한 DC 에이전트 소프트웨어는 RPC over TCP를 사용 하 여 프록시 서비스와 통신 합니다. 기본적으로 프록시 서비스는 사용 가능한 동적 RPC 엔드포인트에서 수신 합니다. 그러나 사용자 환경의 네트워킹 토폴로지 또는 방화벽 요구 사항으로 인해 필요한 경우 서비스를 특정 TCP 포트에서 수신 하도록 구성할 수 있습니다.
      * </a><a id="static" />하 여 정적 포트에서 실행 되도록 서비스를 구성 하려면 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용 합니다.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 이러한 변경 내용을 적용하려면 서비스를 중지했다가 다시 시작해야 합니다.

      * 동적 포트에서 실행 되도록 서비스를 구성 하려면 동일한 절차를 사용 하지만 *Staticport* 를 0으로 다시 설정 합니다.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 이러한 변경 내용을 적용하려면 서비스를 중지했다가 다시 시작해야 합니다.

   > [!NOTE]
   > 암호 보호를 위한 프록시 서비스는 포트 구성 변경 후 수동으로 다시 시작 해야 합니다. 그러나 이러한 구성을 변경한 후에는 도메인 컨트롤러에서 DC 에이전트 서비스 소프트웨어를 다시 시작할 필요가 없습니다.

   * 서비스의 현재 구성을 쿼리하려면 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용 합니다.

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>DC 에이전트 서비스 설치

   `AzureADPasswordProtectionDCAgentSetup.msi` 패키지를 사용 하 여 암호 보호를 위한 DC 에이전트 서비스를 설치 합니다.

   소프트웨어 설치 또는 제거를 다시 시작 해야 합니다. 이 요구 사항은 암호 필터 Dll은 다시 시작 하는 경우에만 로드 되거나 언로드되는 것 이기 때문입니다.

   도메인 컨트롤러가 아닌 컴퓨터에는 DC 에이전트 서비스를 설치할 수 있습니다. 이 경우 서비스가 시작 되어 실행 되지만 컴퓨터가 도메인 컨트롤러로 승격 될 때까지 비활성 상태로 유지 됩니다.

   표준 MSI 절차를 사용 하 여 소프트웨어 설치를 자동화할 수 있습니다. 예:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   설치 관리자가 컴퓨터를 자동으로 다시 부팅 하도록 하려면 `/norestart` 플래그를 생략할 수 있습니다.

도메인 컨트롤러에 DC 에이전트 소프트웨어를 설치 하 고 해당 컴퓨터를 다시 부팅 한 후 설치가 완료 됩니다. 다른 구성은 필요하지 않거나 가능하지 않습니다.

## <a name="upgrading-the-proxy-agent"></a>프록시 에이전트 업그레이드

최신 버전의 Azure AD 암호 보호 프록시 소프트웨어를 사용할 수 있는 경우 최신 버전의 `AzureADPasswordProtectionProxySetup.exe` 소프트웨어 설치 관리자를 실행 하 여 업그레이드를 수행 합니다. 최신 버전의 소프트웨어는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드할 수 있습니다.

최신 버전의 프록시 소프트웨어를 제거할 필요는 없습니다. 설치 관리자에서 전체 업그레이드를 수행 합니다. 프록시 소프트웨어를 업그레이드 하는 경우 다시 부팅할 필요가 없습니다. 소프트웨어 업그레이드는 표준 MSI 절차를 사용 하 여 자동화할 수 있습니다 (예: `AzureADPasswordProtectionProxySetup.exe /quiet`).

프록시 에이전트는 자동 업그레이드를 지원 합니다. 자동 업그레이드는 프록시 서비스와 나란히 설치 되는 Microsoft Azure AD Connect Agent 업데이트 서비스를 사용 합니다. 자동 업그레이드는 기본적으로 설정 되어 있으며 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용 하 여 사용 하거나 사용 하지 않도록 설정할 수 있습니다. `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용 하 여 현재 설정을 쿼리할 수 있습니다. 자동 업그레이드 설정은 항상 사용 하도록 설정 하는 것이 좋습니다.

`Get-AzureADPasswordProtectionProxy` cmdlet은 포리스트에 현재 설치 되어 있는 모든 프록시 에이전트의 소프트웨어 버전을 쿼리 하는 데 사용할 수 있습니다.

## <a name="upgrading-the-dc-agent"></a>DC 에이전트 업그레이드

최신 버전의 Azure AD 암호 보호 DC 에이전트 소프트웨어를 사용할 수 있는 경우 최신 버전의 `AzureADPasswordProtectionDCAgentSetup.msi` 소프트웨어 패키지를 실행 하 여 업그레이드를 수행 합니다. 최신 버전의 소프트웨어는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드할 수 있습니다.

현재 버전의 DC 에이전트 소프트웨어를 제거할 필요는 없습니다. 설치 관리자에서 전체 업그레이드를 수행 합니다. DC 에이전트 소프트웨어를 업그레이드할 때는 항상 다시 부팅 해야 합니다 .이 요구 사항은 핵심 Windows 동작으로 인해 발생 합니다. 

소프트웨어 업그레이드는 표준 MSI 절차를 사용 하 여 자동화할 수 있습니다 (예: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`).

설치 관리자가 컴퓨터를 자동으로 다시 부팅 하도록 하려면 `/norestart` 플래그를 생략할 수 있습니다.

`Get-AzureADPasswordProtectionDCAgent` cmdlet은 포리스트에 현재 설치 되어 있는 모든 DC 에이전트의 소프트웨어 버전을 쿼리 하는 데 사용할 수 있습니다.

## <a name="multiple-forest-deployments"></a>다중 포리스트 배포

Azure AD 암호 보호를 여러 포리스트에 배포하기 위한 추가 요구 사항은 없습니다. 각 포리스트는 "단일 포리스트 배포" 섹션에 설명 된 대로 독립적으로 구성 됩니다. 각 암호 보호 프록시는 연결 된 포리스트의 도메인 컨트롤러만 지원할 수 있습니다. 모든 포리스트의 암호 보호 소프트웨어는 Active Directory 신뢰 구성에 관계 없이 다른 포리스트에 배포 된 암호 보호 소프트웨어를 인식 하지 못합니다.

## <a name="read-only-domain-controllers"></a>읽기 전용 도메인 컨트롤러

암호 변경/집합은 Rodc (읽기 전용 도메인 컨트롤러)에서 처리 및 유지 되지 않습니다. 이러한 도메인 컨트롤러는 쓰기 가능한 도메인 컨트롤러에 전달 됩니다. 따라서 Rodc에는 DC 에이전트 소프트웨어를 설치할 필요가 없습니다.

## <a name="high-availability"></a>고가용성

암호 보호의 주요 가용성은 포리스트의 도메인 컨트롤러가 Azure에서 새로운 정책 또는 기타 데이터를 다운로드 하려고 할 때 프록시 서버를 사용 하는 것입니다. 각 DC 에이전트는 호출할 프록시 서버를 결정할 때 간단한 라운드 로빈 스타일 알고리즘을 사용 합니다. 에이전트에서 응답 하지 않는 프록시 서버를 건너뜁니다. 디렉터리 및 sysvol 폴더 상태 모두의 정상 복제를 사용 하는 완전히 연결 된 Active Directory 배포의 경우 두 프록시 서버는 가용성을 보장 하기에 충분 합니다. 이로 인해 새 정책 및 기타 데이터가 적시에 다운로드 됩니다. 그러나 추가 프록시 서버를 배포할 수 있습니다.

DC 에이전트 소프트웨어의 설계는 고가용성과 관련 된 일반적인 문제를 완화 합니다. DC 에이전트는 가장 최근에 다운로드 한 암호 정책의 로컬 캐시를 유지 관리 합니다. 등록 된 모든 프록시 서버를 사용할 수 없게 되는 경우에도 DC 에이전트는 캐시 된 암호 정책을 계속 적용 합니다. 대량 배포에서 암호 정책에 대 한 적절 한 업데이트 빈도는 일반적으로 몇 시간 이내이 아니라 며칠입니다. 따라서 프록시 서버를 잠깐 중단 하면 Azure AD 암호 보호에 크게 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

온-프레미스 서버에서 Azure AD 암호를 보호 하는 데 필요한 서비스를 설치 했으므로 이제 [설치 후 구성을 수행 하 고 보고 정보를 수집](howto-password-ban-bad-on-premises-operations.md) 하 여 배포를 완료 합니다.

[Azure AD 암호 보호에 대한 개념적 개요](concept-password-ban-bad-on-premises.md)
