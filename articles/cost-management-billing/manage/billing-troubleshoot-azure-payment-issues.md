---
title: Azure 결제 문제 해결
description: Microsoft Azure Portal이나 계정 센터에서 지불 정보 계정을 업데이트할 때 발생하는 문제를 해결합니다.
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: jaserano
ms.openlocfilehash: adc3874ba211de270589d8ea36a04b9431e9e12a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684918"
---
# <a name="troubleshoot-azure-payment-issues"></a>Azure 결제 문제 해결

Microsoft Azure Portal 또는 Azure 계정 센터에서 지불 정보 계정을 업데이트하려고 할 때 문제 또는 오류가 발생할 수 있습니다.

문제를 해결하려면 오류와 가장 유사한 아래 항목을 선택합니다.

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>Azure에 가입하려고 할 때 신용 카드가 거부되었습니다.

거부된 카드와 관련된 문제를 해결하려면 [Azure 가입 시 거부된 카드 문제 해결](troubleshoot-declined-card.md)을 참조하세요.

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>내 계정에서 결제 방법을 업데이트할 구독을 볼 수 없음

구독에 사용한 이메일과 다른 이메일 ID를 사용 중일 수 있습니다.

이 문제를 해결하려면 [Azure Portal 또는 Azure 계정 센터에 대한 구독을 찾을 수 없음 로그인 오류](no-subscriptions-found.md)를 참조하세요.

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>가상 또는 선불 신용 카드 또는 직불 카드를 지불 방법으로 사용할 수 없습니다.

*   가상 카드 또는 선불 신용 카드는 Azure 구독의 지불 옵션으로 허용되지 않습니다.
*   직불 카드는 Azure 구독의 지불 옵션으로 허용되지 않습니다.

자세한 내용은 [Azure 가입 시 거부된 카드 문제 해결](troubleshoot-declined-card.md)을 참조하세요.

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>저장된 청구 지불 방법에서 신용 카드를 제거할 수 없음

기본적으로, 활성 구독에서 신용 카드를 제거할 수 없습니다.

기존 카드를 삭제해야 하는 경우에는 새 카드를 구독에 추가해야 이전 결제 방법을 삭제할 수 있습니다. 아니면 구독을 취소해야 합니다. 그러면 구독이 영구적으로 삭제되고 카드가 제거됩니다.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>새 결제 방법을 추가한 후 이전 결제 방법을 삭제할 수 없음

새 결제 방법이 구독에 연결되지 않은 것일 수 있습니다. 결제 방법을 구독과 연결하는 데 도움이 되는 [Azure에 대한 신용 카드 추가, 업데이트 또는 제거](change-credit-card.md)를 참조하세요.

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>*결제 방법을 삭제할 수 없음* 오류 메시지로 인해 결제 방법을 삭제할 수 없음

이 문제는 미결 잔액 때문에 발생합니다. 미결제 잔액을 정리한 후에 결제 방법을 삭제해 보세요.

## <a name="unable-to-make-payment-for-a-subscription"></a>구독에 대한 결제를 수행할 수 없음

다음과 같은 오류 메시지가 표시되는 경우에는, *결제 기한이 지났습니다. 결제 방법에 문제가 있습니다* 또는 *죄송합니다. 이 정보를 저장할 수 없습니다. 브라우저를 닫고 다시 시도하세요.* ,금융 기관에서 카드를 거부하여 카드에 결제 보류 중인 내역이 남아 있기 때문일 수 있습니다.

신용 카드로 결제할 수 있는 잔액이 충분한 지 확인하십시오. 그렇지 않으면, 다른 카드를 사용하여 결제하거나 금융 기관에 연락하여 문제를 해결하십시오.

다음 문제는 은행에 확인하십시오.

- 국제 거래가 활성화되지 않았습니다.
- 카드에 신용 한도가 있으며 잔액을 정산해야 합니다.
- 카드에 반복 결제가 활성화되어 있습니다.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>브라우저 문제로 인해 결제 방법을 변경할 수 없음(브라우저가 응답하지 않거나 로드되지 않는 등)

모든 활성 Azure 세션에서 로그아웃한 다음, [Microsoft Edge에서 InPrivate 검색 문서](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate)의 단계에 따라 Microsoft Edge나 Internet Explorer 내에서 InPrivate 세션을 시작합니다.

프라이빗 세션에서 [신용 카드 변경 방법](change-credit-card.md)의 단계에 따라 신용 카드 정보를 업데이트하거나 변경합니다.

다음을 수행해 볼 수도 있습니다.

- 브라우저 새로 고침
- 다른 브라우저 사용
- 캐시된 쿠키 삭제

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>결제 방법을 업데이트한 후에도 구독이 비활성 상태임

이 문제는 미결 잔액 때문에 발생합니다. 미결제 잔액을 정리한 후에 결제 방법을 삭제해 보세요.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>XML 오류 응답 페이지 때문에 결제 방법을 변경할 수 없음

[Azure Portal](https://portal.azure.com/)을 사용하여 새 신용 카드를 추가하는 경우 이 메시지가 표시됩니다.

카드 세부 정보를 추가하려면 계정 관리자의 이메일 주소를 사용하여 Azure 계정 포털에 로그인합니다.

## <a name="additional-help-resources"></a>추가 도움말 리소스

Azure 청구 및 구독에 대한 기타 문제 해결 문서

- [거부된 카드](troubleshoot-declined-card.md)
- [구독 로그인 문제](troubleshoot-sign-in-issue.md)
- [구독을 찾을 수 없음](no-subscriptions-found.md)
- [Enterprise 비용 보기 사용 안 함](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>도움이 필요한 경우 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- [Azure Billing 설명서](../../billing/index.md)
