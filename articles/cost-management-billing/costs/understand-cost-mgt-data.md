---
title: Azure Cost Management 데이터 이해 | Microsoft Docs
description: 이 문서는 Azure Cost Management에 포함된 데이터와 이 데이터의 처리, 수집, 표시 및 마감 빈도를 파악하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 156684676758d777231d3b159ba7bc4749b8582a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901767"
---
# <a name="understand-cost-management-data"></a>Cost Management 데이터 이해

이 문서는 Azure Cost Management에 포함된 Azure 비용 및 사용량 데이터를 파악하는 데 도움이 됩니다. 데이터의 처리, 수집, 표시 및 마감 빈도를 설명합니다. Azure 사용 요금은 매월 청구됩니다. 청구 기간은 월별 기간이지만 주기 시작 및 종료 날짜는 구독 유형에 따라 달라집니다. Cost Management가 사용량 데이터를 받는 빈도는 다양한 요인에 따라 달라집니다. 이러한 요인에는 데이터 처리에 걸리는 시간, Azure 서비스에서 사용량을 청구 시스템으로 내보내는 빈도 등이 있습니다.

Cost Management에는 EA(기업계약) 계정에 대한 예약 및 타사 제품을 비롯한 모든 사용량 및 구매가 포함됩니다. Microsoft 고객 계약 계정 및 종량제 요금으로 개별 구독은 Azure 및 Marketplace 서비스의 사용량만 포함합니다. 지원 및 기타 비용은 포함되지 않습니다. 비용은 청구서가 생성될 때까지 예상되며 크레딧에는 영향을 주지 않습니다.

## <a name="supported-microsoft-azure-offers"></a>지원되는 Microsoft Azure 제품

Azure Cost Management에서 현재 지원되는 [Microsoft Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)음 다음과 같습니다. Azure 제품이란 사용자가 소유한 Azure 구독의 유형입니다. 데이터는 날짜에서 **사용 가능한 데이터**에서 시작하는 Cost Management에서 사용할 수 있습니다. 구독이 제품을 변경하는 경우 제품 변경 날짜 이전의 비용을 사용할 수 없습니다.

