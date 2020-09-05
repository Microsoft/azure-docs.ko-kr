---
title: 페더레이션 도메인용 하이브리드 Azure Active Directory 조인 구성 | Microsoft Docs
description: 페더레이션된 도메인용 하이브리드 Azure Active Directory 조인을 구성하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a37353615e35cd75c126c268de71d10077a9071
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268437"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>자습서: 페더레이션 도메인용 하이브리드 Azure Active Directory 조인 구성

디바이스는 조직의 사용자처럼 보호해야 하는 핵심 ID입니다. 디바이스의 ID를 사용하여 언제 어디서든 리소스를 보호할 수 있습니다. 다음 방법 중 하나를 사용하여 디바이스 ID를 Azure AD(Azure Active Directory)로 가져와서 관리하면 이 목표를 달성할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. 동시에 [조건부 액세스](../conditional-access/howto-conditional-access-policy-compliant-device.md)를 사용하여 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

페더레이션 환경은 다음 요구 사항을 지원하는 ID 공급자가 있어야 합니다. AD FS(Active Directory Federation Services)를 사용하는 페더레이션된 환경을 사용하는 경우에는 아래 요구 사항이 이미 지원됩니다.

- **WIAORMULTIAUTHN 클레임:** 이 클레임은 Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인을 수행하는 데 필요합니다.
- **WS-Trust 프로토콜:** 이 프로토콜은 Azure AD를 사용하여 현재 Windows 하이브리드 Azure AD 조인 디바이스를 인증하는 데 필요합니다.
  AD FS를 사용하는 경우 다음 WS-Trust 엔드포인트를 사용하도록 설정해야 합니다. `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 및 **adfs/services/trust/13/windowstransport**는 모두 인트라넷 연결 엔드포인트로만 사용하도록 설정해야 하며 웹 애플리케이션 프록시를 통해 엑스트라넷 연결 엔드포인트로 노출되어서는 안됩니다. WS-Trust Windows 엔드포인트를 비활성화는 방법에 대해 자세히 알아보려면 [프록시에서 WS-Trust Windows 엔드포인트 사용 안 함](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)을 참조하세요. **서비스** > **엔드포인트**에서 AD FS 관리 콘솔을 통해 어떤 엔드포인트가 사용하도록 설정되었는지 확인할 수 있습니다.

이 자습서에서는 AD FS를 사용하여 페더레이션된 환경의 Active Directory 도메인 조인 컴퓨터 디바이스에 대한 하이브리드 Azure AD 조인을 구성하는 방법을 알아봅니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 하이브리드 Azure AD 조인 구성
> * Windows 하위 수준 디바이스 사용
> * 등록 확인
> * 문제 해결

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 사용자가 다음 항목에 대해 잘 알고 있다고 가정합니다.

- [디바이스 ID란?](overview.md)
- [하이브리드 Azure AD 조인 구현을 계획하는 방법](hybrid-azuread-join-plan.md)
- [하이브리드 Azure AD 조인의 제어된 유효성 검사를 수행하는 방법](hybrid-azuread-join-control.md)

이 자습서의 시나리오를 구성하려면 다음이 필요합니다.

- Windows Server 2012 R2 AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 버전 1.1.819.0 이상

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 데 사용할 수 있는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 관련 마법사는 다음 작업을 수행합니다.

- 디바이스 등록을 위한 SCP(서비스 연결 지점) 구성
- 기존 Azure AD 신뢰 당사자 트러스트를 백업합니다.
- Azure AD 트러스트에서 클레임 규칙을 업데이트합니다.

이 문서의 구성 단계는 Azure AD Connect 마법사를 기준으로 합니다. 이전 버전의 Azure AD Connect가 설치된 경우 마법사를 사용하려면 1.1.819 이상으로 업그레이드해야 합니다. 최신 버전의 Azure AD Connect를 설치할 수 없는 경우 [수동으로 하이브리드 Azure AD 조인을 구성하는 방법](hybrid-azuread-join-manual.md)을 참조하세요.

하이브리드 Azure AD 조인을 위해서는 디바이스가 조직의 네트워크 내에서 다음 Microsoft 리소스에 액세스할 수 있어야 합니다.  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- 조직의 보안 토큰 서비스(STS)(페더레이션된 도메인)
- `https://autologon.microsoftazuread-sso.com`(Seamless SSO를 사용하거나 사용할 계획이 있는 경우)

