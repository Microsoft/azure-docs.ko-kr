---
title: Azure Active Directory join 구현을 계획 하는 방법
description: 환경에서 Azure AD 조인 디바이스를 구현하는 데 필요한 단계를 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0863a782b7f4531b900bc3c005a39387c83d983
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268230"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>방법: Azure AD 조인 구현 계획

Azure AD 조인을 사용하면 사용자의 생산성과 보안을 유지하면서도 디바이스를 온-프레미스 Active Directory에 조인할 필요 없이 Azure AD에 직접 조인할 수 있습니다. 기업에서 즉시 사용 가능한 Azure AD 조인은 확장형 배포와 범위가 지정된 배포에 모두 사용할 수 있습니다.   

이 문서에서는 Azure AD 조인 구현을 계획하는 데 필요한 정보를 제공합니다.
 
## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 사용자가 [Azure Active Directory의 디바이스 관리 소개](./overview.md)를 잘 알고 있다고 가정합니다.

## <a name="plan-your-implementation"></a>구현 계획

Azure AD 조인 구현을 계획 하려면 다음을 숙지 해야 합니다.

> [!div class="checklist"]
> - 시나리오 검토
> - ID 인프라 검토
> - 디바이스 관리 평가
> - 애플리케이션 및 리소스에 대한 고려 사항 이해
> - 프로비전 옵션 이해
> - 엔터프라이즈 상태 로밍 구성
> - 조건부 액세스 구성

## <a name="review-your-scenarios"></a>시나리오 검토 

특정 시나리오에는 하이브리드 Azure AD 조인이 더 적합할 수 있지만, Azure AD 조인은 Windows를 사용하는 클라우드 우선 모델로 전환할 수 있습니다. 디바이스 관리를 현대화하고 디바이스 관련 IT 비용을 절감하려는 경우 Azure AD 조인은 이러한 목표를 달성하기 위한 훌륭한 기반을 제공합니다.  
 
목표가 다음 조건과 일치하는 경우 Azure AD 조인을 고려해야 합니다.

- 사용자의 생산성 제품군으로 Microsoft 365를 채택하려 합니다.
- 클라우드 디바이스 관리 솔루션을 사용하여 디바이스를 관리하려고 합니다.
- 지리적으로 분산된 사용자의 디바이스 프로비저닝을 간소화하려고 합니다.
- 애플리케이션 인프라를 현대화할 계획입니다.

## <a name="review-your-identity-infrastructure"></a>ID 인프라 검토  

Azure AD 조인은 관리 환경 및 페더레이션 환경 모두에서 작동합니다.  

### <a name="managed-environment"></a>관리 환경

관리 환경은 Seamless Single Sign On을 사용하여 [암호 해시 동기화](../hybrid/how-to-connect-password-hash-synchronization.md) 또는 [통과 인증](../hybrid/how-to-connect-pta-quick-start.md)을 통해 배포할 수 있습니다.

이러한 시나리오는 인증용 페더레이션 서버를 구성할 필요가 없습니다.

### <a name="federated-environment"></a>페더레이션 환경

페더레이션 환경은 WS-Trust 및 WS-Fed 프로토콜을 지원하는 ID 공급자가 있어야 합니다.

- **WS-Fed:** 이 프로토콜은 디바이스를 Azure AD에 조인하는 데 필요합니다.
- **WS-Trust:** 이 프로토콜은 Azure AD 조인 디바이스에 로그인하는 데 필요합니다.

