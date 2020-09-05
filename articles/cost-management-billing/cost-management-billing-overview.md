---
title: Azure Cost Management + 청구 개요
description: Azure Cost Management + 청구 기능을 사용하여 청구 관리 작업을 수행하고 비용에 대한 청구 액세스를 관리합니다. 또한 Azure 지출을 모니터링 및 제어하고 Azure 리소스 사용을 최적화하는 기능을 사용합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: ''
ms.openlocfilehash: 7f4585d7199b44c82df9a03ea3244885da72d2fa
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685173"
---
# <a name="what-is-azure-cost-management--billing"></a>Azure Cost Management + 청구란?

Microsoft 클라우드를 사용하여 비즈니스 워크로드의 기술적 성능을 크게 향상할 수 있습니다. 또한 조직 자산을 관리하는 데 필요한 비용과 오버헤드를 줄일 수 있습니다. 그러나 클라우드 배포 시 낭비와 비효율성이 발생할 가능성이 있으므로 비즈니스 기회는 위험을 동반합니다. Azure Cost Management + 청구는 Microsoft에서 제공하는 도구 모음으로 워크로드 비용을 분석, 관리 및 최적화하는 데 도움이 됩니다. 이 도구 모음을 사용하면 클라우드에서 제공하는 혜택을 조직에서 활용할 수 있습니다.

Azure 워크로드를 집의 조명으로 생각할 수 있습니다. 하루 종일 외출할 때 조명을 켜 두고 나가시나요? 효율이 더 높은 다른 전구를 사용하면 월별 에너지 비용을 줄이는 데 도움이 될까요? 방에 필요한 수보다 많은 조명을 사용하시나요? Azure Cost Management + 청구를 사용하면 이와 비슷한 사고 프로세스를 조직에서 사용하는 워크로드에 적용할 수 있습니다.

Azure 제품 및 서비스를 사용하면 사용한 만큼만 요금을 지불하면 됩니다. Azure 리소스를 만들고 사용할 때 리소스 요금이 청구됩니다. 새 리소스를 간편하게 배포할 수 있으므로 적절한 분석 및 모니터링 없이 워크로드 비용이 대폭 증가할 수 있습니다. Azure Cost Management + 청구 기능을 사용하여 다음과 같은 일을 할 수 있습니다.

- 청구서 대금 결제와 같은 대금 청구 관리 작업 수행
- 비용에 대한 청구 액세스 관리
- 월간 청구서를 생성하는 데 사용된 비용 및 사용량 데이터 다운로드
- 데이터 분석을 비용에 선제적으로 적용
- 지출 임계값 설정
- 지출을 최적화할 수 있는 워크로드 변경 기회 포착

조직 차원에서 비용 관리에 접근하는 방법에 대한 자세한 내용은 [Azure Cost Management 모범 사례](./costs/cost-mgt-best-practices.md) 문서를 참조하세요.

## <a name="understand-azure-billing"></a>Azure 청구 이해

Azure 청구 기능은 송장이 발부된 비용을 검토하고 청구 정보에 대한 액세스를 관리하는 데 사용됩니다. 대규모 조직에서 일반적으로 조달 및 재무 팀이 청구 작업을 수행합니다.

청구 계정은 Azure를 사용하기 위해 가입할 때 생성됩니다. 청구 계정을 사용하여 청구서, 결제를 관리하고 비용을 추적할 수 있습니다. 여러 청구 계정에 액세스할 수 있습니다. 예를 들어 개인 프로젝트를 위해 Azure에 가입한 경우가 있을 수 있습니다. 따라서 청구 계정이 있는 개별 Azure 구독이 있을 수 있습니다. 또한 조직의 기업계약 또는 Microsoft 고객 계약을 통해 액세스할 수 있습니다. 시나리오마다 별도의 청구 계정을 갖게 됩니다.

### <a name="billing-accounts"></a>청구 계정

현재 Azure Portal에서는 다음과 같은 유형의 청구 계정을 지원합니다.

- **Microsoft Online Services 프로그램**: Microsoft Online Services 프로그램의 개별 청구 계정은 Azure 웹 사이트를 통해 Azure에 가입할 때 생성됩니다. 예를 들어 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입하는 경우 [종량제 요금을 사용하는 계정](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) 자격입니다.

- **기업 계약**: 기업계약에 대한 청구 계정은 조직에서 Azure를 사용하기 위해 [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)에 서명할 때 생성됩니다.

