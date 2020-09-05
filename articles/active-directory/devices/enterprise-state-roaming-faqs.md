---
title: Enterprise State Roaming FAQ-Azure Active Directory
description: ESR에 대 한 질문과 대답
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9510bd564ced2f458a9a78ff23200bb32358c3e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268539"
---
# <a name="settings-and-data-roaming-faq"></a>설정 및 데이터 로밍 FAQ

이 문서에서는 설정 및 앱 데이터 동기화에 대한 IT 관리자의 질문에 답변합니다.

## <a name="what-data-roams"></a>어떤 데이터가 로밍됩니까?

**Windows 설정**: windows 운영 체제에 기본 제공 되는 PC 설정입니다. 일반적으로 PC를 개인 설정하는 설정이며, 다음과 같은 광범위한 범주를 포함합니다.

* '테마' - 바탕 화면 테마, 작업 표시줄 설정 등의 기능이 포함됩니다.
* *Internet Explorer 설정* - 최근에 열어 본 탭, 즐겨찾기 등이 있습니다.
* *Microsoft Edge 브라우저 설정*- 즐겨찾기, 읽기 목록 등
* *암호*(인터넷 암호, wi-fi 프로필 등)를 포함 합니다.
* '언어 기본 설정' - 키보드 레이아웃, 시스템 언어, 날짜 및 시간 등에 대한 설정이 포함됩니다.
* *접근성 기능* - 고대비 테마, 내레이터, 돋보기 등이 있습니다.
* *기타 Windows 설정* - 예를 들어 마우스 설정이 있습니다.

> [!NOTE]
> 이 문서는 7 월 2015에 Windows 10으로 시작 된 Microsoft Edge 레거시 HTML 기반 브라우저에 적용 됩니다. 이 문서는 2020 년 1 월 15 일에 릴리스된 새 Microsoft Edge Chromium 기반 브라우저에는 적용 되지 않습니다. 새 Microsoft Edge의 동기화 동작에 대 한 자세한 내용은 [Microsoft Edge 동기화](/deployedge/microsoft-edge-enterprise-sync)문서를 참조 하세요.

**애플리케이션 데이터**- 범용 Windows 앱은 “로밍” 폴더에 설정 데이터를 쓸 수 있으며, 이 폴더에 쓰여진 모든 데이터는 자동으로 동기화됩니다. 이 기능을 활용하는 앱 디자인 여부는 개별 앱 개발자의 결정에 달렸습니다. 로밍을 사용 하는 유니버설 Windows 앱을 개발 하는 방법에 대 한 자세한 내용은 [appdata STORAGE API](/windows/uwp/design/app-settings/store-and-retrieve-app-data) 및 [Windows 8 appdata 로밍 개발자 블로그](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/)를 참조 하세요.

## <a name="what-account-is-used-for-settings-sync"></a>설정 동기화에 어떤 계정이 사용됩니까?

Windows 8.1에서는 설정 동기화에 항상 소비자 Microsoft 계정이 사용되었습니다. 엔터프라이즈 사용자는 설정 동기화에 대 한 액세스 권한을 얻기 위해 Microsoft 계정를 Active Directory 도메인 계정에 연결할 수 있었습니다. Windows 10에서는이 연결 된 Microsoft 계정 기능이 기본/보조 계정 프레임 워크로 대체 되 고 있습니다.

기본 계정이란 Windows에 로그인하는 데 사용되는 계정으로, Microsoft 계정, Azure Active Directory(Azure AD) 계정, 온-프레미스 Active Directory 계정 또는 로컬 계정일 수 있습니다. 기본 계정 외에도 Windows 10 사용자는 디바이스에 하나 이상의 보조 클라우드 계정을 추가할 수 있습니다. 보조 계정은 일반적으로 Microsoft 계정, Azure AD 계정 또는 Gmail이나 Facebook과 같은 기타 계정입니다. 이러한 보조 계정은 Single Sign-On, Windows 스토어 등의 추가 서비스에 대한 액세스를 제공하지만 설정 동기화는 지원하지 않습니다.

Windows 10에서 디바이스의 기본 계정만 설정 동기화에 사용할 수 있습니다([Windows 8의 Microsoft 계정 설정 동기화에서 Windows 10의 Azure AD 설정 동기화로 업그레이드하려면 어떻게 해야 하나요?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10) 참조).

데이터는 디바이스의 서로 다른 사용자 계정 간에 절대로 혼합되지 않습니다. 설정 동기화에는 두 가지 규칙이 있습니다.