> [!WARNING]
> 조직에서 데이터 손실 방지 또는 Azure AD 테넌트 제한과 같은 시나리오에 대한 SSL 트래픽을 가로채는 프록시 서버를 사용하는 경우 'https://device.login.microsoftonline.com '에 대한 트래픽을 TLS 중단-검사에서 제외해야 합니다. 'https://device.login.microsoftonline.com '을 제외하지 않으면 클라이언트 인증서 인증에 방해가 되어 디바이스 등록 및 디바이스 기반 조건부 액세스에 문제가 발생할 수 있습니다.

Windows 10 1803부터 AD FS를 사용하여 페더레이션된 환경에 대한 즉각적인 하이브리드 Azure AD 조인이 실패하는 경우 Azure AD Connect를 사용하여 Azure AD에서 컴퓨터 개체를 동기화한 다음, 하이브리드 Azure AD 조인에 대한 디바이스 등록을 완료합니다. Azure AD Connect에서 Azure AD에 조인된 하이브리드 Azure AD가 될 디바이스의 컴퓨터 개체를 동기화했는지 확인합니다. 컴퓨터 개체가 특정 OU(조직 구성 단위)에 속한 경우 Azure AD Connect에서도 이러한 OU에 동기화를 구성해야 합니다. Azure AD Connect를 사용하여 컴퓨터 개체를 동기화하는 방법을 자세히 알아보려면 [Azure AD Connect를 사용하여 필터링 구성](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)을 참조하세요.

조직에서 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우에 Windows 10 컴퓨터에서 디바이스를 Azure AD에 등록할 수 있도록 [WPAD(웹 프록시 자동 검색)를 구현](/previous-versions/tn-archive/cc995261(v%3dtechnet.10))하는 것이 좋습니다. WPAD를 구성하고 관리하는 문제가 발생하는 경우 [자동 검색 문제 해결](/previous-versions/tn-archive/cc302643(v=technet.10))을 참조하세요. 

WPAD를 사용하지 않고 컴퓨터에서 프록시 설정을 구성해야 하는 경우 Windows 10 1709로 시작할 수 있습니다. 자세한 내용은 [GPO(그룹 정책 개체)를 사용하여 WinHTTP 설정 구성](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo)을 참조하세요.

> [!NOTE]
> WinHTTP 설정을 사용하여 컴퓨터에서 프록시 설정을 구성하는 경우 구성된 프록시에 연결할 수 없는 모든 컴퓨터는 인터넷에 연결할 수 없습니다.

조직에서 인증된 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우 Windows 10 컴퓨터에서 아웃바운드 프록시를 성공적으로 인증할 수 있는지 확인해야 합니다. Windows 10 컴퓨터는 머신 컨텍스트를 사용하여 디바이스 등록을 실행하므로 머신 컨텍스트를 사용하여 아웃바운드 프록시 인증을 구성해야 합니다. 아웃바운드 프록시 공급자와 함께 구성 요구 사항을 준수하세요.

디바이스가 시스템 계정으로 위의 Microsoft 리소스에 액세스할 수 있는지 확인하기 위해 [디바이스 등록 연결 테스트](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) 스크립트를 사용할 수 있습니다.

## <a name="configure-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인 구성

Azure AD Connect를 사용하여 하이브리드 Azure AD 조인을 구성하려면 다음이 필요합니다.

- Azure AD 테넌트에 대한 글로벌 관리자의 자격 증명  
- 각 포리스트에 대한 엔터프라이즈 관리자 자격 증명
- AD FS 관리자의 자격 증명

**Azure AD Connect를 사용하여 하이브리드 Azure AD 조인을 구성하려면**:

1. Azure AD Connect를 시작한 다음, **구성**을 선택합니다.

   ![시작](./media/hybrid-azuread-join-federated-domains/11.png)

