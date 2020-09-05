---
title: Azure 엔터프라이즈 등록 청구서
description: 이 문서에서는 Azure 엔터프라이즈 청구서를 관리하고 작업하는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 983cf6e7c9596d40f9db6221739af86175c58e87
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689867"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure 엔터프라이즈 등록 청구서

이 문서에서는 Azure EA(Azure 기업계약) 청구서를 관리하고 작업하는 방법에 대해 설명합니다. 청구서는 요금 청구 명세를 나타냅니다. 이에 대한 정확성을 검토합니다. 또한 청구서 관리에 필요할 수 있는 다른 작업에도 익숙해져야 합니다.

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>예정된 초과분 청구서의 PO 번호 변경

엔터프라이즈 관리자가 청구서 날짜 앞에 PO(구매 주문) 번호를 설정하지 않으면 Azure Enterprise Portal에서 기본 PO 번호를 자동으로 생성합니다. 엔터프라이즈 관리자는 자동화된 청구서 알림 이메일을 받은 후 최대 7일 이내에 PO 번호를 업데이트할 수 있습니다.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Azure 서비스 구매 주문 번호를 업데이트하는 방법은 다음과 같습니다.

1. Azure Enterprise Portal에서 **보고서** > **사용 요약**을 차례로 선택합니다.
1. 오른쪽 위 모서리에서 **PO 번호 편집**을 선택합니다.
1. **Azure 서비스** 라디오 단추를 선택합니다.
1. 날짜 범위 드롭다운 메뉴에서 **청구서 기간**을 선택합니다.

   PO 번호는 청구서 알림을 받은 후 청구서를 결제하기 전에 7일 동안 편집할 수 있습니다.
1. **PO 번호** 필드에서 새 PO 번호를 입력합니다.
1. **저장**을 선택하여 변경 내용을 제출합니다.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Azure Marketplace 구매 주문 번호를 업데이트하려는 경우,

1. Azure Enterprise Portal에서 **보고서** > **사용 요약**을 차례로 선택합니다.
1. 오른쪽 위 모서리에서 **PO 번호 편집**을 선택합니다.
1. **Marketplace** 라디오 단추를 선택합니다.
1. 날짜 범위 드롭다운 메뉴에서 **청구서 기간**을 선택합니다.

   PO 번호는 청구서 알림을 받은 후 청구서를 결제하기 전에 7일 동안 편집할 수 있습니다.
1. **PO 번호** 필드에서 새 PO 번호를 입력합니다.
1. **저장**을 선택하여 변경 내용을 제출합니다.

## <a name="azure-enterprise-billing-frequency"></a>Azure 엔터프라이즈 대금 청구 주기

Microsoft는 Microsoft Azure 서비스의 모든 선불 구매에 대한 요금을 매년 등록 개시 날짜에 청구합니다. 선불 금액을 초과하는 사용량에 대해 Microsoft는 미지급 금액으로 청구합니다.

- 선불 요금은 월간 요금 및 연간 요금을 미리 반영하여 산정됩니다.
- 초과분 요금은 매월 계산하여 청구 기간 마지막 날에 체납금으로 청구됩니다.

### <a name="billing-intervals"></a>청구 간격

청구 간격은 선불을 구매하도록 선택하는 방법에 따라 달라집니다. 연간 선불은 다음 중 하나와 관련이 있습니다.

- 등록 갱신 날짜
- 1년 수정계약 구독의 개시 날짜

초과분 청구서를 받는 날짜는 등록 시작 날짜와 설정에 따라 달라집니다.

- **시작 날짜가 2018년 5월 1일 이전인 직접 등록**:
  - 직접 EA(기업계약)를 체결한 경우 Azure Marketplace 서비스를 제외한 Azure 서비스에 대해 연간 청구 기간이 적용됩니다. 청구 기간은 계약이 적용되는 등록 갱신 날짜를 기준으로 합니다.
  - Azure EA 선불 임계값의 150%를 초과하면 등록 갱신 날짜를 기준으로 하는 분기별 청구 주기로 자동으로 전환됩니다. 또한 Azure 서비스 초과분 청구서도 받게 됩니다.
  - Azure 선불 임계값의 150%를 초과하지 않으면 등록이 연간 청구 기간으로 유지됩니다. 초과분 청구서는 선불 연도 말에 수령됩니다.