* Windows 설정은 항상 기본 계정으로 로밍됩니다.
* 앱 데이터는 앱을 획득하는 데 사용되는 계정으로 태그를 지정합니다. 기본 계정으로 태그가 지정 된 앱만 동기화 됩니다. 앱 소유권 태그 지정은 Windows 스토어 또는 MDM (모바일 장치 관리)을 통해 앱을 테스트용으로 로드할 때 결정 됩니다.

앱 소유자를 식별할 수 없는 경우 기본 계정을 사용하여 로밍합니다. 디바이스를 Windows 8 또는 Windows 8.1에서 Windows 10으로 업그레이드하는 경우 모든 앱이 Microsoft 계정을 통해 획득한 것으로 태그가 지정됩니다. 일반적으로 대부분의 앱은 Windows 스토어를 통해 획득되고, Windows 10 이전에서는 Azure AD 계정에 Windows 스토어가 지원되지 않기 때문입니다. 오프라인 라이선스를 통해 앱을 설치하는 경우에는 해당 디바이스의 기본 계정을 사용하여 앱의 태그가 지정됩니다.

> [!NOTE]
> 엔터프라이즈가 소유하고 Azure AD에 연결되어 있는 Windows 10 디바이스는 Microsoft 계정을 도메인 계정에 연결할 수 없습니다. Microsoft 계정을 도메인 계정에 연결하고 모든 사용자 데이터를 Microsoft 계정과 동기화(예: 연결된 Microsoft 계정 및 Active Directory 기능을 통해 Microsoft 계정 로밍)하는 기능은 연결된 Active Directory 또는 Azure AD 환경에 조인되는 Windows 10 디바이스에서 제거되었습니다.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Windows 8의 Microsoft 계정 설정 동기화에서 Windows 10의 Azure AD 설정 동기화로 업그레이드하려면 어떻게 해야 하나요?

Microsoft 계정이 연결된 Windows 8.1을 실행하는 Active Directory 도메인에 조인된 경우 Microsoft 계정을 통해 설정을 동기화합니다. Windows 10으로 업그레이드가 완료되면 도메인에 가입된 사용자인 경우 Active Directory 도메인이 Azure AD와 연결되지 않는 한 계속해서 Microsoft 계정을 통해 사용자 설정을 동기화하게 됩니다.

온-프레미스 Active Directory 도메인이 Azure AD와 연결되면 디바이스에서 연결된 Azure AD 계정을 사용하여 설정을 동기화하려고 시도합니다. Azure AD 관리자가 엔터프라이즈 상태 로밍을 활성화하지 않으면 연결된 Azure AD 계정이 설정 동기화를 중지하게 됩니다. Windows 10 사용자인 경우 Azure AD ID로 로그인하면 관리자가 Azure AD를 통한 설정 동기화를 활성화하는 즉시 Windows 설정 동기화가 시작됩니다.

회사 디바이스에 개인 데이터를 저장한 경우 Windows OS 및 애플리케이션 데이터가 Azure AD와 동기화된다는 사실에 주의해야 합니다. 이 값은 다음과 같은 의미를 갖습니다.

* 개인 Microsoft 계정 사용자는 설정이 회사 또는 학교 Azure AD 계정과 점점 "달라지게" 됩니다. 이는 이제 Microsoft 계정 및 Azure AD 설정 동기화에서 별도 계정을 사용하고 있기 때문입니다.
* 연결된 Microsoft 계정을 통해 이미 동기화된 Wi-Fi 암호, 웹 자격 증명 및 Internet Explorer 즐겨찾기 등 개인 데이터는 Azure AD를 통해 동기화됩니다.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Microsoft 계정과 Azure AD 엔터프라이즈 상태 로밍의 상호 운용성이 어떻게 됩니까?

Windows 10의 2015년 11월 이후 릴리스에서 엔터프라이즈 상태 로밍은 한 번에 한 계정에만 지원됩니다. 회사 및 학교 Azure AD 계정을 사용하여 Windows에 로그인하면 모든 데이터가 Azure AD를 통해 동기화됩니다. 개인용 Microsoft 계정을 사용하여 Windows에 로그인하면 모든 데이터가 Microsoft 계정을 통해 동기화됩니다. 앱 라이선스의 소유자가 기본 계정인 경우에만 유니버설 앱 데이터는 디바이스의 기본 로그인 계정을 사용하여 로밍됩니다. 소유자가 보조 계정인 앱에 대한 유니버설 앱 데이터는 동기화됩니다.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>여러 테넌트의 Azure AD 계정에 대한 설정이 동기화됩니까?

여러 Azure AD 테넌트의 여러 Azure 계정이 동일한 디바이스에 있는 경우 각 Azure AD 테넌트의 Azure Rights Management 서비스와 통신하도록 디바이스 레지스트리를 업데이트해야 합니다.  

