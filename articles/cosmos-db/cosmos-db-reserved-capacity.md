---
title: 예약 용량을 통해 Azure Cosmos DB 리소스 비용 최적화
description: Azure Cosmos DB 예약 용량을 구매하여 계산 비용을 절약하는 방법에 대해 알아봅니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 0e0d1cd98f27ef40681e37a55f7021bf102fda21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892100"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Azure Cosmos DB에서 예약된 용량을 활용해 비용 최적화

Azure Cosmos DB 예약 용량을 통해 1년 또는 3년 동안 Azure Cosmos DB 리소스 비용을 선불로 지불하면 경비를 절약할 수 있습니다. Azure Cosmos DB 예약 용량을 사용하여 Cosmos DB 리소스에 대 해 프로비전된 처리량에 할인을 받을 수 있습니다. 리소스의 예로 데이터베이스 및 컨테이너(테이블, 컬렉션 및 그래프)를 들 수 있습니다.

Azure Cosmos DB 예약 용량은 1년 또는 3년 선불 약정을 통해 Cosmos DB 비용을 정상 가격의 &mdash;최대 65%까지 크게 낮출 수 있습니다. 예약 용량은 청구 할인을 제공하며, Azure Cosmos DB 리소스의 런타임 상태에는 영향을 주지 않습니다.

Azure Cosmos DB 예약 용량은 리소스에 대해 프로비전된 처리량을 포함합니다. 저장소 및 네트워킹 요금은 포함하지 않습니다. 예약 용량을 구매하는 즉시 예약 특성과 일치하는 처리량 요금은 더이상 종량제 요금으로 부과되지 않습니다. 예약에 대한 자세한 내용은 [Azure 예약](../billing/billing-save-compute-costs-reservations.md) 문서를 참조하세요.

Azure Cosmos DB 예약 용량은 [Azure Portal](https://portal.azure.com)에서 구매할 수 있습니다. 예약 용량을 구입할 수 있는 조건은 다음과 같습니다.

* 하나 이상의 Enterprise 또는 종량제 구독의 소유자 역할이어야 합니다.  
* Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 사용하지 않도록 설정한 경우 사용자가 구독에 대한 EA 관리자여야 합니다.
* CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리 담당자 또는 영업 담당자만 Azure Cosmos DB 예약 용량을 구입할 수 있습니다.

## <a name="determine-the-required-throughput-before-purchase"></a>구매 전 필요한 처리량 결정

예약 크기는 기존 또는 곧 배포될 Azure Cosmos DB 리소스에서 사용할 총 처리량을 기반으로 해야 합니다. 필요한 처리량을 결정하는 방법은 다음과 같습니다.

* 모든 하위 지역의 Azure Cosmos DB 계정, 데이터베이스 및 컬렉션에 걸쳐 프로비전된 총 처리량에 대한 기록 데이터를 가져옵니다. 예를 들어 `https://account.azure.com`에서 일일 사용량 명세서를 다운로드하여 일일 평균 프로비전된 처리량을 평가할 수 있습니다.

* EA(기업계약) 고객인 경우 Azure Cosmos DB 처리량 세부 정보를 얻으려면 사용량 파일을 다운로드할 수 있습니다. 사용량 파일의 **추가 정보** 섹션에서 **서비스 유형** 값을 참조하세요.

* Azure Cosmos DB 계정에서 앞으로 1년 또는 3년 동안 실행될 것으로 예상되는 모든 워크로드에 대한 평균 처리량을 합산할 수 있습니다. 그런 다음, 해당 처리량을 예약에 사용할 수 있습니다.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 예약 용량 구매

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  

2. **모든 서비스** > **예약** > **추가**를 차례로 선택합니다.  

3. **제품 유형 선택** 창에서 **Azure Cosmos DB** > **선택**을 선택하여 새 예약을 구입합니다.  

4. 아래 표에서 설명한 대로 필요한 필드를 채웁니다.

   ![예약 용량 양식 채우기](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png)

   |필드  |설명  |
   |---------|---------|
   |이름   |    예약의 이름입니다. 이 필드는 자동으로 `CosmosDB_Reservation_<timeStamp>`로 채워집니다. 예약을 만드는 동안 다른 이름을 제공할 수 있습니다. 또는 예약이 만들어진 후 이름을 바꿀 수 있습니다.      |
   |구독  |   Azure Cosmos DB 예약 용량에 대한 요금을 지불하는 데 사용되는 구독입니다. 선택한 구독에 대한 지불 방법은 선불 비용을 청구하는 데 사용됩니다. 구독 유형은 다음 항목 중 하나여야 합니다. <br/><br/>  기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P): Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과분에 대한 요금으로 청구됩니다. <br/><br/> 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P): 종량제 구독에 대한 요금은 구독 시 선택한 신용 카드 또는 청구서 지불 방법으로 청구됩니다.    |
   |범위   |   얼마나 많은 구독이 예약과 연결된 청구 혜택을 사용할 수 있는지 제어하는 옵션입니다. 또한 예약이 특정 구독에 적용되는 방식을 제어합니다.   <br/><br/>  **단일 구독**을 선택하면 예약 할인이 선택한 구독의 Azure Cosmos DB 인스턴스에 적용됩니다. <br/><br/>  **공유**를 선택하면 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행되는 Azure Cosmos DB 인스턴스에 적용됩니다. 청구 컨텍스트는 Azure에 등록한 방법에 따라 결정됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.  <br/><br/> 예약 용량을 구입한 후 예약 범위를 변경할 수 있습니다.  |
   |예약 용량 유형   |  요청 단위로 프로비전된 처리량입니다. 단일 지역 모두 설정-에 대 한 프로 비전된 된 처리량에 대 한 예약을 구입할 수 있습니다도 여러 지역 쓰기를 기록 합니다.|
   |예약 용량 단위  |      예약하려는 처리량의 크기입니다. 하위 지역별로 모든 Cosmos DB 리소스(예: 데이터베이스 또는 컨테이너)에 필요한 처리량을 확인하여 이 값을 계산할 수 있습니다. 그런 다음, 이 값을 Cosmos DB 데이터베이스에 연결할 하위 지역 수로 곱합니다.  <br/><br/> 예를 들면 다음과 같습니다. 지역마다 100만 RU/초 용량이 필요한 5개의 하위 지역이 있는 경우 구매할 예약 용량으로 500만 RU/초를 선택합니다.    |
   |용어  |   1년 또는 3년입니다.   |

