---
title: Azure 청구서 이해
description: Azure 구독에 대한 사용량 및 청구를 읽고 이해하는 방법에 대해 알아봅니다.
author: bandersmsft
ms.reviewer: jureid
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b7b115f909c57470bfbdab9cc44e6a258f85b8f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685921"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Microsoft Azure 청구서의 조건 이해

청구서는 요금에 대한 요약을 제공하고 결제에 대한 지침을 제공합니다. [Azure Portal](https://portal.azure.com/)에서 .pdf(Portable Document Format)로 다운로드하고 전자 메일을 통해 전송할 수 있습니다. 자세한 내용은 [Azure 청구서 및 일간 사용량 현황 데이터를 받는 방법](../manage/download-azure-invoice-daily-usage-date.md)을 참조하세요.

주의할 몇 가지 사항:

-   체험 평가판 구독을 사용하는 경우 Azure Portal에서 일별 사용 현황 정보를 얻을 수 있으나 청구서는 없습니다.

-   이전 청구 기간이 끝날 때 최대 24시간 동안의 사용량이 현재 청구서에 표시될 수 있습니다.

-   전 세계 고객용 청구 명세서에 나열된 요금은 예측 용도로만 사용합니다. 은행이 전환율에 대해 서로 다른 비용을 부과하기 때문입니다.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>청구서에 대한 자세한 용어 및 설명
다음 섹션에서는 청구서에 표시되는 중요한 용어와 각 용어에 대한 설명을 제공합니다.

### <a name="account-information"></a>계정 정보

청구서의 계정 정보 섹션은 첫 번째 페이지의 맨위에 있으며 사용자 프로필 및 구독에 대한 정보를 보여줍니다.

![청구서의 계정 정보 섹션](./media/understand-invoice/account-information-section.png)

| 용어 | Description |
| --- | --- |
| 고객 PO 번호 |추적을 위해 사용자에 의해 할당된 선택적 구매 주문 번호 |
| 청구서 번호 |추적을 위해 사용할 고유하게 생성된 Microsoft 청구서 번호 |
| 청구 주기 |이 청구서에 해당되는 날짜 범위 |
| 청구서 날짜 |청구서를 만든 날짜이며 일반적으로 청구 주기 종료 다음날 |
| 결제 방법 |계정에 사용되는 지불 유형(청구서 또는 신용 카드) |
| 청구지 |계정에 나열된 청구 주소 |
| 구독 제안(“종량제”) |구매한 구독 제안의 유형(종량제, BizSpark Plus, Azure Pass 등). 자세한 내용은 [Azure 제품 형식](https://azure.microsoft.com/support/legal/offer-details/)을 참조하세요. |
| 계정 소유자 메일 | Microsoft Azure 계정이 등록된 계정 전자 메일 주소입니다. <br /><br />전자 메일 주소를 변경하려면 [연락처 메일, 주소 및 전화 번호와 같은 Azure 계정의 프로필 정보를 변경하는 방법](../manage/change-azure-account-profile.md)을 참조하세요. |

### <a name="understand-the-invoice-summary"></a>청구서 요약 이해
청구서의 **청구서 요약** 섹션은 마지막 청구 기간 이후의 트랜잭션 총량 및 현재 사용 요금을 나열합니다.

![청구서 요약 섹션](./media/understand-invoice/invoice-summary-section.png)

구독 이름(&quot;프로덕션 스토리지&quot;)은 이 청구서에 대한 구독의 이름입니다.

#### <a name="understand-the-previous-charges"></a>이전 요금 이해
청구서의 이전 잔액, 지불 및 미지불 잔액 섹션에는 마지막 청구 기간 이후의 트랜잭션이 요약됩니다.

| 용어 | Description |
| --- | --- |
| 이전 잔액 |최근 청구 기간의 총 금액 |
| 지불 |최근 청구 기간에 적용되는 총 지불액 및 크레딧 |
| 미결제 잔액(이전 청구 주기) |최근 청구 기간 이후 계정의 크레딧 및 잔여 금액 |

#### <a name="understand-the-current-charges"></a>현재 요금 이해
청구서의 현재 요금 섹션은 현재 청구 기간 동안 월별 요금에 대한 세부 정보를 표시합니다.

| 용어 | Description |
| --- | --- |
| 사용 요금 |사용 요금은 현재 청구 기간 동안 구독에 대한 총 월별 요금입니다.|
| 할인 |현재 청구 기간에 적용되는 서비스 할인입니다.|
| 조정 |현재 청구 기간에 적용된 기타 크레딧(체험 사용량, 크레딧 등) 또는 미불 요금입니다.<br/><br/>예를 들어 Visual Studio Enterprise with MSDN 제품을 사용하는 경우 월별 크레딧이 표시됩니다. 구독을 취소하면 구독 제안을 통해 얻을 수 있는 월별 크레딧을 초과하는 월별 사용량 요금이 표시됩니다. 현재 청구 기간의 시작부터 구독 취소 날짜까지 요금이 청구됩니다. |

#### <a name="sold-to-and-payment-instructions"></a>구매처 및 지불 관련 지침

다음 표에서는 청구서의 두 번째 페이지에 표시되는 구매처 및 지불 관련 지침에 대해 설명합니다.

| 용어 |Description |
| --- | --- |
| 구매처 |계정에 있는 프로필 주소입니다. <br/><br/>이 주소를 변경하려면 [연락처 메일, 주소 및 전화 번호와 같은 Azure 계정의 프로필 정보를 변경하는 방법](../manage/change-azure-account-profile.md)을 참조하세요.|
| 지불 관련 지침 |결제 방법(예: 신용 카드 또는 청구서)에 따른 비용 지불 방법에 대한 지침입니다. |

#### <a name="usage-charges"></a>사용 요금

청구서의 사용 요금 섹션은 사용자의 요금에 대한 미터 수준 정보를 표시합니다.

![사용 요금 섹션](./media/understand-invoice/usage-charges-section.png)

다음 표에서는 청구서에 표시된 사용 요금 열 헤더를 설명합니다.

| 용어 |Description |
| --- | --- |
| 속성 |사용 현황의 최상위 서비스를 식별합니다. |
| Type |요율에 영향을 줄 수 있는 Azure 서비스 유형을 정의합니다. |
| 리소스 |사용 중인 미터에 대한 측정 단위를 식별합니다. |
| 지역 |데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. |
| 사용 |청구 기간 동안 사용된 미터의 양입니다. |
| 포함 |현재 청구 기간 동안 무료로 제공되는 미터의 양입니다. |
| 청구 대상 |소비된 수량과 포함된 수량 간의 차이를 나타냅니다. 이 수량에 대해 요금이 청구됩니다. 수량이 제품에 포함되지 않는 종량제 제품의 경우 이 합계는 사용량과 동일합니다. |
| 비용 |청구 가능 단위당 청구되는 요율입니다. |
| 값 |평균 수량 열과 요율 열을 곱한 결과를 보여 줍니다. 소비된 수량이 포함된 수량을 초과하지 않으면 이 열에 청구되는 비용이 없습니다. |
| 소계 |이 청구 기간 동안 모든 세전 요금의 합계 |
| 총합계 |이 청구 기간 동안 모든 세후 요금의 합계 |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>청구서의 요금이 맞는지 확인하려면 어떻게 할까요?
청구서의 요금에 대한 더 자세한 내용을 보려는 경우 [Microsoft Azure의 청구서 이해](review-individual-bill.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
