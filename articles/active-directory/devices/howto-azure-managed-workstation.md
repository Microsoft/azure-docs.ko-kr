---
title: Azure 관리 되는 워크스테이션 배포-Azure Active Directory
description: 안전 하 고 Azure로 관리 되는 워크스테이션을 배포 하 여 잘못 된 구성 또는 손상으로 인 한 위반 위험을 줄이는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841bc3ae4fbddb376ea4da8141bf4df3f895c4dc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269559"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>안전 하 고 Azure로 관리 되는 워크스테이션 배포

이제 [안전한 워크스테이션을 이해](concept-azure-managed-workstation.md)했으므로 이제 배포 프로세스를 시작할 수 있습니다. 이 지침을 사용 하 여 정의 된 프로필을 사용 하 여 처음부터 더 안전 하 게 보호 되는 워크스테이션을 만듭니다.

![보안 워크스테이션 배포](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

솔루션을 배포 하기 전에 프로필을 선택 하십시오. 배포에서 동시에 여러 프로필을 사용 하 고 태그 또는 그룹을 사용 하 여 할당할 수 있습니다.

> [!NOTE]
> 요구 사항에 따라 필요에 따라 프로필을 적용 합니다. Microsoft Intune에서 할당 하 여 다른 프로필로 이동할 수 있습니다.

| 프로필 | 낮음 | 향상된 | 높음 | 특수화 | 보안됨 | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Azure AD의 사용자 | 예 | 예 | 예 | 예 | 예 | 예 |
| Intune 관리 | 예 | 예 | 예 | 예 | 예 | 예 |
| 장치-Azure AD 등록 됨 | 예 |  |  |  |  | |   |
| 장치-Azure AD 조인 됨 |   | 예 | 예 | 예 | 예 | 예 |
| Intune 보안 기준이 적용 됨 |   | 예 <br> 한층 | 예 <br> (HighSecurity) | 예 <br> NCSC | 예 <br> 보안 | 해당 없음 |
| 하드웨어가 안전한 Windows 10 표준을 충족 합니다. |   | 예 | 예 | 예 | 예 | 예 |
| Microsoft Defender ATP 사용 |   | 예  | 예 | 예 | 예 | 예 |
| 관리자 권한 제거 |   |   | 예  | 예 | 예 | 예 |
| Microsoft Autopilot를 사용 하 여 배포 |   |   | 예  | 예 | 예 | 예 |
| Intune에 의해서만 설치 된 앱 |   |   |   | 예 | 예 |예 |
| 승인 된 목록으로 제한 된 Url |   |   |   | 예 | 예 |예 |
| 인터넷 차단 됨 (인바운드/아웃 바운드) |   |   |   |  |  |예 |

> [!NOTE]
> 보안 워크스테이션 지침에서 **장치** 는 프로필 및 정책을 사용 하 여 할당 됩니다. 사용자는 정책을 직접 적용 하지 않으므로 장치 공유 (공유 장치)가 적용 될 수 있습니다. 배포에서 보안 워크스테이션이 공유 되지 않거나 개별 사용자 정책이 필요한 경우 사용자 정책 프로필을 할당 하 여 사용자 및 장치에 할당할 수 있습니다. 

## <a name="license-requirements"></a>라이선스 요구 사항

이 가이드에서 설명 하는 개념은 E5 또는 동등한 SKU Microsoft 365 Enterprise 있다고 가정 합니다. 이 가이드의 일부 권장 사항은 낮은 Sku를 사용 하 여 구현할 수 있습니다. 자세한 내용은 [Microsoft 365 Enterprise 라이선스](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)를 참조 하세요.

라이선스 프로 비전을 자동화 하려면 사용자에 대 한 [그룹 기반 라이선스](../users-groups-roles/licensing-groups-assign.md) 를 고려 합니다.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory 구성

Azure AD (Azure Active Directory)는 관리자 워크스테이션의 사용자, 그룹 및 장치를 관리 합니다. [관리자 계정을](../users-groups-roles/directory-assign-admin-roles.md)사용 하 여 id 서비스 및 기능을 사용 하도록 설정 합니다.

보안 워크스테이션 관리자 계정을 만들 때 현재 워크스테이션에 계정을 노출 합니다. 이 초기 구성과 모든 전역 구성을 수행 하려면 알려진 안전한 장치를 사용 해야 합니다. 첫 번째 환경의 공격 노출을 줄이려면 [맬웨어 감염을 방지 하는 지침](/windows/security/threat-protection/intelligence/prevent-malware-infection)을 수행 하는 것이 좋습니다.

관리자에 게는 이상 multi-factor authentication이 필요 합니다. 구현 지침은 [클라우드 기반 MFA 배포](../authentication/howto-mfa-getstarted.md) 를 참조 하세요.

### <a name="azure-ad-users-and-groups"></a>Azure AD 사용자 및 그룹

1. Azure Portal에서 **Azure Active Directory**  >  **사용자**  >  **새 사용자**로 이동 합니다.
1. [사용자 만들기 자습서](/Intune/quickstart-create-user)의 단계를 수행 하 여 장치 관리자를 만듭니다.
1. 다음을 입력합니다.

   * **이름** -워크스테이션 관리자 보안
   * **사용자 이름** - `secure-ws-admin@identityitpro.com`
   * **디렉터리 역할**  -  **제한 된 관리자** 를 선택 하 고 **Intune 관리자** 역할을 선택 합니다.

1. **만들기**를 선택합니다.

다음으로 워크스테이션 사용자 및 워크스테이션 장치 라는 두 그룹을 만듭니다.

Azure Portal에서 **Azure Active Directory**  >  **그룹**  >  **새 그룹**으로 이동 합니다.

1. 워크스테이션 사용자 그룹의 경우 사용자에 대 한 라이선스 프로 비전을 자동화 하도록 [그룹 기반 라이선스](../users-groups-roles/licensing-groups-assign.md) 를 구성 해야 할 수 있습니다.
1. 워크스테이션 사용자 그룹에 대해 다음을 입력 합니다.

   * **그룹 종류** -보안
   * **그룹 이름** -안전한 워크스테이션 사용자
   * **멤버 자격 유형** -할당 됨

1. 보안 워크스테이션 관리자 사용자를 추가 합니다. `secure-ws-admin@identityitpro.com`
1. 보안 워크스테이션을 관리 하는 다른 사용자를 추가할 수 있습니다.
1. **만들기**를 선택합니다.
1. 워크스테이션 장치 그룹에 다음을 입력 합니다.

   * **그룹 종류** -보안
   * **그룹 이름** -안전한 워크스테이션
   * **멤버 자격 유형** -할당 됨

1. **만들기**를 선택합니다.

### <a name="azure-ad-device-configuration"></a>Azure AD 장치 구성

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Azure AD에 장치를 조인할 수 있는 사용자 지정

Active Directory에서 장치 설정을 구성 하 여 관리 보안 그룹이 사용자의 도메인에 장치를 연결할 수 있도록 합니다. Azure Portal에서이 설정을 구성 하려면:

1. **Azure Active Directory** > **디바이스** > **디바이스 설정**으로 이동하세요.
1. **사용자가 AZURE AD에 장치를 조인할 수 있습니다**. **를 선택한 다음** "보안 워크스테이션 사용자" 그룹을 선택 합니다.

#### <a name="removal-of-local-admin-rights"></a>로컬 관리자 권한 제거

이 방법을 사용 하려면 VIP, DevOps 및 보안 수준 워크스테이션의 사용자에 게 컴퓨터에 대 한 관리자 권한이 있어야 합니다. Azure Portal에서이 설정을 구성 하려면:

1. **Azure Active Directory** > **디바이스** > **디바이스 설정**으로 이동하세요.
1. **AZURE AD 조인 장치의 추가 로컬 관리자**에서 **없음** 을 선택 합니다.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>장치를 연결 하려면 multi-factor authentication 필요

Azure AD에 장치를 조인 하는 프로세스를 더욱 강화 하려면:

1. **Azure Active Directory** > **디바이스** > **디바이스 설정**으로 이동하세요.
1. **다단계 인증 필요에서 예를 선택 하 여 장치를 연결**합니다. **Yes**
1. **저장**을 선택합니다.

#### <a name="configure-mobile-device-management"></a>모바일 장치 관리 구성

Azure 포털에서 다음을 수행합니다.

1. **Azure Active Directory**  >  **Mobility (MDM 및 MAM)**  >  **Microsoft Intune**로 이동 합니다.
1. **MDM 사용자 범위** 설정을 **모두**로 변경 합니다.
1. **저장**을 선택합니다.

이러한 단계를 통해 Intune을 사용 하 여 모든 장치를 관리할 수 있습니다. 자세한 내용은 [Intune 빠른 시작: Windows 10 장치에 대 한 자동 등록 설정](/Intune/quickstart-setup-auto-enrollment)을 참조 하세요. 이후 단계에서 Intune 구성 및 규정 준수 정책을 만듭니다.

#### <a name="azure-ad-conditional-access"></a>Azure AD 조건부 액세스

Azure AD 조건부 액세스를 사용 하면 권한 있는 관리 작업을 규격 장치로 제한할 수 있습니다. **보안 워크스테이션 사용자** 그룹의 미리 정의 된 구성원은 클라우드 응용 프로그램에 로그인 할 때 multi-factor authentication을 수행 해야 합니다. 모범 사례는 정책에서 응급 액세스 계정을 제외 하는 것입니다. 자세한 내용은 [AZURE AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)를 참조 하세요.

## <a name="intune-configuration"></a>Intune 구성

### <a name="configure-enrollment-status"></a>등록 상태 구성

안전한 워크스테이션이 신뢰할 수 있는 깨끗 한 장치 인지 확인 하는 것이 중요 합니다. 새 장치를 구매할 때 공장을 [Windows 10 Pro](/Windows/deployment/Windows-10-pro-in-s-mode)로 설정 하는 것을 선택할 수 있습니다 .이 모드에서는 공급망 관리 중 취약점에 대 한 노출을 제한 합니다. 공급자에서 장치를 받은 후에는 Autopilot를 사용 하 여 S 모드에서 장치를 변경할 수 있습니다. 다음 지침에서는 변환 프로세스를 적용 하는 방법에 대해 자세히 설명 합니다.

장치를 사용 하기 전에 장치를 완전히 구성 하기 위해 Intune은 **모든 앱과 프로필이 설치 될 때까지 장치 사용을 차단**하는 수단을 제공 합니다.

**Azure Portal**에서:

1. **Microsoft Intune**  >  **장치 등록**  >  **Windows 등록**  >  **등록 상태 페이지**  >  **기본**  >  **설정**으로 이동 합니다.
1. **앱 프로필 설치 진행률 표시** 를 **예**로 설정 합니다.
1. **모든 앱 및 프로필을 예로 설치할 때까지 장치 사용 차단** 을 설정 합니다. **Yes**

### <a name="create-an-autopilot-deployment-profile"></a>Autopilot 배포 프로필 만들기

장치 그룹을 만든 후에는 배포 프로필을 만들어 Autopilot 장치를 구성 해야 합니다.

Azure Portal의 Intune에서 다음을 수행 합니다.

1. **장치 등록**  >  **Windows 등록**  >  **배포 프로필**  >  **프로필 만들기**를 선택 합니다.
1. 다음을 입력합니다.

   * 이름- **워크스테이션 배포 프로필을 보호**합니다.
   * 설명- **보안 워크스테이션 배포**
   * **모든 대상 디바이스를 Autopilot으로 변환**을 **예**로 설정합니다. 이렇게 설정하면 목록의 모든 디바이스가 Autopilot 배포 서비스를 사용하여 등록됩니다. 등록을 처리하는 데 48시간 정도가 걸립니다.

1. **다음**을 선택합니다.

   * **배포 모드**의 경우 **자체 배포 (미리 보기)** 를 선택 합니다. 이 프로필을 사용 하는 장치는 장치를 등록 하는 사용자와 연결 됩니다. 디바이스를 등록하려면 사용자 자격 증명이 필요합니다. **자체 배포** 모드에서 장치를 배포 하면 공유 모델에 랩톱을 배포할 수 있습니다. 사용자가 처음으로 사용자에 게 할당 될 때까지 사용자 할당은 발생 하지 않습니다. 따라서 사용자 할당이 완료 될 때까지 BitLocker와 같은 사용자 정책을 사용 하도록 설정 되지 않습니다. 보안 장치에 로그온 하는 방법에 대 한 자세한 내용은 [선택한 프로필](/intune/device-profile-assign)을 참조 하세요.
   * **AZURE ad에 조인** 상자는 **azure ad에 조인** 되 고 회색으로 표시 되어야 합니다.
   * 언어 (지역), 사용자 계정 유형 **표준**을 선택 합니다. 

1. **다음**을 선택합니다.

   * 항목을 미리 구성한 경우 범위 태그를 선택 합니다.

1. **다음**을 선택합니다.
1. **Assignments**  >  선택한 그룹**에 할당 할당을**선택  >  **Selected Groups**합니다. **포함할 그룹 선택**에서 **워크스테이션 보안**을 선택 합니다.
1. **다음**을 선택합니다.
1. **만들기**를 선택하여 프로필을 만듭니다. 이제 Autopilot 배포 프로필을 디바이스에 할당할 수 있습니다.

Autopilot의 장치 등록은 장치 유형 및 역할에 따라 다른 사용자 환경을 제공 합니다. 이 배포 예제에서는 보호 된 장치를 대량으로 배포 하 고 공유할 수 있는 모델을 설명 하지만, 처음 사용 하는 경우 사용자에 게 장치가 할당 됩니다. 자세한 내용은 [Intune Autopilot device 등록](/intune/device-enrollment)을 참조 하세요.

### <a name="configure-windows-update"></a>Windows 업데이트 구성

Windows 10을 최신 상태로 유지 하는 것은 수행할 수 있는 가장 중요 한 작업 중 하나입니다. Windows를 안전한 상태로 유지 하기 위해 업데이트 링을 배포 하 여 업데이트를 워크스테이션에 적용 하는 속도를 관리 합니다. 

이 지침에서는 새 업데이트 링을 만들고 다음과 같은 기본 설정을 변경 하는 것이 좋습니다.

Azure Portal에서 다음을 수행합니다.

1. **Microsoft Intune**  >  **소프트웨어 업데이트**  >  **Windows 10 업데이트 링**으로 이동 합니다.
1. 다음을 입력합니다.

   * 이름- **Azure 관리 되는 워크스테이션 업데이트**
   * 서비스 채널- **Windows 참가자-빠름**
   * 품질 업데이트 지연 (일)- **3**
   * 기능 업데이트 지연 기간 (일)- **3**
   * 자동 업데이트 동작- **최종 사용자 제어 없이 자동 설치 및 다시 부팅**
   * 사용자가 Windows 업데이트를 일시 중지 하지 못하도록 차단- **블록**
   * 근무 외 시간에 다시 시작 하려면 사용자 승인 필요- **필수**
   * 사용자가 다시 시작 하도록 허용 (개입 형 다시 시작)- **필수**
   * 자동 다시 시작 후 사용자가 개입 된 다시 시작으로 전환 (일)- **3**
   * 개입 다시 시작 미리 알림 (일)- **3**
   * 보류 중인 다시 시작에 대 한 최종 기한 설정 (일)- **3**

1. **만들기**를 선택합니다.
1. **할당** 탭에서 **보안 워크스테이션** 그룹을 추가 합니다.

Windows 업데이트 정책에 대 한 자세한 내용은 [정책 CSP-업데이트](/windows/client-management/mdm/policy-csp-update)를 참조 하세요.

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune 통합

Windows Defender ATP 및 Microsoft Intune는 함께 작동 하 여 보안 위반을 방지 합니다. 위반의 영향을 제한할 수도 있습니다. ATP 기능은 실시간 위협 감지를 제공 하 고, 끝점의 포괄적인 감사 및 로깅을 가능 하 게 합니다.

Windows Defender ATP 및 Intune의 통합을 구성 하려면 Azure Portal으로 이동 합니다.

1. **Microsoft Intune**  >  **장치 준수**  >  **Windows Defender ATP**로 이동 합니다.
1. **Windows DEFENDER Atp 구성**의 1 단계에서 windows defender **Security Center에서 Microsoft Intune에 연결 windows defender atp**를 선택 합니다.
1. Windows Defender 보안 센터에서 다음을 수행합니다.

   1. **설정** > **고급 기능**을 선택합니다.
   1. **Microsoft Intune 연결**의 경우 **켜기**를 선택 합니다.
   1. **기본 설정 저장**을 선택합니다.

1. 연결이 설정 되 면 Intune으로 돌아가서 위쪽에서 **새로 고침** 을 선택 합니다.
1. **Windows 디바이스 버전 10.0.15063 이상을 Windows Defender ATP에 연결**을 **켜기**로 설정합니다.
1. **저장**을 선택합니다.

자세한 내용은 [Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)을 참조하세요.

### <a name="finish-workstation-profile-hardening"></a>워크스테이션 프로필 강화 끝내기

솔루션의 강화를 성공적으로 완료 하려면 적절 한 스크립트를 다운로드 하 여 실행 합니다. 원하는 **프로필 수준**에 대 한 다운로드 링크를 찾습니다.

| 프로필 | 다운로드 위치 | 파일 이름 |
| --- | --- | --- |
| 낮은 보안 | 해당 없음 | 해당 없음 |
| 향상된 보안 | https://aka.ms/securedworkstationgit | Windows10-(1809). p s 1 |
| 높은 수준의 보안 | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809) ps1 |
| 특수화 | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC Windows10 (1803) SecurityBaseline.ps1 |
| 특수 규정 준수 * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10 (1803). p s 1 |
| 보안됨 | https://aka.ms/securedworkstationgit | Windows10-(1809) -SecurityBaseline.ps1 |

