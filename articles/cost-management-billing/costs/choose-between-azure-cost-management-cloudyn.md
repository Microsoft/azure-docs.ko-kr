---
title: Azure Cost Management와 Cloudyn 중에서 선택
description: 이 문서는 Azure Cost Management 또는 Cloudyn이 비용 관리 요구사항에 가장 적합한지 여부를 판단하는 데 도움이 됩니다.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684969"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Azure Cost Management와 Cloudyn 중에서 선택

Cloudyn은 2020년 말에 더 이상 사용되지 않습니다. 기존 Cloudyn 기능은 가능한 한 Azure Portal에 직접 통합되고 있습니다. CSP 고객을 제외하고 현재는 신규 고객을 등록할 수 없습니다. 완전히 사용되지 않을 때까지는 기존 제품에 대한 지원이 유지됩니다.

Microsoft는 Cloudyn을 인수했으며 비용 관리 기능을 Cloudyn 포털에서 Azure로 온전히 마이그레이션하고 있습니다. 새 기능을 사용하려면 Azure Portal에 로그인한 후 Azure 서비스 목록에서 [비용 관리 및 청구](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)로 이동하세요. Cloudyn에 비해, 기본 환경은 향상된 성능과 약 8시간의 낮은 데이터 대기 시간을 제공합니다.

기업계약, 종량제 및 MSDN 제품 범주에 대한 Azure Cost Management로의 주요 기능 마이그레이션은 완료되었습니다. CSP 구독은 Azure Cost Management로 마이그레이션이 진행 중입니다.

아직 마이그레이션되지 않은 제품 범주가 있는 경우에는 Cloudyn 포털을 계속 사용해야 합니다. 모든 사용자가 Azure Cost Management를 사용할 수 있습니다.

## <a name="recommended-services-by-offer"></a>제품별 권장 서비스

| Microsoft Azure 제품 | 권장되는 비용 관리 서비스 |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct(PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Microsoft 고객 계약 | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| 파트너에서 지원되는 Microsoft 고객 계약 | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>사용 가능한 Cost Management 기능

다음 기능 중 일부는 Cloudyn에서 사용 가능하지만, 현재 Azure Cost Management에서 모든 기능을 사용할 수 있습니다.

- API
- Azure 비용 최적화 권장 사항은 다음을 포함하지만 이에 제한되지 않습니다.
    - Azure 인스턴스의 적절한 크기 조정 및 종료 권장 사항
    - Azure Reservation 추천
- 예산
- 비용 분석
- Azure Storage 계정으로 데이터 내보내기
- 낮은 대기 시간
- Power BI 템플릿 앱
- 리소스 태그 지원
- AWS에 대한 클라우드 간 비용 분석 지원

## <a name="next-steps"></a>다음 단계
- [Azure Cost Management](../cost-management-billing-overview.md)에 대해 자세히 알아보세요.