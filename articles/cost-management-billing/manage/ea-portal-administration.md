---
title: Azure EA Portal 관리
description: 이 문서에서는 관리자가 Azure EA Portal에서 수행하는 일반적인 작업에 대해 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 764346c7d37e4738992ddf25c11f5ee0913e308d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689901"
---
# <a name="azure-ea-portal-administration"></a>Azure EA Portal 관리

이 문서에서는 관리자가 Azure EA Portal(https://ea.azure.com) 에서 수행하는 일반적인 작업에 대해 설명합니다. Azure EA Portal은 고객이 Azure EA 서비스의 비용을 관리할 수 있는 온라인 관리 포털입니다. Azure EA Portal을 소개하는 내용은 [Azure EA Portal 시작](ea-portal-get-started.md) 문서를 참조하세요.

## <a name="associate-an-account-to-a-department"></a>부서에 계정 연결

엔터프라이즈 관리자는 기존 계정을 등록된 [부서]에 연결할 수 있습니다.

### <a name="to-associate-an-account-to-a-department"></a>계정을 부서에 연결하려면

1. Azure EA Portal에 엔터프라이즈 관리자로 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **부서**를 선택합니다.
1. 마우스로 계정이 있는 행 위를 가리키고, 오른쪽에 있는 연필 아이콘을 선택합니다.
1. 드롭다운 메뉴에서 부서를 선택합니다.
1. **저장**을 선택합니다.

## <a name="department-spending-quotas"></a>부서 지출 할당액

EA 고객은 등록계약에 따라 각 부서의 지출 할당액을 설정하거나 변경할 수 있습니다. 지출 할당 금액은 현재 선불 기간에 대해 설정됩니다. 현재 선불 기간이 종료되면 값이 업데이트되지 않는 한 시스템에서 기존 지출 할당액을 다음 선불 기간으로 연장합니다.

부서 관리자는 지출 할당액을 볼 수 있으며, 엔터프라이즈 관리자만 할당액을 업데이트할 수 있습니다. 할당액이 50%, 75%, 90%, 100%에 도달하면 엔터프라이즈 관리자와 부서 관리자는 알림을 받게 됩니다.

### <a name="enterprise-administrator-to-set-the-quota"></a>엔터프라이즈 관리자가 할당액을 설정하려면,

 1. Azure EA Portal을 엽니다.
 1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
 1. **부서** 탭을 선택합니다.
 1. [부서]를 선택합니다.
 1. [부서 세부 정보] 섹션에서 연필 기호를 선택하거나 **+ 부서 추가** 기호를 선택하여 새 부서와 함께 지출 할당액을 추가합니다.
 1. [부서 세부 정보] 아래의 [지출 할당액 $] 상자에서 지출 할당 금액을 등록계약의 통화 단위로 입력합니다(0보다 커야 함).
    - 이 시점에서 부서 이름과 비용 센터도 편집할 수 있습니다.
 1. **저장**을 선택합니다.

이제 부서 지출 할당액이 [부서] 탭 아래의 [부서 목록] 보기에 표시됩니다. 현재 선불이 종료되면 Azure EA Portal은 다음 선불 기간에 대한 지출 할당액을 유지합니다.

부서 할당 금액은 현재 Azure 선불과 독립적이며, 할당 금액과 경고는 자사의 사용에만 적용됩니다. 부서 지출 할당액은 정보 제공용으로만 제공되며 지출 한도를 적용하지 않습니다.

### <a name="department-administrator-to-view-the-quota"></a>부서 관리자가 할당액을 보려면,

1. Azure EA Portal을 엽니다.
1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **부서** 탭을 선택하고, 지출 할당액이 있는 [부서 목록] 보기를 살펴봅니다.

간접 고객인 경우 채널 파트너가 비용 기능을 사용하도록 설정해야 합니다.

## <a name="enterprise-user-roles"></a>엔터프라이즈 사용자 역할

Azure EA Portal에서 Azure EA 비용 및 사용량을 관리할 수 있습니다. Azure EA Portal에는 다음과 같은 세 가지 주요 역할이 있습니다.

- EA 관리자
- 부서 관리자
- 계정 소유자

각 역할의 액세스 및 권한 수준은 서로 다릅니다.

사용자 역할에 대한 자세한 내용은 [엔터프라이즈 사용자 역할](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles)을 참조하세요.

## <a name="add-an-azure-ea-account"></a>Azure EA 계정 추가

Azure EA 계정은 Azure EA Portal의 조직 구성 단위입니다. 구독을 관리하는 데 사용되며, 보고하는 데에도 사용됩니다. Azure 서비스에 액세스하여 사용하려면 계정을 만들거나 다른 사람이 대신 계정을 만들어주어야 합니다.

Azure 계정에 대한 자세한 내용은 [계정 추가](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#add-an-account)를 참조하세요.

## <a name="enterprise-devtest-offer"></a>Enterprise 개발/테스트 제안

Azure 엔터프라이즈 관리자는 조직의 계정 소유자가 EA 개발/테스트 제안을 기반으로 하여 구독을 만들 수 있도록 설정할 수 있습니다. 이렇게 하려면 Azure EA Portal에서 계정 소유자에 대한 개발/테스트 상자를 선택합니다.

[개발/테스트] 확인란이 선택되면 개발/테스트 구독자 팀에 필요한 EA 개발/테스트 구독을 설정할 수 있도록 계정 소유자에게 알려줍니다.

이 제안을 통해 활성 Visual Studio 구독자는 Azure에서 개발 및 테스트 워크로드를 특별한 개발/테스트 요율로 실행할 수 있습니다. Windows 8.1 및 Windows 10을 포함하여 개발/테스트 이미지의 전체 갤러리에 액세스할 수 있습니다.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Enterprise 개발/테스트 제안을 설정하려면,

1. 엔터프라이즈 관리자 권한으로 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **계정** 탭을 선택합니다.
1. 개발/테스트 액세스를 사용하도록 설정하려는 계정의 행을 선택합니다.
1. 행의 오른쪽에 있는 연필 기호를 선택합니다.
1. [개발/테스트] 확인란을 선택합니다.
1. **저장**을 선택합니다.

Azure EA Portal을 통해 사용자가 계정 소유자로 추가되면 PAYG 개발/테스트 제안 또는 Visual Studio 구독자를 위한 월별 크레딧 제안을 기반으로 하는 계정 소유자와 연결된 모든 Azure 구독이 EA 개발/테스트 제안으로 변환됩니다. 계정 소유자와 관련된 다른 제안 유형(예: PAYG)을 기반으로 하는 구독은 Microsoft Azure 엔터프라이즈 제안으로 변환됩니다.

개발/테스트 제안은 현재 Azure Gov 고객에게 적용되지 않습니다.

## <a name="delete-subscription"></a>구독 삭제

계정 소유자인 구독을 삭제하려면,

1. 계정에 연결된 자격 증명을 사용하여 Azure Portal에 로그인합니다.
1. 허브 메뉴에서 **구독**을 선택합니다.
1. 페이지의 왼쪽 위 모서리에 있는 구독 탭에서 취소하려는 구독을 선택하고, **구독 취소**를 선택하여 취소 탭을 시작합니다.
1. 구독 이름을 입력하고, 취소 이유를 선택하고, **구독 취소** 단추를 선택합니다.

계정 관리자만 구독을 취소할 수 있습니다.

자세한 내용은 [구독이 취소되면 어떻게 되나요?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)를 참조하세요.

## <a name="delete-an-account"></a>계정 삭제

활성 구독이 없는 활성 계정만 제거할 수 있습니다.

1. Enterprise Portal의 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. **계정** 탭을 선택합니다.
1. [계정] 테이블에서 삭제하려는 계정을 선택합니다.
1. [계정] 행의 오른쪽에 있는 X 기호를 선택합니다.
1. 계정에 활성 구독이 없으면 [계정] 행 아래에서 **예**를 선택하여 계정 제거를 확인합니다.

## <a name="update-notification-settings"></a>알림 설정 업데이트

엔터프라이즈 관리자는 등록과 연결된 사용 알림을 받도록 자동으로 등록됩니다. 각 엔터프라이즈 관리자는 개별 알림의 간격을 변경하거나 알림을 완전히 해제할 수 있습니다.

알림 연락처는 Azure EA Portal의 **알림 연락처** 섹션에 표시됩니다. 조직 내 적절한 사용자가 Azure EA 알림을 받도록 알림 연락처를 관리할 수 있습니다.

현재 알림 설정을 보려면 다음을 수행합니다.

1. Azure EA Portal에서 **관리** > **알림 연락처**로 이동합니다.
2. 이메일 주소 – 알림을 받는 엔터프라이즈 관리자의 Microsoft 계정, 회사 또는 학교 계정과 연결된 이메일 주소입니다.
3. 미청구 잔액 알림 빈도 – 각 개별 엔터프라이즈 관리자에게 알림으로 보내도록 설정되는 빈도입니다.

연락처를 추가하려면 다음을 수행합니다.

1. **+ 연락처 추가**를 선택합니다.
2. 이메일 주소를 입력하고 확인합니다.
3. **저장**을 선택합니다.

새 알림 연락처가 **알림 연락처** 섹션에 표시됩니다. 알림 빈도를 변경하려면 알림 연락처를 선택하고, 선택한 행의 오른쪽에 있는 연필 기호를 선택합니다. 빈도를 **매일**, **매주**, **매월** 또는 **없음**으로 설정합니다.

_적용 기간 종료 날짜 임박_ 및 _비활성화 및 프로비전 해제 날짜 임박_ 수명 주기 알림을 사용하지 않을 수 있습니다. 수명 주기 알림을 사용하지 않도록 설정하면 적용 기간 및 계약 종료 날짜에 대한 알림이 표시되지 않습니다.

## <a name="azure-sponsorship-offer"></a>Azure 스폰서쉽 제안

Azure 스폰서쉽 제안은 제한된 후원을 받는 Microsoft Azure 계정입니다. 이 제안은 이메일 초대를 통해 Microsoft에서 선택한 제한된 고객에게만 제공됩니다. Microsoft Azure 스폰서쉽 제안을 받을 자격이 있는 경우 계정 ID에 대한 이메일 초대장을 받게 됩니다.

자세한 내용을 알아보려면 [스폰서쉽 활성화 지원 요청](https://aka.ms/azrsponsorship)을 작성하여 제출하세요.

## <a name="conversion-to-work-or-school-account-authentication"></a>회사 또는 학교 계정 인증으로 변환

Azure 엔터프라이즈 사용자는 Microsoft 계정(MSA 또는 Live ID)에서 회사 또는 학교 계정(Azure에서 Active Directory 사용) 인증 유형으로 변환할 수 있습니다.

시작하려면

1. 필요한 역할의 작업 또는 학교 계정을 Azure EA Portal에 추가합니다.
1. 오류가 발생하면 계정이 활성 디렉터리에서 유효하지 않을 수 있습니다.  Azure는 UPN(사용자 계정 이름)을 사용하며, 항상 이메일 주소와 동일하지는 않습니다.
1. 회사 또는 학교 계정을 사용하여 Azure EA Portal에 인증합니다.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>구독을 Microsoft 계정에서 회사 또는 학교 계정으로 변환하려면

1. 구독을 소유한 Microsoft 계정을 사용하여 관리 포털에 로그인합니다.
1. 계정 소유권 이전을 사용하여 새 계정으로 이전합니다.
1. 이제 Microsoft 계정은 활성 구독에서 구속되지 않으므로 삭제할 수 있습니다.
1. 삭제된 계정은 과거의 청구 이유로 인해 포털에서 비활성 상태로 유지됩니다.  활성 계정만 표시하는 확인란을 선택하여 보기에서 필터링할 수 있습니다.

## <a name="account-subscription-ownership-faq"></a>계정 구독 소유권 FAQ

이 문서에서는 계정 구독 소유권과 관련된 일반적인 질문에 답변합니다.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>구독당 Azure 계정 소유자의 수는 어떻게 되나요?

구독당 하나의 계정 소유자만 허용됩니다.  [Azure Portal](https://portal.azure.com) 페이지의 왼쪽 위 모서리에 있는 구독 탭에서 [역할 기반 액세스] 또는 [액세스 제어(IAM)]를 사용하여 추가 역할을 추가할 수 있습니다.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>한 Azure 계정 소유자가 둘 이상의 부서에 나열될 수 있나요?

아니요, 계정 소유자는 단일 부서에만 연결할 수 있습니다. 이 정책은 Azure EA Portal의 EA 등록계약에 따라 조정되는 부서와 관련된 비용/지출을 정확하게 모니터링하고 할당하는 데 도움이 됩니다.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Azure 계정 소유자는 보안 그룹으로 나열할 수 있나요?

아니요, 구독 소유자는 고유한 MSA(Microsoft 계정) 또는 AAD(Azure Active Directory) 인증이어야 합니다. 조직 내에서의 양도를 처리하기 위해 일반 계정을 만들고 AAD를 사용하여 구독 액세스를 관리하는 것이 좋습니다.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>개별 사용자가 여러 구독을 소유할 수 있나요?

Azure 계정 소유자는 개수에 제한 없이 구독을 만들고 관리할 수 있습니다.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>내 조직의 모든 구독에 액세스하고 이를 확인하려면 어떻게 해야 하나요?

현재 이 작업은 정책을 통해 수행해야 합니다. 즉, 계정은 만들어진 모든 구독에 대해 역할 기반 액세스를 사용하는 구독 역할에 추가되어야 합니다.

### <a name="where-do-i-go-to-create-a-subscription"></a>구독을 만들려면 어떻게 해야 하나요?

엔터프라이즈 Azure(EA) 제안 구독을 만들려면 먼저 Azure EA Portal에서 EA 등록계약의 관리자가 계정을 계정 소유자 역할에 추가해야 합니다. 그런 다음, Azure EA Portal에 로그인하여 EA 제안 유형 구독을 만들 수 있는 권한을 얻어야 합니다. 첫 번째 EA 구독은 EA Portal의 구독 탭에 있는 '+ 구독 추가' 링크에서 만드는 것이 좋습니다.  그러나 권한이 계정에 부여되면 portal.azure.com 페이지의 왼쪽 위 모서리에 있는 구독 탭에서 구독을 더 쉽게 만들 수 있습니다. 여기서는 단일 단계에서 구독을 만들고 구독 이름을 바꿀 수 있습니다.

### <a name="who-can-create-a-subscription"></a>누가 구독을 만들 수 있나요?

엔터프라이즈 Azure 제안 유형 구독을 만들려면 [EA Portal](https://ea.azure.com)에서 계정 소유자 역할에 대한 권한이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [가상 머신 예약](ea-portal-vm-reservations.md)을 통해 비용을 절감하는 방법에 대해 알아보세요.
- Azure EA Portal 이슈를 해결하는 데 도움이 필요한 경우 [Azure EA Portal 액세스 문제 해결](ea-portal-troubleshoot.md)을 참조하세요.