\* 특수 한 규정 준수는 NCSC Windows10 SecurityBaseline에 제공 된 특수 구성을 적용 하는 스크립트입니다.

스크립트가 성공적으로 실행 된 후에는 Intune에서 프로필 및 정책을 업데이트할 수 있습니다. 향상 된 보안 프로필 및 보안 프로필에 대 한 스크립트는 정책 및 프로필을 만들지만 **보안 워크스테이션** 장치 그룹에 정책을 할당 해야 합니다.

* 스크립트: **Azure Portal**  >  **Microsoft Intune**  >  **장치 구성**  >  **프로필**에서 만든 Intune 장치 구성 프로필을 찾을 수 있는 위치는 다음과 같습니다.
* 스크립트에서 만든 Intune 장치 준수 정책을 찾을 수 있는 위치는 **Azure Portal**  >  **Microsoft Intune**  >  **장치 준수**  >  **정책**입니다.

스크립트에서 변경한 내용을 검토 하려면 프로필을 내보낼 수 있습니다. 이러한 방식으로 [Seccon 설명서](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)에 설명 된 대로 필요할 수 있는 추가 강화를 결정할 수 있습니다.

`DeviceConfiguration_Export.ps1` [DeviceConfiguration Gid uhub 리포지토리에서](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) intune 데이터 내보내기 스크립트를 실행 하 여 현재 intune 프로필을 모두 내보냅니다.

