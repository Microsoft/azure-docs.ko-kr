---
title: 액세스 검토란? - Azure Active Directory | Microsoft Docs
description: Azure Active Directory 액세스 검토를 사용하면 조직의 거버넌스, 위험 관리 및 규정 준수 이니셔티브를 충족할 수 있도록 그룹 멤버 자격 및 애플리케이션 액세스를 제어할 수 있습니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 08/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperfq1
ms.openlocfilehash: c69a5e153377eee86eaf0c43d6c982dee2938ddf
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783674"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD 액세스 검토란?

Azure AD(Azure Active Directory) 액세스 검토를 사용하면 조직에서 그룹 멤버 자격을 효율적으로 관리하고 엔터프라이즈 애플리케이션에 액세스하고 권한 있는 역할을 할당할 수 있습니다. 사용자의 액세스는 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

다음은 액세스 검토의 빠른 개요를 제공하는 비디오입니다.

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>액세스 검토가 중요한 이유는 무엇인가요?

Azure AD를 사용하면 조직 내의 사용자 및 외부 사용자와 협업할 수 있습니다. 사용자는 그룹에 가입하고, 게스트를 초대하고, 클라우드 앱에 연결하고, 해당 회사 또는 개인 디바이스에서 원격으로 작업할 수 있습니다. 셀프 서비스 사용의 편리성으로 인해 더 나은 액세스 관리 기능이 필요하게 되었습니다.

- 새로운 직원이 참여했을 때 생산성을 유지하는 데 필요한 액세스 권한을 어떻게 보장할 수 있나요?
- 사용자가 팀을 이동하거나 회사를 떠날 때 이전 액세스 권한이 제거되었는지 어떻게 확인하나요?
- 과도한 액세스 권한이 있으면 손상될 수 있습니다.
- 과도한 액세스 권한은 액세스에 대한 제어가 부족함을 나타내므로 감사 결과로 이어질 수 있습니다.
- 해당 리소스에 대한 액세스 권한이 있는 사용자를 정기적으로 검토하도록 리소스 소유자와 사전에 협의해야 합니다.

## <a name="when-should-you-use-access-reviews"></a>언제 액세스 검토를 사용해야 하나요?

- **권한 있는 역할에 사용자가 너무 많은 경우:** 관리 액세스 권한이 있는 사용자 수, 전역 관리자의 수, 관리 작업을 수행하도록 할당된 후 제거되지 않은 초대된 게스트 또는 파트너가 있는지 여부를 확인하는 것이 좋습니다. [Azure AD PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md) 환경에서 전역 관리자와 같은 [Azure AD 역할](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) 또는 사용자 액세스 관리자와 같은 [Azure 리소스 역할](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)의 역할 할당 사용자를 다시 인증할 수 있습니다.
- **자동화가 불가능한 경우:** 보안 그룹 또는 Office 365 그룹에서 동적 멤버 자격에 대한 규칙을 만들 수 있습니다. 하지만, HR 데이터가 Azure AD에 없거나 사용자가 그룹을 떠난 뒤에도 대체자를 교육시키기 위해 액세스 권한이 필요한 경우 어떻게 해야 하나요? 해당 그룹에서 검토를 만들어서 계속 액세스 권한이 필요한 사용자가 계속 액세스할 수 있도록 합니다.
- **새로운 목적을 위해 그룹이 사용되는 경우:** Azure AD에 동기화될 그룹이 있거나 판매 팀 그룹의 모든 사용자에 대해 애플리케이션 Salesforce를 사용하도록 설정하려는 경우 그룹이 다른 위험 콘텐츠에 사용되기 전에 그룹 소유자에게 다른 그룹 멤버 자격을 검토하도록 요청하는 것이 유용합니다.
- **비즈니스 중요 데이터 액세스:** 특정 리소스의 경우 IT 외부 사용자에게 정기적으로 로그아웃하도록 요청하고 감사 목적을 위해 액세스가 필요한 이유에 대한 근거를 제공해야 할 수 있습니다.
- **정책의 예외 목록을 유지 관리하려면:** 이상적인 환경에서 모든 사용자는 조직의 리소스에 대한 액세스를 보호하는 액세스 정책을 따릅니다. 그러나 경우에 따라 예외가 있는 비즈니스 사례가 있습니다. IT 관리자로서, 이 작업을 관리하고, 정책 예외 감시를 방지하며, 이러한 예외를 정기적으로 검토했음에 대한 증명을 감사자에게 제공할 수 있습니다.
- **그룹 소유자에게 해당 그룹에 게스트가 여전히 필요한지 확인하도록 요청합니다:** 지원 액세스는 일부 온-프레미스 IAM(ID 및 액세스 관리)을 사용하여 직원 액세스를 자동화할 수 있지만 초대된 게스트는 그렇지 않습니다. 그룹이 게스트에게 비즈니스상 중요한 콘텐츠에 대한 액세스 권한을 부여한 경우 게스트에게 액세스에 대한 합법적인 비즈니스 요구가 여전히 있는지를 확인할 책임은 그룹 소유자에게 있습니다.
- **검토를 주기적으로 반복되도록 함:** 주간, 월별, 분기별 또는 연간과 같이 설정된 빈도에 따라 사용자의 액세스 검토 반복을 설정할 수 있으며, 검토자는 각 검토 시작 시 알림을 받게 됩니다. 검토자는 친숙한 인터페이스를 사용하거나 스마트 권장 사항을 활용하여 액세스를 승인하거나 거부할 수 있습니다.

