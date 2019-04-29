---
title: Azure Stream Analytics의 검사점 및 재생 작업 복구 개념
description: 이 문서에서는 Azure Stream Analytics의 검사점 및 재생 작업 복구 개념에 대해 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9dcfbd4b5fcc8462c88b16f585424166ecd3d499
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61361895"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업의 검사점 및 재생 개념
이 문서에서는 Azure Stream Analytics의 내부 검사점 및 재생 개념과 이러한 개념이 작업 복구에 미치는 영향에 대해 설명합니다. Stream Analytics 작업이 실행될 때마다 상태 정보가 내부적으로 유지 관리됩니다. 이러한 상태 정보는 정기적으로 검사점에 저장됩니다. 일부 시나리오에서는 작업 실패 또는 업그레이드가 발생하는 경우 검사점 정보가 작업을 복구하는 데 사용됩니다. 다른 상황에서는 검사점을 복구에 사용할 수 없으며 재생이 필요합니다.

## <a name="stateful-query-logicin-temporal-elements"></a>temporal 요소의 상태 저장 쿼리 논리
Azure Stream Analytics 작업의 고유한 기능 중 하나는 기간 이동 집계, 임시 조인 및 임시 분석 함수 등과 같은 상태 저장 처리를 수행하는 것입니다. 작업이 실행될 때 이러한 각 연산자는 상태 정보를 유지합니다. 이러한 쿼리 요소의 최대 시간 범위는 7일입니다. 

임시 시간 범위 개념은 몇 가지 Stream Analytics 쿼리 요소에 나타납니다.
1. 시간 범위 이동 집계(텀블링, 호핑 및 슬라이딩 시간 범위의 GROUP BY)

2. 임시 조인(DATEDIFF가 있는 JOIN)

3. 임시 분석 함수(LIMIT DURATION이 있는 ISFIRST, LAST 및 LAG)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>노드 실패로부터의 작업 복구(OS 업그레이드 포함)
Stream Analytics 작업이 실행될 때마다 여러 작업자 노드에서 작업을 수행할 수 있도록 내부적으로 확장됩니다. 각 작업자 노드의 상태는 몇 분마다 검사점이 지정되어 실패 발생 시 시스템을 복구하는 데 도움이 됩니다.

때때로 지정된 작업자 노드가 실패하거나 해당 작업자 노드에 대해 운영 체제 업그레이드가 발생할 수 있습니다. 자동으로 복구하기 위해 Stream Analytics는 새 정상 노드를 획득하고, 이전 작업자 노드의 상태가 사용 가능한 최신 검사점에서 복원됩니다. 작업을 다시 시작하려면 검사점이 작성된 시점의 상태를 복원하는 데 약간의 재생이 필요합니다. 일반적으로 복원 간격은 단지 몇 분입니다. 작업에 필요한 만큼 충분한 스트리밍 단위가 선택되면 재생을 신속하게 완료해야 합니다. 

완전 병렬 쿼리에서 작업자 노드가 실패한 후 캐치업하는 데 걸리는 시간은 다음과 비례합니다.

[입력 이벤트 속도] × [간격 길이] / [처리 파티션의 수]