1. 각 Azure AD 테넌트에 대한 GUID를 확인합니다. Azure Portal을 열고 Azure AD 테넌트를 선택합니다. 테넌트의 GUID는 선택한 테넌트, 레이블이 지정된 **Directory ID**에 대한 속성 페이지(https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)에 있습니다. 
2. GUID를 받은 후에는 **\software\microsoft\windows\settingsync\winmsipc \<tenant ID GUID> HKEY_LOCAL_MACHINE**레지스트리 키를 추가 해야 합니다.
   **테넌트 ID GUID** 키에서 **AllowedRMSServerUrls**라는 새 다중 문자열 값(REG-MULTI-SZ)을 만듭니다. 해당 데이터에 대해 디바이스에서 액세스하는 다른 Azure 테넌트의 라이선스 배포 지점 URL을 지정합니다.
3. AADRM 모듈에서 **Get-AadrmConfiguration** cmdlet을 실행하여 라이선싱 배포 지점 URL을 찾을 수 있습니다. **LicensingIntranetDistributionPointUrl** 및 **LicensingExtranetDistributionPointUrl**의 값이 다르면 두 값을 모두 지정합니다. 값이 같으면 값을 한 번만 지정합니다.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>기존 Windows 데스크톱 애플리케이션의 로밍 설정 옵션은 무엇인가요?

로밍은 유니버설 Windows 앱에서만 작동합니다. 기존 Windows 데스크톱 애플리케이션에서 로밍을 활성화할 수 있는 두 가지 옵션이 있습니다.

