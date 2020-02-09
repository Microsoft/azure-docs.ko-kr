---
title: 포함 파일
description: 포함 파일
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67080917"
---
| **Resource** | **기본 제한** | **최대한도** |
| --- | --- | --- |
| 구독 당 지역별 Azure Batch 계정 | 1-3 |50 |
| Batch 계정당 전용 코어 | 90-900 | 지원 문의 |
| Batch 계정당 낮은 우선 순위의 코어 수 | 10-100 | 지원 문의 |
| Batch 계정 당 **[활성](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** 작업 및 작업 일정 (**완료** 된 작업에는 제한이 없음) | 100-300 | 1,000<sup>1</sup> |
| 배치 계정당 풀 | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> 기본 제한은 Batch 계정을 만드는 데 사용되는 구독 유형에 따라 달라집니다. 표시된 코어 할당량은 Batch 서비스 모드의 Batch 계정에 대한 할당량입니다. [배치 계정에서 할당량을 봅니다](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> 이 제한을 초과 하 여 증가를 요청 하려면 Azure 지원에 문의 하세요.
