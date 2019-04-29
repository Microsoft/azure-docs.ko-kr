---
title: Azure Database for PostgreSQL의 쿼리 저장소 모범 사례
description: 이 문서에서는 Azure Database for PostgreSQL의 쿼리 저장소에 대한 모범 사례를 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: af68d5cdc891a8c924ca493d8e798edaa4670ab5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563175"
---
# <a name="best-practices-for-query-store"></a>쿼리 저장소의 모범 사례

**적용 대상:** Azure Database for PostgreSQL 9.6 및 10

이 문서에서는 Azure Database for PostgreSQL에서 쿼리 저장소를 사용하는 모범 사례를 설명합니다.

## <a name="set-the-optimal-query-capture-mode"></a>최적의 쿼리 캡처 모드 설정
쿼리 저장소를 사용하여 중요한 데이터를 캡처할 수 있습니다. 

|**pg_qs.query_capture_mode** | **시나리오**|
|---|---|
|_모두_  |모든 쿼리 및 쿼리 실행 빈도와 기타 통계 측면에서 워크로드를 철저하게 분석합니다. 워크로드의 새 쿼리를 식별합니다. 임시 쿼리는 사용자나 자동 매개 변수화에 대 한 기회를 식별 하는 데 사용 되어 검색 합니다. _모두_는 리소스 사용 비용이 증가합니다. |
|_상위_  |클라이언트에서 실행한 상위 쿼리에 집중합니다.
|_없음_ |조사하려는 쿼리 집합 및 시간 범위를 이미 캡처했으며 다른 쿼리 때문에 발생할 수 있는 방해 요소를 제거하고 싶은 경우에 사용합니다. _없음_은 환경 테스트 및 벤치마킹에 적합합니다. _없음_을 사용하면 중요한 새 쿼리를 추적하고 최적화하는 기회를 놓칠 수 있으므로 주의해서 사용해야 합니다. 지나간 시간 범위의 데이터를 복구할 수 없습니다. |

쿼리 저장소 역시 대기 통계에 대한 저장소를 포함할 수 있습니다. 대기 통계를 제어하는 추가 캡처 모드 쿼리 **pgms_wait_sampling.query_capture_mode**가 있으며 _없음_ 또는 _모두_로 설정할 수 있습니다. 

> [!NOTE] 
> **pg_qs.query_capture_mode**는 **pgms_wait_sampling.query_capture_mode**를 대체합니다. pg_qs.query_capture_mode가 _없음_인 경우 pgms_wait_sampling.query_capture_mode 설정은 영향을 미치지 않습니다. 


## <a name="keep-the-data-you-need"></a>필요한 데이터 유지
**pg_qs.retention_period_in_days** 매개 변수는 쿼리 저장소의 데이터 보존 기간을 일 단위로 지정합니다. 오래된 쿼리 및 통계 데이터는 삭제됩니다. 기본적으로 쿼리 저장소는 데이터를 7일간 보존하도록 구성됩니다. 사용할 계획이 없는 기록 데이터를 보존하지 마세요. 데이터를 더 오래 보존해야 하는 경우 값을 높이세요.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>대기 통계 샘플링 빈도 설정 
**pgms_wait_sampling.history_period** 매개 변수는 대기 이벤트 샘플링 빈도를 밀리초 단위로 지정합니다. 기간이 짧을수록 샘플링 횟수가 많아집니다. 더 많은 정보를 검색하지만, 리소스 사용 비용이 증가합니다. 서버의 부하에 여유가 있거나 세분성이 필요 없는 경우이 기간을 늘리세요.


## <a name="get-quick-insights-into-query-store"></a>신속하게 쿼리 저장소에 대한 인사이트 얻기
Azure Portal에서 [Query Performance Insight](concepts-query-performance-insight.md)를 사용하여 신속하게 쿼리 저장소의 데이터에 대한 인사이트를 얻을 수 있습니다. 시간이 지남에 따라 가장 오래 실행되는 쿼리와 가장 긴 대기 이벤트가 시각화에 노출됩니다.

## <a name="next-steps"></a>다음 단계
- [Azure Portal](howto-configure-server-parameters-using-portal.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용하여 매개 변수를 가져오거나 설정하는 방법을 알아봅니다.