---
title: 안전 하 고 Azure에서 관리 되는 워크스테이션 이해-Azure Active Directory
description: 안전 하 고 Azure로 관리 되는 워크스테이션에 대해 알아보고 중요 한 이유를 이해 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c26197a14e78b1cf1a1e078ba0145eca207206bf
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561963"
---
# <a name="understand-secure-azure-managed-workstations"></a>안전 하 고 Azure로 관리 되는 워크스테이션 이해

안전 하 고 격리 된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요 한 역할의 보안에 매우 중요 합니다. 클라이언트 워크스테이션 보안이 손상 되 면 많은 보안 제어 및 보증이 실패할 수 있습니다.

이 문서에서는 PAW (권한 있는 액세스 워크스테이션) 라고 하는 안전한 워크스테이션을 빌드하는 데 필요한 사항을 설명 합니다. 이 문서에는 초기 보안 제어를 설정 하는 자세한 지침도 포함 되어 있습니다. 이 지침에서는 클라우드 기반 기술에서 서비스를 관리할 수 있는 방법에 대해 설명 합니다. Windows 10RS5, Microsoft Defender ATP (Advanced Threat Protection), Azure Active Directory 및 Microsoft Intune에 도입 된 보안 기능을 사용 합니다.

> [!NOTE]
> 이 문서에서는 보안 워크스테이션 및 중요도의 개념에 대해 설명 합니다. 개념을 이미 잘 알고 있고 배포로 건너뛰려면 [보안 워크스테이션 배포](howto-azure-managed-workstation.md)를 참조 하세요.

## <a name="why-secure-workstation-access-is-important"></a>보안 워크스테이션 액세스가 중요 한 이유

클라우드 서비스를 신속 하 게 채택 하 고 어디서 나 작업 하는 기능을 활용 하 여 새로운 이용 방법을 만들었습니다. 공격자는 관리자가 근무 하는 장치에서 취약 한 보안 제어를 활용 하 여 권한 있는 리소스에 액세스할 수 있습니다.