5. **비용** 섹션에서 예약에 대한 할인 및 가격을 검토합니다. 이 예약 가격은 모든 하위 지역에서 처리량이 프로비전되는 Azure Cosmos DB 리소스에 적용됩니다.  

6. **구매**를 선택합니다. 구매가 성공적으로 완료되면 다음과 같은 페이지가 표시됩니다.

   ![예약 용량 양식 채우기](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png)

예약이 구입되면 예약 조건과 일치하는 기존 Azure Cosmos DB 리소스에 즉시 적용됩니다. 기존 Azure Cosmos DB 리소스가 없으면 배포 시 예약 조건과 일치하는 새 Cosmos DB 인스턴스에 예약이 적용됩니다. 예약 기간은 두 경우 모두 구매하는 즉시 시작됩니다.

예약이 만료되는 경우 Azure Cosmos DB 인스턴스는 계속 실행되지만 일반 종량제 요금으로 청구됩니다.

## <a name="cancellation-and-exchanges"></a>취소 및 교환

바로 예약된 된 용량을 식별 하는 도움말을 참조 하세요 [Azure Cosmos DB에 예약 할인은 적용 하는 방법을 이해](../billing/billing-understand-cosmosdb-reservation-charges.md)합니다. 취소 또는 Azure Cosmos DB 예약을 교환, 참조 해야 하는 경우 [예약 교환 및 환불](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)합니다.

## <a name="next-steps"></a>다음 단계

예약 할인은 예약 범위 및 특성과 일치하는 Azure Cosmos DB 리소스에 자동으로 적용됩니다. 예약 범위는 Azure Portal, PowerShell, Azure CLI 또는 API를 통해 업데이트할 수 있습니다.

*  예약 용량 할인이 Azure Cosmos DB에 적용되는 방법을 알아보려면 [Azure 예약 할인 이해](../billing/billing-understand-cosmosdb-reservation-charges.md)를 참조하세요.

* Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

   * [Azure 예약이란?](../billing/billing-save-compute-costs-reservations.md)  
   * [Azure 예약 관리](../billing/billing-manage-reserved-vm-instance.md)  
   * [엔터프라이즈 등록에서 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [종량제 구독의 예약 사용량 이해](../billing/billing-understand-reserved-instance-usage.md)
   * [파트너 센터 CSP 프로그램에서 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