>[!NOTE]
>액세스 검토를 시도할 준비가 되었으면 [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)를 살펴보세요.

## <a name="where-do-you-create-reviews"></a>검토는 어디에서 만드나요?

검토할 항목에 따라 Azure AD 액세스 검토, Azure AD 엔터프라이즈 앱(미리 보기) 또는 Azure AD PIM에서 액세스 검토를 만들게 됩니다.

| 사용자의 액세스 권한 | 검토자는 다음을 수행할 수 있음 | 검토 생성 위치 | 검토자 경험 |
| --- | --- | --- | --- |
| 보안 그룹 멤버</br>사무실 그룹 멤버 | 지정된 검토자</br>그룹 소유자</br>자체 검토 | Azure AD 액세스 검토</br>Azure AD 그룹 | 액세스 패널 |
| 연결된 앱에 할당됨 | 지정된 검토자</br>자체 검토 | Azure AD 액세스 검토</br>Azure AD 엔터프라이즈 앱(미리 보기) | 액세스 패널 |
| Azure AD 역할 | 지정된 검토자</br>자체 검토 | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portal |
| Azure 리소스 역할 | 지정된 검토자</br>자체 검토 | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portal |

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>몇 개의 라이선스가 있어야 하나요?

디렉터리에는 다음 작업을 수행할 직원 수만큼의 Azure AD Premium P2 라이선스가 필요합니다.

- 검토자로 할당된 멤버 및 게스트 사용자
- 자체 검토를 수행하는 멤버 및 게스트 사용자
- 액세스 검토를 수행하는 그룹 소유자
- 액세스 검토를 수행하는 애플리케이션 소유자

Azure AD Premium P2 라이선스는 액세스 검토를 설정하거나, 설정을 구성하거나, 검토에서 나온 결정 사항을 적용하는 전역 관리자 또는 사용자 관리자 역할을 가진 사용자에게는 필요하지 **않습니다**.

자체 조직의 사용자 중 한 명에게 할당하는 각 유료 Azure AD Premium P2 라이선스에 대해 Azure AD B2B(business-to-business)를 사용하여 외부 사용자 허용에 따라 최대 5명의 게스트 사용자를 초대할 수 있습니다. 이러한 게스트 사용자는 Azure AD Premium P2 기능도 사용할 수 있습니다. 자세한 내용은 [Azure AD B2B 협업 라이선스 지침](../external-identities/licensing-guidance.md)을 참조하세요.

라이선스에 대한 자세한 내용은 [Azure Active Directory 포털을 사용하여 라이선스 할당 또는 제거](../fundamentals/license-users-groups.md)를 참조하세요.

### <a name="example-license-scenarios"></a>라이선스 시나리오 예

필요한 라이선스 수를 결정하는 데 도움이 되는 몇 가지 라이선스 시나리오 예는 다음과 같습니다.

| 시나리오 | 계산 | 라이선스 수 |
| --- | --- | --- |
| 관리자는 75명의 사용자와 1명의 그룹 소유자가 있는 그룹 A의 액세스 검토를 만들고 그룹 소유자를 검토자로 할당합니다. | 검토자인 그룹 소유자용 라이선스 1개 | 1 |
| 관리자는 500명의 사용자와 3명의 그룹 소유자가 있는 그룹 B의 액세스 검토를 만들고 3명의 그룹 소유자를 검토자로 할당합니다. | 검토자인 각 그룹 소유자용 라이선스 3개 | 3 |
| 관리자가 500명의 사용자가 있는 그룹 B의 액세스 검토를 만듭니다. 자체적으로 검토합니다. | 자체 검토자인 각 사용자용 라이선스 500개 | 500 |
| 관리자가 50명의 멤버 사용자와 25명의 게스트 사용자가 있는 그룹 C의 액세스 검토를 만듭니다. 자체적으로 검토합니다. | 자체 검토자인 각 사용자용 라이선스 50개<br/>(게스트 사용자는 5명당 1개 비율로 필요합니다.) | 50 |
| 관리자가 6명의 멤버 사용자와 108명의 게스트 사용자가 있는 그룹 D의 액세스 검토를 만듭니다. 자체적으로 검토합니다. | 자체 검토자인 각 사용자용 라이선스 6개 + 108명의 게스트 사용자 모두를 위한 추가 라이선스 16개(5명당 1개 비율로 필요) 게스트 사용자용 라이선스 6개(6\*5 = 30) 나머지 게스트 사용자(108-6\*5 = 78명)를 위해 78/5 = 16개의 추가 라이선스가 필요합니다. 따라서 총 6 + 16 = 22개의 라이선스가 필요합니다. | 22 |

## <a name="next-steps"></a>다음 단계

- [그룹 또는 애플리케이션의 액세스 검토 만들기](create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [그룹 또는 애플리케이션에 대한 액세스 검토](perform-access-review.md)
- [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)