권한 없는 오용 및 공급망 공격은 공격자가 조직을 위반 하는 데 사용 하는 상위 5 가지 방법 중 하나입니다. [Verizon 위협 보고서](https://enterprise.verizon.com/resources/reports/dbir/)및 [보안 인텔리전스 보고서](https://aka.ms/sir)에 따라 2018에 보고 된 인시던트에서 가장 일반적으로 검색 되는 두 번째 전략 이기도 합니다.

대부분의 공격자는 다음 단계를 수행 합니다.

1. 정찰을 통해 종종 업계에 특정 한 방법을 찾을 수 있습니다.
1. 분석을 통해 정보를 수집 하 고 낮은 가치로 인식 되는 워크스테이션을 군사 하는 가장 좋은 방법을 파악 합니다.
1. [수평](https://en.wikipedia.org/wiki/Network_Lateral_Movement)이동 수단을 찾을 지 속성
1. 기밀 및 중요 한 데이터의 exfiltration.

정찰 하는 동안 공격자는 위험이 적거나 낮은 값으로 보이는 장치를 자주 군사 합니다. 이러한 취약 한 장치를 사용 하 여 측면 이동에 대 한 기회를 찾고 관리 사용자 및 장치를 찾을 수 있습니다. 공격자는 권한 있는 사용자 역할에 대 한 액세스 권한을 얻은 후 높은 가치의 데이터를 식별 하 고 해당 데이터를 성공적으로 탈취.

![일반적인 손상 패턴](./media/concept-azure-managed-workstation/typical-timeline.png)

이 문서에서는 이러한 측면 공격 으로부터 컴퓨팅 장치를 보호 하는 데 도움이 되는 솔루션을 설명 합니다. 이 솔루션은 중요 한 클라우드 리소스에 대 한 액세스 권한이 있는 장치를 infiltrated 하기 전에 체인을 분리 하 여 저렴 한 생산성 장치에서 관리 및 서비스를 격리 합니다. 솔루션은 Microsoft 365 Enterprise 스택에 속하는 네이티브 Azure 서비스를 사용 합니다.

* 장치 관리용 Intune 및 안전한 응용 프로그램 및 Url 목록
* 장치 설정, 배포 및 새로 고침에 대 한 Autopilot
* 사용자 관리, 조건부 액세스 및 multi-factor authentication에 대 한 Azure AD
* 장치 상태 증명 및 사용자 환경에 대 한 Windows 10 (현재 버전)
* 클라우드 관리 엔드포인트 보호, 검색 및 응답을 위한 Defender ATP
* 리소스에 대 한 권한 부여 및 JIT (just-in-time) 특권 수준의 액세스를 관리 하기 위한 Azure AD PIM
* 모니터링 및 경고에 대 한 Log Analytics 및 센티널

## <a name="who-benefits-from-a-secure-workstation"></a>보안 워크스테이션을 활용 하는 사람은 누구 인가요?

모든 사용자 및 운영자는 보안 워크스테이션을 사용할 때 혜택을 제공 합니다. PC 또는 장치를 손상 시키는 공격자는 모든 캐시 된 계정을 가장할 수 있습니다. 장치에 로그인 할 때 자격 증명과 토큰을 사용할 수도 있습니다. 이러한 위험으로 인해 관리자 권한을 포함 하 여 권한 있는 역할에 사용 되는 장치를 보호 하는 것이 중요 합니다. 권한 있는 계정이 있는 장치는 측면 이동 및 권한 상승 공격을 위한 대상입니다. 이러한 계정은 다음과 같은 다양 한 자산에 사용할 수 있습니다.

* 온-프레미스 또는 클라우드 기반 시스템의 관리자
* 중요 시스템용 개발자 워크스테이션
* 노출이 높은 소셜 미디어 계정 관리자
* 매우 중요 한 워크스테이션 (예: SWIFT 결제 터미널)
* 거래 비밀을 처리 하는 워크스테이션

위험을 줄이려면 이러한 계정을 사용 하는 권한 있는 워크스테이션에 대해 높은 수준의 보안 제어를 구현 해야 합니다. 자세한 내용은 [Azure Active Directory 기능 배포 가이드](../fundamentals/active-directory-deployment-checklist-p2.md), [Office 365 로드맵](https://aka.ms/o365secroadmap)및 [권한 있는 액세스 보안 로드맵](https://aka.ms/sparoadmap))을 참조 하세요.

## <a name="why-use-dedicated-workstations"></a>전용 워크스테이션을 사용 해야 하는 이유

기존 장치에 보안을 추가할 수 있지만 보안 기반으로 시작 하는 것이 좋습니다. 높은 수준의 보안 수준을 유지 하기 위해 조직을 가장 적절 한 위치에 배치 하려면 안전한 것으로 알고 있는 장치를 시작 하 고 알려진 보안 제어 집합을 구현 합니다.

전자 메일 및 웹 검색을 통해 공격 벡터의 수가 증가 하면 장치를 신뢰할 수 있는지를 더욱 쉽게 확인할 수 있습니다. 이 가이드에서는 전용 워크스테이션이 표준 생산성, 검색 및 전자 메일과 분리 되어 있다고 가정 합니다. 장치에서 생산성, 웹 검색 및 전자 메일을 제거 하면 생산성에 부정적인 영향을 줄 수 있습니다. 그러나이 보호는 일반적으로 작업 태스크에서 명시적으로 요구 하지 않고 보안 인시던트의 위험이 높은 시나리오에 적합 합니다.

> [!NOTE]
> 웹 검색은 높은 위험 활동 일 수 있는 임의의 웹 사이트에 대 한 일반 액세스를 나타냅니다. 이러한 검색은 웹 브라우저를 사용 하 여 Azure, Office 365, 기타 클라우드 공급자, SaaS 응용 프로그램 등의 서비스에 대 한 잘 알려진 몇 가지 관리 웹 사이트에 액세스 하는 것과는 다릅니다.

포함 전략은 공격자가 중요 한 자산에 액세스 하지 못하도록 하는 컨트롤의 수와 유형을 높여 보안을 강화 합니다. 이 문서에서 설명 하는 모델은 계층화 된 권한 디자인을 사용 하 고 특정 장치에 대 한 관리 권한을 제한 합니다.

## <a name="supply-chain-management"></a>공급망 관리

안전한 워크스테이션에는 ' 신뢰 루트 ' 라는 신뢰할 수 있는 워크스테이션을 사용 하는 공급망 솔루션이 필요 합니다. 신뢰 하드웨어 루트를 선택할 때 고려해 야 하는 기술에는 최신 노트북에 포함 된 다음과 같은 기술이 포함 되어야 합니다. 

* [신뢰할 수 있는 플랫폼 모듈 (TPM) 2.0](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker 드라이브 암호화](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI 보안 부팅](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot)
* [Windows 업데이트를 통해 배포 되는 드라이버 및 펌웨어](https://docs.microsoft.com/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [가상화 및 HVCI 사용](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs)
* [드라이버 및 앱 HVCI-준비](https://docs.microsoft.com/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](https://docs.microsoft.com/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA i/o 보호](https://docs.microsoft.com/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [시스템 가드](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [최신 대기](https://docs.microsoft.com/windows-hardware/design/device-experiences/modern-standby)

이 솔루션의 경우 최신 기술 요구 사항을 충족 하는 하드웨어가 포함 된 [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot) 기술을 사용 하 여 신뢰 루트를 배포 합니다. 워크스테이션을 보호 하기 위해 Autopilot를 사용 하 여 Microsoft OEM 최적화 Windows 10 장치를 활용할 수 있습니다. 이러한 장치는 제조업체 로부터 알려진 양호한 상태로 제공 됩니다. Autopilot는 잠재적으로 안전 하지 않은 장치를 이미지로 다시 설치 하는 대신 Windows 장치를 "업무 지원" 상태로 변환할 수 있습니다. 설정 및 정책을 적용 하 고, 앱을 설치 하 고, Windows 10의 버전을 변경 합니다. 예를 들어, Autopilot는 Windows 10 Pro에서 windows 10 Enterprise로 장치의 Windows 설치를 변경 하 여 고급 기능을 사용할 수 있습니다.

![보안 워크스테이션 수준](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>장치 역할 및 프로필

이 지침에서는 사용자, 개발자 및 IT 담당자를 위한 보다 안전한 솔루션을 만드는 데 도움이 되는 몇 가지 보안 프로필 및 역할을 참조 합니다. 이러한 프로필은 강화 된 워크스테이션 또는 안전한 워크스테이션의 이점을 누릴 수 있는 일반 사용자에 대 한 유용성 및 위험을 분산 합니다. 여기에 제공 된 설정 구성은 업계에서 승인한 표준을 기반으로 합니다. 이 지침에서는 Windows 10을 강화 하 고 장치 또는 사용자 손상과 관련 된 위험을 줄이는 방법을 보여 줍니다. 최신 하드웨어 기술과 신뢰 장치의 루트를 활용 하기 위해 **높은 보안** 프로필에서 시작 하는 데 사용할 수 있는 [디바이스 상태 증명](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643)을 사용 합니다. 이 기능은 장치 조기 부팅 중에 공격자가 영구적으로 사용할 수 없도록 보장 하기 위해 제공 됩니다. 이는 정책과 기술을 사용 하 여 보안 기능 및 위험을 관리 하는 데 도움이 됩니다.
![보안 워크스테이션 수준](./media/concept-azure-managed-workstation/seccon-levels.png)

* **기본 보안** – 관리 되는 표준 워크스테이션은 대부분의 홈 및 소규모 비즈니스 사용을 위한 좋은 시작 지점을 제공 합니다. 이러한 장치는 Azure AD에 등록 되 고 Intune을 사용 하 여 관리 됩니다. 이 프로필을 사용 하면 사용자가 모든 응용 프로그램을 실행 하 고 모든 웹 사이트를 찾아볼 수 있습니다. [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) 와 같은 맬웨어 방지 솔루션을 사용 하도록 설정 해야 합니다.

* **보안 강화** –이 항목 수준의 보호 된 솔루션은 개인 사용자, 소규모 비즈니스 사용자 및 일반 개발자에 게 적합 합니다.

   강화 된 워크스테이션은 낮은 보안 프로필의 보안을 강화 하는 정책 기반 방법입니다. 전자 메일 및 웹 검색과 같은 생산성 도구를 사용 하면서 고객 데이터를 사용할 수 있는 안전한 방법을 제공 합니다. 감사 정책 및 Intune을 사용 하 여 사용자 동작 및 프로필 사용에 대 한 향상 된 워크스테이션을 모니터링할 수 있습니다. Windows10 (1809) 스크립트를 사용 하 여 향상 된 워크스테이션 프로필을 배포 하 고 [ATP (Advanced Threat protection)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)를 사용 하는 고급 맬웨어 보호를 활용 합니다.

* **높은 수준의 보안** – 워크스테이션의 공격 노출 영역을 줄이는 가장 효과적인 방법은 워크스테이션을 자체 관리 하는 기능을 제거 하는 것입니다. 로컬 관리 권한을 제거 하는 단계는 보안을 개선 하지만 잘못 구현 된 경우 생산성에 영향을 줄 수 있습니다. 높은 수준의 보안 프로필은 향상 된 보안 프로필을 기반으로 하며, 로컬 관리자를 제거 하는 것입니다. 이 프로필은 높은 프로필 사용자를 위해 설계 되었습니다. 임원, 급여 및 중요 한 데이터 사용자, 서비스 및 프로세스에 대 한 승인자입니다.

   높은 수준의 보안 사용자는 간단 하 게 사용할 수 있는 환경에서 전자 메일 및 웹 검색과 같은 작업을 수행할 수 있는 동시에 보다 제어 된 환경을 요구 합니다. 사용자는 쿠키, 즐겨찾기 및 기타 바로 가기와 같은 기능을 사용할 것으로 간주 합니다. 그러나 이러한 사용자에 게는 장치를 수정 하거나 디버그 하는 기능이 필요 하지 않을 수 있습니다. 또한 드라이버를 설치할 필요가 없습니다. 높은 수준의 보안 프로필은 Windows10 (1809) 스크립트를 사용 하 여 배포 됩니다.

* **전문** – 공격자는 공격자와 관련 된 시스템을 변경할 수 있기 때문에 개발자와 IT 관리자를 대상으로 합니다. 특수 한 워크스테이션은 로컬 응용 프로그램을 관리 하 고 웹 사이트를 제한 하 여 높은 수준의 보안 워크스테이션 정책을 확장 합니다. 또한 ActiveX, Java, browser 플러그 인 및 기타 Windows 컨트롤과 같은 높은 위험 수준의 생산성 기능을 제한 합니다. DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline 스크립트를 사용 하 여이 프로필을 배포 합니다.

* **보안** – 관리 계정을 손상 시키는 공격자가 데이터 도난, 데이터 변경 또는 서비스 중단으로 인해 상당한 비즈니스 손상을 일으킬 수 있습니다. 이러한 확정 된 상태에서 워크스테이션은 로컬 응용 프로그램 관리의 직접 제어를 제한 하는 모든 보안 제어 및 정책을 사용 하도록 설정 합니다. 보안 워크스테이션에는 생산성 도구가 없으므로 장치를 손상 하기가 더 어려워집니다. 피싱 공격에 대해 가장 일반적인 벡터 인 전자 메일 및 소셜 미디어를 차단 합니다. Secure workstation Windows10 (1809) SecurityBaseline 스크립트를 사용 하 여 보안 워크스테이션을 배포할 수 있습니다.

   ![보안 워크스테이션](./media/concept-azure-managed-workstation/secure-workstation.png)

   안전한 워크스테이션은 일반 응용 프로그램 제어 및 application guard를 갖춘 강화 된 워크스테이션을 관리자에 게 제공 합니다. 워크스테이션은 credential guard, device guard 및 exploit guard를 사용 하 여 악의적인 동작 으로부터 호스트를 보호 합니다. 모든 로컬 디스크도 BitLocker로 암호화 됩니다.

* **격리** –이 사용자 지정 오프 라인 시나리오는 스펙트럼의 극단적인 끝을 나타냅니다. 이 경우 설치 스크립트가 제공 되지 않습니다. 지원 되지 않거나 패치가 적용 되지 않은 레거시 운영 체제가 필요한 업무상 중요 한 기능을 관리 해야 할 수 있습니다. 예를 들어 높은 가치의 생산 라인 또는 수명 지원 시스템입니다. 보안이 중요 하 고 클라우드 서비스를 사용할 수 없기 때문에 이러한 컴퓨터를 수동으로 관리 하 고 업데이트할 수 있으며, ESAE (강화 된 보안 관리 환경)와 같은 격리 된 Active Directory 포리스트 아키텍처로 관리할 수 있습니다. 이러한 경우 기본 Intune 및 ATP 상태 검사를 제외한 모든 액세스를 제거 하는 것이 좋습니다.

   * [Intune 네트워크 통신 요구 사항](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [ATP 네트워크 통신 요구 사항](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>다음 단계

[Azure로 관리 되는 보안 워크스테이션을 배포](howto-azure-managed-workstation.md)합니다.