노드 실패 및 OS 업그레이드로 인해 상당한 처리 지연이 관찰되는 경우 쿼리를 완전히 병렬로 수행하는 것이 좋으며 더 많은 스트리밍 단위를 할당할 수 있도록 작업의 크기를 조정합니다. 자세한 내용은 [처리량을 높이도록 Azure Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.

이러한 종류의 복구 프로세스가 수행되는 경우 현재 Stream Analytics에서는 보고서가 표시되지 않습니다.

## <a name="job-recovery-from-a-service-upgrade"></a>서비스 업그레이드에서 작업 복구 
Microsoft는 경우에 따라 Azure 서비스에서 Stream Analytics 작업을 실행하는 이진 파일을 업그레이드합니다. 이 때 사용자의 실행 중인 작업이 최신 버전으로 업그레이드되고 작업이 자동으로 다시 시작됩니다. 

현재 복구 검사점 형식은 업그레이드 간에 유지되지 않습니다. 결과적으로 스트리밍 쿼리의 상태는 재생 기술을 사용하여 완전히 복원해야 합니다. Stream Analytics 작업에서 이전과 똑같은 입력을 재생할 수 있도록 하려면 원본 데이터에 대한 보존 정책을 쿼리의 시간 범위 이상으로 설정해야 합니다. 이렇게 하지 않으면 원본 데이터가 전체 시간 범위를 포함할 만큼 충분한 기간 동안 다시 유지되지 않을 수 있으므로 서비스 업그레이드 중에 잘못되었거나 부분적인 결과가 발생할 수 있습니다.

일반적으로 필요한 재생의 크기는 시간 범위에 평균 이벤트 속도를 곱한 값에 비례합니다. 예를 들어, 초당 1,000개 이벤트의 입력 속도로 수행되는 작업의 경우 1시간을 초과하는 시간 범위는 큰 재생 크기를 갖는 것으로 간주됩니다. 상태를 초기화하려면 최대 1시간 분량의 데이터를 다시 처리해야 할 수 있으므로 확장된 기간 동안 지연된 출력(출력 없음)을 발생할 수 있는 완전하고 올바른 결과를 생성할 수 있습니다. `JOIN` 또는 `LAG`처럼 시간 범위 없는 쿼리나 다른 임시 연산자는 0의 재생을 갖습니다.

## <a name="estimate-replay-catch-up-time"></a>재생 캐치업 시간 예측
서비스 업그레이드로 인한 지연 시간을 예측하려면 다음 방법을 따를 수 있습니다.

1. 충분한 데이터가 포함된 입력 Event Hub를 로드하여 예상 이벤트 속도로 쿼리의 가장 큰 시간 범위를 처리합니다. 이벤트의 타임스탬프는 라이브 입력 피드인 것처럼 해당 기간 동안 벽 시계 시간에 가까워야 합니다. 예를 들어 쿼리에 3일 시간 범위가 있는 경우 이벤트를 3일 동안 Event Hub로 보내고 계속 이벤트를 보냅니다. 

2. **지금**을 시작 시간으로 사용하여 작업을 시작합니다. 

3. 시작 시간과 첫 번째 출력이 생성되는 시점 사이의 시간을 측정합니다. 시간은 서비스 업그레이드 중에 작업이 지연되는 시간을 대략적으로 나타냅니다.

4. 지연 시간이 너무 길면 작업을 분할하고 SU 수를 늘려 로드가 더 많은 노드로 분산되도록 합니다. 또는 쿼리의 시간 범위를 줄이고, 다운스트림 싱크(예: Azure SQL 데이터베이스 사용)의 Stream Analytics 작업에서 생성되는 출력에 대해 추가 집계 또는 다른 상태 저장 처리를 수행하는 것이 좋습니다.

중요 업무용 작업을 업그레이드하는 동안 일반적인 서비스 안정성 문제가 발생하면 쌍을 이루는 Azure 지역에서 중복 작업을 실행하는 것이 좋습니다. 자세한 내용은 [서비스 업데이트 도중 Stream Analytics 작업 안정성 보장](stream-analytics-job-reliability.md)을 참조하세요.

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>사용자가 시작한 중지 및 시작에서 작업 복구
스트리밍 작업에서 쿼리 구문을 편집하거나 입력 및 출력을 조정하려면, 작업 디자인을 변경하고 업그레이드하기 위해 작업을 중지해야 합니다. 이러한 시나리오에서 사용자가 스트리밍 작업을 중지하고 다시 시작하는 경우 복구 시나리오는 서비스 업그레이드와 비슷합니다. 

사용자가 시작한 작업 다시 시작에는 검사점 데이터를 사용할 수 없습니다. 이러한 다시 시작 중에 출력 지연을 예측하려면 이전 섹션에서 설명한 것과 동일한 절차를 사용하고, 지연이 너무 길면 비슷한 완화를 적용합니다.

## <a name="next-steps"></a>다음 단계
안정성 및 확장성에 대한 자세한 내용은 다음 문서를 참조하세요.
- [자습서: Azure Stream Analytics 작업에 대한 경고 설정](stream-analytics-set-up-alerts.md)
- [처리량을 높이도록 Azure Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
- [서비스 업데이트 도중 Stream Analytics 작업 안정성 보장](stream-analytics-job-reliability.md)
