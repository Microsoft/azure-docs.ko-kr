---
title: Azure 엔터프라이즈 등록 청구서
description: 이 문서에서는 Azure 엔터프라이즈 청구서를 관리하고 작업하는 방법을 설명합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/04/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 2a75972d00733d23df7f4a59b2d5010404df8dc3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023398"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure 엔터프라이즈 등록 청구서

이 문서에서는 Azure EA(Azure 기업계약) 청구서를 관리하고 작업하는 방법에 대해 설명합니다. 청구서는 요금 청구 명세를 나타냅니다. 이에 대한 정확성을 검토합니다. 또한 청구서 관리에 필요할 수 있는 다른 작업에도 익숙해져야 합니다.

## <a name="change-a-po-number-for-an-overage-invoice"></a>초과분 청구서의 PO 번호 변경

엔터프라이즈 관리자가 청구서 날짜 앞에 PO(구매 주문) 번호를 설정하지 않으면 Azure Enterprise Portal에서 기본 PO 번호를 자동으로 생성합니다. 엔터프라이즈 관리자는 자동화된 청구서 알림 이메일을 받은 후 최대 7일 이내에 PO 번호를 업데이트할 수 있습니다.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Azure 서비스 구매 주문 번호를 업데이트하는 방법은 다음과 같습니다.

1. Azure Enterprise Portal에서 **보고서** > **사용 요약**을 차례로 선택합니다.
1. 오른쪽 위 모서리에서 **PO 번호 편집**을 선택합니다.
1. **Azure 서비스** 라디오 단추를 선택합니다.
1. 날짜 범위 드롭다운 메뉴에서 **청구서 기간**을 선택합니다.

   PO 번호는 청구서 알림을 받은 후 청구서를 결제하기 전에 7일 동안 편집할 수 있습니다.
1.  **PO 번호** 필드에서 새 PO 번호를 입력합니다.
1.  **저장** 을 선택하여 변경 내용을 제출합니다.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Azure Marketplace 구매 주문 번호를 업데이트하려는 경우,

1. Azure Enterprise Portal에서 **보고서** > **사용 요약**을 차례로 선택합니다.
1. 오른쪽 위 모서리에서 **PO 번호 편집**을 선택합니다.
1. **Marketplace** 라디오 단추를 선택합니다.
1. 날짜 범위 드롭다운 메뉴에서 **청구서 기간**을 선택합니다.

   PO 번호는 청구서 알림을 받은 후 청구서를 결제하기 전에 7일 동안 편집할 수 있습니다.
1.  **PO 번호** 필드에서 새 PO 번호를 입력합니다.
1.  **저장** 을 선택하여 변경 내용을 제출합니다.

## <a name="cadence-of-azure-enterprise-billing"></a>Azure 엔터프라이즈 청구 흐름

Microsoft는 Microsoft Azure 서비스의 모든 약정 구매에 대한 요금을 매년 등록 개시 날짜에 청구합니다. 약정 금액을 초과하는 사용량에 대해 Microsoft는 미지급 금액으로 청구합니다.

- 약정 요금은 월간 요금 및 연간 요금을 미리 반영하여 산정됩니다.
- 초과분 요금은 매월 계산하여 청구 기간 마지막 날에 체납금으로 청구됩니다.

### <a name="billing-intervals"></a>청구 간격

청구 간격은 약정을 구매하도록 선택하는 방법에 따라 달라집니다. 연간 약정은 다음 중 하나와 관련이 있습니다.

- 등록 갱신 날짜
- 1년 수정계약 구독의 개시 날짜

초과분 청구서를 받는 날짜는 등록 시작 날짜와 설정에 따라 달라집니다.

- **시작 날짜가 2018년 5월 1일 이전인 직접 등록**:
  - 직접 EA(기업계약)를 체결한 경우 Azure Marketplace 서비스를 제외한 Azure 서비스에 대해 연간 청구 기간이 적용됩니다. 청구 기간은 계약이 적용되는 등록 갱신 날짜를 기준으로 합니다.
  - EA MC(약정 금액) 임계값의 150%를 초과하면 등록 갱신 날짜를 기준으로 하는 분기별 청구 주기로 자동으로 전환됩니다. 또한 Azure 서비스 초과분 청구서도 받게 됩니다.
  - MC 임계값의 150%를 초과하지 않으면 등록이 연간 청구 기간으로 유지됩니다. 초과분 청구서는 약정 연도 말에 수령됩니다.

- **시작 날짜가 2018년 5월 1일 이후인 직접 등록**:
  - Azure 사용량 및 별도 청구 요금 청구서는 월간 청구 기간을 기준으로 합니다.
  - 약정 금액에 포함되지 않는 모든 요금은 초과분 지불로 인한 것입니다.  

