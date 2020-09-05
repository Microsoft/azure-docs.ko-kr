---
title: Azure 비용 할당
description: 이 문서에서는 구독, 리소스 그룹 또는 태그 비용을 다른 부서에 분산하도록 비용 할당 규칙을 만드는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/11/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 025b9b7e503f38a111bd158f17b7fbeec5b23579
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684986"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Azure 비용 할당 규칙 만들기 및 관리(미리 보기)

대기업의 경우 Azure 서비스 또는 리소스를 중앙에서 관리하지만 내부의 다른 부서 또는 사업부에서 사용하는 경우가 많습니다. 일반적으로 중앙의 관리 팀은 서비스를 적극적으로 사용하는 내부 부서나 조직의 사업부에 공유 서비스 비용을 다시 할당하기를 원합니다. 이 문서는 Azure Cost Management의 비용 할당을 이해하고 사용하는 데 도움이 됩니다.

비용 할당을 사용하면 구독, 리소스 그룹 또는 태그에서 조직의 다른 구독, 리소스 그룹 또는 태그로 공유 서비스 비용을 재할당하거나 분산할 수 있습니다. 비용 할당은 공유 서비스 비용을 다른 구독, 리소스 그룹 또는 공유 서비스를 사용하는 내부 부서나 사업부 소유의 태그로 이동합니다. 즉, 비용 할당을 통해 _비용 책임_을 관리하고 한 곳에서 다른 곳으로의 비용 책임 이동을 표시할 수 있습니다.

비용 할당은 청구서에 영향을 주지 않습니다. 청구 책임은 변경되지 않습니다. 비용 할당의 주요 목적은 다른 부서에 비용을 청구할 수 있도록 도와주는 것입니다. 모든 차지백 프로세스는 Azure 외부의 조직에서 발생합니다. 비용 할당은 다시 할당되거나 분산되는 비용을 표시하여 비용 차지백을 도와줍니다.

할당된 비용은 비용 분석에 표시됩니다. 할당된 비용은 비용 할당 규칙을 만들 때 지정하는 대상 구독, 리소스 그룹 또는 태그와 관련된 추가 항목으로 표시됩니다.

> [!NOTE]
> Azure Cost Management의 비용 할당 기능은 현재 공개 미리 보기로 제공됩니다. Cost Management의 일부 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 비용 할당은 현재 [Microsoft 고객 계약](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) 또는 [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)를 체결한 고객만 지원합니다.
- 비용 할당 규칙을 만들거나 관리하려면 [기업 계약](../manage/understand-ea-roles.md)에 대한 엔터프라이즈 관리자 계정을 사용해야 합니다. 또는 Microsoft 고객 계약의 [청구 계정](../manage/understand-mca-roles.md) 소유자여야 합니다.

## <a name="create-a-cost-allocation-rule"></a>비용 할당 규칙 만들기

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.
2. **Cost Management + 청구** > **Cost Management**로 이동합니다.
3. **설정** > **구성**에서 **비용 할당(미리 보기)** 을 선택합니다.
4. 올바른 EA 등록 또는 청구 계정이 선택되었는지 확인합니다.
5. **+추가**를 선택합니다.
6. 비용 할당 규칙 이름으로 구체적인 텍스트를 입력합니다.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="규칙 이름 만들기를 보여주는 예제" lightbox="./media/allocate-costs/rule-name.png" :::

규칙의 평가 시작 날짜는 미리 채워진 비용 할당 비율을 생성하는 데 사용됩니다.

1. **원본 추가**를 선택하고 구독, 리소스 그룹 또는 태그 중에 비용을 분산할 항목을 선택합니다.
2. **대상 추가**를 선택하고 구독, 리소스 그룹 또는 태그 중에 할당된 비용을 받을 항목을 선택합니다.
3. 비용 할당 규칙을 더 만들어야 하는 경우 이 프로세스를 반복합니다.

## <a name="configure-the-allocation-percentage"></a>할당 비율 구성

할당 비율을 구성하여 지정된 대상 간에 비용을 비례적으로 분배하는 방법을 정의합니다. 수동으로 정수 백분율을 정의하여 할당 규칙을 만들 수 있습니다. 또는 지정된 대상의 현재 컴퓨팅, 스토리지 또는 네트워크 사용량에 따라 비용을 비례적으로 분할할 수도 있습니다.

