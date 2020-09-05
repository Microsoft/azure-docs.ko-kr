---
title: 포함 파일
description: 포함 파일
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 2a35180e4c5ca26d53be135718e345f9657af6a2
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88655115"
---
| **리소스** | **기본 제한** | **최대 한도** |
| --- | --- | --- |
| 구독당 지역별 Azure Batch 계정 | 1-3 |50 |
| Batch 계정당 전용 코어 | 90-900 | 지원에 문의 |
| Batch 계정당 낮은 우선 순위의 코어 수 | 10-100 | 지원에 문의 |
| Batch 계정당 **[활성](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 작업 및 작업 일정(**완료된** 작업에는 제한이 없음) | 100-300 | 1,000<sup>1</sup> |
| Batch 계정당 풀 | 20-100 | 500<sup>1</sup> |

<sup>1</sup> 이 한도를 초과하여 늘리려면 Azure 지원에 문의합니다.

> [!IMPORTANT]
> 전용 할당량을 요청하고 관리하는 방법을 변경하는 중입니다.  총 전용 vCPU 수는 현재 강제 적용된 값이며, VM 시리즈당 전용 할당량을 곧 적용할 예정입니다. 낮은 우선 순위 할당량은 총 한도에 따라 계속 적용되며, VM 시리즈에 의해 적용되지 않습니다.

> [!NOTE]
> 기본 제한은 Batch 계정을 만드는 데 사용되는 구독 유형에 따라 달라집니다. 표시된 코어 할당량은 Batch 서비스 모드의 Batch 계정에 대한 할당량입니다. [배치 계정에서 할당량을 봅니다](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> 전 세계 감염병 대유행 기간 동안 용량을 보다 효율적으로 관리하기 위해 일부 지역의 신규 Batch 계정과 일부 유형의 구독에 대한 기본 코어 할당량이 위의 값 범위에서 줄었으며, 일부 경우 0개 코어로 줄었습니다. 새 Batch 계정을 만들 때 [코어 할당량을 확인](../articles/batch/batch-quota-limit.md#view-batch-quotas)하고 필요한 경우 [코어 할당량 증가를 요청](../articles/batch/batch-quota-limit.md#increase-a-quota)합니다. 또는 이미 할당량이 충분한 배치 계정을 다시 사용하는 것이 좋습니다. 
