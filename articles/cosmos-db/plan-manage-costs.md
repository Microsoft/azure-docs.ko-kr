---
title: Azure Cosmos DB에 대 한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용 하 여 Azure Cosmos DB에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152587"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 비용 계획 및 관리

이 문서에서는 Azure Cosmos DB에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 Azure Cosmos DB 용량 계산기를 사용 하 여 리소스를 추가 하기 전에 비용을 계획 하는 데 도움을 줍니다. 그런 다음 Azure 리소스를 추가할 때 예상 비용을 검토할 수 있습니다. Azure Cosmos DB 리소스 사용을 시작한 후에는 cost management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링할 수 있습니다. 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별할 수도 있습니다.

Azure Cosmos DB에 대 한 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 다른 Azure 서비스를 사용 하는 경우 타사 서비스를 포함 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대 한 요금이 청구 됩니다. 이 문서에서는 Azure Cosmos DB에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. Azure Cosmos DB에 대 한 비용 관리에 익숙해 졌으 면 구독에 사용 되는 모든 Azure 서비스에 대 한 비용을 관리 하는 비슷한 방법을 적용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md)을 참조하세요.

## <a name="review-estimated-costs-with-capacity-calculator"></a>용량 계산기를 사용 하 여 예상 비용 검토

Azure Cosmos 계정에 리소스를 만들기 전에 [Azure Cosmos DB 용량 계산기](https://cosmos.azure.com/capacitycalculator/) 를 사용 하 여 비용을 예측 합니다. 용량 계산기는 워크 로드에 필요한 처리량 및 비용의 추정치를 얻는 데 사용 됩니다. 적절 한 크기의 프로 비전 된 처리량을 사용 하 여 Azure Cosmos 데이터베이스 및 컨테이너를 구성 하거나 작업에 대 한 [요청 단위 (r u/초)](request-units.md)를 구성 하는 것은 비용과 성능을 최적화 하는 데 필요 합니다. API 형식, 지역 수, 항목 크기, 초당 읽기/쓰기 요청, 예상 비용을 얻기 위해 저장 된 총 데이터 등의 세부 정보를 입력 해야 합니다. 용량 계산기에 대해 자세히 알아보려면 [예상](estimate-ru-with-capacity-planner.md) 문서를 참조 하세요.

다음 스크린샷은 용량 계산기를 사용 하 여 처리량 및 비용 예측을 보여 줍니다.

![Azure Cosmos DB 용량 계산기의 예상 비용](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Azure Portal에서 예상 비용을 검토 합니다.

Azure Portal에서 Azure Cosmos DB 리소스를 만들 때 예상 비용을 볼 수 있습니다. 다음 단계를 사용 하 여 예상 비용을 검토 합니다.

1. Azure Portal에 로그인 하 여 Azure Cosmos 계정으로 이동 합니다.
1. **데이터 탐색기**로 이동 합니다.
1. 그래프 컨테이너와 같은 새 컨테이너를 만듭니다.
1. 400 r u/초와 같은 워크 로드에 필요한 처리량을 입력 합니다. 처리량 값을 입력 한 후에는 다음 스크린샷에 표시 된 가격 책정을 확인할 수 있습니다.

   ![예상 비용 Azure Portal](./media/plan-manage-costs/cost-estimate-portal.png)

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과 하 여 지출 하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용 됩니다. 신용 한도에 도달 하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용 하지 않도록 설정 됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대 한 자세한 내용은 [Azure 지출 한도](../billing/billing-spending-limit.md)를 참조 하세요.

## <a name="use-budgets-and-cost-alerts"></a>예산 및 비용 경고 사용

[예산을](../cost-management/tutorial-acm-create-budgets.md) 만들어 비용을 관리 하 고 관련자에 게 잘못 된 지출 및 과도 한 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 그러나 더 높은 수준에서 비용을 추적 하도록 설계 되었기 때문에 Azure Cosmos DB 비용과 같은 개별 Azure 서비스 비용을 관리 하는 기능이 제한 될 수 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Cosmos DB에서 리소스를 사용 하는 경우 비용이 발생 합니다. 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일) 또는 요청 단위 사용에 따라 달라 집니다. Azure Cosmos DB를 시작 하는 즉시 비용이 발생 하며 Azure Portal의 [비용 분석](../cost-management/quick-acm-cost-analysis.md) 창에서이를 볼 수 있습니다.

비용 분석을 사용 하면 서로 다른 시간 간격에 대 한 그래프 및 테이블의 Azure Cosmos DB 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재, 이전 달 및 연도가 있습니다. 예산 및 예상 비용에 대 한 비용을 볼 수도 있습니다. 시간이 지남에 따라 더 긴 보기로 전환 하면 지출 추세를 파악 하 고 낭비를 발생 시킬 수 있는 위치를 확인할 수 있습니다. 예산을 만든 경우 해당 위치를 쉽게 확인할 수도 있습니다. 비용 분석에서 Azure Cosmos DB 비용을 보려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Cost Management + 청구** 창을 열고 메뉴에서 **비용 관리** 를 선택한 다음 **비용 분석**을 선택 합니다. 그런 다음 **범위** 드롭다운에서 특정 구독에 대 한 범위를 변경할 수 있습니다.

1. 기본적으로 모든 서비스에 대 한 비용은 첫 번째 도넛형 차트에 표시 됩니다. "Azure Cosmos DB" 이라는 레이블이 지정 된 차트에서 영역을 선택 합니다.

1. Azure Cosmos DB와 같은 단일 서비스에 대 한 비용을 좁히려면 **필터 추가** 를 선택 하 고 **서비스 이름**을 선택 합니다. 그런 다음 목록에서 **Azure Cosmos DB** 을 선택 합니다. Azure Cosmos DB에 대 한 비용을 보여 주는 예제는 다음과 같습니다.
 
   ![비용 분석 창으로 비용 모니터링](./media/plan-manage-costs/cost-analysis-pane.png)

위의 예제에서는 2 월의 Azure Cosmos DB에 대 한 현재 비용을 볼 수 있습니다. 차트에는 위치 및 리소스 그룹별 Azure Cosmos DB 비용도 포함 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 가격 책정의 작동 방식에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure Cosmos DB의 가격 책정 모델](how-pricing-works.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](optimize-cost-queries.md)
* [Azure Cosmos DB에서 저장소 비용 최적화](optimize-cost-storage.md)