AD FS를 사용하는 경우 다음 WS-Trust 엔드포인트를 사용하도록 설정해야 합니다. `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

ID 공급자가 이러한 프로토콜을 지원하지 않는 경우 Azure AD 조인이 기본적으로 작동하지 않습니다. 

>[!NOTE]
> 현재 Azure AD 조인은 [기본 인증 방법으로 외부 인증 공급자를 사용 하 여 구성 된 AD FS 2019](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary)에서 작동 하지 않습니다. Azure AD 조인은 기본 방법으로 암호 인증을 기본값으로 설정 하므로이 시나리오에서 인증 오류가 발생 합니다.


### <a name="smartcards-and-certificate-based-authentication"></a>스마트 카드 및 인증서 기반 인증

스마트 카드 또는 인증서 기반 인증을 사용하여 디바이스를 Azure AD에 조인할 수 없습니다. 그러나 AD FS가 구성된 경우 스마트 카드를 사용하여 Azure AD 조인 디바이스에 로그인할 수 있습니다.

**권장 사항:** Windows 10 디바이스에 강력한 암호 없는 인증을 사용하려면 비즈니스용 Windows Hello를 구현하세요.

### <a name="user-configuration"></a>사용자 구성

다음 위치에서 사용자를 만드는 경우:

- **온-프레미스 Active Directory** - [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md)를 사용하여 Azure AD에 동기화해야 합니다. 
- **Azure AD** - 추가 설치가 필요 없습니다.

Azure AD UPN과는 다른 온-프레미스 UPN은 Azure AD 가입 디바이스에서 지원되지 않습니다. 사용자가 온-프레미스 UPN을 사용하는 경우 Azure AD에서 사용자의 기본 UPN을 사용하도록 전환할 계획을 세워야 합니다.

## <a name="assess-your-device-management"></a>디바이스 관리 평가

### <a name="supported-devices"></a>지원되는 디바이스

Azure AD 조인:

- Windows 10 디바이스에만 적용됩니다. 
- 이전 버전의 Windows 또는 다른 운영 체제에는 적용되지 않습니다. Windows 7/8.1 디바이스의 경우 Azure AD 조인을 배포하려면 Windows 10으로 업그레이드해야 합니다.
- 는 FIPS 규격 TPM 2.0에 대해 지원 되지만 TPM 1.2에는 지원 되지 않습니다. 장치에 FIPS 규격 TPM 1.2가 있는 경우 Azure AD 조인을 계속 하기 전에 사용 하지 않도록 설정 해야 합니다. TPM은 TPM 제조업체에 따라 다르므로 tpm에서 FIPS 모드를 사용 하지 않도록 설정 하는 도구는 제공 하지 않습니다. 하드웨어 OEM에 지원을 문의 하세요.
 
**권장 사항:** 항상 최신 Windows 10 릴리스를 사용하여 업데이트된 기능을 활용하는 것이 좋습니다.

### <a name="management-platform"></a>관리 플랫폼

Azure AD 가입 장치에 대 한 장치 관리는 Intune 및 MDM Csp와 같은 MDM 플랫폼을 기반으로 합니다. Windows 10에는 모든 호환 MDM 솔루션과 함께 작동하는 MDM 에이전트가 기본적으로 제공됩니다.

> [!NOTE]
> 그룹 정책은 온-프레미스 Active Directory에 연결 되어 있지 않으므로 Azure AD 조인 장치에서 지원 되지 않습니다. MDM을 통해서만 Azure AD 조인 장치를 관리할 수 있습니다.

Azure AD 조인 디바이스를 관리하는 두 가지 방법이 있습니다.

- **MDM 전용** - Intune 같은 MDM 공급자를 통해서만 디바이스를 관리합니다. 모든 정책은 MDM 등록 프로세스의 일부로 전달됩니다. Azure AD Premium 또는 EMS 고객의 경우 MDM 등록 단계가 Azure AD 조인의 일부이며 자동화되어 있습니다.
- **공동 관리** - MDM 공급자와 SCCM이 디바이스를 함께 관리합니다. 이 방법에서는 특정 측면을 관리하기 위해 SCCM 에이전트가 MDM 관리 디바이스에 설치됩니다.

그룹 정책을 사용하는 경우 [MMAT(MDM 마이그레이션 분석 도구)](https://github.com/WindowsDeviceManagement/MMAT)를 사용하여 MDM 정책 패리티를 평가합니다. 

지원되는 정책과 지원되지 않는 정책을 검토하여 그룹 정책 대신 MDM 솔루션을 사용할 수 있는지 확인합니다. 지원되지 않는 정책의 경우 다음 사항을 고려합니다.

- 지원되지 않는 정책이 Azure AD 조인 디바이스 또는 사용자에게 필요합니까?
- 지원되지 않는 정책을 클라우드 기반 배포에 적용할 수 있습니까?

Azure AD 앱 갤러리를 통해 MDM 솔루션을 사용할 수 없는 경우 [MDM과 Azure Active Directory 통합](/windows/client-management/mdm/azure-active-directory-integration-with-mdm)에 설명된 프로세스에 따라 추가할 수 있습니다. 

공동 관리를 통해 SCCM을 사용하여 디바이스의 특정 측면을 관리할 수 있으며, 정책은 MDM 플랫폼을 통해 제공됩니다. Microsoft Intune은 SCCM을 사용한 공동 관리를 지원합니다. Windows 10 장치에 대 한 공동 관리에 대 한 자세한 내용은 [공동 관리 란?](/configmgr/core/clients/manage/co-management-overview)을 참조 하세요. Intune이 아닌 MDM 제품을 사용하는 경우 해당하는 공동 관리 시나리오에서 MDM 공급자를 확인하세요.

**권장 사항:** Azure AD 조인 디바이스에는 MDM 전용 관리를 고려해 보세요.

## <a name="understand-considerations-for-applications-and-resources"></a>애플리케이션 및 리소스에 대한 고려 사항 이해

보다 나은 사용자 환경과 액세스 제어를 위해 애플리케이션을 온-프레미스에서 클라우드로 마이그레이션하는 것이 좋습니다. 그러나 Azure AD 조인 디바이스는 온-프레미스 및 클라우드 애플리케이션에 대한 액세스를 원활하게 제공할 수 있습니다. 자세한 내용은 [온-프레미스 리소스에 대한 SSO가 Azure AD 조인 디바이스에서 작동하는 방식](azuread-join-sso.md)을 참조하세요.

다음 섹션에는 다양한 종류의 애플리케이션 및 리소스에 대한 고려 사항이 나열되어 있습니다.

### <a name="cloud-based-applications"></a>클라우드 기반 애플리케이션

Azure AD 앱 갤러리에 애플리케이션이 추가되면 사용자는 Azure AD 조인 디바이스를 통해 SSO를 얻습니다. 추가적인 서버 구성은 필요하지 않습니다. 사용자는 Microsoft Edge 및 Chrome 브라우저 둘 다에서 SSO를 얻습니다. Chrome의 경우 [Windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 배포해야 합니다. 

다음에 해당하는 모든 Win32 애플리케이션:

- 토큰 요청에 WAM(Web Account Manager)을 사용하는 애플리케이션 역시 Azure AD 조인 디바이스에서 SSO를 얻습니다. 
- WAM을 사용하지 않는 애플리케이션은 사용자에게 인증을 요구할 수 있습니다. 

### <a name="on-premises-web-applications"></a>온-프레미스 웹 애플리케이션

앱이 맞춤 제작되었거나 온-프레미스에 호스트되는 경우 다음 작업을 수행하려면 브라우저의 신뢰할 수 있는 사이트에 앱을 추가해야 합니다.

- Windows 통합 인증 지원 
- 사용자에게 프롬프트 없는 SSO 환경 제공 

AD FS를 사용하는 경우 [AD FS를 사용하여 Single Sign-On 확인 및 관리](/previous-versions/azure/azure-services/jj151809(v%3dazure.100))를 참조하세요. 

**권장 사항:** 보다 나은 환경을 제공할 수 있도록 클라우드에 호스트하고(예: Azure) Azure AD와 통합하는 방안을 고려합니다.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>레거시 프로토콜에 의존하는 온-프레미스 애플리케이션

디바이스가 도메인 컨트롤러에 액세스할 수 있는 경우 사용자는 Azure AD 조인 디바이스에서 SSO를 얻습니다. 

**권장 사항:** 이러한 애플리케이션에 안전하게 액세스할 수 있도록 [Azure AD 앱 프록시](../manage-apps/application-proxy.md)를 배포합니다.

### <a name="on-premises-network-shares"></a>온-프레미스 네트워크 공유

디바이스가 온-프레미스 도메인 컨트롤러에 액세스할 수 있는 경우 사용자는 Azure AD 조인 디바이스에서 SSO를 얻습니다.

### <a name="printers"></a>프린터

프린터의 경우 Azure AD 조인 디바이스에서 프린터를 검색하기 위한 [하이브리드 클라우드 인쇄](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)를 배포해야 합니다. 

클라우드 전용 환경에서는 프린터를 자동으로 검색할 수 없지만, 사용자가 프린터의 UNC 경로를 사용하여 프린터를 직접 추가할 수도 있습니다. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>머신 인증에 의존하는 온-프레미스 애플리케이션

Azure AD 조인 디바이스는 머신 인증에 의존하는 온-프레미스 애플리케이션을 지원하지 않습니다. 

**권장 사항:** 다음 애플리케이션을 폐기하고 현대식 대안 애플리케이션으로 이동하는 방안을 고려합니다.

### <a name="remote-desktop-services"></a>원격 데스크톱 서비스

Azure AD 조인 디바이스에 원격 데스크톱 연결을 설정하려면 호스트 머신이 Azure AD에 조인되거나 하이브리드 Azure AD에 조인되어야 합니다. 조인되지 않은 디바이스 또는 Windows 이외의 디바이스에서 원격 데스크톱으로 연결하는 기능은 지원되지 않습니다. 자세한 내용은 [원격 Azure AD 조인 pc에 연결](/windows/client-management/connect-to-remote-aadj-pc)을 참조하세요.

Windows 10 2004 업데이트를 시작 하면 사용자가 Azure AD에 등록 된 Windows 10 장치에서 원격 데스크톱을 Azure AD 조인 장치에 사용할 수 있습니다. 

## <a name="understand-your-provisioning-options"></a>프로비전 옵션 이해

다음 방법을 사용하여 Azure AD 조인을 프로비전할 수 있습니다.

- **OOBE/설정에서 셀프 서비스** - 셀프 서비스 모드에서는 사용자가 Windows OOBE(첫 실행 경험) 동안 또는 Windows 설정에서 Azure AD 조인 프로세스를 진행합니다. 자세한 내용은 [회사 디바이스를 조직의 네트워크에 조인](../user-help/user-help-join-device-on-network.md)을 참조하세요. 
- **Windows Autopilot** - Windows Autopilot을 사용하여 디바이스를 미리 구성하면 OOBE에서 보다 원활하게 Azure AD 조인을 수행하는 환경을 구축할 수 있습니다. 자세한 내용은 [Windows Autopilot 개요](/windows/deployment/windows-autopilot/windows-10-autopilot)를 참조하세요. 
- **대량 등록** - 대량 등록을 사용하면 대량 프로비전 도구를 사용하여 디바이스를 구성하는 관리자 중심 Azure AD 조인을 지원할 수 있습니다. 자세한 내용은 [Windows 디바이스 대량 등록](/intune/windows-bulk-enroll)을 참조하세요.
 
다음은 세 방법을 비교한 내용입니다. 
 
| 요소 | 셀프 서비스 설정 | Windows Autopilot | 대량 등록 |
| --- | --- | --- | --- |
| 설정에서 사용자 상호 작용이 필요 | 예 | 예 | 예 |
| IT 활동이 필요 | 예 | 예 | 예 |
| 적용 가능한 흐름 | OOBE 및 설정 | OOBE만 | OOBE만 |
| 기본 사용자에 대한 로컬 관리자 권한 | 기본적으로 예 | 구성 가능 | 아니요 |
| 디바이스 OEM 지원 필요 | 예 | 예 | 예 |
| 지원되는 버전 | 1511+ | 1709+ | 1703+ |
 
위의 표를 검토하고 방법 채택에 대한 다음 고려 사항을 검토하여 배포 접근 방식을 선택합니다.  

- 기술 지식을 갖춘 사용자가 직접 설치합니까? 
   - 이러한 사용자에게는 셀프 서비스가 가장 적합할 수 있습니다. 사용자 환경을 개선하기 위해 Windows Autopilot을 고려해 보세요.  
- 사용자가 원격으로 일합니까 아니면 회사 프레미스 내에서 일합니까? 
   - 원격 사용자에게는 설치 방법이 간편한 셀프 서비스 또는 Autopilot이 가장 적합합니다. 
- 사용자 중심 구성과 관리자 관리 구성 중 무엇을 선호하나요? 
   - 디바이스를 설정하여 사용자에게 전달하는 관리자 중심 배포에는 대량 등록이 가장 적합합니다.     
- OEM 1-2곳에서 구입하십니까 아니면 다양한 유통업체에서 OEM 디바이스를 구입하십니까?  
   - Autopilot까지 지원하는 소수의 OEM에서 구입하는 경우 Autopilot과 긴밀하게 통합하여 혜택을 얻을 수 있습니다. 

## <a name="configure-your-device-settings"></a>디바이스 설정 구성

Azure Portal에서 조직의 Azure AD 조인 디바이스 배포를 제어할 수 있습니다. 관련 설정을 구성하려면 **Azure Active Directory 페이지**에서 `Devices > Device settings`을 선택합니다.

### <a name="users-may-join-devices-to-azure-ad"></a>사용자가 디바이스를 Azure AD에 조인할 수 있음

배포 범위 및 Azure AD 조인 디바이스를 설정할 수 있도록 허용할 사용자에 따라 이 옵션을 **모두** 또는 **선택한 사용자**로 설정합니다. 

![사용자가 디바이스를 Azure AD에 조인할 수 있음](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Azure AD 조인 디바이스의 추가 로컬 관리자

**선택한 사용자**를 선택하고 모든 Azure AD 조인 디바이스의 로컬 관리자 그룹에 추가하려는 사용자를 선택합니다. 

![Azure AD 조인 디바이스의 추가 로컬 관리자](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>디바이스를 조인하려면 다단계 인증 필요

디바이스를 Azure AD에에 조인하는 동안 사용자에게 MFA를 요구하려면 **"예"** 를 선택합니다. MFA를 사용하여 디바이스를 Azure AD에 조인하는 사용자의 경우 디바이스 자체가 두 번째 요소입니다.

![디바이스를 조인하려면 다단계 인증 필요](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>모바일 설정 구성

모바일 설정을 구성하려면 먼저 MDM 공급자를 추가해야 합니다.

**MDM 공급자를 추가하려면**:

1. **Azure Active Directory 페이지**의 **관리** 섹션에서 `Mobility (MDM and MAM)`를 클릭합니다. 
1. **애플리케이션 추가**를 클릭합니다.
1. 목록에서 MDM 공급자를 선택합니다.

   ![애플리케이션 추가](./media/azureadjoin-plan/04.png)

MDM 공급자를 선택하여 관련 설정을 구성합니다. 

### <a name="mdm-user-scope"></a>MDM 사용자 범위

배포 범위에 따라 **일부** 또는 **모두**를 선택합니다. 

![MDM 사용자 범위](./media/azureadjoin-plan/05.png)

범위에 따라 다음 중 하나가 발생합니다. 

- **사용자가 MDM 범위에 있음**: Azure AD Premium 구독을 보유한 경우 Azure AD 조인과 함께 MDM 등록이 자동화됩니다. 범위가 지정된 모든 사용자는 MDM에 대한 적절한 라이선스가 있어야 합니다. 이 시나리오에서 MDM 등록이 실패하면 Azure AD 조인도 롤백됩니다.
- **사용자가 MDM 범위에 없음**: 사용자가 MDM 범위에 없는 경우 MDM 등록 없이 Azure AD 조인이 완료됩니다. 이는 결국 관리되지 않는 디바이스로 이어집니다.

### <a name="mdm-urls"></a>MDM URL

MDM 구성과 관련된 세 가지 URL이 있습니다.

- MDM 사용 약관 URL
- MDM 검색 URL 
- MDM 규정 준수 URL

![애플리케이션 추가](./media/azureadjoin-plan/06.png)

각 URL에는 사전 정의된 기본값이 있습니다. 이러한 필드가 비어 있으면 MDM 공급자에게 자세한 정보를 문의하세요.

### <a name="mam-settings"></a>MAM 설정

MAM은 Azure AD 조인에 적용되지 않습니다. 

## <a name="configure-enterprise-state-roaming"></a>엔터프라이즈 상태 로밍 구성

사용자가 자신의 설정을 디바이스 간에 동기화 할 수 있도록 Azure AD에 상태 로밍을 사용하려면 [Azure Active Directory에서 엔터프라이즈 상태 로밍 사용](enterprise-state-roaming-enable.md)을 참조하세요. 

**권장 사항**: 하이브리드 Azure AD 조인 디바이스에도 이 설정을 사용하세요.

## <a name="configure-conditional-access"></a>조건부 액세스 구성

Azure AD 조인 디바이스에 대한 MDM 공급자가 구성된 경우 공급자는 디바이스 관리가 시작되는 즉시 디바이스에 준수 플래그를 지정합니다. 

![규정 준수 디바이스](./media/azureadjoin-plan/46.png)

이 구현을 사용 하 여 [조건부 액세스를 통해 클라우드 앱 액세스를 위한 관리 되는 장치를 요구할](../conditional-access/require-managed-devices.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [처음 실행 하는 동안 AZURE AD에 새 Windows 10 장치 조인](azuread-joined-devices-frx.md) 
>  회사 [장치를 조직의 네트워크에 연결](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png