1. **추가 작업** 페이지에서 **디바이스 옵션 구성**을 선택한 다음, **다음**을 선택합니다.

   ![추가 작업](./media/hybrid-azuread-join-federated-domains/12.png)

1. **개요** 페이지에서 **다음**을 선택합니다.

   ![개요](./media/hybrid-azuread-join-federated-domains/13.png)

1. **Azure AD에 연결** 페이지에서 Azure AD 테넌트에 대한 글로벌 관리자 자격 증명을 입력하고 **다음**을 선택합니다.

   ![Azure에 연결](./media/hybrid-azuread-join-federated-domains/14.png)

1. **디바이스 옵션** 페이지에서 **하이브리드 Azure AD 조인 구성**을 선택한 후 **다음**을 선택합니다.

   ![디바이스 옵션](./media/hybrid-azuread-join-federated-domains/15.png)

1. **SCP** 페이지에서 다음 단계를 완료한 후 **다음**을 선택합니다.

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. 포리스트를 선택합니다.
   1. 인증 서비스를 선택합니다. 조직에서 Windows 10 클라이언트를 독점적으로 소유하고 있고 관리자가 컴퓨터/디바이스 동기화를 구성했거나 조직에서 Seamless SSO를 사용하지 않는 한, **AD FS 서버**를 선택해야 합니다.
   1. **추가**를 선택하여 엔터프라이즈 관리자 자격 증명을 입력합니다.

1. **디바이스 운영 체제** 페이지에서 Active Directory 환경의 디바이스에서 사용되는 운영 체제를 선택한 후 **다음**을 선택합니다.

   ![디바이스 운영 체제](./media/hybrid-azuread-join-federated-domains/17.png)

1. **페더레이션 구성** 페이지에서 AD FS 관리자에 대한 자격 증명을 입력하고 **다음**을 선택합니다.

   ![페더레이션 구성](./media/hybrid-azuread-join-federated-domains/18.png)

1. **구성 준비** 페이지에서 **구성**을 선택합니다.

   ![구성 준비](./media/hybrid-azuread-join-federated-domains/19.png)