- **2018년 5월 1일 이전에 시작된 등록을 사용하여 간접 등록**:
  
  시작 날짜가 2018년 5월 1일 이전인 간접 EA(기업계약) 고객인 경우 분기별 청구 기간으로 설정됩니다. CP(채널 파트너)는 청구서를 직접 발부합니다.  

- **시작 날짜가 2018년 5월 1일 이후인 간접 등록**:
  
  월간 청구 기간이 적용됩니다.  

### <a name="increase-your-monetary-commitment"></a>약정 금액 증가

약정 금액은 언제든지 늘릴 수 있습니다. 해당 연도의 약정 기간 동안 남아 있는 개월 수에 대한 요금이 청구됩니다. 예를 들어 1년 수정계약 구독에 가입한 다음, 6개월 동안 약정을 늘리면 해당 기간의 남은 6개월에 대한 청구서가 발부됩니다. 그런 다음, 약정 기간의 마지막 6개월에 대한 약정 수량이 업데이트됩니다. 이러한 새 수량은 초과분 요금을 결정하는 데 사용됩니다.

### <a name="overage"></a>초과분

초과분의 경우 청구 기간 동안 약정을 초과한 사용량 또는 예약에 대한 요금이 청구됩니다. 개별 항목의 초과분 수량을 계산한 방법에 대해 분석하려면 사용 요약 보고서를 참조하거나 채널 파트너에게 문의하세요.

청구서의 각 항목에 표시되는 정보는 다음과 같습니다.

- **확장 금액**: 총 청구 요금
- **약정 사용량**: 요금을 포함하는 데 사용되는 약정 금액
- **순 금액**: 약정 금액을 초과하는 요금

관련 세금은 약정 금액을 초과하는 순 금액에서만 계산됩니다.

초과분은 자동으로 청구됩니다. 알림 및 청구서의 타이밍은 청구 기간 종료 날짜에 따라 달라집니다.

- 초과분 알림은 일반적으로 청구 종료 날짜 이후 7일 내에 보내집니다.
- 초과분 청구서는 알림 이후 7-9일 내에 보내집니다.
- 초과분 알림과 청구서 발부 사이에서 7일 동안 요금을 검토하고 시스템 생성 PO 번호를 업데이트할 수 있습니다.

### <a name="azure-marketplace"></a>Azure Marketplace

2019년 4월 청구 기간부터 고객은 모든 Azure 및 Azure Marketplace 요금을 두 개의 별도 청구서 대신 단일 청구서로 결합한 단일 Azure 청구서를 받기 시작했습니다. 이 변경은 오스트레일리아, 일본 또는 싱가포르의 고객에게 적용되지 않습니다.

결합 청구서로 전환하는 동안 부분 Azure Marketplace 청구서를 받게 됩니다. 이 마지막 개별 청구서에는 청구 업데이트 날짜 이전에 발생한 Azure Marketplace 요금이 표시됩니다. 해당 날짜 이후에 발생한 Azure Marketplace 요금은 Azure 청구서에 표시됩니다. 전환 기간 후에는 모든 Azure 및 Azure Marketplace 요금이 결합 청구서에 표시됩니다.  

## <a name="adjust-billing-frequency"></a>대금 청구 주기 변경

고객의 대금 청구 주기는 연간, 분기별 또는 월간입니다. 청구 기간은 고객이 계약서에 서명할 때 결정됩니다. 월간 청구는 가장 짧은 청구 간격입니다.

- 직접 등록의 청구 기간을 연간에서 분기별로 변경하려면 엔터프라이즈 관리자의 **승인**이 필요합니다. 간접 등록의 경우 파트너 관리자의 승인이 필요합니다. 현재 청구 분기가 끝나면 변경 내용이 적용됩니다.
- 청구 기간을 연간 또는 분기별에서 월간으로 변경하려면 계약에 대한 **수정**이 필요합니다.  기존 등록 청구 기간을 변경하려면 엔터프라이즈 관리자 또는 "대금 청구 담당자"의 승인이 필요합니다.
- 승인을 [Azure Enterprise Portal 지원](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)에 **제출**합니다. 발급 범주, 즉 **요금 청구 및 청구서 발부**를 선택합니다.

현재 청구 분기가 끝나면 변경 내용이 적용됩니다.

수정계약 M503에 서명한 경우 청구 주기를 월간 청구로 변경할 수 있습니다. 

## <a name="credits-and-adjustments"></a>크레딧 및 조정

