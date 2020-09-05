---
title: Azure 기업계약 청구서 검토
description: Azure 기업계약에 대한 사용량 및 청구서를 읽고 이해하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5340d6124ae4fdc669a30458b9e58cf4c86d6746
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684663"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Azure 기업계약 청구서 이해

기업계약을 체결한 Azure 고객은 조직의 크레딧을 초과하거나 크레딧이 적용되지 않는 서비스를 사용할 경우 청구서를 받게 됩니다.

조직의 크레딧에는 현금 약정 금액이 포함됩니다. 현금 약정 금액은 Azure 서비스를 사용하기 위해 조직이 선불로 지급한 금액입니다. Microsoft 계정 관리자나 재판매인에게 연락하여 기업계약에 현금 약정 금액을 추가할 수 있습니다.

이 자습서는 Microsoft 기업계약을 체결한 Azure 고객에게만 적용됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 송장 발부 요금 검토
> * 서비스 초과 요금 검토
> * Marketplace 청구서 검토

## <a name="prerequisites"></a>사전 요구 사항

청구서 요금을 검토하고 확인하려면 엔터프라이즈 관리자여야 합니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](../manage/understand-ea-roles.md)를 참조하세요. 조직의 엔터프라이즈 관리자가 누구인지 모르는 경우 [고객 지원팀에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

## <a name="review-invoiced-charges-for-most-customers"></a>대부분의 고객에 대한 송장 발부 요금 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르의 Azure 고객에게는 적용되지 않습니다.

청구 기간 중에 다음 이벤트 중 하나가 발생하면 Azure 청구서를 받게 됩니다.

- **서비스 초과**: 조직의 사용 요금이 크레딧 잔액을 초과합니다.
- **별도 청구 요금**: 조직에서 사용한 서비스가 크레딧에 적용되지 않습니다. 다음 서비스에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다.
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - OpenLogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise(월간)
    - Visual Studio Enterprise(연간)
    - Visual Studio Professional(월간)
    - Visual Studio Professional(연간)
- **Marketplace 요금**: Azure Marketplace의 구매 및 사용량이 조직의 크레딧에 적용되지 않습니다. 따라서 Marketplace 요금에 대해서는 크레딧 잔액과 관계없이 청구서를 받게 됩니다. Enterprise Portal에서 엔터프라이즈 관리자는 Marketplace 구매를 사용하거나 사용하지 않도록 설정할 수 있습니다.

청구서에는 Azure 사용량 요금과 관련 비용이 먼저 표시되고, 그 다음에 마켓플레이스 요금이 표시됩니다. 크레딧 잔액이 있는 경우 Azure 사용량에 적용되며, Azure 사용량과 마켓플레이스 사용량이 아무런 비용 없이 청구서에 표시됩니다.

Enterprise Portal의 **보고서** > **사용량 요약**에 표시된 총 결합 금액을 Azure 청구서와 비교합니다. **사용량 요약**의 금액에는 세금이 포함되어 있지 않습니다.

[Azure EA Portal](https://ea.azure.com)에 로그인합니다. 그런 다음, **보고서**를 선택합니다. 탭의 오른쪽 맨 위 모서리에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.  

![사용 요약의 M + C 옵션을 보여주는 스크린샷.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**총 사용량** 및 **Azure Marketplace**의 결합된 금액은 송장의 **총 확장 금액**과 일치해야 합니다. 요금에 대한 자세한 내용을 보려면 **사용량 다운로드**로 이동하세요.  

![사용량 다운로드 탭을 보여주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>기타 고객에 대한 송장 발부 요금 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르의 Azure 고객에게만 적용됩니다.

다음 이벤트 중 하나라도 발생하면 하나 이상의 Azure 청구서를 받게 됩니다.

- **서비스 초과**: 조직의 사용 요금이 크레딧 잔액을 초과합니다.
- **별도 청구 요금**: 조직에서 사용한 서비스가 크레딧에 적용되지 않습니다. 다음 서비스에 대해 송장이 청구됩니다.
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - OpenLogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise(월간)
    - Visual Studio Enterprise(연간)
    - Visual Studio Professional(월간)
    - Visual Studio Professional(연간)
- **Marketplace 요금**: Azure Marketplace의 구매 및 사용량ㅇ은 조직의 크레딧에 적용되지 않으며 요금이 별도로 청구됩니다. Enterprise Portal에서 엔터프라이즈 관리자는 Marketplace 구매를 사용하거나 사용하지 않도록 설정할 수 있습니다.

청구 기간 동안 서비스 초과분 요금과 별도 청구 요금이 부과되는 경우 청구서 1개를 받게 됩니다. 여기에는 두 가지 유형의 요금이 모두 포함됩니다. Marketplaces 요금은 항상 별도로 청구됩니다.

## <a name="review-service-overage-charges-for-other-customers"></a>기타 고객에 대한 서비스 초과 요금 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르에 있는 경우에만 적용됩니다.

Enterprise Portal의 **보고서** > **사용 요약**에 있는 총 사용량 금액을 서비스 초과분 청구서와 비교합니다. 서비스 초과분 청구서에는 조직의 크레딧을 초과한 사용 및/또는 크레딧이 적용되지 않는 서비스가 포함됩니다. **사용 요약**의 금액에는 세금이 포함되어 있지 않습니다.

[Azure EA Portal](https://ea.azure.com)에 로그인한 다음, **보고서**를 선택합니다. 탭의 오른쪽 맨 위 모서리에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.  

![사용 요약의 M + C 옵션을 보여주는 스크린샷.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**총 사용량** 금액이 서비스 초과분 청구서의 **총 확장 금액**과 일치해야 합니다. 요금에 대한 자세한 내용을 보려면 **사용량 다운로드** > **고급 보고서 다운로드**로 이동합니다. 이 보고서에는 세금, 예약료 또는 Marketplace 요금이 포함되어 있지 않습니다.  

![사용량 다운로드 탭의 고급 보고서 다운로드를 보여 주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

다음 표에는 청구서 및 Enterprise Portal의 **사용 요약**에 표시되는 용어 및 설명이 나와 있습니다.

|청구서 용어|사용 요약 용어|Description|
|---|---|---|
|총 확장 금액|총 사용량|차변이 적용되기 전, 특정 기간에 대한 총 세전 사용량 요금입니다.|
|약정 사용량|약정 사용량|해당 특정 기간에 적용된 크레딧입니다.|
|총 판매|총 초과분|크레딧 금액을 초과하는 총 사용량 요금입니다. 이 금액에는 세금이 포함되어 있지 않습니다.|
|세액|해당 없음|특정 기간의 총 판매 금액에 적용되는 세금입니다.|
|총 금액|해당 없음|크레딧이 적용되고 세금이 추가된 후 청구서의 지불액입니다.|

### <a name="review-marketplace-invoice"></a>Marketplace 청구서 검토

이 섹션은 오스트레일리아, 일본 또는 싱가포르에 있는 경우에만 적용됩니다.

Enterprise Portal의 **보고서** > **사용 요약**에 있는 Azure Marketplace 합계와 마켓플레이스 청구서를 비교합니다. 마켓플레이스 청구서는 Azure Marketplace 구매 및 사용에만 해당됩니다. **사용량 요약**의 양은 게시자에 의해 결정되는 세금을 이미 포함하고 있습니다.

[Enterprise Portal](https://ea.azure.com)에 로그인한 다음, **보고서**를 선택합니다. 탭의 오른쪽 맨 위 모서리에서 보기를 **M**에서 **C**로 전환하고 청구서의 기간과 똑같이 설정합니다.  

![사용 요약의 M + C 옵션을 보여 주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

**Azure Marketplace** 합계가 마켓플레이스 청구서의 **총 판매**와 일치해야 합니다. 사용량 기반 요금에 대한 자세한 내용을 보려면 **사용량 다운로드**로 이동합니다. **마켓플레이스 요금** 아래에서 **다운로드**를 선택합니다. 마켓플레이스 가격은 게시자가 결정한 세금을 포함합니다. 고객은 트랜잭션에 대한 세금을 징수하기 위해 게시자로부터 별도의 송장을 받지 않습니다.

![마켓플레이스 요금 아래의 다운로드 옵션을 보여 주는 스크린샷](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>가격표 정보 보기

엔터프라이즈 관리자는 Azure 서비스 등록과 관련된 가격표를 볼 수 있습니다.

현재 가격표를 보려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서**, **가격표**를 차례로 선택합니다.
2. 가격표를 보거나 **다운로드**를 선택합니다.

![가격표 정보를 보여주는 예제](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

이전 가격 목록을 다운로드하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서**를 선택한 다음, **사용량 다운로드**를 선택합니다.
2. 가격표를 다운로드합니다.

![이전 가격표를 다운로드하는 위치를 보여주는 예제](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

가격의 차이에 대한 몇 가지 이유는 다음과 같습니다.

- 이전 등록과 새 등록의 가격 책정이 변경되었을 수 있습니다. 가격 책정은 계약의 시작 날짜부터 종료 날짜까지 특정 등록에 대한 계약이므로 가격 변경이 발생할 수 있습니다.
- 새 등록으로 이전하는 경우 가격이 새 계약으로 변경됩니다. 가격 책정은 가격표로 정의되며, 새 등록에서 더 높을 수 있습니다.
- 등록 기간이 연장되는 경우에도 가격 책정이 변경됩니다. 가격이 종량제 요금으로 변경됩니다.

## <a name="request-detailed-usage-information"></a>자세한 사용 정보 요청

엔터프라이즈 관리자는 Azure Enterprise Portal에서 사용량 데이터, 사용된 약정 금액, 추가 사용량 관련 요금에 대한 요약 정보를 볼 수 있습니다. 요금 정보는 모든 계정 및 구독에서 요약 정보로 제공됩니다.

특정 계정의 자세한 사용량을 보려면 **보고서** > **사용량 다운로드**로 차례로 이동하여 사용량 세부 정보 보고서를 다운로드합니다.

> [!NOTE]
> 사용량 세부 정보 보고서에는 관련 세금이 포함되지 않습니다. 사용량이 발생한 시점부터 보고서에 반영되는 시점까지 최대 8시간의 대기 시간이 있을 수 있습니다.

간접 등록의 경우 파트너가 표시 함수를 사용하도록 설정해야만 비용 관련 정보를 볼 수 있습니다.

## <a name="reports"></a>보고서

엔터프라이즈 관리자는 Azure Enterprise Portal에서 사용량 데이터, 사용된 약정 금액, 추가 사용량 관련 요금에 대한 요약 정보를 볼 수 있습니다. 요금 정보는 모든 계정 및 구독에서 요약 정보로 제공됩니다.

### <a name="azure-enterprise-reports"></a>Azure 엔터프라이즈 보고서

- 사용 요약 및 그래프
- 서비스 사용 보고서
- 잔액 및 요금 보고서
- 사용량 세부 정보 보고서
- Azure Marketplace 요금 보고서
- 가격표
- 고급 보고서 다운로드
- CSV 보고서 서식 지정

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>사용 요약 보고서 및 그래프를 보려면 다음을 수행합니다.

1. Azure Enterprise portal로 이동합니다.
1. 왼쪽 창에서 **보고서**를 선택합니다.
1. **사용 요약** 탭을 선택합니다.
1. 왼쪽 위의 날짜 범위 메뉴에서 약정 기간을 선택합니다.
1. 그래프에서 기간 또는 월을 선택하여 추가 세부 정보를 봅니다.
1. 이 탭에서는 다음을 수행할 수 있습니다.
   - 사용량, 서비스 과잉 청구 요금, 별도 청구 요금 및 Azure Marketplace 요금에 대한 자세한 분석과 함께 전월 대비 사용량 그래프를 봅니다.
   - 그래프 아래의 부서, 계정 및 구독을 기준으로 필터링합니다.
   - **서비스별 요금** 분석과 **계층 구조별 요금** 분석 간을 전환합니다.
   - Azure 서비스, 별도 청구 요금 및 Azure Marketplace 요금에 대한 세부 정보를 봅니다.

## <a name="service-usage-report"></a>서비스 사용 보고서

엔터프라이즈 관리자는 서비스 사용 보고서 페이지에서 서비스 사용량 데이터의 요약 정보를 볼 수 있습니다. 사용량은 모든 계정 및 구독에서 요약 수준으로 제공됩니다. 자세한 사용량을 보려면 계정 또는 구독을 기준으로 보고서를 필터링할 수 있습니다.

> [!NOTE]
> 발생한 사용량 날짜와 해당 사용량이 이 보고서에 반영되는 날짜 사이에 최대 5일의 대기 시간이 있을 수 있습니다.

### <a name="to-view-the-report"></a>보고서를 보려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **보고서**를 선택합니다.
1. **사용 요약** 탭을 선택합니다.
1. 날짜 범위를 선택합니다.
1. 보고 싶은 계정 또는 구독을 선택합니다.
1. 필요에 따라 다음을 수행할 수 있습니다.
   - **서비스별 요금** 및 **계층 구조별 요금** 간의 보기를 변경하여 서로 다른 분석을 표시합니다.
   - 서비스 이름, 측정 단위, 사용한 단위, 유효 요율 및 확장 비용에 대한 세부 정보를 봅니다.

## <a name="download-csv-reports"></a>CSV 보고서 다운로드

엔터프라이즈 관리자는 월간 보고서 다운로드 페이지를 통해 여러 보고서를 CSV 파일로 다운로드할 수 있습니다. 다운로드할 수 있는 보고서는 다음과 같습니다.

- 잔액 및 요금 보고서
- 사용량 세부 정보 보고서
- Azure Marketplace 요금 보고서
- 가격표

### <a name="to-download-reports"></a>보고서를 다운로드하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서**를 선택합니다.
1. 위쪽 리본에서 **사용량 다운로드**를 선택합니다.
1. 적절한 월의 보고서 옆에 있는 **다운로드**를 선택합니다.

### <a name="csv-report-formatting-issues"></a>CSV 보고서 서식 지정 문제

Azure Enterprise Portal의 CSV 보고서를 유로 화폐 단위로 표시하는 고객에게는 쉼표 및 마침표와 관련된 서식 지정 문제가 발생할 수 있습니다.

예를 들어 다음 메시지가 표시될 수 있습니다.

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 시간 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

다음과 같은 결과가 표시됩니다.

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| 시간 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

이 서식 지정 문제는 Excel의 가져오기 기능의 기본 설정으로 인해 발생합니다. Excel에서는 모든 필드를 '일반' 텍스트로 가져오고, 수학적 표준에서 숫자가 구분되어 있다고 가정합니다. 다음은 그 예입니다.  "1,000.00"에서 구분되는 것으로 가정합니다.

유럽 통화에서 1000 단위 구분 기호로 마침표(.)를 사용하고 소수 자릿수 구분 기호로 쉼표(,)를 사용하는 경우 올바르게 표시되지 않습니다. 다음은 그 예입니다.  "1.000,00"입니다. 가져오기 결과는 지역별 언어 설정에 따라 달라질 수 있습니다.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>서식 지정 문제 없이 CSV 파일을 가져오려면 다음을 수행합니다.

1. Microsoft Excel에서 **파일** > **열기**로 차례로 이동합니다.
   텍스트 가져오기 마법사가 나타납니다.
1. **원본 데이터 형식** 아래에서 **구분 기호로 분리됨**을 선택합니다.  기본값은 **고정 폭**입니다.
1. **다음**을 선택합니다.
1. 구분 기호 아래에서 **쉼표**의 확인란을 선택합니다. 이 확인란이 선택되면 **탭**을 지웁니다.
1. **다음**을 선택합니다.
1. **ResourceRate** 및 **ExtendedCost** 열로 스크롤합니다.
1. **ResourceRate** 열을 선택합니다. 검은색으로 강조 표시됩니다.
1. **열 데이터 서식** 섹션 아래에서 **일반** 대신 **텍스트**를 선택합니다. 열 머리글이 **일반**에서 **텍스트**로 변경됩니다.
1. **확장 비용** 열에 대해 8단계와 9단계를 반복한 다음, **마침**을 선택합니다.

> [!TIP]
> Excel에서 CSV 파일을 자동으로 열도록 설정한 경우 Excel에서 **열기** 기능을 대신 사용해야 합니다. Excel에서 **파일** > **열기**로 차례로 이동하세요.

## <a name="balance-and-charge-report"></a>잔액 및 요금 보고서

잔액 및 요금 보고서는 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과분 요금에 대한 월간 요약 정보를 제공합니다.

Azure 서비스 약정 요약 테이블의 모든 행은 전월 대비 고정된 상태로 유지됩니다. 모든 구매 및 조정에 대한 세부 정보는 **새 구매 세부 정보** 및 **조정 세부 정보** 섹션에서 확인할 수 있습니다.

### <a name="download-the-balance-and-charge-report"></a>잔액 및 요금 보고서 다운로드

1. 엔터프라이즈 관리자 권한으로 Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 창에서 **보고서**를 선택합니다.
1. **보고서 다운로드** 탭을 선택합니다.
1. **잔액 및 요금** 열 아래에서 적절한 월을 선택하고, 보고서를 다운로드하도록 선택합니다.

## <a name="usage-detail-report"></a>사용량 세부 정보 보고서

사용량 세부 정보 보고서는 서비스 및 수량의 등록 사용량에 대한 월간 요약 정보를 제공합니다. 여기에는 측정기 이름, 측정기 종류 및 사용한 수량에 대한 정보가 포함됩니다.

### <a name="download-the-usage-detail-report"></a>사용량 세부 정보 보고서 다운로드

1. 엔터프라이즈 관리자 권한으로 Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **보고서**를 선택합니다.
1. **사용량 다운로드** 탭을 선택합니다.
1. **사용량 세부 정보** 열에서 해당 월을 선택하고, 보고서를 다운로드하도록 선택합니다.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure Enterprise Portal 보고서의 Azure Marketplace 요금

다음 두 가지 유형의 Azure Marketplace 요금이 있습니다.

- **사용량 기준:** 트랜잭션 단위로 측정되는 제품입니다.  예를 들어 가상 머신은 시간을 기준으로 요금이 청구되고, Bing API 검색은 검색 건수를 기준으로 요금이 청구됩니다.
- **월간 요금 기준:** 사용량 또는 액세스를 기준으로 매월 청구됩니다.

Azure Marketplace 요금에 대한 자세한 내용은 [Azure Marketplace FAQ](https://azure.microsoft.com/marketplace/faq/)를 참조하세요.

Azure Enterprise Portal에서 다른 요금을 보려면,

- **사용 요약 보고서**: 사용량 기준 및 월간 요금 기준 Azure Marketplace 요금을 **모두** 표시합니다.
- **Marketplace 요금 보고서**: 사용량 기준 Azure Marketplace 요금**만** 표시합니다.  일회성 요금은 표시되지 않습니다.

> [!NOTE]
> MPSA 등록에는 Azure Marketplace를 사용할 수 없습니다.

## <a name="advanced-report-download"></a>고급 보고서 다운로드

특정 날짜 범위 또는 계정에 대한 보고를 원하는 경우 고급 보고서 다운로드를 사용할 수 있습니다. 2016년 8월 30일부터 출력 파일의 형식은 더 큰 레코드 세트를 수용할 수 있는 CSV입니다.

1. Azure Enterprise Portal에서 **고급 보고서 다운로드**를 선택합니다.
1. **적절한 날짜 범위**를 선택합니다.
1. **올바른 계정**을 선택합니다.
1. **사용 데이터 요청**을 선택합니다.
1. 보고서 상태가 **다운로드**로 업데이트될 때까지 **새로 고침** 단추를 선택합니다.
1. 보고서를 다운로드합니다.

## <a name="reporting-for-non-enterprise-administrators"></a>비-엔터프라이즈 관리자를 위한 보고서

엔터프라이즈 관리자는 DA(부서 관리자)와 AO(계정 소유자)에게 등록에 대한 요금을 볼 수 있는 권한을 부여할 수 있습니다. 액세스 권한이 있는 계정 소유자는 자신의 계정 및 구독과 관련된 CSV 보고서를 다운로드할 수 있습니다. 또한 Azure Enterprise Portal의 보고 섹션에서 정보를 시각적으로 볼 수도 있습니다.

### <a name="to-enable-access"></a>액세스를 설정하려면 다음을 수행합니다.

 1. 엔터프라이즈 관리자 권한으로 Azure Enterprise Portal에 로그인합니다.
 1. 왼쪽 탐색 영역에서 **관리**를 선택합니다.
 1. **등록** 탭을 선택합니다.
 1. **등록 세부 정보** 섹션 아래에서 **DA 요금 보기** 또는 **AO 요금 보기** 옆에 있는 연필 아이콘을 선택합니다.
 1. **사용**을 선택합니다.
 1. **저장**을 선택합니다.

### <a name="to-view-reports"></a>보고서를 보려면 다음을 수행합니다.

1. 부서 관리자 또는 계정 소유자 권한으로 Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **보고서**를 선택합니다.
1. **사용 요약** 탭을 선택하여 계정 및 구독에 대한 정보를 시각적으로 봅니다.
1. **사용량 다운로드**를 선택하여 CSV 보고서를 봅니다.

부서 관리자 및 계정 소유자는 **고급 보고서 다운로드** 기능을 사용하는 경우 요금을 볼 수 없습니다. 비용이 $0로 표시됩니다.

요금을 볼 수 있는 계정 소유자 권한은 계정 소유자 및 연결된 구독에 대한 권한이 있는 모든 사용자로 확장됩니다. 간접 고객인 경우 채널 파트너가 비용 기능을 사용하도록 설정해야 합니다.

## <a name="power-bi-reporting"></a>Power BI 보고

Power BI 보고는 청구 정보를 볼 수 있는 액세스 권한이 있는 EA 직접 고객, 파트너 및 간접 고객에게 제공됩니다.

### <a name="power-bi-pro"></a>Power BI Pro

EA 고객은 Power BI Pro를 사용할 수 있습니다. Power BI Pro를 사용하면 보고서를 생성하고 공유하여 비용 데이터를 효과적으로 관리할 수 있습니다. 추가 협업 및 데이터 새로 고침 기능도 있습니다 Power BI Pro는 더 높은 데이터 용량 및 데이터 스트리밍 제한을 제공합니다.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Microsoft Azure Consumption Insights에 액세스하려면

1. [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)로 이동합니다.
1. **지금 가져오기**를 선택합니다.
1. 등록 번호와 개월 수를 제공하고, **다음**을 선택합니다.
1. 연결할 API 액세스 키를 입력합니다. [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)에서 등록에 대한 키를 찾을 수 있습니다.
1. **로그인**을 선택하여 가져오기 프로세스를 자동으로 시작합니다.
1. 프로세스가 완료되면 탐색 창에 새 대시보드, 보고서 및 모델이 표시됩니다. 대시보드를 선택하여 가져온 데이터를 확인합니다.

> [!TIP]
>
> - 등록에 대한 API 키를 생성하는 방법에 대한 자세한 내용은 [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)에서 API 보고서 도움말 파일을 참조하세요.
> - Power BI를 Azure 사용량에 연결하는 방법에 대한 자세한 내용은 [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management)를 참조하세요.

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>레거시 Power BI EA 콘텐츠 팩에 액세스하려면 다음을 수행합니다.

1. [Power BI 웹 사이트](https://app.powerbi.com/getdata/services/azure-enterprise)로 이동합니다.
1. 유효한 회사 또는 학교 계정으로 로그인합니다.

   회사 또는 학교 계정은 Azure Enterprise Portal을 통해 등록에 액세스하는 데 사용하는 것과 동일하거나 다를 수 있습니다.
1. 서비스 대시보드에서 **Microsoft Azure 엔터프라이즈**, **연결**을 차례로 선택합니다.
1. **Azure 엔터프라이즈에 연결** 화면에서 다음 필드를 채웁니다.
    - Azure 환경 URL: [https://ea.azure.com](https://ea.azure.com/)
    - 개월 수: 1-36
    - 등록 번호: 사용자의 등록 번호
1. **다음**을 선택합니다.
1. **인증 키 상자**에서 API 키를 입력합니다.

    API 키는 Azure Enterprise Portal의 **사용량 다운로드** 탭 아래에서 가져올 수 있습니다. **API 액세스 키**를 선택한 다음, 해당 키를 **계정 키** 상자에 붙여넣습니다.
1. 데이터 세트의 크기에 따라 Power BI에서 데이터를 로드하는 데 약 5-30분이 걸립니다.

## <a name="reports-faq"></a>보고서 FAQ

이 섹션에서는 보고서에 대한 일반적인 질문을 다룹니다.

### <a name="why-is-my-cost-showing-as-0"></a>비용이 $0로 표시되는 이유는 무엇인가요?

**직접 등록** 고객의 경우 엔터프라이즈 관리자는 계정 소유자 및 부서 관리자에게 사용량 보고서에 대한 비용/가격 책정 정보에 대한 액세스 권한을 부여할 수 있습니다. 다음 단계를 수행하세요.

1. Azure Enterprise Portal의 왼쪽 탐색 영역에서 **관리**를 선택합니다.
1. DA(부서 관리자) 요금 보기 옆에 있는 파란색 연필을 선택합니다.
1. **사용**를 선택하고 저장합니다.
1. AO(계정 소유자) 요금 보기 옆에 있는 파란색 연필을 선택합니다.
1. **사용**를 선택하고 저장합니다.

> [!NOTE]
> 계정 소유자 또는 부서 관리자인 경우 엔터프라이즈 관리자에게 문의하여 가격 책정 기능을 사용하도록 설정합니다.

**간접 등록** 고객의 경우 파트너에게 문의하여 가격 책정 기능을 사용하도록 설정했는지 확인합니다. 파트너만 이 작업을 수행할 수 있습니다. 사용하도록 설정되면 엔터프라이즈 관리자가 등록에 대한 비용과 가격 책정을 볼 수 있습니다.

계정 소유자 또는 부서 관리자의 요금 보기 기능을 사용하도록 설정하려면 파트너가 **직접 등록**의 단계를 수행합니다.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>사용량 세부 정보 보고서에 SKU 정보가 없는 이유는 무엇인가요?

사용량 세부 정보 보고서에는 SKU 정보가 포함되지 않습니다. 그러나 이 보고서에는 사용량 정보가 포함되어 있으므로 가격표 보고서를 다운로드하여 SKU 정보를 얻을 수 있습니다.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Azure Marketplace의 총 금액이 사용 요약 및 세부 정보 보고서와 일치하지 않는 이유는 무엇인가요?

Azure Marketplace 요금 보고서에는 사용량 기준 요금만 표시됩니다. 일회성 요금은 표시되지 않습니다. 최신의 사용량 기준 요금과 일회성 요금에 대해서는 사용 요약 페이지를 참조하세요.

### <a name="why-is-there-no-information-on-my-api-report"></a>API 보고서에 정보가 없는 이유는 무엇인가요?

API 키는 6개월마다 만료됩니다. 문제가 있는 경우 엔터프라이즈 관리자가 새 API 키를 생성해야 합니다. API 보고서 FAQ의 단계를 수행해야 합니다.

### <a name="why-isnt-my-power-bi-report-working"></a>Power BI 보고서가 작동하지 않는 이유는 무엇인가요?

Power BI 관련 문제의 경우 [Power BI 지원 팀](https://support.powerbi.com)을 통해 티켓을 기록하세요.

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>보고서에 리소스 태그가 표시되지 않는 이유는 무엇인가요?

리소스 태그는 Azure Portal에서 관리됩니다. [Azure Portal](https://portal.azure.com)에서 Azure 구독 팀에 문의할 수 있습니다. [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 문서의 단계를 수행합니다.

### <a name="why-does-my-resource-rate-change-every-day"></a>리소스 요금이 매일 변경되는 이유는 무엇인가요?

자세한 사용량 보고서에 표시된 리소스 요율은 계산된 값입니다. 이는 서비스에 대해 청구된 월간 평균 요율을 나타냅니다. 리소스 요율은 서비스 단위에 대한 월간 약정 및 월간 초과분 요금의 평균에서 계산됩니다. 약정 및 초과분 요율에 대해 청구되는 사용량 부분은 해당 월의 마감 날짜로 변경됩니다. 따라서 나열된 리소스 요율도 해당 월 중에 변경됩니다. 해당 월이 끝난 후 5일째에 리소스 요금이 잠깁니다.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>리소스 요금을 계산하는 프로세스 용어집

- **총 원시 단위:** 자세한 사용량 보고서의 사용한 수량입니다.
- **단위당 MOCP 리소스:** 업스트림 사용 시스템에서는 각 서비스의 사용량을 서로 다른 단위로 내보냅니다. (사전 설정)
- **단위당 사용량:** Azure Enterprise 측정 단위입니다. (사전 설정)
- **가격:** Azure Enterprise Portal의 단가입니다.
- **총 비용:** 자세한 사용량 보고서의 확장 비용 또는 Azure Enterprise Portal의 약정 사용량 + 초과분입니다.

### <a name="charges-calculations"></a>요금 계산

- **사용된 MOCP 리소스로 변환** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **사용된 단위로 변환** = `Consumed MOCP Resources / Consumption per Unit`
- **총 비용 계산** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>사용량 계산 논리의 논리

**리소스 요율** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

리소스 요율은 요금을 기준으로 도출됩니다. 가격표의 실제 단가와 일치하지 않을 수 있습니다.

사용량 데이터 보고서 다운로드에서 원시 리소스 사용량을 소수점 이하 최대 6 자릿수까지 확인할 수 있습니다. 이 데이터는 초과분 요금을 계산하는 데 사용됩니다. 그러나 Azure Enterprise Portal에 표시된 사용량 데이터는 약정 단위에 대해 소수점 이하 4 자릿수로 반올림되고 초과분 단위에 대해 소수점 이하 0 자릿수로 잘립니다. Azure Enterprise Portal에서 모든 초과분 사용량에 대한 요금은 전체 단위에 대해서만 청구됩니다. 초과분 또는 혼합 월로 청구되는 사용량에 대한 단가와 리소스 요율 사이에는 큰 차이가 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 송장 발부 요금 검토
> * 서비스 초과 요금 검토
> * Marketplace 청구서 검토

Azure EA Portal을 사용하여 자세히 알아보려면 다음 문서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure EA Portal 시작](../manage/ea-portal-get-started.md)
