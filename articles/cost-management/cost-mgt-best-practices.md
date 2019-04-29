---
title: Azure Cost Management로 클라우드 투자 최적화 | Microsoft Docs
description: 이 문서는 클라우드 투자의 가치를 극대화하고, 비용을 줄이고, 비용이 어디에 지출되는지 평가하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 493b71e503b6023eeea4a71e34d5405f84821e28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736884"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Azure Cost Management로 클라우드 투자를 최적화하는 방법

Azure Cost Management에는 지출을 계획, 분석 및 절약하여 클라우드 투자를 극대화할 수 있는 도구가 제공됩니다. 이 문서에서는 비용 관리의 체계적인 접근 방식을 제공하고 조직의 비용 문제를 해결할 때 사용할 수 있는 도구를 설명합니다. Azure를 통해 클라우드 솔루션을 쉽게 구축하고 배포할 수 있습니다. 하지만 조직의 비용을 최소화하도록 이러한 솔루션을 최적화하는 것이 중요합니다. 이 문서에 설명되어 있는 원칙을 따르고 도구를 사용하면 성공을 위한 준비를 완료할 수 있습니다.

## <a name="methodology"></a>방법

비용 관리는 조직의 문제이며 클라우드 리소스에 비용을 지출하기 전에 시작해야 하는 지속적인 업무입니다. 비용 관리를 성공적으로 구현하고 비용을 최적화하려면 조직에서 다음을 수행해야 합니다.

- 성공을 위한 적절한 도구 준비
- 비용에 대한 책임감
- 지출을 최적화하기 위한 적절한 조치 수행

아래에 요약되어 있는 세 가지 핵심 그룹은 조직 내에서 비용을 성공적으로 관리할 수 있도록 정비되어야 합니다.

- **재무** - 클라우드 지출 예측을 기반으로 조직 전체의 예산 요청에 대한 승인을 책임지는 담당자입니다. 해당하는 청구 금액을 지불하고 다양한 팀에 비용을 할당하여 책임감을 유발합니다.
- **관리자** - 최상의 지출 결과를 찾기 위해 클라우드 지출을 이해해야 하는 조직의 비즈니스 의사 결정자입니다.
- **앱 팀** - 매일 클라우드 리소스를 관리하고 조직의 요구를 충족시키는 서비스를 개발하는 엔지니어입니다. 이 팀은 정해진 예산에서 최고의 가치를 제공할 수 있는 유연성이 필요합니다.

### <a name="key-principles"></a>핵심 원칙

아래에 요약된 원칙을 사용하여 클라우드 비용 관리의 성공을 위해 포지셔닝하십시오.

#### <a name="planning"></a>계획

포괄적인 사전 계획을 세우면 특정 비즈니스 요구 사항에 맞게 클라우드 사용량을 조정할 수 있습니다. 확인할 사항:

- 해결해야 하는 비즈니스 문제가 무엇입니까?
- 리소스에서 어떤 사용 패턴을 기대합니까?

이러한 질문에 대한 답변은 가장 적합한 제품을 선택하는 데 도움이 됩니다. 답변에 따라 사용할 인프라와 Azure 효율성을 극대화하는 방법이 결정됩니다.

#### <a name="visibility"></a>표시 유형

구조가 잘 갖추어져 있는 경우, Cost Management를 사용하면 Azure 비용이나 기타 지출 비용에 대한 정보를 알리는 데 도움이 됩니다. Azure에는 비용이 *어디에* 지출되는 지에 대한 인사이트를 제공하도록 설계된 서비스가 있습니다. 이러한 도구를 활용해보십시오. 이러한 도구를 사용하면 사용이 저조한 리소스를 찾아서 낭비를 없애고 비용을 절약할 수 있는 기회를 최대화하는 데 도움이 됩니다.

#### <a name="accountability"></a>책임감

담당자들이 팀의 지출에 대해 설명할 수 있도록 조직 내에서 비용에 대한 책임을 분명히 합니다. Azure에 대한 조직의 지출을 충분히 이해하려면 비용 기여에 대한 인사이트를 극대화하도록 리소스를 체계화해야 합니다. 바람직한 조직은 비용을 관리하고 절약하며 효율적인 지출에 대한 책임감을 가질 수 있도록 도와줍니다.

#### <a name="optimization"></a>최적화