- **시작 날짜가 2018년 5월 1일 이후인 직접 등록**:
  - Azure 사용량 및 별도 청구 요금 청구서는 월간 청구 기간을 기준으로 합니다.
  - Azure 선불에 포함되지 않는 모든 요금은 초과분 지불로 인한 것입니다.  

- **2018년 5월 1일 이전에 시작된 등록을 사용하여 간접 등록**:

  시작 날짜가 2018년 5월 1일 이전인 간접 EA(기업계약) 고객인 경우 분기별 청구 기간으로 설정됩니다. CP(채널 파트너)는 청구서를 직접 발부합니다.  

- **시작 날짜가 2018년 5월 1일 이후인 간접 등록**:

  월간 청구 기간이 적용됩니다.  

### <a name="increase-your-azure-prepayment"></a>Azure 선불 늘리기

선불 금액은 언제든지 늘릴 수 있습니다. 해당 연도의 선불 기간 동안 남아 있는 개월 수에 대한 요금이 청구됩니다. 예를 들어 1년 수정계약 구독에 가입한 다음, 6개월 동안 선불을 늘리면 해당 기간의 남은 6개월에 대한 청구서가 발부됩니다. 그런 다음, 선불 기간의 마지막 6개월에 대한 선불 수량이 업데이트됩니다. 이러한 새 수량은 초과분 요금을 결정하는 데 사용됩니다.

### <a name="overage"></a>초과분

초과분의 경우 청구 기간 동안 선불을 초과한 사용량 또는 예약에 대한 요금이 청구됩니다. 개별 항목의 초과분 수량을 계산한 방법에 대해 분석하려면 사용 요약 보고서를 참조하거나 채널 파트너에게 문의하세요.

청구서의 각 항목에 표시되는 정보는 다음과 같습니다.

- **확장 금액**: 총 청구 요금
- **선불 사용량**: 요금을 포함하는 데 사용되는 선불 금액
- **순 금액**: 선불 금액을 초과하는 요금

관련 세금은 선불을 초과하는 순 금액에서만 계산됩니다.

초과분은 자동으로 청구됩니다. 알림 및 청구서의 타이밍은 청구 기간 종료 날짜에 따라 달라집니다.

- 초과분 알림은 일반적으로 청구 종료 날짜 이후 7일 내에 보내집니다.
- 초과분 청구서는 알림 이후 7-9일 내에 보내집니다.
- 초과분 알림과 청구서 발부 사이에서 7일 동안 요금을 검토하고 시스템 생성 PO 번호를 업데이트할 수 있습니다.

### <a name="azure-marketplace"></a>Azure Marketplace

2019년 4월 청구 기간부터 고객은 모든 Azure 및 Azure Marketplace 요금을 두 개의 별도 청구서 대신 단일 청구서로 결합한 단일 Azure 청구서를 받기 시작했습니다. 이 변경은 오스트레일리아, 일본 또는 싱가포르의 고객에게 적용되지 않습니다.

결합 청구서로 전환하는 동안 부분 Azure Marketplace 청구서를 받게 됩니다. 이 마지막 개별 청구서에는 청구 업데이트 날짜 이전에 발생한 Azure Marketplace 요금이 표시됩니다. 해당 날짜 이후에 발생한 Azure Marketplace 요금은 Azure 청구서에 표시됩니다. 전환 기간 후에는 모든 Azure 및 Azure Marketplace 요금이 결합 청구서에 표시됩니다.  

### <a name="adjust-billing-frequency"></a>대금 청구 주기 변경

고객의 대금 청구 주기는 연간, 분기별 또는 월간입니다. 청구 기간은 고객이 계약서에 서명할 때 결정됩니다. 월간 청구는 가장 짧은 청구 간격입니다.

- 직접 등록의 청구 기간을 연간에서 분기별로 변경하려면 엔터프라이즈 관리자의 **승인**이 필요합니다. 간접 등록의 경우 파트너 관리자의 승인이 필요합니다. 현재 청구 분기가 끝나면 변경 내용이 적용됩니다.
- 청구 기간을 연간 또는 분기별에서 월간으로 변경하려면 계약에 대한 **수정**이 필요합니다.  기존 등록 청구 기간을 변경하려면 엔터프라이즈 관리자 또는 "대금 청구 담당자"의 승인이 필요합니다.
- 승인을 [Azure Enterprise Portal 지원](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)에 **제출**합니다. 발급 범주, 즉 **요금 청구 및 청구서 발부**를 선택합니다.