## <a name="additional-configurations-and-hardening-to-consider"></a>고려할 추가 구성 및 강화

여기의 지침에 따라 보안 워크스테이션을 배포 했습니다. 그러나 추가 컨트롤을 고려해 야 합니다. 예를 들면 다음과 같습니다.

* 대체 브라우저에 대 한 액세스 제한
* 아웃 바운드 HTTP 허용
* 웹 사이트 선택 차단
* 사용자 지정 PowerShell 스크립트 실행에 대 한 사용 권한 설정

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>방화벽 구성 서비스 공급자 (CSP)에서 규칙 설정

허용 및 차단 된 끝점에 대 한 필요에 따라 인바운드 및 아웃 바운드 규칙의 관리를 추가로 변경할 수 있습니다. 보안 워크스테이션을 계속 강화 하면서 모든 인바운드 및 아웃 바운드 트래픽을 거부 하는 제한을 느슨하게 할 수 있습니다. 허용 되는 아웃 바운드 사이트를 추가 하 여 공통 및 신뢰할 수 있는 웹 사이트를 포함할 수 있습니다. 자세한 내용은 [방화벽 구성 서비스](/Windows/client-management/mdm/firewall-csp)를 참조 하세요.

제한적인 URL 트래픽 관리에는 다음이 포함 됩니다.

