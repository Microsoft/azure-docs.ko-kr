---
title: 액세스 검토란? - Azure Active Directory | Microsoft Docs
description: Azure Active Directory 액세스 검토를 사용 하 여 조직의 거 버 넌 스, 위험 관리 및 규정 준수 이니셔티브를 충족 하기 위해 그룹 멤버 자격 및 응용 프로그램 액세스를 제어할 수 있습니다.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185738"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD 액세스 검토란?

Azure AD (Azure Active Directory) 액세스 검토를 사용 하면 조직에서 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스는 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

다음은 액세스 검토의 빠른 개요를 제공하는 비디오입니다.

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>액세스 검토가 중요한 이유는 무엇인가요?

Azure AD를 사용하면 조직 내에서 내부적으로, 또한 파트너와 같은 외부 조직의 사용자와 공동 작업을 수행할 수 있습니다. 사용자는 그룹에 가입하고, 게스트를 초대하고, 클라우드 앱에 연결하고, 해당 회사 또는 개인 디바이스에서 원격으로 작업할 수 있습니다. 셀프 서비스 기능을 활용할 수 있는 편의성으로 인해 더 나은 액세스 관리 기능이 필요하게 되었습니다.

- 새로운 직원이 참여했을 때 생산성을 높일 수 있는 적절한 액세스 권한을 어떻게 보장할 수 있나요?
- 사람들이 팀을 이동하거나 회사를 떠날 때, 특히 게스트가 포함된 경우 해당 사용자의 이전 액세스 권한이 제거되었는지 어떻게 보장할 수 있나요?
- 과도한 액세스 권한은 액세스에 대한 제어가 부족함을 나타내므로 결과 및 손상에 대한 감사로 이어질 수 있습니다.
- 해당 리소스에 대한 액세스 권한이 있는 사용자를 정기적으로 검토하도록 리소스 소유자와 사전에 협의해야 합니다.

## <a name="when-to-use-access-reviews"></a>언제 액세스 검토를 사용하나요?

- **권한 있는 역할의 사용자가 너무 많음:** 관리 작업을 수행 하도록 할당 된 후에 제거 되지 않은 초대 된 게스트 또는 파트너가 있는지 확인 하는 것이 좋습니다 .이는 관리자 액세스 권한이 있는 사용자 수를 확인 하는 것입니다. 전역 관리자와 같은 [AZURE AD 역할](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) 또는 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 환경에서 사용자 액세스 관리자와 같은 [azure 리소스 역할](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) 의 역할 할당 사용자를 다시 인증할 수 있습니다.
- **자동화가 불가능 한 경우:** 보안 그룹 또는 Office 365 그룹에 대 한 동적 멤버 자격에 대 한 규칙을 만들 수 있습니다. 그러나 HR 데이터가 Azure AD에 없는 경우 또는 사용자가 계속 해 서 그룹을 사용 하 여 대체를 학습 한 후 액세스 해야 하는 경우는 어떻게 되나요? 해당 그룹에서 검토를 만들어서 계속 액세스 권한이 필요한 사용자가 계속 액세스할 수 있도록 합니다.
- **새로운 목적을 위해 그룹이 사용되는 경우:** Azure AD에 동기화될 그룹이 있거나 판매 팀 그룹의 모든 사용자에 대해 애플리케이션 Salesforce를 사용하도록 설정하려는 경우 그룹이 다른 위험 콘텐츠에서 사용되기 전에 그룹 소유자에게 다른 그룹 멤버 자격을 검토하도록 요청하는 것이 유용합니다.
- **비즈니스에 중요 한 데이터 액세스:** 특정 리소스의 경우 외부 사용자에 게 정기적으로 로그 아웃 하 고 감사 목적으로 액세스 해야 하는 이유에 대 한 근거를 제공 해야 할 수 있습니다.
- **정책의 예외 목록을 유지 관리 하려면 다음을 수행 합니다.** 이상적인 세계에서는 모든 사용자가 액세스 정책을 따라 조직의 리소스에 안전 하 게 액세스할 수 있습니다. 그러나 경우에 따라 예외가 있는 비즈니스 사례가 있습니다. IT 관리자로서, 이 작업을 관리하고, 정책 예외 감시를 방지하며, 이러한 예외를 정기적으로 검토했음에 대한 증명을 감사자에게 제공할 수 있습니다.
- **그룹 소유자에 게 해당 그룹에서 게스트를 계속 필요로 하는지 확인 합니다.** 직원 액세스는 일부 온-프레미스 IAM으로 자동화 될 수 있지만 초대 된 게스트는 사용할 수 없습니다. 그룹이 게스트에게 비즈니스상 중요한 콘텐츠에 대한 액세스 권한을 부여한 경우 게스트에게 액세스에 대한 합법적인 비즈니스 요구가 여전히 있는지를 확인할 책임은 그룹 소유자에게 있습니다.
- **검토를 주기적으로 반복되도록 함:** 주간, 월별, 분기별 또는 연간과 같이 설정된 주기로 사용자의 액세스 검토 반복을 설정할 수 있으며, 검토자는 각 검토 시작 시 알림을 받게 됩니다. 검토자는 친숙한 인터페이스를 사용하거나 스마트 권장 사항을 활용하여 액세스를 승인하거나 거부할 수 있습니다.

## <a name="where-do-you-create-reviews"></a>검토는 어디에서 만드나요?

검토 하려는 내용에 따라 Azure AD 액세스 검토, Azure AD enterprise apps (미리 보기) 또는 Azure AD PIM에서 액세스 검토를 만듭니다.