컴퓨팅 비용, 스토리지 비용 또는 네트워크 비용을 기준으로 비용을 분산하는 경우 선택한 대상의 비용을 평가하여 비례 비율을 계산합니다. 비용은 현재 청구 월의 리소스 종류와 연결됩니다.

총 비용에 비례하여 비용을 분산하는 경우 현재 청구 월에 대해 선택한 대상의 합계 또는 총 비용을 기준으로 비례 비율이 할당됩니다.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="할당 백분율을 보여주는 예제" lightbox="./media/allocate-costs/cost-distribution.png" :::

설정이 끝나면 정의된 미리 채우기 백분율이 고정됩니다. 진행 중인 모든 할당에 이 백분율이 사용됩니다. 규칙을 수동으로 업데이트하는 경우에만 백분율이 변경됩니다.

1. **미리 채우기 백분율** 목록에서 다음 옵션 중 하나를 선택합니다.
    - **균등하게 분산** – 각 대상에 동일한 총 비용 대비 비율이 할당됩니다.
    - **총 비용** – 총 비용을 기준으로 대상에 비례하는 비율을 만듭니다. 이 비율은 선택한 원본의 비용을 분산하는 데 사용됩니다.
    - **컴퓨팅 비용** - Azure 컴퓨팅 비용([Microsoft.Compute](https://docs.microsoft.com/azure/templates/microsoft.compute/allversions) 네임스페이스의 리소스 종류)을 기준으로 대상에 비례하는 비율을 만듭니다. 이 비율은 선택한 원본의 비용을 분산하는 데 사용됩니다.
    - **스토리지 비용** - Azure 스토리지 비용([Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) 네임스페이스의 리소스 종류)을 기준으로 대상에 비례하는 비율을 만듭니다. 이 비율은 선택한 원본의 비용을 분산하는 데 사용됩니다.
    - **네트워크 비용** - Azure 네트워크 비용([Microsoft.Network](https://docs.microsoft.com/azure/templates/microsoft.network/allversions) 네임스페이스의 리소스 종류)을 기준으로 대상에 비례하는 비율을 만듭니다. 이 비율은 선택한 원본의 비용을 분산하는 데 사용됩니다.
    - **사용자 지정** – 정수 백분율을 수동으로 지정할 수 있습니다. 지정된 합계는 100%여야 합니다.
1. 규칙을 구성할 때 **만들기**를 선택합니다.

할당 규칙이 처리를 시작합니다. 규칙이 활성화되면 선택한 모든 원본의 비용이 지정된 대상에 할당됩니다.

> [!NOTE] 
> 새 규칙 처리가 완료되어 활성화될 때까지 최대 2시간이 걸릴 수 있습니다.

## <a name="verify-the-cost-allocation-rule"></a>비용 할당 규칙 확인

비용 할당 규칙이 활성화되면 선택한 원본의 비용은 지정된 할당 대상에 분산됩니다. 다음 정보를 사용하여 비용이 대상에 올바르게 할당되었는지 확인합니다.

### <a name="view-cost-allocation-for-a-subscription"></a>구독의 비용 할당 보기

비용 분석에서 할당 규칙의 영향을 볼 수 있습니다. Azure Portal에서 [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)으로 이동합니다. 목록에서 활성 비용 할당 규칙의 대상으로 지정된 구독을 선택합니다. 그런 다음, 메뉴에서 **비용 분석**을 선택합니다. 비용 분석에서 **그룹화 방법**을 선택한 다음, **비용 할당**을 선택합니다. 그 결과로 표시되는 보기는 구독에서 생성한 빠른 비용 분석을 보여줍니다. 다음 이미지처럼 구독에 할당된 비용도 표시됩니다.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="비용 분석을 보여주는 예제" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>리소스 그룹의 비용 할당 보기

비슷한 프로세스를 사용하여 리소스 그룹에 대한 비용 할당 규칙의 영향을 볼 수 있습니다. Azure Portal에서 [리소스 그룹](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)으로 이동합니다. 목록에서 활성 비용 할당 규칙의 대상으로 지정된 리소스 그룹을 선택합니다. 그런 다음, 메뉴에서 **비용 분석**을 선택합니다. 비용 분석에서 **그룹화 방법**을 선택한 다음, **비용 할당**을 선택합니다. 리소스 그룹에서 생성한 빠른 비용 분석이 보기에 표시됩니다. 리소스 그룹에 할당된 비용도 표시됩니다.

### <a name="view-cost-allocation-for-tags"></a>태그의 비용 할당 보기

Azure Portal에서 **Cost Management + 청구** > **Cost Management** > **비용 분석**으로 이동합니다. 비용 분석에서 **필터 추가**를 선택합니다. **태그**를 선택하고, 태그 키를 선택하고, 할당된 비용이 있는 태그 값을 선택합니다.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="태그가 지정된 항목의 비용을 보여주는 예제" lightbox="./media/allocate-costs/tagged-costs.png" :::

## <a name="edit-an-existing-cost-allocation-rule"></a>기존 비용 할당 규칙 편집

비용 할당 규칙을 편집하여 원본 또는 대상을 변경하거나 컴퓨팅, 스토리지 또는 네트워크 옵션에 대해 미리 채워진 백분율을 업데이트할 수 있습니다. 규칙을 만들 때와 동일한 방법으로 규칙을 편집합니다. 기존 규칙을 수정하면 다시 처리하는 데 최대 2시간이 걸릴 수 있습니다.

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

다음 섹션에는 비용 할당에 대해 자주 묻는 질문이 포함되어 있습니다.

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>공개 미리 보기로 제공되는 비용 할당의 현재 제한은 무엇인가요?
<a name="limitations"></a>

현재 비용 할당은 Cost Management의 비용 분석, 예산 및 예측 보기에서 지원됩니다. 할당된 비용은 구독 목록과 구독 개요 페이지에도 표시됩니다.

다음 항목은 현재 비용 할당 공개 미리 보기에서 지원되지 않습니다.

- 예약된 [내보내기](tutorial-export-acm-data.md)
- [사용량 세부 정보](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API에서 공개하는 데이터
- 청구 구독 영역
- [Cost Management Power BI 앱](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop 커넥터](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>비용은 예산 및 예측 보기에 포함되나요?
<a name="budgets-forecast"></a>

예. 할당된 비용은 예산 및 예측에 포함되고 지원됩니다. 예산 및 예측 보기에는 비용 할당 규칙에서 구성된 대로 할당된 비용이 표시됩니다.

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>비용 할당 규칙을 삭제하면 어떻게 되나요?
<a name="delete-rule"></a>

비용 할당 규칙을 삭제하면 대상에 할당되는 모든 미해결 및 현재 청구 월 비용이 제거됩니다. 비용 할당 규칙이 수개월 동안 적용되었다면 할당 데이터의 이전 월 기록은 할당 규칙에서 원래 설정된 대로 유지됩니다.

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>등록 관리자 또는 청구 계정 관리자가 비용 할당 규칙을 만들어야 하는 이유는 무엇인가요?
<a name="why-admin"></a>

비용 할당 규칙은 등록 범위(기업계약) 또는 청구 계정 범위(Microsoft 고객 계약)에서 생성됩니다. 이러한 범위에서 변경 작업을 수행하려면 청구 관리자 권한이 필요합니다.

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>원본 및 대상이 규칙당 25개로 제한되는 이유는 무엇인가요?
<a name="source-target-rule-limit"></a>

이 제한은 적절한 비용 할당 성능 및 확장성을 보장하기 위해 미리 보기에 적용되는 제한입니다. 비용 할당이 GA(일반 공급)로 전환될 때 이 제한이 상향 조정되거나 제거될 가능성이 높습니다.

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>비용 할당 규칙(원본/대상)이 겹치면 어떻게 되나요?
<a name="rule-overlap"></a>

원본 또는 대상이 겹치는 규칙은 권장하지 않습니다. 비용 할당 규칙은 만든 날짜 순으로 적용되므로 비용 할당 규칙이 겹치면 만든 날짜가 가장 빠른 할당 규칙이 우선 적용됩니다.

## <a name="next-steps"></a>다음 단계

- [비용 할당 Rest API](https://go.microsoft.com/fwlink/?linkid=2135004)를 사용하여 할당 규칙 만들기 또는 업데이트
- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](cost-mgt-best-practices.md) 자세히 알아보기