* 모든 인바운드 트래픽 거부-보안 워크스테이션 프로필 스크립트에서 설정 합니다.
* Azure Cloud Shell를 포함 하 여 선택한 Azure 및 Microsoft 서비스를 제외 하 고 Azure 암호 재설정을 허용 하는 모든 아웃 바운드를 거부 합니다.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

차단 규칙에 대 한 더 많은 세분성을 제공 하려는 경우 [도메인 블록 목록을](https://www.spamhaus.org/dbl/)유지 관리 하는 Spamhaus 프로젝트를 참조할 수 있습니다. 차단 사이트에 대해 구현할 고급 규칙 집합으로 사용할 좋은 리소스입니다.

### <a name="manage-local-applications"></a>로컬 응용 프로그램 관리

보안 워크스테이션은 생산성 응용 프로그램을 포함 하 여 로컬 응용 프로그램이 제거 될 때 완전히 확정 된 상태로 전환 됩니다. 여기서는 Chrome을 기본 브라우저로 추가 하 고 Intune을 사용 하 여 브라우저 및 해당 플러그 인을 수정 하는 사용자의 기능을 제한 합니다.

#### <a name="deploy-applications-using-intune"></a>Intune을 사용 하 여 응용 프로그램 배포

일부 경우에는 보안 워크스테이션에 Google Chrome 브라우저와 같은 응용 프로그램이 필요 합니다. 다음 예제에서는 보안 그룹 보안 **워크스테이션**의 장치에 Chrome을 설치 하는 지침을 제공 합니다.

1. [Windows 64 비트에 대 한](https://cloud.google.com/chrome-enterprise/browser/download/)오프 라인 설치 관리자 Chrome 번들을 다운로드 합니다.
1. 파일의 압축을 풀고 파일의 위치를 적어둡니다 `GoogleChromeStandaloneEnterprise64.msi` .
1. **Azure Portal** 에서 **Microsoft Intune**  >  **클라이언트 앱**  >  **앱**  >  **추가**로 이동 합니다.
1. **앱 유형**에서 **기간 업무**(lob)를 선택 합니다.
1. **앱 패키지 파일**의 `GoogleChromeStandaloneEnterprise64.msi` 압축을 푼 위치에서 파일을 선택 하 고 **확인**을 선택 합니다.
1. **앱 정보**에서 설명과 게시자를 제공 합니다. **확인**을 선택합니다.
1. **추가**를 선택합니다.
1. **할당 탭에서** **할당 유형**아래에 **있는 등록 된 장치에 대해 사용 가능** 을 선택 합니다.
1. **포함 된 그룹**아래에서 **보안 워크스테이션** 그룹을 추가 합니다.
1. **확인**을 선택하고 **저장**을 선택합니다.

Chrome 설정 구성에 대 한 자세한 내용은 [Microsoft Intune를 사용 하 여 Chrome 브라우저 관리](https://support.google.com/chrome/a/answer/9102677)를 참조 하세요.

#### <a name="configuring-the-company-portal-for-custom-apps"></a>사용자 지정 앱에 대 한 회사 포털 구성

보안 모드에서는 응용 프로그램 설치가 Intune 회사 포털로 제한 됩니다. 그러나 포털을 설치 하려면 Microsoft Store에 대 한 액세스가 필요 합니다. 보안 솔루션에서는 오프 라인 모드를 통해 모든 장치에서 회사 포털을 사용할 수 있도록 설정할 수 있습니다.

Intune에서 관리 하는 [회사 포털](/Intune/store-apps-company-portal-app) 복사본은 보안 워크스테이션의 사용자에 게 푸시할 수 있는 추가 도구에 대 한 주문형 액세스를 제공 합니다.

배포에 특별 한 준비가 필요한 Windows 32 비트 앱 또는 다른 앱을 설치 해야 할 수 있습니다. 이러한 경우 [Microsoft win32 콘텐츠 준비 도구](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) 는 설치를 위해 바로 사용할 수 있는 서식 파일을 제공할 수 있습니다 `.intunewin` .

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>보안 워크스테이션에서 Azure Portal에 액세스할 수 있도록 허용 하는 조건부 액세스

Azure AD는 Azure 클라우드 관리 포털에 액세스할 수 있는 사용자 및 사용자를 관리 하 고 제한 하는 기능을 제공 합니다. [조건부 액세스](../conditional-access/overview.md) 를 사용 하도록 설정 하면 보안 워크스테이션 에서만 리소스를 관리 하거나 변경할 수 있습니다. [응급 액세스](../users-groups-roles/directory-emergency-access.md) 기능을 사용 하는 경우에만 극단적인 사례 및 정책을 통해 관리 되는 계정에만 사용 해야 하는 경우이 기능을 배포 하는 것이 중요 합니다.

> [!NOTE]
> 사용자 그룹을 만들고 조건부 액세스 정책을 무시할 수 있는 응급 사용자를 포함 해야 합니다. 이 예에서는 **긴급** 도로 불리는 보안 그룹이 있습니다.

1. **Azure portal**  >  **Microsoft Intune**  >  **조건부 액세스 정책**Microsoft Intune  >  **새 정책**Azure Portal으로 이동 합니다.
1. 정책의 **이름을** 제공 합니다.
1. **사용자 및 그룹**선택  >  **사용자 및 그룹 선택** 
1. 디렉터리 **역할 포함**을 선택 하  >  **Directory roles** 여 전역 관리자, 권한 있는 역할 관리자, 권한 있는 인증 관리자, 보안 관리자, 준수 관리자, 조건부 액세스 관리자, 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, Intune 서비스 관리자 > 역할 > 선택 합니다.
1. **제외** > 선택 **하 > 사용자 및 그룹을 선택 하 고** **제외 된 사용자 선택** 을 선택 하 > **응급 사용자 투명** 그룹을 선택 합니다.
1. **클라우드 앱 또는 작업** > 선택 하 여 **모든 클라우드 앱** 선택
1. **조건** > 선택 하 > **장치 플랫폼** 을 선택 하 > 선택 **예** 를 선택 하 고 **장치 플랫폼 선택** 선택 **Windows** 를 선택 합니다.
1. **액세스 제어** > 선택 하 **고 액세스 허용** **예** > 선택 **하 고 준수 상태로 표시 된 장치 필요를**선택 합니다. 
1. **정책 사용**  >  **을** 선택 합니다.
 
이 정책 집합은 관리자가 Intune 및 WDATP에 의해 설정 되는 호환 되는 Windows 장치를 사용 하도록 합니다. 

또한 조직에서는 환경에서 레거시 인증 프로토콜을 차단 하는 것을 고려해 야 합니다. 이 작업을 수행 하는 방법에는 여러 가지가 있습니다. 레거시 인증 프로토콜을 차단 하는 방법에 대 한 자세한 내용은 [방법: 조건부 액세스를 사용 하 여 AZURE AD에 레거시 인증 차단](../conditional-access/block-legacy-authentication.md)문서를 참조 하세요.

### <a name="use-powershell-to-create-custom-settings"></a>PowerShell을 사용 하 여 사용자 지정 설정 만들기

PowerShell을 사용 하 여 호스트 관리 기능을 확장할 수도 있습니다. 이 예제 스크립트는 호스트의 기본 배경을 설정 합니다. Azure Portal 및 프로필을 통해서도 사용할 수 있는 기능입니다. 예제 스크립트는 PowerShell 기능을 설명 하는 용도로만 사용 됩니다.

보안 워크스테이션에서 일부 사용자 지정 컨트롤 및 설정을 지정 해야 할 수 있습니다. 이 예제에서는 Powershell의 기능을 사용 하 여 즉시 사용 가능한 보안 워크스테이션으로 장치를 쉽게 식별할 수 있도록 워크스테이션의 배경을 변경 합니다.

Microsoft Scripting Center의 [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) 스크립트를 사용 하면 시작할 때 Windows에서 [일반 배경 이미지를 무료로](https://i.imgur.com/OAJ28zO.png) 로드할 수 있습니다.

1. 로컬 장치에 스크립트를 다운로드 합니다.
1. CustomerXXXX 및 배경 이미지의 다운로드 위치를 업데이트 합니다. 이 예제에서는 customerXXXX를 배경으로 바꿉니다.
1. **Azure Portal**  >  **Microsoft Intune**  >  **장치 구성**  >  **PowerShell 스크립트**  >  **추가**로 이동 합니다.
1. 스크립트의 **이름을** 지정 하 고 **스크립트 위치**를 지정 합니다.
1. **구성**을 선택합니다.
   1. **로그온 된 자격 증명을 사용 하 여이 스크립트 실행** 을 **예**로 설정 합니다.
   1. **확인**을 선택합니다.
1. **만들기**를 선택합니다.
1. **할당**을 선택 하 고  >  **그룹을 선택**합니다.
   1. 보안 그룹 보안 **워크스테이션**을 추가 합니다.
   1. **저장**을 선택합니다.

## <a name="enroll-and-validate-your-first-device"></a>첫 번째 장치 등록 및 유효성 검사

1. 장치를 등록 하려면 다음 정보가 필요 합니다.
   * **일련 번호** -장치 섀시에 있습니다.
   * **Windows 제품 ID** - **System**  >  windows 설정 메뉴의 시스템**정보** 에서 찾을 수 있습니다.
   * [WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) 를 실행 하 여 장치 등록에 필요한 모든 정보가 포함 된 CSV 해시 파일을 가져올 수 있습니다.
   
     `Get-WindowsAutoPilotInfo – outputfile device1.csv`을 실행 하 여 Intune으로 가져올 수 있는 CSV 파일로 정보를 출력 합니다.

     > [!NOTE]
     > 스크립트에는 상승 된 권한이 필요 합니다. 원격으로 서명 된 상태로 실행 됩니다. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`명령을 사용 하 여 스크립트를 제대로 실행할 수 있습니다.

   * Windows 10 버전 1809 이상 장치에 로그인 하 여이 정보를 수집할 수 있습니다. 하드웨어 재판매인도이 정보를 제공할 수 있습니다.
1. **Azure Portal**에서 **Microsoft Intune**  >  **장치 등록**  >  **windows 등록**  >  **장치-windows Autopilot 장치 관리**로 이동 합니다.
1. **가져오기** 를 선택 하 고 CSV 파일을 선택 합니다.
1. **보안 워크스테이션** 보안 그룹에 장치를 추가 합니다.
1. 구성 하려는 windows 10 장치에서 **windows 설정**  >  **업데이트 & 보안**  >  **복구**로 이동 합니다.
   1. **이 PC 다시 설정**에서 **시작** 을 선택 합니다.
   1. 메시지를 따라 프로필 및 규정 준수 정책을 구성 하 여 장치를 다시 설정 하 고 다시 구성 합니다.

장치를 구성한 후에는 검토를 완료 하 고 구성을 확인 합니다. 배포를 계속 하기 전에 첫 번째 장치가 올바르게 구성 되어 있는지 확인 합니다.

## <a name="assign-devices"></a>장치 할당

장치 및 사용자를 할당 하려면 [선택한 프로필](/intune/device-profile-assign) 을 보안 그룹에 매핑해야 합니다. 서비스에 대 한 사용 권한이 필요한 모든 새 사용자도 보안 그룹에 추가 해야 합니다.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>센티널 및 Windows Defender ATP를 사용 하 여 보안 인시던트 모니터링 및 대응

보안 워크스테이션 배포 모니터링은 [센티널]을 사용 하도록 설정 하 고 [위협 및 취약성 관리](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) 를 활용 하 여 수행할 수 있습니다. 지침은 철저 한 위협 구하기를 제공 하지 않지만 잠재적인 보안 문제를 모니터링 하 고이에 대응 하는 좋은 일반적인 의미를 제공 합니다.

**Azure 센티널** 을 사용 하 여 다음을 수행 합니다. 

* 사용자 및 장치 모니터링을 위해 Intune, Azure Portal 및 Azure AD에서 데이터 수집
* 사용자 및 장치 구성에 대해 의심 스러운 활동 조사 지원
* 그리고 WDATP를 사용 하 여 보안 조사를 탐색 하는 기능을 구동 합니다.

센티널 모니터링을 사용 하려면 Azure AD와 같은 데이터 원본에 대 한 커넥터를 설정 해야 합니다.

1. **Azure Portal**에서 **Azure 센티널 (미리 보기)** 로 이동 하 > **추가** 를 선택 합니다.
1. **Azure 센티널에 추가할 작업 영역 선택** 에서 **새 작업 영역 만들기** 를 선택 합니다.
1. 다음을 입력합니다.
   * **작업 영역 Log Analytics** -' 안전한 워크스테이션 모니터링 '
   * **구독** -활성 구독을 선택 합니다.
   * **리소스 그룹** -* * 새로 만들기 * * > 보안 워크스테이션 RG > **확인을** 선택 합니다.
   * **위치** -배포에 가장 적합 한 위치를 선택 합니다.
   * **가격 책정 계층** - **GB 당 선택 (2018)**
1. **확인**을 선택합니다.

다음으로 사용 가능한 보안 워크스테이션 데이터 원본을 모니터링에 연결 합니다.

1. **Azure Portal**에서 **Azure 센티널 작업 영역** 으로 이동 하 > **보안 워크스테이션 모니터링** 작업 영역을 선택 합니다.
1. **데이터 커넥터** 선택
1. 필수 구성 요소를 검토 한 후 **Azure Active Directory** > 커넥터 페이지 열기 >를 선택 합니다. 구성으로 진행 하 여 azure ad 로그인 로그와 Azure AD 감사 로그에 대해 모두 **연결** 을 선택 합니다.
1. 필수 구성 요소를 검토 한 후 **Azure 활동** > 커넥터 페이지 > 열기를 선택 합니다. Azure 활동 로그 구성을 계속 진행 하 > 구독 > 선택 하 고 **연결** 을 선택 합니다.

센티널에서 데이터를 수집 하면 **Azure Portal**를 선택 하 여 활동을 관찰할 수 있으며 **Azure 센티널 개요** 로 이동 합니다. 

**Windows DEFENDER ATP (WDATP)** 를 사용 하 여 다음을 수행 합니다.

* 지속적으로 취약성 및 잘못 확인 관찰 및 모니터링
* WDATP를 활용 하 여 와일드 카드의 동적 위협 우선 순위 지정
* EDR (끝점 검색 및 응답) 경고와 관련 된 취약성의 상관 관계
* 조사 하는 동안 대시보드를 사용 하 여 컴퓨터 수준 취약점 식별
* Intune에 재구성 푸시

[DEFENDER ATP 대시보드](https://securitycenter.windows.com/machines)를 구성 합니다. [위협 & 취약성 관리 대시보드 개요](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)에서 지침을 사용 합니다.

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Microsoft Monitoring Agent를 사용 하 여 응용 프로그램 동작 모니터링 (MMA)
특수 워크스테이션부터 앱 락커는 워크스테이션에서 응용 프로그램 활동을 모니터링할 수 있도록 설정 됩니다. 모니터링을 Log Analytics 작업 영역에 통합 하려면 MMA 에이전트 및 구성을 따라야 합니다. 

> [!NOTE]
> 특수 워크스테이션 프로필에는 AppLocker 모니터링 정책이 포함 됩니다. 클라이언트에서 응용 프로그램 활동을 모니터링 하려면 정책을 배포 해야 합니다.

Intune PowerShell 스크립트를 사용 하 여 MMA 에이전트 배포

1. [로컬 장치에 설치 스크립트](https://aka.ms/securedworkstationgit)를 다운로드 합니다.
1. 매개 변수, **$WorkSpaceID** 및 **$WorkSpaceKey** 를 업데이트 합니다.
1. **Azure Portal**  >  **Microsoft Intune**  >  **장치 구성**  >  **PowerShell 스크립트**  >  **추가**로 이동 합니다.
1. 스크립트의 **이름을** 지정 하 고 **스크립트 위치**를 지정 합니다.
1. **구성**을 선택합니다.
   1. **로그온 된 자격 증명을 사용 하 여이 스크립트 실행** 을 **예**로 설정 합니다.
   1. **확인**을 선택합니다.
1. **만들기**를 선택합니다.
1. **할당**을 선택 하 고  >  **그룹을 선택**합니다.
   1. 보안 그룹 보안 **워크스테이션**을 추가 합니다.
   1. **저장**을 선택합니다.

다음으로 새 로그를 수신 하도록 Log Analytics를 설정 해야 합니다.
1. **Azure Portal**에서 **Log Analytics 작업 영역** 으로 이동 > ' 보안 워크스테이션 모니터링 '을 선택 합니다.
1. **고급 설정**  >  **데이터**  >  **Windows 이벤트 로그** 를 선택 합니다.
1. **다음 이벤트 로그에서 이벤트 수집** 
1. 다음을 입력합니다.
   * ' Microsoft-Windows-AppLocker/EXE 및 DLL ' > 선택 취소 **정보**
   * ' Microsoft-Windows-AppLocker/MSI 및 스크립트 ' > 선택 취소 **정보**
   * ' Microsoft-Windows-AppLocker/패키지 된 앱 배포 ' > 선택 취소 **정보**
   * ' Microsoft-Windows-AppLocker/패키지 된 앱 실행 ' > 선택 취소 **정보**
1. **저장**을 선택합니다.

응용 프로그램 로깅은 선택한 Log Analytics 작업 영역에서 사용할 수 있습니다.

## <a name="monitoring"></a>모니터링

* [Azure 센티널을 사용 하 여 위협을 검색](../../sentinel/tutorial-detect-threats-built-in.md) 하는 방법 알아보기
* [Azure 센티널을 사용 하 여 인시던트 조사](../../sentinel/tutorial-investigate-cases.md)
* [Azure Sentinel에서 자동화된 위협 응답 설정](../../sentinel/tutorial-respond-threats-playbook.md)
* [노출 점수](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score) 를 검토 하는 방법 이해
* [보안 권장 사항](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation) 검토
* 보안 [재구성](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) 관리
* [끝점 검색 및 응답](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response) 관리
* [Intune 프로필 모니터링](/intune/device-profile-monitor)을 사용 하 여 프로필을 모니터링 합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft Intune](/intune/index)에 대해 자세히 알아보세요.
* [AZURE AD](../index.yml)를 이해 합니다.
* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 사용
* [Azure 센티널](../../sentinel/index.yml) 검색