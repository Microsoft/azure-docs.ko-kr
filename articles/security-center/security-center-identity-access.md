---
title: Azure Security Center에서 ID 및 액세스 모니터링 | Microsoft Docs
description: Azure Security Center의 ID 및 액세스 기능을 사용하여 사용자의 액세스 작업과 ID 관련 문제를 모니터링하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 313f10c77da499f4c405134028b975900d2d2a9e
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590237"
---
# <a name="monitor-identity-and-access"></a>ID 및 액세스 모니터링

> [!TIP]
> 2020년 3월부터 Azure Security Center의 ID 및 액세스 권장 사항이 무료 가격 책정 계층의 모든 구독에 포함됩니다. 무료 계층에 대한 구독이 있는 경우 이전에 ID 및 액세스 보안에 대해 평가하지 않았으므로 Secure Score가 영향을 받습니다. 

Security Center에서 잠재적인 보안 취약점을 식별하는 경우 리소스를 보호하고 강화하는 데 필요한 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다.

보안 경계가 네트워크 경계에서 ID 경계로 발전되었습니다. 보안 수준이 네트워크 보호에 대해서는 낮아지고 데이터를 보호하고 앱과 사용자의 보안을 관리하는 방법에 대해서는 높아집니다. 오늘날에는 추가 데이터와 앱을 클라우드로 이동하면 ID는 새 경계가 됩니다.

ID 작업을 모니터링하여 문제가 발생하기 전에 사전 대응 조치를 취하거나 사후 대응을 통해 공격 시도를 중단할 수 있습니다. 예를 들어 Security Center는 더 이상 사용되지 않는 계정(더 이상 필요하지 않으며 Azure Active Directory에서 로그인이 차단됨)을 제거하기 위해 플래그를 지정합니다. 

다음은 Azure Security Center의 **ID 및 액세스** 리소스 보안 섹션에서 볼 수 있는 권장 사항의 예입니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.

이러한 권장 사항과 여기에 표시되는 권장 사항의 전체 목록에 대한 자세한 내용은 [ID 및 액세스 권장 사항](recommendations-reference.md#recs-identity)을 참조하세요.

> [!NOTE]
> 구독에 600개 초과의 계정이 있는 경우 Security Center는 사용자 구독에 대해 ID 권장 사항을 실행할 수 없습니다. 실행되지 않은 권장 사항은 아래 "사용할 수 없는 평가"에 나와 있습니다.
Security Center는 CSP(클라우드 솔루션 공급자) 파트너의 관리 에이전트에 대해 ID 권장 사항을 실행할 수 없습니다.
>


모든 ID 및 액세스 권장 사항은 **권장 사항** 페이지의 두 가지 보안 제어 내에서 사용할 수 있습니다.

- 액세스 및 사용 권한 관리 
- MFA 사용

![ID 및 액세스와 관련된 권장 사항이 포함된 두 가지 보안 제어](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>MFA(다단계 인증) 사용

MFA를 사용하려면 [Azure AD(Active Directory) 테넌트 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)이 필요합니다. 

- AD Premium Edition을 사용하는 경우 [조건부 액세스](../active-directory/conditional-access/concept-conditional-access-policy-common.md)를 사용하여 MFA를 사용하도록 설정합니다.

- AD free edition을 사용 하는 경우 [ad 설명서](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)에 설명 된 대로 Azure Active Directory에서 **보안 기본값** 을 사용 하도록 설정 합니다.


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
- [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
- [Azure Security Center에서 Azure SQL 서비스 및 데이터 보호](security-center-sql-service-recommendations.md)