지출을 줄이기 위해 행동합니다. 계획을 수립하고 비용 가시성을 높여서 얻은 결과를 기반으로 지출을 최대한 활용합니다. 인프라 배포 변경과 함께 구매 및 라이선스 최적화를 고려할 수 있습니다. 자세한 내용은 이 문서의 뒷부분에 설명되어 있습니다.

#### <a name="iteration"></a>반복

조직의 모든 사용자가 비용 관리 수명 주기에 관여해야 합니다. 비용을 최적화하기 위해 지속적으로 참여해야 합니다. 반복적인 프로세스를 엄격하게 준수하고 조직 내에서 책임감 있는 클라우드 관리를 위한 핵심 요소로 활용해야 합니다.

![표시 유형, 책임감 및 최적화를 보여주는 핵심 원칙 다이어그램](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>비용을 고려한 계획

클라우드 리소스를 배포하기 전에 다음 항목을 평가합니다.

- 요구에 가장 적합한 Azure 제품
- 사용하려는 리소스
- 예상되는 비용

Azure는 평가 프로세스를 지원하는 도구를 제공합니다. 도구를 사용하면 워크로드를 활성화하는 데 필요한 투자를 파악할 수 있습니다. 그런 다음 상황에 맞는 최상의 구성을 선택할 수 있습니다.

### <a name="azure-onboarding-options"></a>Azure 온보딩 옵션

Cost Management 내에서 경험 극대화를 위한 첫 번째 단계 가장 적합한 Azure 제품이 무엇인지 조사하고 결정하는 것입니다. 앞으로 Azure를 어떻게 사용할 계획인지 생각해보십시오. 또한 청구 모델을 어떻게 구성할지도 고려해보십시오. 결정을 내릴 때는 다음 질문을 고려하는 것이 좋습니다.

- Azure를 얼마나 오래 사용할 계획입니까? 테스트 중입니까? 아니면 장기적인 인프라를 구축할 계획입니까?
- Azure에 어떻게 지불하시겠습니까? 할인된 가격을 선불하시겠습니까? 아니면 월말에 청구서를 받으시겠습니까?

다양한 옵션에 대해 알아보려면 [Azure 구입 방법](https://azure.microsoft.com/pricing/purchase-options/)을 참조하세요. 가장 일반적인 결제 모델 중 몇 가지가 아래에 나열되어 있습니다.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Free](https://azure.microsoft.com/free/)

- 12개월 동안 평가판 서비스
- $200 크레딧으로 30일 간 서비스 체험하기
- 25개 이상의 서비스 항상 무료

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[종량제](https://azure.microsoft.com/offers/ms-azr-0003p)

- 최소 요금이나 약정 없음
- 경쟁력 있는 가격
- 사용한 만큼만 요금 지불
- 언제든지 취소 가능

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[기업 계약](https://azure.microsoft.com/pricing/enterprise-agreement/)

- 선불 요금 약정 옵션
- 축소된 Azure 가격에 액세스

## <a name="estimate-the-cost-of-your-solution"></a>솔루션 비용 예측

인프라를 배포하기 전에, 솔루션 비용을 평가합니다. 평가를 수행하면 워크로드에 대한 조직의 예산을 미리 작성하는 데 도움이 됩니다. 그런 다음, 시간 경과에 따른 예산을 사용하여 초기 예측의 유효성을 벤치마킹할 수 있습니다. 또한 배포한 솔루션의 실제 비용과 비교할 수도 있습니다.

### <a name="azure-pricing-calculator"></a>Azure 요금 계산기

Azure 가격 계산기를 사용하면 Azure 서비스의 다양한 조합을 시도하여 비용 견적을 확인할 수 있습니다. Azure에서 다양한 방법을 사용하여 솔루션을 구현할 수 있으며, 각 솔루션은 전체 지출에 영향을 미칠 수 있습니다. 클라우드 배포에 필요한 모든 인프라 요구 사항을 미리 고려하면 도구를 가장 효과적으로 사용하는 데 도움이 됩니다. Azure에 대한 예상 지출 견적을 구할 수 있습니다.

자세한 내용은 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 참조하세요.

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate는 온-프레미스 데이터 센터에서 조직의 현재 워크로드를 평가하는 서비스입니다. Azure 대체 솔루션을 통해 무엇이 필요할지를 파악할 수 있습니다. 먼저 마이그레이션은 온-프레미스 머신을 분석하여 마이그레이션이 가능한지 여부를 확인합니다. 그런 다음 Azure에서 성능 최대화를 위한 VM 크기 조정을 권장합니다. 마지막으로, Azure 기반 솔루션에 대한 비용 견적을 생성합니다.

자세한 내용은 [Azure Migrate](../migrate/migrate-overview.md)를 참조하세요.

## <a name="analyze-and-manage-your-costs"></a>비용 분석 및 관리

조직의 비용이 시간이 경과하면서 어떻게 변하는지 지속적으로 알려줍니다. 지출을 제대로 이해하고 관리하려면 다음 기법을 사용하십시오.

### <a name="organize-and-tag-your-resources"></a>리소스 체계화 및 태그 지정

비용을 고려하여 리소스를 체계화합니다. 구독 및 리소스 그룹을 만드는 경우, 관련 비용을 담당하는 팀을 고려합니다. 조직을 고려하여 보고서를 준비해야 합니다. 구독 및 리소스 그룹은 조직 전반의 지출을 체계화하고 분류하는 유용한 버킷을 제공합니다. 태그는 비용에 특성을 부여하는 유용한 방법입니다. 태그를 필터로 사용할 수 있습니다. 데이터를 분석하고 비용을 조사할 때 태그를 사용하여 그룹화할 수 있습니다. 기업 계약 고객은 부서를 만들어서 그 아래에 구독을 배치할 수 있습니다. Azure의 비용 기반 조직은 관련 인력이 팀의 지출을 줄이는데 책임감을 갖도록 도와줍니다.

### <a name="use-cost-analysis"></a>비용 분석 사용

비용 분석을 통해 표준 리소스 속성을 사용하여 비용을 세분화하면 조직의 전체 비용을 심층적으로 분석할 수 있습니다. 분석을 위한 지침으로 다음과 같은 일반적인 질문을 고려하십시오. 이러한 질문에 대한 답변을 정기적으로 준비하면 보다 많은 정보를 확보할 수 있고 비용을 많이 고려하여 결정을 내리는 데 도움이 됩니다.

- **당월 예상 비용** – 이번 달 비용이 지금까지 얼마나 발생했습니까? 예산 내에서 유지가 가능합니까?
- **변칙 조사** – 비용이 정상적인 사용량 범위를 유지하도록 일상적인 점검을 수행합니다. 추세란 무엇인가요? 이상치가 있습니까?
- **청구서 조정** - 최신 청구 비용이 전월보다 높습니까? 월별 지출 습관이 어떻게 변하고 있습니까?
- **내부 지불 거절** - 청구 금액을 알게 된 후, 해당 비용을 조직 내에서 어떻게 세분화할 수 있습니까?

자세한 내용은 [비용 분석](quick-acm-cost-analysis.md)을 참조하세요.

### <a name="export-billing-data-on-a-schedule"></a>일정에 따라 청구 데이터 내보내기

대시보드나 재무 시스템과 같은 외부 시스템으로 청구 데이터를 가져올 필요가 있습니까? Azure Storage에 자동화된 내보내기를 설정하고 수동으로 매달 파일을 다운로드하지 않도록 방지 합니다. 그런 다음, 다른 시스템을 통해 자동 통합을 쉽게 설정하여 청구 데이터를 동기화 상태로 유지할 수 있습니다.

청구 데이터 내보내기에 대한 자세한 내용은 [내보낸 데이터를 만들고 관리](tutorial-export-acm-data.md)를 참조하세요.

### <a name="create-budgets"></a>예산 만들기

지출 패턴을 파악하고 분석한 다음에는 자신과 팀의 한도를 설정하는 것이 중요합니다. Azure 예산을 사용하면 다수의 임계값과 경고를 사용하여 비용 기반 또는 사용량 기반 예산을 설정할 수 있습니다. 정기적으로 예산을 생성하여 검토하여 예산 소비 상황을 확인하고 필요에 따라 예산을 변경할 수 있습니다. Azure 예산을 사용하면 지정한 예산 임계값에 도달하는 경우 자동 트리거를 구성할 수도 있습니다. 예를 들어 VM을 종료하도록 서비스를 구성할 수 있습니다. 또는 예산 트리거에 대응하여 다른 가격 책정 계층으로 인프라를 이동할 수 있습니다.

자세한 내용은 [Azure 예산](tutorial-acm-create-budgets.md)을 참조하세요.

예산 기반 자동화에 대한 자세한 내용은 [예산 기반 자동화](../billing/billing-cost-management-budget-scenario.md)를 참조하세요.

## <a name="act-to-optimize"></a>최적화를 위한 조치
다음과 같은 방법으로 지출을 최적화할 수 있습니다.

### <a name="cut-out-waste"></a>낭비 줄이기

Azure에 인프라를 배포한 후에는 해당 인프라의 사용 여부를 확인하는 것이 중요합니다. 절약을 즉시 시작할 수 있는 가장 쉬운 방법은 리소스를 검토하고 사용되지 않는 리소스가 있으면 제거하는 것입니다. 이때 리소스가 최대한 효율적으로 사용되고 있는지 확인해야 합니다.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor는 주로 CPU 또는 네트워크 사용의 관점에서 사용률이 낮은 가상 머신을 식별하는 서비스입니다. 머신을 계속 실행하는 경우 예상되는 비용을 기반으로 머신을 종료할지 또는 크기를 조정할지 결정할 수 있습니다. Advisor에는 예약 인스턴스 구매에 대한 권장 사항도 제공됩니다. 권장 사항은 최근 30일 간의 가상 머신 사용량을 기반으로 합니다. 이에 대한 조치가 취해지면 지출을 줄이는 데 도움이 될 수 있습니다.

자세한 내용은 [Azure Advisor](../advisor/advisor-overview.md)를 참조하세요.

### <a name="size-your-vms-properly"></a>VM 크기를 적절하게 조정

VM 크기 조정은 전반적인 Azure 비용에 상당한 영향을 미칩니다. Azure에 필요한 VM 수는 현재 온-프레미스 데이터 센터에 배포되어 있는 VM 수와 다를 수 있습니다. 실행하려는 워크로드에 맞는 크기를 선택해야 합니다.

자세한 내용은 [Azure IaaS: 적절한 크기 조정 및 비용](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/)을 참조하세요.

### <a name="use-purchase-discounts"></a>구매 할인 사용

Azure는 조직에서 비용을 절약하기 위해 활용할 수 있는 많은 할인 혜택을 제공합니다.

#### <a name="azure-reservations"></a>Azure Reservations

Azure Reservations를 통해 1년 또는 3년에 해당하는 가상 머신 비용 또는 SQL Database 컴퓨팅 용량을 선불로 결제할 수 있습니다. 선불로 결제하면 사용하는 리소스에 대해 할인 혜택을 받을 수 있습니다. Azure Reservations는 1년 또는 3년 사전 약정을 통해 가상 머신 또는 SQL 데이터베이스 컴퓨팅 비용을 종량제 가격의 최대 72%까지 절약할 수 있습니다. Reservations에는 청구 할인이 제공되며, 가상 머신이나 SQL 데이터베이스의 런타임 상태에 영향을 주지 않습니다.

자세한 내용은 [Azure Reservations란?](../billing/billing-save-compute-costs-reservations.md)을 참조하세요.

#### <a name="use-azure-hybrid-benefit"></a>Azure 하이브리드 혜택 사용

온-프레미스 배포에 Windows Server 또는 SQL Server 라이선스가 이미 포함되어 있으면, Azure 하이브리드 혜택 프로그램을 사용하여 Azure에 저장할 수 있습니다. Windows Server 혜택을 활용하면 각 라이선스에 OS(최대 두 대의 가상 머신) 비용이 포함되므로 기본적인 계산 비용만 지불하면 됩니다. 기존 SQL Server 라이선스를 사용하면 vCore 기반 SQL Database 옵션을 최대 55%까지 절약할 수 있습니다. 옵션에는 Azure Virtual Machines의 SQL Server와 SQL Server Integration Services가 포함됩니다.

자세한 내용은 [Azure 하이브리드 혜택 절감액 계산기](https://azure.microsoft.com/pricing/hybrid-benefit/)를 참조하세요.

### <a name="other-resources"></a>기타 리소스

Azure에서는 Azure에 남는 용량을 활용하여 서비스 요금을 낮추는 서비스를 빌드할 수 있습니다. 자세한 내용은 [Batch에서 우선 순위가 낮은 VM 사용](../batch/batch-low-pri-vms.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- Cost Management를 처음 접하는 경우 [Azure Cost Management란?](overview-cost-mgt.md)에서 Cost Management가 Azure 지출을 모니터링하고 컨트롤하는 데 어떻게 도움이 되는지 알아보고 리소스 사용을 최적화할 수 있습니다.
