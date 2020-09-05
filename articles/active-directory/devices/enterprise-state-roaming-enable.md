---
title: Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화
description: Windows 디바이스의 Enterprise State Roaming 설정에 대해 자주 묻는 질문과 대답입니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b554fbef63f23b3540fe49e5c45976122add25
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268607"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화

Azure AD Premium 또는 EMS(Enterprise Mobility + Security) 라이선스를 사용하는 모든 조직에서 Enterprise State Roaming을 사용할 수 있습니다. Azure AD 구독 방법에 대한 자세한 내용은 [Azure AD 제품 페이지](https://azure.microsoft.com/services/active-directory)를 참조하세요.

Enterprise State Roaming을 사용하도록 설정하면 Azure Information Protection에서 Azure Rights Management 보호에 대해 제한된 체험용 라이선스가 조직에 자동으로 부여됩니다. 이 체험용 구독은 엔터프라이즈 설정 암호화 및 Enterprise State Roaming에서 동기화한 애플리케이션 데이터를 암호화하고 암호를 해독하도록 제한됩니다. [유료 구독](https://azure.microsoft.com/pricing/details/information-protection/)이 있어야 Azure Rights Management 서비스의 전체 기능을 사용할 수 있습니다.

> [!NOTE]
> 이 문서는 7 월 2015에 Windows 10으로 시작 된 Microsoft Edge 레거시 HTML 기반 브라우저에 적용 됩니다. 이 문서는 2020 년 1 월 15 일에 릴리스된 새 Microsoft Edge Chromium 기반 브라우저에는 적용 되지 않습니다. 새 Microsoft Edge의 동기화 동작에 대 한 자세한 내용은 [Microsoft Edge 동기화](/deployedge/microsoft-edge-enterprise-sync)문서를 참조 하세요.

## <a name="to-enable-enterprise-state-roaming"></a>Enterprise State Roaming을 사용하려면

1. [Azure AD 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
1. Enterprise State Roaming **Azure Active Directory**  >  **장치**  >  **Enterprise State Roaming**를 선택 합니다.
1. **사용자가 디바이스에서 설정 및 앱 데이터를 동기화할 수 있습니다.** 를 선택합니다. 자세한 내용은 [디바이스 설정을 구성하는 방법](./device-management-azure-portal.md)을 참조하세요.
  
   ![사용자자 디바이스 간에 설정 및 앱 데이터를 동기화할 수 있습니다.라고 레이블이 지정된 디바이스 설정 이미지](./media/enterprise-state-roaming-enable/device-settings.png)
  
Enterprise State Roaming 서비스를 사용하는 Windows 10 디바이스의 경우 Azure AD ID를 사용하여 디바이스를 인증해야 합니다. 디바이스가 Azure AD에 조인된 경우 사용자의 기본 로그인 ID는 해당 Azure AD ID이므로 추가 구성이 필요하지 않습니다. 디바이스에서 온-프레미스 Active Directory를 사용하는 경우 IT 관리자가 [하이브리드 Azure Active Directory 조인 디바이스를 구성](./hybrid-azuread-join-plan.md)해야 합니다. 

## <a name="data-storage"></a>데이터 스토리지

Enterprise State Roaming 데이터는 Azure Active Directory 인스턴스에 설정된 국가/지역 값에 가장 적합한 하나 이상의 [Azure 지역](https://azure.microsoft.com/regions/)에서 호스트됩니다. 엔터프라이즈 상태 로밍 데이터는 북아메리카, EMEA, APAC의 주요 지리적 지역 3개를 기준으로 분할됩니다. 테넌트에 대한 엔터프라이즈 상태 로밍 데이터는 지리적 영역에 로컬로 위치하며 여러 지역에 걸쳐 복제되지 않습니다.  예를 들면 다음과 같습니다.

| 국가/지역 값 | 해당 데이터가 다음 지역에서 호스트되는 경우 |
| -------------------- | ------------------------ |
| 프랑스 또는 잠비아와 같은 EMEA 국가/지역 | 유럽 내 하나 이상의 Azure 지역 |
| 미국 또는 캐나다와 같은 북아메리카 국가/지역 | 미국 내 하나 이상의 Azure 지역 |
| 오스트레일리아 또는 뉴질랜드와 같은 APAC 국가/지역 | 아시아 내 하나 이상의 Azure 지역 |
| 남아메리카 및 남극 대륙 지역 | 미국 내 하나 이상의 Azure 지역 |

국가/지역 값은 Azure AD 디렉터리 생성 프로세스의 일부로 설정되며 나중에 수정할 수 없습니다. 데이터 스토리지 위치에 대한 자세한 내용을 보려면 [Azure 지원](https://azure.microsoft.com/support/options/)에서 티켓을 저장하세요.

## <a name="view-per-user-device-sync-status"></a>사용자별 디바이스 동기화 상태 보기

사용자별 디바이스 동기화 상태 보고서를 보려면 이러한 단계를 수행합니다.

1. [Azure AD 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
1. 사용자 **Azure Active Directory**  >  **Users**  >  **모든 사용자**를 선택 합니다.
1. 사용자를 선택한 다음 **디바이스**를 선택합니다.
1. **표시** 아래에서 **설정 및 앱 데이터를 동기화하는 디바이스**를 선택하여 동기화 상태를 표시합니다.
  
   ![디바이스 동기화 데이터 설정의 이미지](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. 이 사용자에게 동기화된 디바이스가 있는 경우 여기에 표시된 대로 디바이스가 표시됩니다.
  
   ![디바이스 동기화 열 형식 데이터의 이미지](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>데이터 보존

Enterprise State Roaming을 사용하여 Microsoft 클라우드에 동기화된 데이터는 수동으로 삭제되거나 문제의 데이터가 부실하다고 판단될 때까지 유지됩니다. 

### <a name="explicit-deletion"></a>명시적 삭제

명시적 삭제는 Azure 관리자가 사용자 또는 디렉터리를 삭제하거나 데이터를 삭제하도록 명시적으로 요청하는 경우입니다.

* **사용자 삭제**: 사용자가 Azure AD에서 삭제되면 사용자 계정 로밍 데이터는 90~180일 후에 삭제됩니다. 
* **디렉터리 삭제**: Azure AD에서 전체 디렉터리를 삭제하면 그 즉시 디렉터리가 삭제됩니다. 해당 디렉터리와 관련된 모든 설정 데이터는 90~180일 후에 삭제됩니다. 
* **요청 시 삭제**: Azure AD 관리자는 특정 사용자의 데이터 또는 설정 데이터를 수동으로 삭제하고 싶으면 [Azure 지원](https://azure.microsoft.com/support/)에서 티켓을 발행하면 됩니다. 

### <a name="stale-data-deletion"></a>부실 데이터 삭제

1년("보존 기간") 동안 액세스되지 않은 데이터는 부실한 것으로 간주되고 Microsoft 클라우드에서 삭제할 수 있습니다. 보존 기간은 변경될 수 있지만 90일 이상입니다. 특정 Windows/애플리케이션 설정이 유효하지 않을 수도 있고 사용자의 모든 설정이 유효하지 않을 수도 있습니다. 예를 들면 다음과 같습니다.

* 특정 설정 컬렉션에 아무 디바이스도 액세스하지 않는 경우(예: 디바이스에서 애플리케이션이 제거되거나 &quot;테마&quot;와 같은 설정 그룹이 사용자의 모든 디바이스에 대해 비활성화되는 경우) 해당 컬렉션은 보존 기간이 끝난 후 부실하다고 취급되어 삭제될 수 있습니다. 
* 사용자가 모든 장치에서 설정 동기화를 해제 한 경우에는 설정 데이터에 액세스할 수 없으며, 해당 사용자의 모든 설정 데이터는 유효 하지 않게 되 고 보존 기간 후 삭제 될 수 있습니다. 
* Azure AD 디렉터리 관리자가 전체 디렉터리에 대해 엔터프라이즈 상태 로밍을 끄면 해당 디렉터리의 모든 사용자가 설정 동기화를 중지하게 되며, 모든 사용자의 모든 설정 데이터는 보존 기간이 끝난 후 유효하지 않은 것으로 취급되어 삭제될 수 있습니다. 

### <a name="deleted-data-recovery"></a>삭제된 데이터 복구

데이터 보존 정책을 구성할 수 없습니다. 데이터가 영구적으로 삭제되면 복구할 수 없습니다. 그러나 설정 데이터는 최종 사용자 디바이스가 아니라 Microsoft 클라우드에서만 삭제됩니다. 나중에 디바이스에서 Enterprise State Roaming 서비스에 다시 연결하면 설정이 다시 동기화되어 Microsoft 클라우드에 저장됩니다.

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 상태 로밍 개요](enterprise-state-roaming-overview.md)
* [설정 및 데이터 로밍 FAQ](enterprise-state-roaming-faqs.md)
* [설정 동기화에 대한 그룹 정책 및 MDM 설정](enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 로밍 설정 참조](enterprise-state-roaming-windows-settings-reference.md)
* [문제 해결](enterprise-state-roaming-troubleshooting.md)