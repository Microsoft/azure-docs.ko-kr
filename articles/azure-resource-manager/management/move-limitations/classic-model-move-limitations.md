---
title: Azure 클래식 배포 리소스 이동
description: Azure Resource Manager를 사용하여 클래식 배포 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75485287"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>클래식 배포 모델 리소스의 이동 지침

클래식 모델을 통해 배포된 리소스를 이동하는 단계는 리소스를 구독 내에서 이동하는지 또는 새 구독으로 이동하는지에 따라 다릅니다.

## <a name="move-in-the-same-subscription"></a>동일한 구독에서 이동

한 리소스 그룹에서 같은 구독 내 다른 리소스 그룹으로 리소스를 이동할 경우 다음 제한 사항이 적용됩니다.

* 가상 네트워크(클래식)는 이동할 수 없습니다.
* 가상 머신(클래식)은 클라우드 서비스로 이동해야 합니다.
* 클라우드 서비스는 이동에 모든 가상 머신이 포함된 경우에만 이동할 수 있습니다.
* 한 번에 하나의 클라우드 서비스만 이동할 수 있습니다.
* 한 번에 하나의 스토리지 계정(클래식)만 이동할 수 있습니다.
* Storage 계정(클래식)은 가상 머신 또는 클라우드 서비스와 같은 작업으로 이동할 수 없습니다.

클래식 리소스를 동일한 구독 내의 새 리소스 그룹으로 이동하려면 포털, Azure PowerShell, Azure CLI 또는 REST API를 통해 [표준 이동 작업](../move-resource-group-and-subscription.md)을 사용합니다. Resource Manager 리소스 이동을 위해 사용하는 동일한 작업을 사용합니다.

## <a name="move-across-subscriptions"></a>구독 간 이동

새 구독으로 리소스 이동 시 다음 제한 사항이 적용됩니다.

* 구독의 모든 클래식 리소스는 동일한 작업에서 이동해야 합니다.
* 대상 구독은 다른 어떠한 클래식 리소스도 포함할 수 없습니다.
* 이동은 클래식 이동에 대한 별도의 REST API를 통해서만 요청할 수 있습니다. 클래식 리소스를 새 구독으로 이동할 경우 표준 Resource Manager 이동 명령은 작동하지 않습니다.

클래식 리소스를 새 구독으로 이동하려면 클래식 리소스와 관련된 REST 작업을 사용합니다. REST를 사용하려면 다음 단계를 수행합니다.

1. 원본 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     요청 본문에 다음을 포함합니다.

   ```json
   {
    "role": "source"
   }
   ```

     유효성 검사 작업에 대한 응답은 다음 형식입니다.

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. 대상 구독이 구독 간 이동에 참여할 수 있는지 확인합니다. 다음 작업을 사용합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     요청 본문에 다음을 포함합니다.

   ```json
   {
    "role": "target"
   }
   ```

     응답이 원본 구독 유효성 검사와 동일한 형식입니다.
1. 두 구독이 유효성 검사를 통과하면 다음 작업으로 한 구독에서 다른 구독으로 모든 클래식 리소스를 이동합니다.

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    요청 본문에 다음을 포함합니다.

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

이 작업은 몇 분 정도 실행될 수 있습니다.

## <a name="next-steps"></a>다음 단계

클래식 리소스를 이동하는 데 문제가 있는 경우 [지원](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 담당자에게 문의하세요.

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../move-resource-group-and-subscription.md)을 참조하세요.