| **범주**  | **제품 이름** | **할당량 ID** | **제품 번호** | **사용 가능한 데이터** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014년 5월<sup>1</sup> |
| **EA(기업 계약)** | Enterprise 개발/테스트                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014년 5월<sup>1</sup> |
| **EA(기업 계약)** | [Microsoft Azure 엔터프라이즈](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014년 5월<sup>1</sup> |
| **Microsoft 고객 계약** | [Microsoft Azure 플랜](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | 해당 없음 | 2019년 3월<sup>3</sup> |
| **Microsoft 고객 계약** | [개발/테스트용 Microsoft Azure 플랜](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | 해당 없음 | 2019년 3월<sup>3</sup> |
| **파트너에서 지원되는 Microsoft 고객 계약** | Microsoft Azure 계획 | CSP_2015-05-01, CSP_MG_2017-12-01 및 CSPDEVTEST_2018-05-01<br><br>할당량 ID는 Microsoft 고객 계약 및 레거시 CSP 구독에 다시 사용됩니다. 현재 Microsoft 고객 계약 구독만 지원됩니다. | 해당 없음 | 2019년 10월 |
| **MSDN(Microsoft Developer Network)** | [MSDN 플랫폼](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018년 10월 2일<sup>2</sup> |
| **종량제** | [종량제](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018년 10월 2일<sup>2</sup> |
| **종량제** | [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018년 10월 2일<sup>2</sup> |
| **종량제** | [Microsoft 파트너 네트워크](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018년 10월 2일<sup>2</sup> |
| **종량제** | [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018년 10월 2일<sup>2</sup> |
| **종량제** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018년 10월 2일<sup>2</sup> |
| **종량제** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2018년 10월 2일<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018년 10월 2일<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018년 10월 2일<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018년 10월 2일<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018년 10월 2일<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018년 10월 2일<sup>2</sup> |

_<sup>**1**</sup> 2014년 5월 이전의 데이터는 [Azure Enterprise Portal](https://ea.azure.com)을 방문하세요._

_<sup>**2**</sup> 2018년 10월 2일 이전의 데이터는 [Azure 계정 센터](https://account.azure.com/subscriptions)를 방문하세요._

_<sup>**3**</sup> Microsoft 고객 계약은 2019년 3월에 시작했으며 이 시점 이전의 기록 데이터가 없습니다._

_<sup>**4**</sup> 크레딧 기반 및 사전 지불 구독에 대한 기록 데이터는 청구서와 일치하지 않을 수 있습니다. 아래 [기록 데이터가 청구서와 일치하지 않을 수 있음](#historical-data-might-not-match-invoice)을 참조하세요._

지원되지 않는 제품은 다음과 같습니다.

| Category  | **제품 이름** | **할당량 ID** | **제품 번호** |
| --- | --- | --- | --- |
| **Azure 독일** | [Azure 독일 종량제](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government 종량제 | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **CSP(클라우드 솔루션 공급자)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP(클라우드 솔루션 공급자)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP(클라우드 솔루션 공급자)** | Microsoft 클라우드 독일용 CSP의 Azure 독일   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **종량제**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **종량제** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **종량제**                 | [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **지원 플랜** | 표준 지원                    | Default_2014-09-01 | MS-AZR-0041P |
| **지원 플랜** | Professional Direct 지원         | Default_2014-09-01 | MS-AZR-0042P |
| **지원 플랜** | 개발자 지원                   | Default_2014-09-01 | MS-AZR-0043P |
| **지원 플랜** | 독일 지원 플랜                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **지원 플랜** | Azure Government 표준 지원   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **지원 플랜** | Azure Government Pro-Direct 지원 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **지원 플랜** | Azure Government 개발자 지원  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>제품 유형 결정
구독 데이터가 표시되지 않고 구독이 지원되는 제품에 해당하는지 확인하려는 경우 구독이 지원되는지 확인할 수 있습니다. Azure 구독이 지원되는지 확인하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다. 그런 다음, 왼쪽 메뉴 창에서 **모든 서비스**를 선택합니다. 서비스 목록에서 **구독**을 선택합니다. 구독 목록 메뉴에서 확인할 구독을 선택합니다. 구독이 개요 탭에 표시되며, **제품** 및 **제품 ID**를 확인할 수 있습니다. 다음 이미지에 예가 나와 있습니다.

![제품 및 제품 ID가 표시된 구독 개요 탭의 예](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Cost Management에 포함된 비용

다음 표에는 Cost Management에 포함된 데이터와 포함되지 않은 데이터가 나와 있습니다. 모든 비용은 청구서가 생성될 때까지 예상됩니다. 표시된 비용은 무료 및 선불 크레딧을 포함하지 않습니다.

**비용 및 사용량 데이터**

| **포함됨** | **포함되지 않음** |
| --- | --- |
| Azure 서비스 사용량<sup>5</sup>        | 지원 요금 - 자세한 내용은 [청구서 용어 설명](../understand/understand-invoice.md)을 참조하세요. |
| Marketplace 제품 사용량<sup>6</sup> | 세금 - 자세한 내용은 [청구서 용어 설명](../understand/understand-invoice.md)을 참조하세요. |
| Marketplace 구매<sup>6</sup>      | 크레딧 - 자세한 내용은 [청구서 용어 설명](../understand/understand-invoice.md)을 참조하세요. |
| 예약 구매<sup>7</sup>      |  |
| 예약 구매에 대한 상환<sup>7</sup>      |  |

_<sup>**5**</sup> Azure 서비스 사용량은 예약 및 협상 가격을 기준으로 합니다._

_<sup>**6**</sup> Marketplace 구매는 현재 종량제, MSDN 및 Visual Studio 제품에 사용할 수 없습니다._

_<sup>**7**</sup> 예약 구매는 현재 EA(기업계약) 계정에만 사용할 수 있습니다._

**메타데이터**

| **포함됨** | **포함되지 않음** |
| --- | --- |
| 리소스 태그<sup>8</sup> | 리소스 그룹 태그 |

_<sup>**8**</sup> 리소스 태그는 각 서비스에서 사용량을 내보낼 때 적용되며, 과거 사용량에 소급 적용되지 않습니다._

**평가판을 종량제로 업그레이드**

평가판 제품(044P)을 PAYG 제품(003P)으로 변환하는 고객은 평가판 기간 동안 사용량을 볼 수 있습니다. 그러나 변환 후에는 무료 트레일 사용량에 대한 가시성을 잃게 됩니다. 변환 후에는 PAYG 사용량과 비용도 Cost Management에 표시됩니다.

## <a name="rated-usage-data-refresh-schedule"></a>평가된 사용량 데이터 새로 고침 일정

비용 및 사용량 데이터는 Azure Portal의 비용 관리 + 청구와 [지원 API](../index.yml)에서 확인할 수 있습니다. 비용을 검토할 때 다음 사항에 유의하세요.

- 현재 청구 기간의 예상 요금은 하루에 6번 업데이트됩니다.
- 현재 청구 기간의 예상 요금은 사용량이 증가함에 따라 변경될 수 있습니다.
- 각 업데이트는 누적되며, 이전 업데이트의 모든 라인 항목 및 정보를 포함합니다.
- Azure는 청구 기간이 종료된 후 최대 72시간(3일) 이내에 현재 청구 기간을 ‘마감’합니다. 

다음 예제에서는 청구 기간이 종료될 수 있는 방식을 보여 줍니다.

EA(기업계약) 구독 - 청구 월이 3월 31일에 종료되는 경우 예상 요금이 최대 72시간 후에 업데이트됩니다. 이 예제에서는 4월 4일 자정(UTC)까지입니다.

종량제 구독 - 청구 월이 5월 15일에 종료되는 경우 예상 요금이 최대 72시간 후에 업데이트될 수 있습니다. 이 예제에서는 5월 19일 자정(UTC)까지입니다.

### <a name="rerated-data"></a>데이터 재평가

[Cost Management API](../index.yml), Power BI 또는 Azure Portal을 사용하여 데이터를 검색하든 상관없이, 청구서가 마감되기 전에는 현재 청구 기간의 요금이 재평가되고 그에 따라 변경될 수 있습니다.

## <a name="usage-data-update-frequency-varies"></a>사용량 데이터 업데이트 빈도 변동

Cost Management에서 발생한 사용량 데이터의 사용 가능 여부는 다음과 같은 몇 가지 요인에 따라 달라집니다.

- Azure 서비스(예: 스토리지, 컴퓨팅, CDN 및 SQL)에서 사용량을 내보내는 빈도
- 평가 엔진 및 비용 관리 파이프라인을 통해 사용량 데이터를 처리하는 데 걸리는 시간

일부 서비스는 다른 서비스보다 더 빈번하게 사용량을 내보냅니다. 따라서 일부 서비스에 대한 데이터는 데이터를 내보내는 빈도가 더 낮은 다른 서비스보다 먼저 Cost Management에 표시될 수 있습니다. 일반적으로 서비스 사용량이 Cost Management에 표시되기까지 8-24시간이 걸립니다. 업데이트는 누적되므로 사용량이 증가함에 따라 진행 중인 월의 데이터가 새로 고쳐집니다.

## <a name="historical-data-might-not-match-invoice"></a>기록 데이터가 청구서와 일치하지 않을 수 있음

크레딧 기반 및 사전 지불 제품에 대한 기록 데이터는 청구서와 일치하지 않을 수 있습니다. 일부 Azure 종량제, MSDN 및 Visual Studio 제품에는 청구서에 적용되는 Azure 크레딧 및 고급 지불이 있을 수 있습니다. 그러나 Cost Management에 표시되는 기록 데이터는 예상 사용량 요금에 따라 결정됩니다. Cost Management 기록 데이터는 지불액과 크레딧을 포함하지 않습니다. 따라서 다음 제품에 대해 표시된 기록 데이터가 청구서와 정확히 일치하지 않을 수 있습니다.

- Azure for Students(MS-AZR-0170P)
- Azure in Open(MS-AZR-0111P)
- Azure Pass(MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- 평가판(MS-AZR-0044P)
- MSDN(MS-AZR-0062P)
- Visual Studio(MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>참고 항목

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](../../cost-management/quick-acm-cost-analysis.md)을 참조하세요.
