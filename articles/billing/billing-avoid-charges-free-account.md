---
title: Azure 체험 계정에 대한 요금 청구 방지 | Microsoft Docs
description: Azure 무료 계정에 요금이 표시되는 이유를 이해합니다. 이러한 요금을 방지하는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 7bb8e4f53dd07a613ab4b9ff05600a22f76a4ba7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617857"
---
# <a name="avoid-getting-charged-for-your-azure-free-account"></a>Azure 무료 계정에 대한 요금 청구 방지

Azure 체험 계정은 처음 30일 동안 $200의 Azure 크레딧과 12개월 동안 무료 서비스라는 제한된 수량을 제공합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요. 크레딧 상태에 따라 크레딧을 사용하거나 체험 서비스 및 수량을 초과한 사용량에 대한 요금이 청구될 수 있습니다.

## <a name="you-still-have-active-azure-free-account-credit"></a>아직 활성 Azure 무료 계정 크레딧이 있음 
아직 만료되지 않은 Azure 크레딧이 있는 경우 Azure에서는 체험 서비스 및 수량을 초과한 사용량에 대한 요금을 지불하기 위해 크레딧을 사용합니다.

## <a name="your-credit-ran-out-or-has-expired"></a>크레딧이 부족하거나 만료됨
크레딧이 부족하거나 30일이 만료된 경우 Azure는 구독을 비활성화합니다. Azure 서비스를 계속 사용하려면 구독을 종량제 구독으로 업그레이드해야 합니다. 자세한 내용은 [평가판 Azure 구독을 종량제로 업그레이드](billing-upgrade-azure-subscription.md)를 참조하세요. 업그레이드한 후에도 구독은 12개월 동안 체험 서비스에 액세스할 수 있습니다. 체험 서비스 및 수량을 초과한 사용량에 대해서만 요금이 청구됩니다.

Azure 무료 계정에 요금이 부과되는 몇 가지 이유를 알아보겠습니다.

### <a name="your-usage-exceeds-the-limits-of-free-services-included-with-your-azure-free-account"></a>사용량이 Azure 체험 계정에 포함된 체험 서비스 한도를 초과했습니다.

Azure 무료 계정으로 매월 제한된 양의 무료 서비스를 얻습니다. 사용 가능한 양은 해당 월이 끝날 때 만료되고 다음 달로 이월되지 않습니다. 예를 들어 매월 5GB의 File Storage를 얻는다고 가정합니다. 한 달에 2GB만 사용할 경우 나머지 3GB는 다음 달로 이월되지 않습니다. 요금이 청구되는 것을 방지하려면 사용량을 한도 내로 유지합니다. 무료 서비스의 한도에 대한 내용은 [Azure 무료 계정이란 무엇인가요?](https://azure.microsoft.com/free/free-account-faq/)를 참조하세요. 무료 서비스 사용량을 확인하려면 [Azure 무료 계정에 포함된 무료 서비스 사용량 확인](billing-check-free-service-usage.md)을 참조하세요.

### <a name="youre-using-services-not-included-for-free-with-your-azure-free-account"></a>Azure 체험 계정에서 체험용으로 포함되지 않은 서비스를 사용하고 있습니다.

Azure 체험 계정에 체험용으로 포함되지 않은 서비스를 사용할 경우 종량제 요금이 청구됩니다. 무료 계정에 포함되는 서비스에 대한 자세한 내용은 [Azure 무료 계정이란 무엇인가요?](https://azure.microsoft.com/free/free-account-faq/)를 참조하세요. Azure Portal에서 또는 Azure 사용량 파일을 통해 사용하고 있는 서비스의 사용량을 확인할 수 있습니다. 자세한 내용은 [Portal에서 비용 분석 및 진행 속도를 정기적으로 확인](billing-getting-started.md#costs) 및 [계정 센터에서 사용량 다운로드](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. 

### <a name="youve-reached-the-end-of-your-12-months-free-services"></a>12개월 체험 서비스가 끝났습니다.

체험 서비스 및 수량이 12개월 후에 만료됩니다. Azure Portal에서 무료 서비스가 만료되는 시기를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 탐색 영역에서 **모든 서비스**를 선택합니다.

3.  **구독**을 선택합니다.

4.  무료 계정을 등록할 때 만든 구독을 선택합니다.

5.  아래로 스크롤하여 체험 서비스 그리드를 찾습니다. 그리드의 왼쪽 위에 있는 도구 설명을 클릭합니다.

![체험 계정 혜택 만료 시기를 확인할 수 있는 위치를 보여주는 스크린샷](./media/billing-avoid-charges-free-account/freeaccount-benefits-expiration-date.png)


체험 서비스 및 수량이 만료되면 Azure에서는 사용자에게 사용 중인 모든 서비스에 대해 종량제 요금을 청구합니다. Azure Portal을 사용하여 사용하지 않는 서비스에 대한 리소스를 삭제할 수 있습니다. 모든 Azure 서비스를 사용하지 않으려는 경우 [구독을 취소](billing-how-to-cancel-azure-subscription.md)할 수 있습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.