[Azure Enterprise Portal](https://ea.azure.com)의 **보고서** 섹션에서 등록에 적용된 모든 크레딧 또는 조정을 볼 수 있습니다.

크레딧을 보려면,

1. [Azure Enterprise Portal](https://ea.azure.com)에서 **보고서** 섹션을 선택합니다.
1. **사용 요약**을 선택합니다.
1. 오른쪽 위 모서리에서 **M**을 **C** 보기로 변경합니다.
1. Azure 서비스 약정 테이블에서 조정 필드를 확장합니다.
1. 등록에 적용되는 크레딧과 간단한 설명이 표시됩니다. 다음은 그 예입니다.  서비스 수준 계약 크레딧이라는 설명이 표시될 수 있습니다.

## <a name="request-an-invoice-copy"></a>청구서 사본 요청

청구서 사본을 요청하려면 파트너에게 문의하세요.

## <a name="overage-offset"></a>초과분 오프셋

약정 금액을 초과분에 적용하려면 다음 조건을 충족해야 합니다.

- 약정 금액이 지불되지 않았으며 청구된 서비스 종료 날짜로부터 1년 이내에 초과분 요금이 발생했습니다.
- 사용 가능한 약정 금액에는 과거의 모든 미결제 Azure 청구서를 포함하여 발생한 전체 요금이 포함됩니다.
- 완료하려는 청구 기간은 완전히 종결되어야 합니다. 청구는 매월 5일 이후에 완전히 종결됩니다.
- 오프셋하려는 청구 기간은 완전히 종결되어야 합니다.
- ACD(Azure 약정 할인)는 실제 신규 약정에서 이전 사용량에 계획된 자금을 뺀 금액을 기준으로 합니다. 이 요구 사항은 발생한 초과분 요금에만 적용됩니다. 이는 약정 금액을 사용하는 서비스에만 유효하므로 Azure Marketplace 요금에는 적용되지 않습니다. Azure Marketplace 요금은 별도로 청구됩니다.

초과분 오프셋을 완료하기 위해 사용자 또는 계정 팀에서 지원 요청을 열 수 있습니다. 엔터프라이즈 관리자 또는 대금 청구 담당자의 이메일 승인이 필요합니다.

## <a name="view-price-sheet-information"></a>가격표 정보 보기

엔터프라이즈 관리자는 Azure 서비스 등록과 관련된 가격표를 볼 수 있습니다.

현재 가격표를 보려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서**, **가격표**를 차례로 선택합니다.
2. 가격표를 보거나 **다운로드**를 선택합니다.

![가격표 정보를 보여주는 예제](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

이전 가격 목록을 다운로드하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서**를 선택한 다음, **사용량 다운로드**를 선택합니다.
2. 가격표를 다운로드합니다.

![이전 가격표를 다운로드하는 위치를 보여주는 예제](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

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

1. Microsoft Excel에서 **파일** ** > 열기**로 차례로 이동합니다.
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

## <a name="move-usage-data-to-another-enrollment"></a>사용량 데이터를 다른 등록으로 이동

사용량 데이터는 양도를 소급 적용하는 경우에만 이동됩니다. 다음과 같은 두 가지 옵션을 통해 한 등록에서 다른 등록으로 사용량 데이터를 이동할 수 있습니다.

- 한 등록에서 다른 등록으로 계정 양도
- 한 등록에서 다른 등록으로 등록 양도

두 옵션 모두 지원을 받으려면 [EA 지원 팀](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)에 지원 요청을 제출해야 합니다. 

## <a name="azure-ea-pricing-overview"></a>Azure EA 가격 책정 개요

이 섹션에서는 사용량을 계산하는 방법에 대해 자세히 설명합니다. 여기서는 계산이 더 복잡한 기업계약에서 다양한 Azure 서비스의 요금에 대해 자주 묻는 많은 질문에 답변합니다.

### <a name="price-protection"></a>가격 보호

고객 또는 채널 파트너는 CPS(고객 가격표)에 표시된 가격 이하의 가격 또는 Azure 구매 개시 날짜에 적용되는 가격을 받을 수 있습니다. 이 가격을 기준 가격이라고 합니다. Azure 구매 후에 도입된 서비스의 경우 서비스가 처음 도입될 때 적용되는 할인 수준으로 적용되는 가격이 청구됩니다. 이 가격 보호는 기업계약에 따라 1년 또는 3년의 약정 기간 동안 적용됩니다.

### <a name="price-changes"></a>가격 변경

Microsoft는 등록 기간 동안 개별 Azure 서비스에 대한 현재 기업계약 가격을 인하할 수 있습니다. 더 낮은 가격이 적용되는 동안 이러한 할인 요율은 기존 고객으로까지 확장됩니다. 이러한 요율이 나중에 인상되는 경우 위에서 정의한 대로 등록의 서비스 가격은 가격 보호 최고 한도 이상으로 증가하지 않습니다. 그러나 이 요율은 이전에 인하된 가격에 비해 증가할 수 있습니다. 두 경우 모두에서 등록과 연결된 엔터프라이즈 및 파트너 관리자에게 이메일을 보내 고객과 간접 파트너에게 향후의 가격 변경을 알립니다.

### <a name="current-effective-pricing"></a>현재 실효 가격 책정

고객 및 채널 파트너는 [Azure Enterprise Portal](https://ea.azure.com/)에 로그인하고 등록에 대한 가격표 페이지를 표시하여 해당 등록의 현재 가격 책정을 볼 수 있습니다. 채널 파트너 중 하나를 통해 Azure를 간접적으로 구매하는 경우 고객의 등록에 대한 가격 인상 가격 책정을 표시하도록 설정하지 않았으면 채널 파트너로부터 가격 책정 업데이트를 얻어야 합니다.

### <a name="introduction-of-new-azure-services"></a>새 Azure 서비스 도입

Microsoft는 Azure를 지속적으로 개선하고 있으며, 기존 서비스와 별도로 가격이 책정되는 새로운 서비스를 정기적으로 추가하고 있습니다. 일부 미리 보기 서비스는 자동으로 사용할 수 있지만, 다른 일부는 고객이 [Azure 계정 포털](https://account.windowsazure.com/PreviewFeatures)에서 작업을 수행해야 합니다.

일부 서비스는 처음 도입될 때 판촉 가격 책정으로 시작되며 이후에 가격이 인상될 수 있습니다.

서비스가 미리 보기에서 GA(일반 공급)로 전환되면 전체 성능과 안정성 SLA가 적용됨에 따라 가격이 인상될 수 있습니다. 이러한 인상은 일반적인 기준 가격 보호로 제한되지 않습니다. 이러한 서비스의 사용량은 인상된 요율로 청구됩니다. 이러한 새 서비스와 관련된 요금을 방지하려면 해당 서비스의 사용을 옵트아웃해야 합니다.

### <a name="introduction-of-regional-pricing"></a>영역별 가격 책정 도입

새 서비스의 도입 외에도, 서비스에 대한 지역별 지원이 증가함에 따라 이러한 서비스는 단일 글로벌 기반에서 더 많은 지역별 모델로 정기적으로 변경됩니다.

서비스의 지역화가 처음 도입되면 이러한 새 지역에 대한 기준 가격 보호가 적용됩니다. 그러나 나중에 동일한 서비스에 대한 추가 지역이 도입되면 이러한 지역은 새 서비스로 간주되며, 기준 가격 보호에 관계없이 자체의 개별 요금으로 제공됩니다.

### <a name="enterprise-devtest"></a>Enterprise 개발/테스트

엔터프라이즈 관리자는 계정 소유자가 Enterprise 개발/테스트 제품을 기반으로 하여 구독을 만들 수 있도록 설정할 수 있습니다. 계정 소유자는 기본 구독자에게 필요한 Enterprise 개발/테스트 구독을 설정해야 합니다. 이 구성을 통해 활성 Visual Studio 구독자는 특별한 Enterprise 개발/테스트 요율로 Azure에서 개발 및 테스트 워크로드를 실행할 수 있습니다. 자세한 내용은 [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)를 참조하세요.

## <a name="enterprise-agreement-usage-calculations"></a>기업계약 사용량 계산

각 개별 서비스에 대한 기본 퍼블릭 가격 책정 정보, 측정 단위, FAQ 및 사용량 보고 지침은 [Azure 서비스](https://azure.microsoft.com/services/) 및 [Azure 가격 책정](https://azure.microsoft.com/pricing/)을 참조하세요. EA 계산에 대한 자세한 내용은 다음 섹션에서 확인할 수 있습니다.

### <a name="enterprise-agreement-units-of-measure"></a>기업계약 측정 단위

기업계약의 측정 단위는 MOSA(Microsoft Online Services 계약) 프로그램과 같은 다른 프로그램에 표시되는 측정 단위와 다른 경우가 많습니다. 이 차이는 여러 서비스의 경우 측정 단위를 집계하여 정규화된 가격 책정을 제공한다는 것을 의미합니다. Azure Enterprise Portal의 사용 요약 보기에 표시되는 측정 단위는 항상 엔터프라이즈 측정값입니다. 각 서비스에 대한 현재 측정 단위 및 변환의 전체 목록은 [친숙한 서비스 이름](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) Excel 파일에 제공됩니다.

### <a name="rounding-rules"></a>반올림 규칙

Azure Enterprise Portal은 IEEE 표준 뱅커 반올림(Banker Rounding) 또는 가우스 반올림(Gaussian Rounding) 논리를 따릅니다. 이 논리는 반 자릿수 값에 대해 가장 가까운 짝수 자릿수로 숫자를 반올림합니다. 더 일반적인 절반 반올림되는 반올림 논리는 항상 반 자릿수를 다음 가장 높은 자릿수로 반올림합니다. 이 Azure Enterprise Portal 방법은 표준 Excel 논리와 비교할 때 실제로 그룹에 대해 더 정확한 총 합계를 제공합니다.

예를 들어 드롭된 첫 번째 자릿수가 5이고 다음 자릿수가 없거나 0이면 가장 가까운 짝수 자릿수로 반올림합니다. 즉, 2.315와 2.325는 모두 가장 가까운 소수점 이하 2 자릿수로 반올림되어 2.32가 됩니다.

참고로, 다음 표에서는 반올림 및 변환을 위한 Azure Enterprise Portal 규칙을 모델링하는 데 사용할 수 있는 Excel 수식을 보여 줍니다.

| 시나리오 | 뱅커 논리 수식 |
| --- | --- |
| 반올림 사용 | =MROUND({_source_}, 0.0002) |
| 가격 책정 반올림(2자리) | =MROUND({_source_}, 0.02) |
| 가격 책정 반올림(0자리) | =MROUND({_source_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>사용량 세부 정보 보고서와 사용 요약 페이지 간 변환

사용량 데이터 보고서 다운로드에서 원시 리소스 사용량을 소수점 이하 최대 6 자릿수까지 확인할 수 있습니다. 그러나 Azure Enterprise Portal에 표시된 사용량 데이터는 약정 단위에 대해 소수점 이하 4 자릿수로 반올림되고 초과분 단위에 대해 소수점 이하 0 자릿수로 잘립니다. 원시 사용량 데이터는 Azure Enterprise Portal에서 사용되는 단위로 변환하기 전에 먼저 소수점 이하 4 자릿수로 반올림됩니다. 그런 다음, 변환된 엔터프라이즈 단위가 소수점 이하 4 자릿수로 다시 반올림됩니다. Azure Enterprise Portal 내에서가 아니라 사용량 보고서 다운로드에서 변환 이전의 실제 사용한 시간만 확인할 수 있습니다.

다음은 그 예입니다.  694.533404 실제 SQL Server 시간이 사용량 세부 정보 보고서에 보고되는 경우가 있습니다. 이러한 단위는 100 컴퓨팅 시간의 6.94533404로 변환된 다음, 6.9453으로 반올림되어 Azure Enterprise Portal에 표시됩니다.

- 확장 청구 금액을 결정하기 위해 표시된 단위에 약정 단가를 곱하고 결과를 소수점 이하 2 자릿수로 자릅니다. 일본 엔(JPY) 및 한국 원(KRW)의 경우 확장된 금액은 소수 0번째 자리로 반올림됩니다.
- 초과분의 경우 청구 단위를 소수점 이하 6 자릿수로 자른 다음, 초과분 단가를 곱하여 확장 청구 금액을 결정합니다.
- MSP(관리형 서비스 공급자) 청구의 경우 MSP로 표시된 부서와 관련된 모든 사용량은 EA 측정 단위로 변환된 다음, 소수점 이하 0 자릿수로 잘립니다. 결과적으로 이 사용량의 합계는 Azure Enterprise Portal에 보고된 모든 사용량의 총 합계보다 낮을 수 있습니다. 이는 MSP가 약정 금액 잔액 내에 있는지, 아니면 초과분에 있는지에 따라 달라집니다.

### <a name="graduated-pricing"></a>등급별 가격 책정

기업계약 가격 책정에는 현재 사용량이 증가할수록 단위당 요금이 감소하는 등급별 가격 책정이 포함되지 않습니다. 등급별 가격 책정을 사용하는 MOSA 프로그램에서 기업계약으로 전환하는 경우 가격은 서비스의 기본 계층에서 기업계약 할인에 적용되는 모든 조정을 뺀 값에 비례하여 설정됩니다.

### <a name="partial-hour-billing"></a>부분 시간 요금 청구

모든 청구된 사용량은 전체 시간 증분이 아니라 부분 시간 단위로 변환된 시간(분 단위)을 기준으로 합니다. 나열된 시간당 엔터프라이즈 요율은 총 시간에 부분 시간을 더한 시간에 적용됩니다.

### <a name="average-daily-consumption"></a>평균 일별 사용량

일부 서비스는 월 기준으로 가격이 책정되지만, 사용량은 일 단위로 보고됩니다. 이러한 경우 사용량을 하루에 한 번 31로 나누어 계산한 후 해당 청구 월의 일 수에 걸쳐 합산합니다. 따라서 요율은 모든 월에 대해 예상보다 높지 않으며, 31일 미만인 해당 월에 대해서는 약간 낮습니다.

### <a name="compute-hours-conversion"></a>컴퓨팅 시간 변환

2016년 1월 28일 이전에는 A0, A2, A3 및 A4 표준 및 기본 Virtual Machines 및 Cloud Services의 사용량을 A1 Virtual Machine 측정기 시간(분)으로 내보냈습니다. A0 VM은 A1 VM 시간(분)의 분수로 계산되었지만, A2, A3 및 A4는 배수로 변환되었습니다. 이 정책으로 인해 고객을 약간 혼란스럽게 만들었으므로 분당 사용량을 전용 A0, A2, A3 및 A4 측정기에 할당하도록 변경했습니다.

새 계량 방법은 2016년 1월 27~28일에 적용되었습니다. 이 전환 기간 동안의 사용량을 보여 주는 CSV 다운로드에서 다음 두 가지 항목을 모두 확인할 수 있습니다.

- A1 측정기에서 내보낸 사용량
- 배포 크기에 해당하는 새 전용 측정기에서 내보낸 사용량

| **배포 크기** | **2016년 1월 26일까지 A1의 배수로 내보낸 사용량** | **2016년 1월 27일부터 전용 미터에서 내보낸 사용량** |
| --- | --- | --- |
| A0 | A1 시간의 0.25배 | A0 시간의 1배 |
| A2 | A1 시간의 2배 | A2 시간의 1배 |
| A3 | A1 시간의 4배 | A3 시간의 1배 |
| A4 | A1 시간의 8배 | A4 시간의 1배 |

### <a name="regions"></a>영역

영역 및 지역의 가격 책정에 영향을 주는 서비스의 경우 영역 및 지역에 대한 맵은 다음 표를 참조하세요.

| 지역 | 데이터 전송 영역 | CDN 영역 |
| --- | --- | --- |
| 영역 1 | 북유럽 <br> 서유럽 <br> 미국 서부 <br> 미국 동부 <br> 미국 중북부 <br> 미국 중남부 <br> 미국 동부 2 <br> 미국 중부 | 북아메리카 <br> 유럽 |
| 영역 2 | 아시아 태평양 동부 <br> 아시아 태평양 남동부 <br> 일본 동부 <br> 일본 서부 <br> 오스트레일리아 동부 <br> 오스트레일리아 남동부 | 아시아 태평양 <br> 일본 <br> 라틴 아메리카 <br> 중동/아프리카 <br> 오스트레일리아 동부 <br> 오스트레일리아 남동부 |
| 영역 3 | 브라질 남부 |   |

동일한 데이터 센터 내에 있는 서비스 간의 데이터 송신에 대한 요금은 없습니다. 예를 들어 Office 365 및 Azure가 있습니다.

### <a name="monetary-commitment-and-unbilled-usage"></a>현금 약정 금액 및 청구되지 않은 사용량

Azure 현금 약정 금액은 Azure 서비스 선결제 금액입니다. 서비스를 사용하면 그에 따라 현금 약정 금액에서 차감됩니다. 자사 Azure 서비스는 약정 금액에 대해 청구됩니다. 그러나 일부 요금은 별도로 청구되며, Azure Marketplace 서비스는 약정 금액을 사용하지 않습니다.

### <a name="charges-billed-separately"></a>별도 청구 요금

타사에서 제공하는 일부 제품과 서비스는 Azure 현금 약정 금액을 사용하지 않습니다. 대신 이러한 항목에 대한 요금은 표준 청구 기간의 초과분 청구서의 일부로 별도로 청구됩니다.

모든 Azure 및 Azure Marketplace 요금은 등록의 청구 기간에 부합하는 단일 청구서로 결합되었습니다. 결합 청구서는 오스트레일리아, 일본 또는 싱가포르의 고객에게 적용되지 않습니다.

결합 청구서에는 먼저 Azure 사용량이 표시된 다음, Azure Marketplace 요금이 표시됩니다. 오스트레일리아, 일본 또는 싱가포르의 고객은 별도의 청구서에서 Azure Marketplace 요금을 확인할 수 있습니다.

표준 청구 기간이 끝날 때 초과분 사용량이 없는 경우 별도 청구 요금이 별도로 청구됩니다. 이 프로세스는 오스트레일리아, 일본 및 싱가포르의 고객에게 적용됩니다.

별도로 청구되는 서비스는 다음과 같습니다.

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop Registered User
- OpenLogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise(월간)
- Visual Studio Enterprise(연간)
- Visual Studio Professional(월간)
- Visual Studio Professional(연간)

## <a name="azure-marketplace-for-ea-customers"></a>EA 고객용 Azure Marketplace

직접 고객의 경우 Azure Marketplace 요금은 Azure Enterprise Portal에 표시됩니다. Azure Marketplace 구매 및 소비량은 분기별 또는 월간 주기 및 미지급에 대한 약정 금액 외의 금액으로 청구됩니다.

간접 고객은 Azure Enterprise Portal의 **구독 관리** 페이지에서 Azure Marketplace 구독을 찾을 수 있지만, 가격은 숨겨집니다. 고객은 Azure Marketplace 요금에 대한 자세한 내용을 LSP(라이선스 솔루션 공급자)에 문의해야 합니다.

매월 또는 매년 반복되는 새로운 Azure Marketplace 구매에 대한 전체 요금은 Azure Marketplace 항목을 구매하는 기간 동안 청구됩니다. 이러한 항목은 그 다음 기간의 최초 구매와 동일한 날짜에 자동 갱신됩니다.

매월 반복되는 기존 요금은 각 달력 월의 1일에 계속 갱신됩니다. 연간 요금은 구매 갱신 날짜에 갱신됩니다.

Azure Marketplace에서 사용할 수 있는 일부 타사 재판매인 서비스는 이제 EA(기업계약) 약정 금액 잔액을 사용합니다. 이전에는 이러한 서비스가 EA 현금 약정 금액과 별도로 청구되었으며 별도의 청구서가 발송되었습니다. EA 약정 금액을 Azure Marketplace의 이러한 서비스에 사용하면 고객 구매 및 결제 관리를 간소화할 수 있습니다. 현재 약정 금액을 사용하는 서비스의 전체 목록은 [Azure 웹 사이트에서 2018년 3월 6일 업데이트](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)를 참조하세요.

### <a name="partners"></a>파트너

LSP는 Azure Enterprise Portal의 가격표 페이지에서 Azure Marketplace 가격표를 다운로드할 수 있습니다. 오른쪽 위에서 **Marketplace 가격표** 링크를 선택합니다. Azure Marketplace 가격표에는 사용 가능한 모든 서비스 및 해당 가격이 표시됩니다.

가격표를 다운로드 하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서** > **가격표**로 차례로 이동합니다.
1. 오른쪽 위 모서리에서 사용자 이름 아래에 있는 Azure Marketplace 가격표에 대한 링크를 찾습니다.
1. 마우스 오른쪽 단추로 해당 링크를 클릭하고, **대상을 다른 이름으로 저장**을 선택합니다.
1. **저장** 창에서 문서 제목을 `AzureMarketplacePricelist.zip`으로 변경합니다. 그러면 파일이 .xlsx에서 .zip 파일로 변경됩니다.
1. 다운로드가 완료되면 국가별 가격표가 포함된 zip 파일이 제공됩니다.
1. LSP는 국가별 가격 책정에 대한 개별 국가 파일을 참조해야 합니다. LSP는 **알림** 탭을 사용하여 완전히 새롭거나 사용 중지된 SKU를 인식할 수 있습니다.
1. 가격 변경은 자주 발생하지 않습니다. LSP는 30일 전에 가격 인상 및 FX(외환) 변경에 대한 이메일 알림을 받습니다.
1. LSP는 등록별, ISV별, 분기별로 하나의 청구서를 받습니다.

### <a name="enabling-azure-marketplace-purchases"></a>Azure Marketplace 구매 사용

엔터프라이즈 관리자는 등록된 모든 Azure 구독에 대해 Azure Marketplace 구매를 사용하거나 사용하지 않도록 설정할 수 있습니다. 엔터프라이즈 관리자가 구매를 사용하지 않도록 설정하고 이미 Azure Marketplace 구독이 포함된 Azure 구독이 있는 경우 해당 Azure Marketplace 구독은 취소되거나 영향을 받지 않습니다.

고객이 Azure 구독을 Azure Enterprise Portal의 등록에 직접 연결하여 자신의 직접 Azure 구독을 Azure EA로 변환할 수 있지만, 이 경우 자식 구독을 자동으로 변환하지 않습니다.

Azure Marketplace 구매를 사용하도록 설정하려면 다음을 수행합니다.

1. 엔터프라이즈 관리자 권한으로 Azure Enterprise Portal에 로그인합니다.
1. **관리**로 이동합니다.
1. **등록 세부 정보** 아래에서 **Azure Marketplace** 품목 옆에 있는 연필 아이콘을 선택합니다.
1. **사용/사용 안 함** 또는 무료 **BYOL SKU만**을 적절하게 전환합니다.
1. **저장**을 선택합니다.

> [!NOTE]
> BYOL(사용자 라이선스 필요) 및 무료 전용 옵션은 Azure Marketplace SKU의 구매 및 취득을 BYOL 및 무료 SKU로 제한합니다.

[Azure Enterprise Portal 보고서의 Azure Marketplace 요금](#azure-marketplace-charges-in-azure-enterprise-portal-reports)에서 자세한 내용을 확인하세요.

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA에 대한 시간당 서비스 요금이 청구되는 서비스

MOSP의 월별 요율 대신 기업계약에 따른 시간당 요금이 청구되는 서비스는 다음과 같습니다.

- 애플리케이션 전달 네트워크
- 웹 애플리케이션 방화벽

### <a name="azure-remoteapp"></a>Azure RemoteApp

기업계약을 체결한 경우 기업계약 가격 수준에 따라 Azure RemoteApp에 대한 요금을 지불합니다. 추가 요금이 없습니다. 표준 가격에는 초기 40시간이 포함됩니다. 무제한 가격에는 초기 80시간이 포함됩니다. 80시간을 초과하면 RemoteApp에서 사용량 내보내기를 중지합니다.

## <a name="azure-marketplace-faq"></a>Azure Marketplace FAQ

이 섹션에서는 Azure 약정 금액이 Azure Marketplace의 일부 타사 재판매인 서비스에 적용되는 방법에 대해 설명합니다.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Azure Marketplace 서비스와 EA 약정 금액으로 변경된 사항은 무엇인가요?

2018년 3월 1일부터 일부 타사 재판매인 서비스는 EA MC(약정 금액)를 사용합니다. 이전에는 Azure RI(예약 VM 인스턴스)를 제외한 서비스에 대한 요금이 EA 약정 금액 이외의 금액으로 청구되었으며, 청구서가 별도로 발부되었습니다.

타사 게시 Azure Marketplace 서비스 중 일부를 포함하도록 MC 사용을 확장했습니다. EA 약정 금액을 Azure Marketplace의 이러한 서비스에 사용하면 구매 및 결제 관리를 간소화할 수 있습니다.

### <a name="why-did-we-make-this-change"></a>왜 이렇게 변경했나요?

고객은 선불 MC 결제를 활용할 수 있는 추가적인 방법을 지속적으로 찾고 있습니다. 이 변경은 고객이 자주 요청했으며, 대부분의 Azure Marketplace 고객에게 영향을 주었습니다.

### <a name="how-do-you-benefit"></a>혜택을 받으려면 어떻게 하나요?

더 간단한 청구 환경을 받을 수 있고, EA 약정 금액을 더 효율적으로 사용할 수 있습니다. 이러한 서비스는 선불 MC에 포함되어 있으므로 EA 약정 금액이 더 유용합니다.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>EA 약정 금액을 사용하는 Azure Marketplace 서비스는 무엇이며, 어떻게 알 수 있나요?

MC를 사용하는 서비스를 구입하면 Azure Marketplace에 대한 고지 사항이 표시됩니다. Red Hat, SUSE, Autodesk 및 Oracle에서 게시한 일부 서비스가 지원됩니다. 현재 다른 당사자가 게시한 비슷한 이름의 서비스는 MC에서 공제되지 않습니다. 전체 목록은 이 FAQ의 마지막 부분에 제공됩니다.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>EA 약정 금액이 소진되면 어떻게 되나요?

모든 MC를 소진하여 초과분으로 이동하는 경우 이러한 서비스와 관련된 요금은 다른 소비 서비스와 함께 다음 초과분 청구서에 표시됩니다. 2018년 3월 1일 변경 전에 이러한 요금은 다른 Azure Marketplace 서비스에서 청구되었습니다.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>모든 Azure Marketplace에서 EA 약정 금액을 사용하지 않는 이유는 무엇인가요?

EA 약정 금액과 관련하여 최상의 고객 환경을 제공하기 위해 열심히 노력하고 있습니다. 이 변경에 따라 많은 고객과 Azure Marketplace의 총 지출 중 상당 부분이 해결되었습니다. 나중에 다른 서비스가 추가될 수 있습니다.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>간접 등록 및 파트너에는 어떤 영향이 있을까요?

간접 등록 고객 또는 파트너에게는 아무 영향을 주지 않습니다. 이러한 서비스는 다른 사용 서비스와 동일한 파트너 가격 인상 기능을 따릅니다. 요금이 다른 청구서에 표시되고 요금 지불은 고객의 EA 약정 금액에서 나온다는 것이 유일한 변경입니다.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>EA 약정 금액을 사용하는 Azure Marketplace 서비스의 목록이 있나요?

특정 Azure Marketplace 제품은 약정 금액 자금을 사용할 수 있습니다. 이 프로그램에 참여하는 제품의 전체 목록은 [약정 금액을 사용하는 타사 서비스](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)를 참조하세요.

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

- 다음 Excel 파일은 Azure 서비스에 대한 세부 정보를 제공하며, 매월 6일 및 20일에 업데이트됩니다.

   | 제목 | Description | 파일 이름 |
   | --- | --- | --- |
   | [친숙한 서비스 이름](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | 모든 활성 서비스를 나열하고 다음을 포함합니다. <br>  <ul><li>서비스 범주</li>   <li>친숙한 서비스 이름</li>   <li>약정 이름 및 부품 번호</li> <li>사용량 이름 및 부품 번호</li>   <li>측정 단위</li>   <li>보고된 사용량과 표시된 Enterprise Portal 사용량 간의 변환 비율</li></ul> | Friendly\_Service\_Names.xlsx |
   | [서비스 다운로드 필드](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | 이 스프레드시트는 사용량 다운로드 보고서에서 가능한 모든 서비스 관련 필드 조합을 제공합니다. | Service\_Download\_Fields.xlsx |

- 청구서 및 요금을 이해하는 방법에 대한 자세한 내용은 [Azure 기업계약 요금의 이해](../understand/review-enterprise-agreement-bill.md)를 참조하세요.
- Azure Enterprise Portal을 시작하려면 [Azure EA Portal 시작](ea-portal-get-started.md)을 참조하세요.
