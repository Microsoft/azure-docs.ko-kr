---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9000cab026ec08bd78d8a8f334c8967a8a747c95
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202295"
---
지금은 premium Ssd만 공유 디스크를 사용 하도록 설정할 수 있습니다. 이 기능을 지 원하는 디스크 크기는 P15 이상입니다. 다른 디스크 크기의 `maxShares` 제한이 있을 수 있으며이는 `maxShares` 값을 설정할 때 초과할 수 없습니다.

각 디스크에 대해 디스크를 동시에 공유할 수 있는 최대 노드 수를 나타내는 `maxShares` 값을 정의할 수 있습니다. 예를 들어 2 노드 장애 조치 (failover) 클러스터를 설정 하려는 경우 `maxShares=2`를 설정 합니다. 최대값은 상한 값입니다. 노드 수가 지정 된 `maxShares` 값 보다 적으면 노드가 클러스터를 연결 하거나 탈퇴할 수 있습니다 (디스크 탑재 또는 분리).

> [!NOTE]
> 디스크가 모든 노드에서 분리 된 경우에만 `maxShares` 값을 설정 하거나 편집할 수 있습니다.

다음 표에서는 디스크 크기에 `maxShares`에 대해 허용 되는 최대 값을 보여 줍니다.

|디스크 크기  |maxShares 제한  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

디스크에 대 한 IOPS 및 대역폭 제한은 `maxShares` 값의 영향을 받지 않습니다. 예를 들어 P15 디스크의 최대 IOPS는 maxShares = 1 또는 maxShares > 1 인지 여부를 1100 합니다.