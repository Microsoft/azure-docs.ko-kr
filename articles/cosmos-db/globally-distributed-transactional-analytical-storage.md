---
title: Azure Cosmos 컨테이너에 대한 전역 분산 트랜잭션 및 분석 (비공개 미리 보기) 저장소
description: Azure Cosmos 컨테이너에 대한 트랜잭션 및 분석 저장소와 해당 구성 옵션에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 18cf43ba137c92fc00d5f8e82e13501d03b4b6a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445397"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos 컨테이너에 대한 전역 분산 트랜잭션 및 분석 저장소

Azure Cosmos 컨테이너는 내부적으로 두 개의 저장소 엔진 (트랜잭션 저장소 엔진) 및 업데이트 가능한 분석 저장소 엔진 (비공개 미리 보기)에서 지원 됩니다. 저장소 엔진은 모두 더 빠른 업데이트를 위해 로그 구조 및 쓰기에 최적화 되어 있습니다. 그러나 각 항목은 서로 다르게 인코딩됩니다.

* **트랜잭션 저장소 엔진** – 빠른 트랜잭션 읽기 및 쿼리를 위해 행 기반 형식으로 인코딩됩니다.

* **분석 저장소 엔진** -빠른 분석 쿼리 및 검색을 위해 칼럼 형식으로 인코딩됩니다.

![저장소 엔진 및 Azure Cosmos DB API 매핑](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

트랜잭션 저장소 엔진은 로컬 Ssd에서 지원 되는 반면 분석 저장소는 저렴 한 클러스터 SSD 저장소에 저장 됩니다. 다음 표에서는 트랜잭션과 분석 저장소 간의 주요 차이점을 캡처합니다.


|기능  |트랜잭션 저장소  |분석 저장소 |
|---------|---------|---------|
|Azure Cosmos 컨테이너 당 최대 저장소 |   제한 없음      |    제한 없음     |
|논리적 파티션 키 당 최대 저장소   |   10GB      |   제한 없음      |
|저장소 인코딩  |   내부 형식을 사용 하는 행 기반입니다.   |   Apache Parquet format을 사용 하는 열 기반. |
|저장소 위치 |   로컬/클러스터 간 Ssd에서 지원 되는 복제 된 저장소입니다. |  저렴 한 원격/오프 클러스터 Ssd에서 지원 되는 복제 된 저장소입니다.       |
|내구성  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|데이터에 액세스 하는 Api  |   SQL, MongoDB, Cassandra, Gremlin, Tables 및 etcd가 있습니다.       | Apache Spark         |
|보존 (TTL (time-to-live) 또는 TTL)   |  `DefaultTimeToLive` 속성을 사용 하 여 Azure Cosmos 컨테이너에 구성 된 정책 기반입니다.       |   `ColumnStoreTimeToLive` 속성을 사용 하 여 Azure Cosmos 컨테이너에 구성 된 정책 기반입니다.      |
|GB당 가격    |   [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|저장소 트랜잭션 가격    |  [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>트랜잭션 및 분석 저장소의 이점

### <a name="no-etl-operations"></a>ETL 작업 없음

일반적인 분석 파이프라인은 계산 및 저장소 계층에 대한 ETL (추출-변환-로드) 작업을 필요로 하는 여러 단계로 복잡 합니다. 이를 통해 복잡 한 데이터 처리 아키텍처가 생성 됩니다. 저장소 및 계산의 여러 단계에 대한 높은 비용을 의미 하 고, 다양 한 저장소 및 계산 단계 간에 대량 데이터를 전송 하기 때문에 대기 시간이 깁니다.  

Azure Cosmos DB를 사용 하 여 ETL 작업을 수행할 수 있는 오버 헤드가 없습니다. 각 Azure Cosmos 컨테이너는 트랜잭션 및 분석 저장소 엔진 둘 다에서 지원 되며, 트랜잭션 및 분석 저장소 엔진 간의 데이터 전송은 데이터베이스 엔진 내에서 수행 되 고 네트워크 홉 없이 수행 됩니다. 결과 대기 시간 및 비용은 기존 분석 솔루션에 비해 훨씬 더 낮습니다. 그리고 트랜잭션 및 분석 워크 로드 모두에 대해 전역적으로 분산 된 단일 저장소 시스템을 얻을 수 있습니다.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>여러 버전 저장, 업데이트 및 분석 저장소 쿼리

분석 저장소는 완전히 업데이트 가능 하며 Azure Cosmos 컨테이너에서 발생 한 모든 트랜잭션 업데이트의 전체 버전 기록을 포함 합니다.

트랜잭션 저장소에 대한 모든 업데이트는 30 초 이내에 분석 저장소에 표시 되도록 보장 됩니다. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>전역으로 분산 되 고 다중 마스터 분석 저장소

Azure Cosmos 계정이 단일 지역으로 범위가 지정 된 경우 컨테이너의 트랜잭션 및 분석 저장소에 저장 된 데이터도 단일 지역으로 범위가 지정 됩니다. 반면, Azure Cosmos 계정이 전역적으로 분산 된 경우 컨테이너에 저장 된 데이터도 전역적으로 분산 됩니다.

여러 쓰기 영역으로 구성 된 Azure Cosmos 계정에 대해 컨테이너 (트랜잭션 및 분석 저장소 둘 다)에 대한 쓰기는 항상 로컬 지역에서 수행 되므로 속도가 빠릅니다.

단일 쓰기 지역 (단일 마스터) 또는 다중 쓰기 영역 (다중 마스터 라고도 함)에 관계 없이 단일 또는 다중 지역 Azure Cosmos 계정에 대해 트랜잭션 및 분석 읽기/쿼리가 모두 지정 된 지역에서 로컬로 수행 됩니다.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>트랜잭션 및 분석 워크 로드 간의 성능 격리

지정 된 지역에서 트랜잭션 워크 로드는 컨테이너의 트랜잭션/행 저장소에 대해 작동 합니다. 반면에 분석 워크 로드는 컨테이너의 분석/열 저장소에 대해 작동 합니다. 두 저장소 엔진은 독립적으로 작동 하며 워크 로드 간에 엄격한 성능 격리를 제공 합니다.

트랜잭션 워크 로드는 프로 비전 된 처리량 (RUs)을 사용 합니다. 트랜잭션 워크 로드와 달리 분석 작업 처리량은 실제 사용량을 기준으로 합니다. 분석 워크 로드는 주문형 리소스를 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 ttl (Time to live)](time-to-live.md)
