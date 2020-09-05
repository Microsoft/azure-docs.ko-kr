---
title: 크기 조정 서버 그룹-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 서버 그룹 메모리, 디스크 및 CPU 리소스를 조정 하 여 증가 된 부하 처리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 85a1f0dcc2e778a09cf0d19b2a85d6faf371f032
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134526"
---
# <a name="server-group-size"></a>서버 그룹 크기

Citus (Hyperscale) 배포 옵션은 협동 데이터베이스 서버를 사용 하 여 쿼리 실행을 병렬화 하 고 더 많은 데이터를 저장 합니다. 서버 그룹 "크기"는 서버 수와 각각의 하드웨어 리소스를 나타냅니다.

## <a name="picking-initial-size"></a>처음 크기 선택

노드 수와 하드웨어 용량 면에서 서버 그룹의 크기를 쉽게 변경할 수 있습니다 ([아래 참조](#scale-a-hyperscale-citus-server-group)). 그러나 새 서버 그룹에 대 한 초기 크기를 선택 해야 합니다. 적절 한 선택에 대 한 몇 가지 팁은 다음과 같습니다.

### <a name="multi-tenant-saas-use-case"></a>다중 테 넌 트 SaaS 사용 사례

기존 단일 노드 PostgreSQL 데이터베이스 인스턴스에서 Citus (Hyperscale)로 마이그레이션하는 경우 작업자 vCores 수와 총 RAM이 원본 인스턴스의 수와 같은 클러스터를 선택 하는 것이 좋습니다. 이러한 시나리오에서는 분할 리소스 사용률을 향상 시키고 더 작은 인덱스를 허용 하기 때문에 2 ~ 3 배 성능이 개선 되었습니다.

코디네이터 노드에 필요한 vCores 수는 기존 작업 (쓰기/읽기 처리량)에 따라 달라 집니다. 코디네이터 노드에는 작업자 노드 만큼 RAM이 필요 하지 않지만, RAM 할당은 하이퍼 [확장 구성 옵션](concepts-hyperscale-configuration-options.md)에 설명 된 대로 vcore 수를 기준으로 결정 되므로 vcore 수는 본질적으로 실제 결정입니다.

### <a name="real-time-analytics-use-case"></a>실시간 분석 사용 사례

총 vCores: 작업 데이터가 RAM에 적합 한 경우 작업자 코어 수에 비례하여 Citus (Hyperscale)의 선형 성능 향상을 예측할 수 있습니다. 요구 사항에 적합 한 vCores 수를 결정 하려면 단일 노드 데이터베이스의 쿼리에 대 한 현재 대기 시간 및 Hyperscale의 필요한 대기 시간 (Citus)을 고려 하세요. 현재 대기 시간을 원하는 대기 시간으로 나누고 결과를 반올림합니다.

작업자 RAM: 최상의 사례는 대부분의 작업 집합이 메모리에 맞는 충분한 메모리를 제공하는 것입니다. 응용 프로그램에서 사용 하는 쿼리 형식은 메모리 요구 사항에 영향을 줍니다. 쿼리에 대해 설명 하는 분석을 실행 하 여 필요한 메모리 양을 확인할 수 있습니다. VCores 및 RAM은 [Hyperscale 구성 옵션](concepts-hyperscale-configuration-options.md) 문서에 설명 된 대로 함께 확장 됩니다.

## <a name="scale-a-hyperscale-citus-server-group"></a>Citus (Hyperscale) 서버 그룹 크기 조정

Citus (Azure Database for PostgreSQL-Hyperscale)는 늘어난 부하를 처리 하기 위한 셀프 서비스 크기 조정을 제공 합니다. Azure Portal를 사용 하면 새 작업자 노드를 쉽게 추가 하 고 기존 노드의 vCores를 늘릴 수 있습니다. 노드를 추가 하면 가동 중지 시간이 발생 하지 않으며 쿼리를 중단 하지 않고 분할를 새 노드 (분할 된 [균형 재조정](#rebalance-shards)이라고 함)로 이동 하기도 합니다.

### <a name="add-worker-nodes"></a>작업자 노드 추가

노드를 추가 하려면 Citus (Hyperscale) 서버 그룹의 **구성** 탭으로 이동 합니다.  **작업자 노드 수** 에 대 한 슬라이더를 끌면 값이 변경 됩니다.

![리소스 슬라이더](./media/howto-hyperscale-scaling/01-sliders-workers.png)

**저장** 단추를 클릭 하 여 변경 된 값이 적용 되도록 합니다.

> [!NOTE]
> 증가 하 고 저장 한 후에는 슬라이더를 사용 하 여 작업자 노드 수를 줄일 수 없습니다.

#### <a name="rebalance-shards"></a>분할 리 밸런스

새로 추가 된 노드를 활용 하려면 분산 테이블 [분할](concepts-hyperscale-distributed-data.md#shards)의 균형을 다시 조정 해야 합니다 .이는 일부 분할을 기존 노드에서 새 노드로 이동 하는 것을 의미 합니다. 먼저 새 작업자의 프로 비전이 성공적으로 완료 되었는지 확인 합니다. 그런 다음 psql을 사용 하 여 클러스터 코디네이터 노드에 연결 하 고 다음을 실행 하 여 분할 된 분할을 시작 합니다.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards`함수는 해당 인수에서 이라는 테이블의 [공동 배치](concepts-hyperscale-colocation.md) 그룹에 있는 모든 테이블의 균형을 다시 조정 합니다. 따라서 분산 된 모든 테이블에 대해 함수를 호출할 필요는 없으며, 각 공동 배치 그룹의 대표 테이블에 대해이 함수를 호출 하기만 하면 됩니다.

### <a name="increase-or-decrease-vcores-on-nodes"></a>노드에서 vCores 증가 또는 감소

> [!NOTE]
> 이 기능은 현재 미리 보기로 제공됩니다. 서버 그룹의 노드에 대 한 vCores 변경을 요청 하려면 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

새 노드를 추가 하는 것 외에도 기존 노드의 기능을 늘릴 수 있습니다. 계산 용량을 늘리거나 줄일 수 있는 것은 성능 실험 뿐만 아니라 트래픽 요구에 대 한 단기 또는 장기 변경에 유용할 수 있습니다.

모든 작업자 노드에 대 한 vCores를 변경 하려면 **구성 (작업자 노드당)** 에서 **vcores** 슬라이더를 조정 합니다. 코디네이터 노드의 vCores는 독립적으로 조정할 수 있습니다. **구성 (코디네이터 노드)** 에서 **vcores** 슬라이더를 조정 합니다.

## <a name="next-steps"></a>다음 단계

- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보세요.
