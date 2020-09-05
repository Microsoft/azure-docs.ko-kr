---
title: Azure의 미지불금액 이메일
description: Azure 구독에 미지불금액이 있는 경우 결제 방법에 대해 설명합니다.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 8c792ae1b6ac43ead15fd4f27d2468c517153354
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684816"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Azure 구독의 미지불금액 해결

이 문서는 신용 카드로 Azure online에 등록하고 Microsoft Online Services 프로그램 청구 계정이 있는 고객에게 적용됩니다. [청구 계정 유형을 확인](#check-the-type-of-your-account)하는 방법을 알아보세요. Microsoft 고객 계약 청구 계정이 있는 경우, 대신 [Microsoft Azure에 대한 청구서 지불](../understand/pay-bill.md)을 참조하세요.

지불이 수신되지 않거나 지불을 처리할 수 없는 경우 이메일을 받게 되고 Azure Portal에서 구독 기한이 지난 것을 알리는 경고가 표시됩니다. 기본 결제 방법이 신용 카드인 경우 [계정 관리자](billing-subscription-transfer.md#whoisaa)는 Azure Portal에서 미납된 요금을 납부할 수 있습니다. 청구서로 지불하는 경우(수표/전신 송금) 청구서 맨 아래에 기재된 위치로 결제 금액을 송금합니다.

> [!IMPORTANT]
> * 동일한 신용 카드를 사용하는 구독이 여러 개 있고 모두 기한이 초과된 경우 전체 미결 잔액을 한 번에 지불해야 합니다.
> * 미납된 요금을 납부하는 데 사용하는 신용 카드는 실패한 결제 방법을 사용하는 모든 구독에 대한 새로운 기본 결제 방법이 됩니다.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Azure Portal에서 미지불액 해결

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.
1. **개요** 페이지에서 기한이 지난 구독을 선택합니다.
1. **구독 개요** 페이지에서 빨간색 연체 배너를 클릭하여 잔액을 결제합니다.
    > [!NOTE]
    > 계정 관리자가 아닌 경우 잔액을 결제할 수 없습니다.
1. 새 **잔액 결제** 페이지에서 **결제 방법 선택**을 클릭합니다.
    ![결제 방법 선택 링크를 보여 주는 스크린샷](./media/resolve-past-due-balance/settle-balance-screen.png)

1. 오른쪽의 새 블레이드에 있는 드롭다운에서 신용 카드를 선택하거나 파란색 **새 결제 방법 추가** 링크를 클릭하여 새 카드를 추가합니다. 이 신용 카드는 현재 실패한 결제 방법을 사용하는 모든 구독에 대해 활성 결제 방법이 됩니다.
     > [!NOTE]
     > * 총 미결 잔액은 실패한 결제 방법을 사용하는 모든 Microsoft 서비스에 대해 미납된 요금을 반영합니다.
     > * 선택한 결제 방법에도 Microsoft 서비스에 대한 미납된 요금이 청구되는 경우 총 미결 잔액에 반영됩니다. 이러한 미납된 요금도 지불해야 합니다.
1. **지불**을 클릭합니다.

## <a name="troubleshoot-declined-credit-card"></a>거부된 신용 카드 문제 해결

금융 기관에서 신용 카드를 거부한 경우 금융 기관에 연락하여 문제를 해결해야 합니다. 은행에 다음 사항을 확인하세요.
- 해당 카드에서는 국제 거래가 활성화되어 있습니다.
- 카드에는 잔액을 지급하기 위한 충분한 신용 한도액 또는 자금이 있습니다.
- 카드에 반복 결제가 활성화되어 있습니다.

## <a name="not-getting-billing-email-notifications"></a>청구 전자 메일 알림을 받고 있지 않으면?

계정 관리자인 경우 [알림에 사용되는 전자 메일 주소를 확인합니다](change-azure-account-profile.md). 정기적으로 확인하는 전자 메일 주소를 사용하는 것이 좋습니다. 전자 메일이 올바른 경우 스팸 폴더를 확인합니다.

## <a name="if-i-forget-to-pay-what-happens"></a>지불하는 것을 잊으면 어떻게 될까요?

서비스는 취소되고 리소스는 더 이상 사용할 수 없게 됩니다. 귀하의 Azure 데이터는 서비스가 종료된 후 90일 후에 삭제됩니다. 자세한 내용은 [Microsoft 보안 센터 - 데이터 관리 방법](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)을 참조하세요.

결제가 처리되었으나 구독이 여전히 사용할 수 없게 설정된 경우 [Azure 지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하세요.

## <a name="check-the-type-of-your-account"></a>계정 유형 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
