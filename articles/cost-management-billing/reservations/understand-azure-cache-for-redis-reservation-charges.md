---
title: 예약 할인이 Azure Cache for Redis에 적용되는 방법 이해 | Microsoft Docs
description: 예약 할인이 Azure Cache for Redis 인스턴스에 적용되는 방법을 알아봅니다.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 10fedaf5680976ae93984c9b2c607882f650019d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213272"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>예약 할인이 Azure Cache for Redis에 적용되는 방법

Azure Cache for Redis 예약된 용량을 구입하면 예약 할인이 예약의 특성 및 수량과 일치하는 캐시 인스턴스에 자동으로 적용됩니다. 예약에는 Azure Cache for Redis의 컴퓨팅 비용만 포함됩니다. 정상 요금으로 스토리지 및 네트워킹에 대한 요금이 청구됩니다. 예약된 용량은 [프리미엄 계층](/https://docs.microsoft.com/azure/azure-cache-for-redis/quickstart-create-redis) 캐시에만 사용할 수 있습니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 ***use-it-or-lose-it*** 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간은 소멸됩니다.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Azure Cache for Redis에 적용되는 할인

Azure Cache for Redis 예약된 용량 할인은 시간 단위로 캐시에 적용됩니다. 구입한 예약은 실행되는 캐시에서 내보낸 컴퓨팅 사용량과 일치합니다. 이러한 캐시에서 전체 시간을 실행하지 않으면 해당 예약이 예약 특성과 일치하는 다른 캐시에 자동으로 적용됩니다. 할인은 동시에 실행되는 캐시에 적용될 수 있습니다. 예약 특성과 일치하는 전체 시간 동안 실행되는 캐시가 없는 경우 해당 시간에 대한 예약 할인의 모든 혜택을 받을 수 없습니다.

다음 예제에서는 Azure Cache for Redis 예약된 용량 할인이 구입한 캐시 수와 실행 시간에 따라 적용되는 방법을 보여 줍니다.

* **예제 1**: 6GB 캐시에 해당하는 Azure Cache for Redis 예약된 용량을 구입합니다. 예약의 나머지 특성과 일치하는 13GB 캐시를 실행하는 경우 7GB Azure Cache for Redis 컴퓨팅 사용량에 대한 종량제 가격이 청구되고 나머지 6GB 캐시 컴퓨팅 사용량에 대해 시간당 예약 할인이 적용됩니다.

이러한 예제의 나머지 부분에서는 구입하는 Azure Cache for Redis 예약된 용량이 26GB 캐시에 해당하며, 나머지 예약 특성이 실행되는 캐시와 일치한다고 가정합니다.

* **예제 2**: 한 시간 동안 두 개의 13GB 캐시를 실행합니다. 26GB 예약 할인이 두 캐시의 컴퓨팅 사용량에 적용됩니다.

* **예제 3**: 오후 1시부터 오후 1시 30분까지 하나의 26GB를 실행합니다. 오후 1시 30분부터 오후 2시까지 다른 하나의 26GB 캐시를 실행합니다. 둘 다 예약 할인에 포함됩니다.

* **예제 4**: 오후 1시부터 오후 1시 45분까지 하나의 26GB를 실행합니다. 오후 1시 30분부터 오후 2시까지 다른 하나의 26GB 캐시를 실행합니다. 15분의 중복에 대한 종량제 가격으로 요금이 청구됩니다. 예약 할인은 나머지 시간에 대한 컴퓨팅 사용량에 적용됩니다.

청구 사용량 보고서에서 Azure 예약의 애플리케이션을 이해하고 보려면 [Azure 예약 사용량 이해](/azure/billing/billing-understand-reserved-instance-usage-ea)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처
질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
