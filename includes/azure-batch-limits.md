---
title: 포함 파일
description: 포함 파일
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776378"
---
| **리소스** | **기본 제한** | **최대 한도** |
| --- | --- | --- |
| 지역당 구독 당 azure Batch 계정 | 1-3 |50 |
| Batch 계정당 전용 코어 | 10-100 | 해당 없음<sup>1</sup> |
| Batch 계정당 낮은 우선 순위의 코어 수 | 10-100 | 해당 없음<sup>2</sup> |
| Batch 계정당 활성 작업 및 작업 일정<sup>3</sup> | 100-300 | 1,000<sup>4</sup> |
| Batch 계정당 풀 | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> 기본 제한은 Batch 계정을 만드는 데 사용되는 구독 유형에 따라 달라집니다. 표시된 코어 할당량은 Batch 서비스 모드의 Batch 계정에 대한 할당량입니다. [배치 계정에서 할당량을 봅니다](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>Batch 계정당 전용된 코어 수를 늘릴 수 있지만 최대 수가 지정 되지 않았습니다. 옵션을 논의 하려면 증가, Azure 지원에 문의 합니다.

<sup>2</sup>Batch 계정당 낮은 우선 순위 코어 수를 늘릴 수 있지만 최대 수가 지정 되지 않았습니다. 옵션을 논의 하려면 증가, Azure 지원에 문의 합니다.

<sup>3</sup>완료 된 작업 및 작업 일정은 아닙니다.

<sup>4</sup>이 제한 초과 증가 요청 하려면 Azure 지원에 문의 합니다.
