---
title: 그룹-Azure Active Directory에 대 한 라이선스 할당 문제 해결 | Microsoft Docs
description: Azure Active Directory 그룹 기반 라이선스를 사용할 때 라이선스 할당 문제를 식별하고 해결하는 방법
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4060c90af3825122c871696a5555e8579d0ad0a
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67358097"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결

Azure AD(Azure Active Directory)의 그룹 기반 라이선스에는 라이선스 오류 상태의 사용자에 대한 개념이 도입되었습니다. 이 문서에서는 사용자가 이 상태가 되는 이유를 설명합니다.

그룹 기반 라이선스를 사용하지 않고 개별 사용자에게 직접 라이선스를 할당하면 할당 작업이 실패할 수 있습니다. 예를 들어 사용자 시스템에 `Set-MsolUserLicense` PowerShell cmdlet을 실행하면 비즈니스 논리와 관련된 여러 가지 이유로 cmdlet이 실패할 수 있습니다. 라이선스 수가 부족하거나 동시에 할당할 수 없는 두 서비스 간에 충돌이 있는 경우를 예로 들 수 있습니다. 문제는 즉시 사용자에게 보고됩니다.

그룹 기반 라이선스를 사용하는 경우 같은 오류가 발생할 수 있지만 Azure AD 서비스에서 라이선스를 할당하면 문제가 백그라운드에서 발생합니다. 이러한 이유로 사용자에게 즉시 오류를 전달 수 없습니다. 대신 오류가 사용자 개체에 기록된 후 관리 포털을 통해 보고됩니다. 사용자에게 라이선스를 부여하는 원래의 의도는 절대 없어지지 않지만 향후 조사 및 문제 해결을 위해 라이선스를 오류 상태로 기록됩니다.

## <a name="how-to-find-license-assignment-errors"></a>라이선스 할당 오류를 찾는 방법
**라이선스 할당 오류를 찾으려면**

1. 특정 그룹에서 오류 상태인 사용자를 찾으려면 그룹의 창을 엽니다. 오류 상태인 사용자가 있는 경우 **라이선스** 아래에서 알림이 표시됩니다.

   ![그룹 및 오류 알림 메시지](./media/licensing-groups-resolve-problems/group-error-notification.png)