* [데스크톱 브리지](https://aka.ms/desktopbridge) 를 사용하면 유니버설 Windows 플랫폼에 기존 Windows 데스크톱 앱을 쉽게 가져올 수 있습니다. 이때 Azure AD 앱 데이터 로밍을 활용하려면 최소한의 코드 변경 작업이 필요합니다. 데스크톱 브리지는 앱 ID를 사용하여 앱을 제공하며 이는 기존 데스크톱 앱에 앱 데이터 로밍을 사용하는 데 필요합니다.
* [UE-V(사용자 경험 가상화)](/previous-versions//dn458947(v=vs.85)) 를 사용하면 기존 Windows 데스크톱 앱에 대한 사용자 지정 설정 템플릿을 만들 수 있고 Win32 앱에 대해서만 로밍을 사용할 수 있습니다. 이 옵션은 앱의 코드를 변경하기 위해 앱 개발자가 필요하지 않습니다. UE-V는 Microsoft Desktop Optimization Pack을 구매한 고객에 대한 온-프레미스 Active Directory 로밍으로 제한됩니다.

관리자는 [UE-V 그룹 정책](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)을 통해 Windows OS 설정 및 유니버설 앱 데이터의 로밍을 변경하여 Windows 데스크톱 앱 데이터를 로밍하도록 UEV를 구성할 수 있습니다.

* Windows 설정 로밍 그룹 정책
* Windows 앱 동기화 안 함 그룹 정책
* 애플리케이션 섹션의 Internet Explorer 로밍

앞으로 Microsoft는 UE-V를 Windows와 긴밀하게 통합하고 Azure AD 클라우드를 통해 설정을 로밍할 수 있도록 UE-V를 확장하는 방법을 연구할 것입니다.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>동기화 된 설정 및 데이터를 온-프레미스에 저장할 수 있나요?

Enterprise State Roaming은 Microsoft 클라우드에 동기화된 모든 데이터를 저장합니다. UE-V는 온-프레미스 로밍 솔루션을 제공합니다.

## <a name="who-owns-the-data-thats-being-roamed"></a>로밍되는 데이터를 소유한 사람은 누구인가요?

엔터프라이즈는 엔터프라이즈 상태 로밍을 통해 로밍되는 데이터를 소유합니다. 데이터는 Azure 데이터 센터에 저장됩니다. 모든 사용자 데이터는 Azure Information Protection의 Azure Rights Management 서비스를 사용하여 클라우드에서 전송 중 및 대기 중에 암호화됩니다. 이는 사용자 자격 증명 같은 민감한 특정 데이터만 디바이스에서 내보내기 전에 암호화하는 Microsoft 계정 기반 설정 동기화에 비해 향상된 기능입니다.

Microsoft는 고객 데이터를 보호하기 위해 최선을 다하고 있습니다. 엔터프라이즈 사용자의 설정 데이터는 Windows 10 디바이스에서 나오기 전에 Azure Rights Management 서비스를 통해 자동으로 암호화되므로 다른 사용자가 이 데이터를 읽을 수 없습니다. 조직에서 Azure Rights Management 서비스에 대한 유료 구독을 보유한 경우 문서 추적 및 해제, 민감한 정보가 포함된 이메일 자동 보호, 사용자 고유 키(BYOK("Bring Your Own Key" 솔루션)라고도 함) 관리 등 다른 보호 기능을 사용할 수 있습니다. 이러한 기능 및 이 보호 서비스의 작동 방식에 대한 자세한 내용은 [Azure Rights Management란](/azure/information-protection/what-is-information-protection)을 참조하세요.

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>특정 앱 또는 설정에 대한 동기화를 관리할 수 있나요?

Windows 10에는 개별 애플리케이션의 로밍을 비활성화하는 MDM 또는 그룹 정책 설정이 없습니다. 테넌트 관리자는 관리 디바이스의 모든 앱에 대해 앱 데이터 동기화를 비활성화할 수 있지만 앱당 또는 앱 내부 수준에서 더욱 정교하게 제어하는 방법은 없습니다.

## <a name="how-can-i-enable-or-disable-roaming"></a>로밍을 활성화/비활성화하려면 어떻게 해야 하나요?

**설정** 앱에서 **계정** > **설정 동기화**로 이동합니다. 이 페이지에서 설정 로밍에 사용되는 계정을 확인할 수 있으며, 개별 설정 그룹의 로밍을 활성화 또는 비활성화할 수 있습니다.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Microsoft는 Windows 10 로밍 활성화에 대해 무엇을 권장합니까?

Microsoft에서는 사용자 프로필 로밍, UE-V, 엔터프라이즈 상태 로밍을 포함하여 몇 가지 설정 로밍 솔루션을 제공하고 있습니다.  Microsoft에서는 이후 버전의 Windows에서도 엔터프라이즈 상태 로밍에 투자하기 위해 최선을 다하고 있습니다. 조직에서 데이터를 클라우드로 이동할 준비가 덜 되었거나 불편하게 생각하는 경우에는 기본 로밍 기술로 UE-V를 사용하시기를 권장합니다. 조직에서 기존 Windows 데스크톱 애플리케이션에 대한 로밍 지원이 필요하지만 클라우드로 이동하고 싶은 경우 Microsoft에서는 엔터프라이즈 상태 로밍과 UE-V를 모두 사용하시기를 권장합니다. UE-V와 엔터프라이즈 상태 로밍이 매우 비슷한 기술이지만 상호 배타적인 관계는 아니며 현재는 조직이 사용자에게 필요한 로밍 서비스를 제공할 수 있도록 서로를 보완해 주는 관계에 있습니다.  

엔터프라이즈 상태 로밍과 UE-V를 모두 사용할 경우 다음 규칙이 적용됩니다.

* Enterprise State Roaming은 디바이스의 기본 로밍 에이전트입니다. UE-V는 “Win32 갭”을 보완하기 위해 사용됩니다.
* UE-V 그룹 정책을 사용 하는 경우 Windows 설정 및 최신 UWP 앱 데이터에 대 한 UE-V 로밍을 사용 하지 않도록 설정 해야 합니다. UE-V 그룹 정책을 사용하여 비활성화해야 합니다.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>엔터프라이즈 상태 로밍은 VDI(가상 데스크톱 인프라)를 어떻게 지원하나요?

엔터프라이즈 상태 로밍은 서버 SKU가 아니라 Windows 10 클라이언트 SKU에서 지원됩니다. 클라이언트 VM이 하이퍼바이저 컴퓨터에서 호스트되는 경우 사용자가 가상 머신에 원격으로 로그인하면 사용자의 데이터가 로밍됩니다. 여러 사용자가 동일한 OS를 공유하고 전체 데스크톱 환경을 위해 서버에 원격으로 로그인하는 경우 로밍이 작동하지 않을 수 있습니다. 두 번째 세션 기반 시나리오는 공식적으로 지원되지 않습니다.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>조직이 로밍 사용 후 Azure Rights Management를 포함하는 구독을 구매하는 경우 어떻게 되나요?

조직에서 Azure Rights Management 사용 제한된 무료 구독을 통해 Windows 10에서 이미 로밍을 사용 중인 경우에는 Azure Rights Management 보호 서비스를 포함하는 [유료 구독](https://azure.microsoft.com/pricing/details/information-protection/)을 구입해도 로밍 기능에 영향을 주지 않으며, IT 관리자가 구성을 변경할 필요가 없습니다.

## <a name="known-issues"></a>알려진 문제

알려진 문제 목록은 [문제 해결](enterprise-state-roaming-troubleshooting.md) 섹션의 설명서를 참조 하세요. 

## <a name="next-steps"></a>다음 단계 

개요는 [엔터프라이즈 상태 로밍 개요](enterprise-state-roaming-overview.md) 를 참조 하세요.