| 사용자의 액세스 권한 | 검토자는 다음을 수행할 수 있음 | 검토 생성 위치 | 검토자 경험 |
| --- | --- | --- | --- |
| 보안 그룹 멤버</br>사무실 그룹 멤버 | 지정된 검토자</br>그룹 소유자</br>자동 검토 | Azure AD 액세스 검토</br>Azure AD 그룹 | 액세스 패널 |
| 연결된 앱에 할당됨 | 지정된 검토자</br>자동 검토 | Azure AD 액세스 검토</br>Azure AD 엔터프라이즈 앱(미리 보기) | 액세스 패널 |
| Azure AD 역할 | 지정된 검토자</br>자동 검토 | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portal |
| Azure 리소스 역할 | 지정된 검토자</br>자동 검토 | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portal |


## <a name="create-access-reviews"></a>액세스 검토 만들기

액세스 검토를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 액세스 검토를 관리 하 고 전역 관리자 또는 사용자 관리자 권한으로 로그인 합니다.

1. **Azure Active Directory**를 검색하고 선택합니다.

      ![Azure Active Directory Azure Portal 검색](media/access-reviews-overview/search-azure-active-directory.png)

1. **Id 거 버 넌 스**를 선택 합니다.

1. 시작 페이지에서 **액세스 검토 만들기** 단추를 클릭 합니다.

   ![액세스 검토 시작 페이지](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>액세스 검토에 대 한 자세한 정보

액세스 검토를 만들고 수행하는 데 관해 자세히 알아보려면 다음 짧은 데모를 시청하세요.

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

조직에서 액세스 검토를 배포할 준비가 경우 비디오의 단계에 따라 등록하고, 관리자를 교육하고, 첫 번째 액세스 검토를 만드세요!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>필요한 라이선스 수는 몇 개입니까?

다음 작업을 수행 하는 직원이 있으므로 디렉터리에 적어도 Azure AD Premium P2 라이선스가 있는지 확인 합니다.

- 검토자로 할당 된 멤버 및 게스트 사용자
- 자체 검토를 수행 하는 멤버 및 게스트 사용자
- 액세스 검토를 수행 하는 그룹 소유자
- 액세스 검토를 수행 하는 응용 프로그램 소유자

Azure AD Premium P2 라이선스는 다음 작업에 필요 **하지 않습니다** .

- 전역 관리자 또는 사용자 관리자 역할을 가진 사용자에 게 액세스 검토를 설정 하거나, 설정을 구성 하거나, 검토에서 결정을 적용 하는 데 필요한 라이선스가 없습니다.

자신의 조직 사용자 중 하나에 할당 하는 각 유료 Azure AD Premium P2 라이선스에 대해 Azure AD B2B (기업 간)를 사용 하 여 외부 사용자 수에서 최대 5 명의 게스트 사용자를 초대할 수 있습니다. 이러한 게스트 사용자는 Azure AD Premium P2 기능도 사용할 수 있습니다. 자세한 내용은 [AZURE AD B2B 공동 작업 라이선스 지침](../b2b/licensing-guidance.md)을 참조 하세요.

라이선스에 대 한 자세한 내용은 [Azure Active Directory 포털을 사용 하 여 라이선스 할당 또는 제거](../fundamentals/license-users-groups.md)를 참조 하세요.

### <a name="example-license-scenarios"></a>예제 라이선스 시나리오

다음은 보유 해야 하는 라이선스 수를 결정 하는 데 도움이 되는 몇 가지 예제 라이선스 시나리오입니다.

| 시나리오 | 계산 | 라이선스 수 |
| --- | --- | --- |
| 관리자는 75 사용자 및 1 그룹 소유자 인 그룹 A에 대 한 액세스 검토를 만들고 그룹 소유자를 검토자로 할당 합니다. | 검토자로 서 그룹 소유자에 대 한 라이선스 1 개 | 1 |
| 관리자가 그룹 B에 대 한 액세스 검토를 500 사용자 및 3 그룹 소유자로 만들고 세 그룹 소유자를 검토자로 할당 합니다. | 검토자로 서 각 그룹 소유자에 대 한 라이선스 3 개 | 3 |
| 관리자가 500 사용자로 그룹 B에 대 한 액세스 검토를 만듭니다. 자체 검토를 수행 합니다. | 500 각 사용자에 대 한 라이선스를 자체 검토자로 | 500 |
| 관리자가 그룹 C에 대 한 액세스 검토를 50 멤버 사용자 및 25 개의 게스트 사용자로 만듭니다. 자체 검토를 수행 합니다. | 50 각 사용자에 대 한 라이선스를 자체 검토자로 사용 합니다.<br/>(게스트 사용자는 필요한 1:5 비율로 적용 됩니다.) | 50 |
| 관리자가 6 명의 멤버 사용자 및 108 게스트 사용자를 사용 하 여 그룹 D에 대 한 액세스 검토를 만듭니다. 자체 검토를 수행 합니다. | 각 사용자에 대 한 라이선스 6 개-모든 108 게스트 사용자에 게 필요한 1:5 비율 내에 있는 자체 검토자 + 16 추가 라이선스 6\*5 = 30 게스트 사용자를 포함 하는 라이선스 6 개 나머지 (108-6\*5) = 78 게스트 사용자의 경우 78/5 = 16 추가 라이선스가 필요 합니다. 따라서 총 6 + 16 = 22 개의 라이선스가 필요 합니다. | 22 |

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램에 대 한 액세스 검토 만들기](create-access-review.md)
- [Azure AD 관리 역할에서 사용자 액세스 검토 만들기](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [그룹 또는 응용 프로그램에 대 한 액세스 검토](perform-access-review.md)
- [그룹 또는 응용 프로그램에 대 한 액세스 검토 완료](complete-access-review.md)