- **Microsoft 고객 계약**: Microsoft 고객 계약에 대한 청구 계정은 조직이 Microsoft 담당자와 협력하여 Microsoft 고객 계약에 서명할 때 생성됩니다. Azure 웹 사이트를 통해 [종량제 요금을 사용한 계정](https://azure.microsoft.com/offers/ms-azr-0003p/)에 가입하거나 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)을 업그레이드한 특정 지역의 일부 고객은 Microsoft 고객 계약에 대한 청구 계정도 가질 수도 있습니다. 자세한 내용은 [Microsoft 고객 계약의 청구 계정 시작](./understand/mca-overview.md)을 참조하세요.

### <a name="scopes-for-billing-accounts"></a>청구 계정의 범위
범위는 청구를 보고 관리하는 데 사용하는 청구 계정 내의 노드입니다. 여기서 청구 데이터, 결제, 청구서를 관리하고 일반 계정 관리를 수행합니다.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services 프로그램

|범위  |정의  |
|---------|---------|
|청구 계정     | 하나 이상의 Azure 구독에 대한 단일 소유자(계정 관리자)를 나타냅니다. 계정 관리자는 구독 만들기, 청구서 보기, 구독의 청구 변경 등 다양한 청구 작업을 수행할 수 있는 권한이 있습니다.  |
|Subscription     |  Azure 리소스의 그룹화를 나타냅니다. 청구서는 구독 범위에서 생성됩니다. 청구서를 결제하는 데 사용되는 고유한 결제 방법이 있습니다.|

#### <a name="enterprise-agreement"></a>기업 계약

|범위  |정의  |
|---------|---------|
|청구 계정    | 기업계약 등록을 나타냅니다. 청구서는 청구 계정 범위에서 생성됩니다. 부서 및 등록 계정을 사용하여 구성됩니다.  |
|department     |  등록 계정의 선택적 그룹화입니다.      |
|등록 계정     |  단일 계정 소유자를 나타냅니다. Azure 구독은 등록 계정 범위 아래에 생성됩니다.  |

#### <a name="microsoft-customer-agreement"></a>Microsoft 고객 계약

|범위  |작업  |
|---------|---------|
|청구 계정     |   여러 Microsoft 제품 및 서비스에 대한 고객 계약을 나타냅니다. 청구 계정은 청구 프로필 및 청구서 섹션을 사용하여 구성됩니다.   |
|청구 프로필     |  청구서 및 결제 방법을 나타냅니다. 이 범위에서 청구서가 생성됩니다. 청구 프로필에는 청구서 섹션이 여러 개 있을 수 있습니다.      |
|청구서 섹션     |   청구서의 비용 그룹을 나타냅니다. 구독과 기타 구매는 청구서 섹션 범위에 연결됩니다.    |

## <a name="understand-azure-cost-management"></a>Azure Cost Management 이해

관련은 있지만 청구와 비용 관리는 다릅니다. 청구는 상품이나 서비스에 대한 비용을 고객에게 청구하고 상업적 관계를 관리하는 프로세스입니다.

Cost Management는 고급 분석을 통해 조직의 비용 및 사용 패턴을 보여 줍니다. Cost Management의 보고서에는 Azure 서비스 및 타사 Marketplace 제품에서 사용된 사용량 기준 비용이 표시됩니다. 비용은 예약 및 Azure 하이브리드 혜택 할인의 협상된 가격 및 비율을 기준으로 결정됩니다. 전체적으로 이 보고서는 사용량 및 Azure Marketplace 요금에 대한 내부 및 외부 비용을 표시합니다. 예약 구매, 지원 및 세금과 같은 다른 요금은 보고서에 아직 표시되지 않습니다. 이러한 보고서는 지출 및 리소스 사용을 이해하는 데 도움이 되고 지출 문제를 찾는 데도 도움이 될 수 있습니다. 예측 분석도 사용할 수 있습니다. Cost Management는 Azure 관리 그룹, 예산 및 권장 사항을 사용하여 지출이 구성되는 방식과 비용을 줄일 수 있는 방법을 명확히 보여 줍니다.

내보내기 자동화를 위해 Azure Portal 또는 다양한 API를 사용하여 비용 데이터를 외부 시스템 및 프로세스와 통합할 수 있습니다. 자동화된 청구 데이터 내보내기 및 예약된 보고서도 사용할 수 있습니다.

Azure Cost Management가 Azure에서 비용을 절약하는 방법에 대한 간략한 개요를 보려면 [Azure Cost Management 개요 비디오](https://www.youtube.com/watch?v=el4yN5cHsJ0)를 시청하세요. 다른 비디오를 시청하려면 [Cost Management YouTube 채널](https://www.youtube.com/c/AzureCostManagement)을 방문하세요.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>비용 계획 및 제어

Cost Management를 통해 비용을 계획하고 제어하는 방법에는 비용 분석, 예산, 추천 및 Cost Management 데이터 내보내기가 포함됩니다.

비용 분석을 사용하여 조직 비용을 탐색하고 분석합니다. 조직의 집계된 비용을 확인하여 어느 부분에서 비용이 누적되었는지를 이해하고 지출 패턴을 식별할 수 있습니다. 또한 시간에 따라 누적된 비용을 확인하여 예산을 기준으로 월 단위, 분기 단위 또는 연 단위 비용 추세를 평가할 수 있습니다.

예산은 조직에서 재무 책임을 계획하고 충족하는 데 도움이 됩니다. 또한 비용 임계값 또는 한도를 초과하지 않도록 하는 데 도움이 됩니다. 또한 예산은 다른 사람에게 지출에 대한 정보를 제공하여 비용을 사전에 관리하는 데 도움을 줄 수 있습니다. 또한 시간이 지남에 따라 지출이 진행되는 방식을 확인할 수 있습니다.

권장 사항은 유휴 및 미달 사용 리소스를 식별하여 최적화를 수행하고 효율성을 개선하는 방법을 보여 줍니다. 또는 비용이 덜 드는 리소스 옵션을 표시할 수 있습니다. 권장 사항에 따라 작업을 수행하면서 리소스 사용 방식을 변경하여 금액을 절약할 수 있습니다. 작업을 수행하려면 먼저 비용 최적화 권장 사항을 검토하여 잠재적인 사용 비효율성을 확인합니다. 다음으로, 권장 사항에 따라 작업하여 Azure 리소스 사용을 좀 더 비용 효율적인 옵션으로 수정합니다. 그런 다음, 해당 작업을 확인하여 수행하는 변경이 성공적인지 검토합니다.

외부 시스템을 사용하여 비용 관리 데이터를 액세스 및 검토하는 경우 Azure에서 데이터를 쉽게 내보낼 수 있습니다. 또한 일별로 예약된 내보내기를 CSV 형식으로 설정하고 데이터 파일을 Azure Storage에 저장할 수 있습니다. 그런 다음, 외부 시스템에서 데이터에 액세스할 수 있습니다.

### <a name="cloudyn-deprecation"></a>Cloudyn 사용 중단

[Cloudyn](./cloudyn/overview.md)은 2020년 말에 더 이상 사용되지 않는 Cost Management와 관련된 Azure 서비스입니다. 기존 Cloudyn 기능은 가능한 한 Azure Portal에 직접 통합되고 있습니다. 현재 온보딩되는 신규 고객은 없지만, 완전히 사용되지 않을 때까지는 제품에 대한 지원이 유지됩니다.
 
비즈니스 요구 사항에 따라 언제 Azure Cost Management 또는 Cloudyn을 사용해야 하는지에 대해 자세히 알아 보려면 [Azure Cost Management 및 Cloudyn 비디오](https://www.youtube.com/watch?v=15DzKPMBRxM)를 시청하세요. 다른 비디오를 시청하려면 [Cost Management YouTube 채널](https://www.youtube.com/c/AzureCostManagement)을 방문하세요.
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

### <a name="additional-azure-tools"></a>추가 Azure 도구

Azure에는 Azure Cost Management + 청구 기능 세트에 포함되지 않는 다른 도구가 있습니다. 그러나 비용 관리 프로세스에서 중요한 역할을 합니다. 이러한 도구에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) - 이 도구를 사용하여 선불 클라우드 비용을 예측합니다.
- [Azure Migrate](../migrate/migrate-overview.md) - 현재 데이터 센터 워크로드를 평가하여 Azure 대체 솔루션에서 필요한 항목을 이해합니다.
- [Azure Advisor](../advisor/advisor-overview.md) - 사용하지 않은 VM을 식별하고 Azure 예약 인스턴스 구매에 대한 권장 사항을 받습니다.
- [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) - Azure의 VM에 대해 현재 온-프레미스 Windows Server 또는 SQL Server 라이선스를 사용하여 비용을 절감합니다.

## <a name="next-steps"></a>다음 단계

이제 Cost Management + 청구에 대해 숙지했으므로, 다음 단계로 이 서비스를 사용해 보겠습니다.

- Azure Cost Management를 사용하여 [비용 분석](./costs/quick-acm-cost-analysis.md)을 시작하세요.
- [Azure Cost Management 모범 사례](./costs/cost-mgt-best-practices.md)에 대해 자세히 알아볼 수도 있습니다.
