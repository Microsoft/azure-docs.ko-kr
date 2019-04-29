---
title: Azure 무료 서비스 사용량 모니터링 및 추적 | Microsoft Docs
description: 무료 서비스 사용량을 확인하는 방법을 알아봅니다. Azure Portal 및 사용량 csv를 사용합니다.
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
ms.openlocfilehash: 4e940a12cd57ef136cfd9ead298f9afcd2d6ad1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617799"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Azure 무료 계정에 포함된 무료 서비스의 사용량 확인 

이러한 무료 서비스의 제한을 초과하지 않는 한, Azure 체험 계정에 무료로 포함된 서비스 요금은 청구되지 않습니다. 제한을 초과하지 않으려면 Azure Portal 또는 사용량 파일을 사용하여 무료 서비스의 사용량을 모니터링하고 추적할 수 있습니다. 

## <a name="check-usage-on-the-azure-portal"></a>Azure Portal에서 사용량 확인

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

2.  왼쪽 탐색 영역에서 **모든 서비스**를 선택합니다.

3.  **구독**을 선택합니다.

4.  무료 계정을 등록할 때 만든 구독을 선택합니다.

    ![모든 구독을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  개요 섹션에는 구독 ID, 제안 유형 및 구독 이름과 같은 구독에 대한 중요한 정보가 표시됩니다. 또한 무료 계정 크레딧이 만료되는 시기에 대한 정보도 찾을 수 있습니다.

    ![구독의 중요 정보를 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  현재와 예상 비용에 대한 정보를 찾으려면 아래로 스크롤합니다. 비용에는 체험 계정에 포함되지 않는 서비스의 사용량과 무료 서비스 한계를 초과하는 사용량에 대한 요금이 포함됩니다. 

    ![구독 비용 정보를 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  개요 섹션의 마지막 부분에는 무료 서비스 사용량에 대한 표가 제공됩니다. 

    ![무료 서비스의 사용량을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    표에는 다음과 같은 열이 있습니다.

* **측정기 이름:** 사용 중인 측정기에 대한 측정 단위를 식별합니다. 서비스-측정기 매핑에 대한 자세한 내용은 [무료 서비스-측정기 매핑 이해](billing-understand-free-service-meter-mapping.md)를 참조하세요.
* **사용/한도:** 현재 월의 사용량 및 측정기의 제한입니다. 이 정보는 상태 표시줄에서 찾을 수 있습니다.
* **상태:** 측정기의 사용 상태입니다. 사용 패턴에 따라, 다음 상태 중 하나일 수 있습니다.
  * **사용 중이 아님:** 측정기를 사용하지 않았거나 측정기의 사용량이 청구 시스템에 연결되지 않았습니다.
  * **\<Date>에 초과됨:** \<Date>에 측정기 제한에 도달했습니다.
  * **초과할 가능성이 낮음:** 측정기의 제한을 초과할 가능성이 낮습니다.
  * **\<Date>에 초과:** \<Date>에 측정기 제한에 도달할 가능성이 높습니다.

## <a name="check-usage-through-the-usage-file"></a>사용량 파일을 통해 사용량 확인

사용량 파일은 Azure 구독에 대한 세부적인 정보를 제공합니다. Azure 계정 센터에서 월별 및 하루 사용량 파일을 다운로드할 수 있습니다. 사용량 파일을 다운로드하는 방법을 알아보고 필요한 액세스 권한을 이해하려면 [청구서 및 사용량 가져오기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. 사용량 파일의 열에 대한 자세한 내용은 [사용량에서 용어 이해](billing-understand-your-usage.md)를 참조하세요.

사용량 파일에는 무료 및 유료 서비스에 대한 사용량 정보가 있습니다. 무료 서비스 측정기에서 측정기 이름 끝에는 **Free**가 표시됩니다. 무료 측정기를 찾으려면 Excel에서 해당 파일을 열고 **측정기 범주 열**에서 **- Free** 텍스트가 있는 셀을 필터링합니다(텍스트 필터 &rarr; 필터 포함 사용)&nbsp;.

![무료 서비스의 사용량을 보여 주는 스크린샷](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.