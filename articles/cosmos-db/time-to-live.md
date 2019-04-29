---
title: TTL(Time To Live)을 사용한 Azure Cosmos DB의 데이터 만료
description: TTL을 사용하여 Microsoft Azure Cosmos DB는 일정 기간 후에 시스템에서 문서를 자동으로 삭제하는 기능을 제공합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729004"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB의에서 TTL ()를 지속 시간 

사용 하 여 **Time to Live** TTL, Azure Cosmos DB는 특정 시간이 지나면 자동으로 컨테이너에서 항목을 삭제 하는 기능을 제공 합니다. 기본적으로 컨테이너 수준에서 TTL(Time to Live)을 설정하거나 항목별 기준으로 값을 재정의할 수 있습니다. 컨테이너 또는 항목 수준에서 TTL을 설정하면 Azure Cosmos DB는 마지막으로 수정한 시간으로부터 해당 시간이 지나면 이러한 항목이 자동으로 제거합니다. TTL(Time to Live) 값은 초 단위로 구성됩니다. TTL을 구성할 때 클라이언트 응용 프로그램에서 명시적으로 발급 된 삭제 작업을 하지 않고도 TTL 값에 따라 만료 된 항목이 자동으로 삭제 합니다.

## <a name="time-to-live-for-containers-and-items"></a>컨테이너 및 항목에 대한 TTL(Time to live)

시간 제한 값 (초), 집합과 항목을 마지막으로 수정한 시간 델타로 해석 됩니다. TTL(Time to Live)은 컨테이너 또는 컨테이너 내 항목에서 설정할 수 있습니다.

1. **컨테이너에서 TTL(Time to Live)**(`DefaultTimeToLive` 사용하여 설정):

   - 누락(또는 null로 설정)되는 경우 항목이 자동으로 삭제되지 않습니다.

   - 표시 되 고 값을 "-1"로 설정 하면 무한대가 같은지 및 항목은 기본적으로 만료 되지 않습니다.

   - 표시 되 고 값을 특정 수로 설정 되어 있으면 *"n"* – 항목이 만료 됩니다 *"n"* 가 마지막으로 수정한 시간 후 시간 (초)입니다.

2. **항목에서 TTL(Time to Live)**(`ttl` 사용하여 설정):

   - 이 속성은 `DefaultTimeToLive`가 표시되고 부모 컨테이너에 대해 null로 설정되지 않은 경우에만 적용할 수 있습니다.

   - 표시되면 부모 컨테이너의 `DefaultTimeToLive` 값을 재정의합니다.

## <a name="time-to-live-configurations"></a>TTL(Time to Live) 구성

* TTL로 설정 되어 있으면 *"n"* 컨테이너를 한 다음 해당 컨테이너의 항목 후에 만료 됩니다 *n* 시간 (초)입니다.  자신의 시간이 live,-1 (만료 되지 않는 나타냄)로 설정 하는 동일한 컨테이너에 있는 항목이 또는 일부 항목 time to live 다른 번호로 설정 재정의 하는 경우 이러한 항목이 만료 될 경우 구성 된 TTL 값 자체에 기반 합니다. 

* 컨테이너에서 TTL을 설정하지 않으면, 이 컨테이너의 항목에서 TTL(Time to Live)은 효과가 없습니다. 

* 컨테이너에서 TTL이 -1로 설정된 경우 TTL(Time to Live)이 n으로 설정된 이 컨테이너의 항목은 n초 후에 만료되고 나머지 항목은 만료되지 않습니다. 

TTL을 기준으로 항목을 삭제하는 것은 무료입니다. TTL 만료의 결과로 항목을 삭제하는 경우 추가 비용은 청구되지 않습니다(즉, 추가 RU 없음).

## <a name="next-steps"></a>다음 단계

다음 문서에서 Time to Live 구성 하는 방법에 알아봅니다.

* [TTL(Time to Live) 구성 방법](how-to-time-to-live.md)