1. **구성 완료** 페이지에서 **끝내기**를 선택합니다.

   ![구성 완료](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows 하위 수준 디바이스 사용

도메인에 가입된 디바이스 중 일부가 Windows 하위 수준 디바이스인 경우 다음을 수행해야 합니다.

- 디바이스 등록에 대한 로컬 인트라넷 설정 구성
- Windows 하위 수준 컴퓨터용 Microsoft Workplace Join 설치

> [!NOTE]
> Windows 7 지원은 2020년 1월 14일에 종료되었습니다. 자세한 내용은[Windows 7에 대한 지원이 종료됨](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)을 참조하세요.

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>디바이스 등록에 대한 로컬 인트라넷 설정 구성

Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인을 성공적으로 완료하고 디바이스가 Azure AD를 인증할 때 인증서 프롬프트를 표시하지 않으려면 도메인에 가입된 디바이스에 정책을 푸시하여 Internet Explorer의 로컬 인트라넷 영역에 다음 URL을 추가할 수 있습니다.

- `https://device.login.microsoftonline.com`
- 조직의 STS(페더레이션된 도메인)
- `https://autologon.microsoftazuread-sso.com`(Seamless SSO)

또한, 사용자의 로컬 인트라넷 영역에서 **스크립트를 통해 상태 표시줄 업데이트 허용**을 사용하도록 설정해야 합니다.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Windows 하위 수준 컴퓨터용 Microsoft Workplace Join 설치

Windows 하위 수준 디바이스를 등록하려면 조직에서는 [비 Windows 10 컴퓨터용 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)을 설치해야 합니다. 비 Windows 10 컴퓨터용 Microsoft Workplace Join은 Microsoft 다운로드 센터에서 사용할 수 있습니다.

 [Microsoft Endpoint Configuration Manager](/configmgr/)와 같은 소프트웨어 배포 시스템을 사용하여 패키지를 배포할 수 있습니다. 패키지는 `quiet` 매개 변수에 표준 자동 설치 옵션을 지원합니다. 구성 관리자의 현재 분기는 완료된 등록을 추적하는 기능과 같은 이전 버전보다 나은 이점이 추가로 제공됩니다.

설치 관리자는 사용자 컨텍스트에서 실행되는 예약된 작업을 시스템에 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 Azure AD를 사용하여 인증한 후 사용자 자격 증명을 사용하여 Azure AD에 디바이스를 자동으로 조인합니다.

## <a name="verify-the-registration"></a>등록 확인

디바이스 상태를 찾고 확인하는 3가지 방법은 다음과 같습니다.

### <a name="locally-on-the-device"></a>디바이스에서 로컬로

1. Windows PowerShell을 엽니다.
2. `dsregcmd /status`를 입력합니다.
3. **AzureAdJoined** 및 **DomainJoined**가 모두 **예**로 설정되어 있는지 확인합니다.
4. **DeviceId**를 사용하고 Azure Portal 또는 PowerShell을 사용하여 서비스의 상태를 비교할 수 있습니다.

### <a name="using-the-azure-portal"></a>Azure Portal 사용

1. [직접 링크](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)를 사용하여 디바이스 페이지로 이동합니다.
2. 디바이스를 찾는 방법에 대한 정보는 [Azure Portal을 사용하여 디바이스 ID를 관리하는 방법](./device-management-azure-portal.md)에서 찾을 수 있습니다.
3. **Registered** 열에 **보류 중**이 표시되면 하이브리드 Azure AD 조인이 완료되지 않은 것입니다. 페더레이션된 환경에서는 등록에 실패하고 AAD 연결이 디바이스를 동기화하도록 구성된 경우에만 이 문제가 발생할 수 있습니다.
4. **Registered** 열에 **날짜/시간**이 포함되어 있으면 하이브리드 Azure AD 조인이 완료된 것입니다.

### <a name="using-powershell"></a>PowerShell 사용

**[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** 를 사용하여 Azure 테넌트의 디바이스 등록 상태를 확인합니다. 이 cmdlet은 [Azure Active Directory PowerShell 모듈](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-2.0)에 있습니다.

**Get-MSolDevice** cmdlet을 사용하여 서비스 세부 정보를 확인하려는 경우 다음이 적용됩니다.

- **디바이스 ID**가 Windows 클라이언트의 ID와 일치하는 개체가 있어야 합니다.
- **DeviceTrustType** 값은 **Domain Joined**입니다. 이 설정은 Azure AD 포털에서 **디바이스** 페이지의 **하이브리드 Azure AD 조인**과 같습니다.
- 조건부 액세스에 사용되는 디바이스의 경우 **Enabled** 값은 **True**이고, **DeviceTrustLevel**은 **Managed**입니다.

1. 관리자 권한으로 Windows PowerShell을 엽니다.
2. `Connect-MsolService`를 입력하여 Azure 테넌트에 연결합니다.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>모든 하이브리드 Azure AD 조인 디바이스 수(**보류 중** 상태 제외)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>**보류 중** 상태가 포함된 모든 하이브리드 Azure AD 조인 디바이스 수

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>모든 하이브리드 Azure AD 조인 디바이스 나열

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>**보류 중** 상태가 포함된 모든 하이브리드 Azure AD 조인 디바이스 나열

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>단일 디바이스의 세부 정보를 나열합니다.

1. `get-msoldevice -deviceId <deviceId>`(디바이스에서 로컬로 가져온 **DeviceId**)를 입력합니다.
2. **Enabled**가 **True**로 설정되어 있는지 확인인합니다.

## <a name="troubleshoot-your-implementation"></a>구현 문제 해결

도메인 가입 Windows 디바이스에 대한 하이브리드 Azure AD 조인을 완료할 때 문제가 발생하면 다음을 참조하세요.

- [dsregcmd 명령을 사용하여 디바이스 문제 해결](./troubleshoot-device-dsregcmd.md)
- [Windows 최신 디바이스의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 디바이스 ID를 관리](device-management-azure-portal.md)하는 방법을 알아봅니다.

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png