현재 청구 분기가 끝나면 변경 내용이 적용됩니다.

수정계약 M503에 서명한 경우 청구 주기를 월간 청구로 변경할 수 있습니다. 

### <a name="request-an-invoice-copy"></a>청구서 사본 요청

청구서 사본을 요청하려면 파트너에게 문의하세요.

## <a name="credits-and-adjustments"></a>크레딧 및 조정

[Azure Enterprise Portal](https://ea.azure.com)의 **보고서** 섹션에서 등록에 적용된 모든 크레딧 또는 조정을 볼 수 있습니다.

크레딧을 보려면,

1. [Azure Enterprise Portal](https://ea.azure.com)에서 **보고서** 섹션을 선택합니다.
1. **사용 요약**을 선택합니다.
1. 오른쪽 위 모서리에서 **M**을 **C** 보기로 변경합니다.
1. Azure 서비스 선불 테이블에서 조정 필드를 확장합니다.
1. 등록에 적용되는 크레딧과 간단한 설명이 표시됩니다. 예를 들면 다음과 같습니다. 서비스 수준 계약 크레딧이라는 설명이 표시될 수 있습니다.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Azure 선불을 사용하여 초과분 지불

Azure 선불을 초과분에 적용하려면 다음 조건을 충족해야 합니다.

- 약정 금액이 지불되지 않았으며 청구된 서비스 종료 날짜로부터 1년 이내에 초과분 요금이 발생했습니다.
- 사용 가능한 Azure 선불 금액에는 과거의 모든 미결제 Azure 청구서를 포함하여 발생한 전체 요금이 포함됩니다.
- 완료하려는 청구 기간은 완전히 종결되어야 합니다. 청구는 매월 5일 이후에 완전히 종결됩니다.
- 오프셋하려는 청구 기간은 완전히 종결되어야 합니다.
- ACD(Azure 선불 할인)는 실제 신규 선불에서 이전 사용량에 계획된 자금을 뺀 금액을 기준으로 합니다. 이 요구 사항은 발생한 초과분 요금에만 적용됩니다. 이는 Azure 선불을 사용하는 서비스에만 유효하므로 Azure Marketplace 요금에는 적용되지 않습니다. Azure Marketplace 요금은 별도로 청구됩니다.

초과분 오프셋을 완료하기 위해 사용자 또는 계정 팀에서 지원 요청을 열 수 있습니다. 엔터프라이즈 관리자 또는 대금 청구 담당자의 이메일 승인이 필요합니다.

## <a name="move-charges-to-another-enrollment"></a>다른 등록으로 요금 이동

사용량 데이터는 양도를 소급 적용하는 경우에만 이동됩니다. 다음과 같은 두 가지 옵션을 통해 한 등록에서 다른 등록으로 사용량 데이터를 이동할 수 있습니다.

- 한 등록에서 다른 등록으로 계정 양도
- 한 등록에서 다른 등록으로 등록 양도

두 옵션 모두 지원을 받으려면 [EA 지원 팀](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)에 지원 요청을 제출해야 합니다. 

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

사용량 데이터 보고서 다운로드에서 원시 리소스 사용량을 소수점 이하 최대 6 자릿수까지 확인할 수 있습니다. 그러나 Azure Enterprise Portal에 표시된 사용량 데이터는 선불 단위에 대해 소수점 이하 4 자릿수로 반올림되고 초과분 단위에 대해 소수점 이하 0 자릿수로 잘립니다. 원시 사용량 데이터는 Azure Enterprise Portal에서 사용되는 단위로 변환하기 전에 먼저 소수점 이하 4 자릿수로 반올림됩니다. 그런 다음, 변환된 엔터프라이즈 단위가 소수점 이하 4 자릿수로 다시 반올림됩니다. Azure Enterprise Portal 내에서가 아니라 사용량 보고서 다운로드에서 변환 이전의 실제 사용한 시간만 확인할 수 있습니다.

예를 들면 다음과 같습니다. 694.533404 실제 SQL Server 시간이 사용량 세부 정보 보고서에 보고되는 경우가 있습니다. 이러한 단위는 100 컴퓨팅 시간의 6.94533404로 변환된 다음, 6.9453으로 반올림되어 Azure Enterprise Portal에 표시됩니다.

- 확장 청구 금액을 결정하기 위해 표시된 단위에 선불 단가를 곱하고 결과를 소수점 이하 2 자릿수로 자릅니다. 일본 엔(JPY) 및 한국 원(KRW)의 경우 확장된 금액은 소수 0번째 자리로 반올림됩니다.
- 초과분의 경우 청구 단위를 소수점 이하 6 자릿수로 자른 다음, 초과분 단가를 곱하여 확장 청구 금액을 결정합니다.
- MSP(관리형 서비스 공급자) 청구의 경우 MSP로 표시된 부서와 관련된 모든 사용량은 EA 측정 단위로 변환된 다음, 소수점 이하 0 자릿수로 잘립니다. 결과적으로 이 사용량의 합계는 Azure Enterprise Portal에 보고된 모든 사용량의 총 합계보다 낮을 수 있습니다. 이는 MSP가 Azure 선불 잔액 내에 있는지, 아니면 초과분에 있는지에 따라 달라집니다.

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

동일한 데이터 센터 내에 있는 서비스 간의 데이터 송신에 대한 요금은 없습니다. 예를 들어 Microsoft 365 및 Azure입니다.

### <a name="azure-prepayment-and-unbilled-usage"></a>Azure 선불 및 청구되지 않은 사용량

Azure 선불은 Azure 서비스 선결제 금액입니다. 서비스를 사용하면 그에 따라 Azure 선불에서 차감됩니다. 자사 Azure 서비스는 Azure 선불에 대해 청구됩니다. 그러나 일부 요금은 별도로 청구되며, Azure Marketplace 서비스는 Azure 선불을 사용하지 않습니다.

### <a name="charges-billed-separately"></a>별도 청구 요금

타사에서 제공하는 일부 제품과 서비스는 Azure 선불을 사용하지 않습니다. 대신 이러한 항목에 대한 요금은 표준 청구 기간의 초과분 청구서의 일부로 별도로 청구됩니다.

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

## <a name="what-to-expect-after-change-of-channel-partner"></a>채널 파트너를 변경한 후에 필요한 항목

채널 파트너(COCP)의 변경이 월 중간에 발생하는 경우 고객은 이전에 연결된 파트너의 사용량에 대한 청구서와 새 파트너의 사용량에 대한 다른 청구서를 받게 됩니다.

청구서는 청구 기간이 끝난 다음달에 청구됩니다. 청구 주기가 매월인 경우 9월의 청구서가 10월에 두 파트너 모두에게 청구됩니다. 청구 주기가 분기별 또는 매년인 경우 고객은 이전 관련 파트너의 해당 기간 동안 사용량에 대한 청구서로 간주되고, 나머지는 청구 주기를 기준으로 새 파트너에게 청구됩니다.

## <a name="next-steps"></a>다음 단계

- 다음 Excel 파일은 Azure 서비스에 대한 세부 정보를 제공하며, 매월 6일 및 20일에 업데이트됩니다.

   | 제목 | Description | 파일 이름 |
   | --- | --- | --- |
   | [친숙한 서비스 이름](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | 모든 활성 서비스를 나열하고 다음을 포함합니다. <br>  <ul><li>서비스 범주</li>   <li>친숙한 서비스 이름</li>   <li>선불 이름 및 부품 번호</li> <li>사용량 이름 및 부품 번호</li>   <li>측정 단위</li>   <li>보고된 사용량과 표시된 Enterprise Portal 사용량 간의 변환 비율</li></ul> | Friendly\_Service\_Names.xlsx |
   | [서비스 다운로드 필드](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | 이 스프레드시트는 사용량 다운로드 보고서에서 가능한 모든 서비스 관련 필드 조합을 제공합니다. | Service\_Download\_Fields.xlsx |

- 청구서 및 요금을 이해하는 방법에 대한 자세한 내용은 [Azure 기업계약 요금의 이해](../understand/review-enterprise-agreement-bill.md)를 참조하세요.
- Azure Enterprise Portal을 시작하려면 [Azure EA Portal 시작](ea-portal-get-started.md)을 참조하세요.