2. 알림을 선택하면 영향을 받는 모든 사용자 목록이 열립니다. 자세한 내용을 보려면 개별적으로 각 사용자를 선택하면 됩니다.

   ![라이선스 오류 상태인 그룹의 사용자 목록](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

3. 하나 이상의 오류를 포함하는 모든 그룹을 찾으려면 **Azure Active Directory** 블레이드에서 **라이선스** 및 **개요**를 차례로 선택합니다. 그룹에서 주의가 필요한 경우 정보 상자가 표시됩니다.

   ![개요 및 오류 상태인 그룹에 대 한 정보](./media/licensing-groups-resolve-problems/group-errors-widget.png)

4. 오류가 발생한 모든 그룹의 목록을 보려면 상자를 선택합니다. 자세한 내용은 각 그룹을 선택하면 됩니다.

   ![개요 및 오류 그룹의 목록](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)


다음 섹션에서는 잠재적인 문제 및 문제 해결 방법을 설명합니다.

## <a name="not-enough-licenses"></a>라이선스 부족

**문제:** 그룹에 지정된 제품 중 하나에 사용 가능한 라이선스가 충분하지 않습니다. 제품에 대한 추가 라이선스를 구매하거나 다른 사용자 또는 그룹에서 사용하지 않는 라이선스를 해제해야 합니다.

사용할 수 있는 라이선스 수를 확인하려면 **Azure Active Directory** > **라이선스** > **모든 제품**으로 이동합니다.

라이선스를 사용 중인 사용자와 그룹을 확인하려면 제품을 선택합니다. **사용이 허가된 사용자**에는 라이선스가 직접 할당되었거나 하나 이상의 그룹을 통해 할당된 모든 사용자의 목록이 표시됩니다. **사용이 허가된 그룹**에는 제품이 할당된 모든 그룹이 표시됩니다.

**PowerShell:** PowerShell cmdlet은 이 오류를 _CountViolation_으로 보고합니다.

## <a name="conflicting-service-plans"></a>서비스 계획 충돌

**문제:** 그룹에 지정된 제품 중 하나에 이미 다른 제품을 통해 사용자에게 할당된 다른 서비스 계획과 충돌하는 서비스 계획이 포함되어 있습니다. 일부 서비스 계획은 관련된 다른 서비스 계획과 동일한 사용자에게 할당할 수 없는 방식으로 구성되어 있습니다.

아래 예제를 고려해 보세요. 모든 계획을 사용하도록 설정된 Office 365 Enterprise *E1*의 라이선스가 한 사용자에게 직접 할당되었습니다. 이 사용자는 Office 365 Enterprise *E3* 제품이 할당된 그룹에 추가되었습니다. E3 제품에는 E1에 포함된 계획과 중복될 수 없는 서비스 계획이 포함되어 있으므로 그룹 라이선스 할당은 "서비스 계획 충돌" 오류로 인해 실패합니다. 이 예제에서 충돌하는 서비스 계획은 다음과 같습니다.

-   SharePoint Online(계획 2)은 SharePoint Online(계획 1)과 충돌합니다.
-   Exchange Online(계획 2)은 Exchange Online(계획 1)과 충돌합니다.

이 충돌을 해결하려면 계획 중 두 가지를 사용하지 않도록 설정해야 합니다. 사용자에게 직접 할당되지 않은 E1 라이선스를 해제할 수 있습니다. 또는 전체 그룹 라이선스 할당을 수정하고 E3 라이선스에서 계획을 사용하지 않도록 설정해야 합니다. 또는 E1 라이선스가 E3 라이선스의 컨텍스트에서 중복될 경우 사용자에게서 E1 라이선스를 제거하기로 결정할 수 있습니다.

충돌하는 제품 라이선스 문제를 해결하는 방법은 항상 관리자가 결정합니다. Azure AD는 라이선스 충돌을 자동으로 해결하지 않습니다.

**PowerShell:** PowerShell cmdlet은 이 오류를 _MutuallyExclusiveViolation_으로 보고합니다.

## <a name="other-products-depend-on-this-license"></a>기타 제품은 이 라이선스에 종속됨

**문제:** 그룹에 지정된 제품 중 하나에 다른 제품의 다른 서비스 계획이 작동하기 위해 사용되어야 하는 서비스 계획이 포함되어 있습니다. 이 오류는 Azure AD가 기본 서비스 계획을 제거하려고 시도할 때 발생합니다. 예를 들어 그룹에서 사용자를 제거하는 경우에 발생할 수 있습니다.

이 문제를 해결하기 위해 다른 메서드를 통해 필수 계획을 사용자에게 이미 할당하거나 해당 사용자에 대한 종속 서비스를 비활성화했는지 확인해야 합니다. 그 후 해당 사용자에서 그룹 라이선스를 적절하게 제거할 수 있습니다.

**PowerShell:** PowerShell cmdlet은 이 오류를 _DependencyViolation_으로 보고합니다.

## <a name="usage-location-isnt-allowed"></a>사용 위치가 허용되지 않음

**문제:** 현지법 및 규정으로 인해 지역에 따라 일부 Microsoft 서비스가 제공되지 않을 수 있습니다. 사용자에게 라이선스를 할당하려면 먼저 사용자에 대한 **사용 위치** 속성을 지정해야 합니다. Azure Portal의 **사용자** > **프로필** > **설정** 섹션에서 위치를 지정할 수 있습니다.

Azure AD가 사용 위치가 지원되지 않는 사용자에게 그룹 라이선스를 할당하려고 하면 작업이 실패하고 사용자에 대한 오류를 기록합니다.

이 문제를 해결하려면 라이선스가 부여된 그룹의 지원되지 않는 위치에서 사용자를 제거하면 됩니다. 또는 현재 사용 위치 값이 실제 사용자의 위치를 나타내지 않는 경우에는 다음에 라이선스가 정확히 할당되도록 사용 위치 값을 수정하면 됩니다(새 위치가 지원되는 경우).

**PowerShell:** PowerShell cmdlet은 이 오류를 _ProhibitedInUsageLocationViolation_으로 보고합니다.

> [!NOTE]
> Azure AD가 그룹 라이선스를 할당하면 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다. 현지법 및 규정을 준수하는 그룹 기반 라이선스를 사용하기 전에 관리자가 올바른 사용 위치 값을 사용자에게 설정하는 것이 좋습니다.

## <a name="duplicate-proxy-addresses"></a>프록시 주소 중복

Exchange Online을 사용하는 경우 테넌트의 일부 사용자가 동일한 프록시 주소 값으로 올바르게 구성되지 않을 수 있습니다. 그룹 기반 라이선스가 이러한 사용자에게 라이선스를 할당하려 할 때 실패하면 “프록시 주소가 이미 사용 중”이라는 메시지가 표시됩니다.

> [!TIP]
> 중복 프록시 주소가 있는지를 확인하려면 Exchange Online에 대해 다음과 같은 PowerShell cmdlet을 실행합니다.
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> 이 문제에 대한 자세한 내용은 [Exchange Online에서 "프록시 주소를 이미 사용 중입니다."라는 오류 메시지](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online)가 표시됩니다. 문서에는 [원격 PowerShell을 사용하여 Exchange Online에 연결하는 방법](https://technet.microsoft.com/library/jj984289.aspx)에 대한 정보도 포함됩니다.

영향을 받는 사용자에 대한 프록시 주소 문제를 해결한 후에 그룹에 대해 강제로 라이선스 처리를 수행하여 라이선스가 적용될 수 있게 합니다.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Azure AD 메일 및 ProxyAddresses 특성 변경

**문제:** 사용자 또는 그룹에 라이선스 할당을 업데이트 하는 동안 일부 사용자의 Azure AD 메일 및 ProxyAddresses 특성 증명이 변경 되었는지 확인할 수 있습니다.

프록시 주소 트리거될 수를 계산 하는 사용자 원인에 대 한 라이선스 할당을 업데이트 하는 사용자 특성을 변경할 수 있습니다. 변경의 정확한 이유를 이해 하 고 문제를 해결 하려면이 문서를 참조 [Azure AD에서 proxyAddresses 특성이 채워지는 방법을](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)합니다.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>한 그룹에 두 개 이상의 제품 라이선스가 있으면 어떻게 되나요?

한 그룹에 두 개 이상의 제품 라이선스를 할당할 수 있습니다. 예를 들어 그룹에 Office 365 Enterprise E3 및 Enterprise Mobility + Security를 할당하면 해당 사용자에 대해 포함된 모든 서비스를 쉽게 사용하도록 설정할 수 있습니다.

Azure AD는 그룹에 지정된 모든 라이선스를 각 사용자에게 할당하려고 시도합니다. Azure AD가 비즈니스 논리 문제 때문에 제품 중 하나를 할당할 수 없는 경우 그룹에 다른 라이선스를 할당하지 않습니다. 모든 그룹에 라이선스가 충분하지 않은 경우 또는 사용자에게 사용할 수 있는 다른 서비스와 충돌하는 경우를 예로 들 수 있습니다.

할당에 실패한 사용자를 확인하고 이 문제의 영향을 받는 제품을 확인할 수 있습니다.

## <a name="what-happens-when-a-group-with-licenses-assigned-is-deleted"></a>라이선스가 할당된 경우 그룹을 삭제하면 어떻게 되나요?

그룹을 삭제하기 전에 그룹에 할당된 모든 라이선스를 제거해야 합니다. 그러나 그룹에 있는 모든 사용자의 라이선스를 제거하는 데는 시간이 걸릴 수 있습니다. 그룹에서 라이선스 할당을 제거하는 동안 사용자에게 종속 라이선스가 할당되어 있거나 라이선스 제거를 금지하는 프록시 주소 충돌 문제가 있는 경우 오류가 발생할 수 있습니다. 사용자에게 그룹 삭제로 인해 제거되는 라이선스에 종속되는 라이선스가 있으면 해당 사용자에 대한 라이선스 할당이 상속에서 직접으로 변환됩니다.

예를 들어, 비즈니스용 Skype 요금제가 사용하도록 설정된 Office 365 E3/E5가 그룹에 할당되어 있다고 가정합니다. 또한 그룹의 멤버 일부에게 Audio Conferencing 라이선스가 직접 할당되어 있다고 가정합니다. 해당 그룹을 삭제하면 그룹 기반 라이선스는 모든 사용자의 Office 365 E3/E5를 제거하려고 합니다. Audio Conferencing은 비즈니스용 Skype에 종속되므로, Audio Conferencing이 할당된 모든 사용자의 경우 그룹 기반 라이선스는 Office 365 E3/E5 라이선스를 직접 라이선스 할당으로 변환합니다.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>필수 구성 요소와 제품에 대한 라이선스를 관리하는 방법

사용자가 보유한 일부 Microsoft Online 제품은 *추가 기능*일 수 있습니다. 추가 기능은 라이선스를 할당하기 전에 사용자나 그룹에 대해 필수 구성 요소 서비스 계획을 활성화해야 합니다. 그룹 기반 라이선스를 사용하는 경우 시스템에서는 필수 구성 요소와 추가 기능 서비스 계획이 동일한 그룹에 있어야 합니다. 이것은 그룹에 추가된 모든 사용자가 완전히 작동하는 제품을 받을 수 있게 하기 위한 것입니다. 다음 예제를 살펴보겠습니다.

Microsoft Workplace Analytics는 추가 기능 제품입니다. 여기에는 이름이 같은 단일 서비스 계획이 포함됩니다. 다음 필수 구성 요소 중 하나가 함께 할당되었을 때에만 이 서비스 계획을 사용자 또는 그룹에 할당할 수 있습니다.
- Exchange Online(계획 1) 
- Exchange Online(계획 2)

이 제품만을 그룹에 할당하려고 하면 포털이 오류를 반환합니다. 오류 알림을 선택하면 다음과 같은 세부 정보를 보여줍니다.

![그룹, 필수 구성 요소 누락](./media/licensing-groups-resolve-problems/group-prerequisite-required.png)

세부 정보를 선택하면 다음 오류 메시지가 표시됩니다.

>라이선스 작업에 실패했습니다. 종속 서비스를 추가하거나 제거하기 전에 그룹에 필요한 서비스가 있는지 확인하십시오. **Microsoft Workplace Analytics 서비스에는 Exchange Online(계획 2)도 함께 활성화해야 합니다.**

이 추가 기능 라이선스를 그룹에 할당하려면 그룹에 필수 구성 요소 서비스 계획도 포함되어야 합니다. 예를 들어 전체 Office 365 E3 제품이 포함된 기존 그룹을 업데이트하고 추가 기능 제품을 여기에 추가할 수 있습니다.

추가 기능이 작동할 수 있게 최소 필요 제품만을 포함하도록 독립 실행형 그룹을 만들 수도 있습니다. 이를 사용하여 추가 기능 제품에 대해 선택한 사용자에게만 라이선스를 부여할 수 있습니다. 이 예에서는 동일한 그룹에 다음 제품을 할당했습니다.
- Exchange Online(계획 2) 서비스 계획만을 활성화한 Office 365 Enterprise E3
- Microsoft Workplace Analytics

![그룹, 필수 구성 요소 포함](./media/licensing-groups-resolve-problems/group-addon-with-prerequisite.png)

이제부터 이 그룹에 추가되는 모든 사용자는 E3 제품의 라이선스 한 개와 Workplace Analytics 제품의 라이선스 한 개를 사용합니다. 동시에 이 사용자는 전체 E3 제품을 부여하는 다른 그룹의 구성원이 될 수 있고 해당 제품에 대해서는 여전히 한 개의 라이선스만 사용합니다.

> [!TIP]
> 각 필수 구성 요소 서비스 계획에 대해 여러 그룹을 만들 수 있습니다. 예를 들어 사용자에게 Office 365 Enterprise E1 및 Office 365 Enterprise E3를 모두 사용할 경우 Microsoft Workplace Analytics에 라이선스를 부여하기 위해 두 그룹을 만들 수 있습니다. 한 그룹은 필수 구성 요소인 E1을 사용하고 다른 그룹은 E3를 사용합니다. 이렇게 하면 추가 라이선스를 사용하지 않고 E1과 E3 사용자에게 추가 기능을 배포할 수 있습니다.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>오류를 해결하기 위해 그룹의 라이선스를 강제로 처리하는 방법

오류를 해결하기 위해 수행한 단계에 따라 그룹의 처리를 수동으로 트리거하여 사용자 상태를 업데이트해야 할 수 있습니다.

예를 들어 사용자에게서 직접 라이선스 할당을 제거하여 라이선스를 확보한 경우 모든 사용자 구성원에게 완전한 사용 권한을 부여하기 위해 이전에 실패한 그룹 처리를 트리거해야 합니다. 그룹을 다시 처리하려면 그룹 창으로 이동하고 **라이선스**를 열고 도구 모음에서 **다시 처리** 단추를 선택합니다.

## <a name="how-do-you-force-license-processing-on-a-user-to-resolve-errors"></a>오류를 해결하기 위해 사용자의 라이선스를 강제로 처리하는 방법은?

오류를 해결하기 위해 수행한 단계에 따라 사용자의 처리를 수동으로 트리거하여 사용자 상태를 업데이트해야 할 수 있습니다.

예를 들어 영향을 받은 사용자를 위해 중복 프록시 주소 문제를 해결한 후 사용자 처리를 트리거해야 합니다. 사용자를 다시 처리하려면 사용자 창으로 이동하여 **라이선스**를 연 다음, 도구 모음에서 **다시 처리** 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계

그룹을 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Azure Active Directory의 그룹 기반 라이선스란?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 라이선스 할당](licensing-groups-assign.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](licensing-groups-migrate-users.md)
* [Azure Active Directory에서 그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 마이그레이션하는 방법](licensing-groups-change-licenses.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](licensing-group-advanced.md)
* [Azure Active Directory의 그룹 기반 라이선싱에 대한 PowerShell 예제](licensing-ps-examples.md)
