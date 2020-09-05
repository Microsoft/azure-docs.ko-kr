---
title: 데이터 수정을 위해 Azure Table storage 디자인 | Microsoft Docs
description: Azure 테이블 저장소에서 데이터를 수정 하기 위한 테이블을 디자인 합니다. 삽입, 업데이트 및 삭제 작업을 최적화 합니다. 저장 된 엔터티의 일관성을 유지 합니다.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236763"
---
# <a name="design-for-data-modification"></a>데이터 수정을 위한 디자인
이 아티클에서는 삽입, 업데이트 및 삭제를 최적화하기 위한 디자인 고려 사항을 중점적으로 알아봅니다. 관계형 데이터베이스의 디자인과 마찬가지로(관계형 데이터베이스의 경우 디자인 장단점을 관리하는 기술이 다름), 쿼리에 최적화된 디자인과 데이터 수정에 최적화된 디자인 간의 장단점을 평가해야 하는 경우가 있을 수 있습니다. 테이블 디자인 패턴 섹션은 Table service에 대한 몇 가지 자세한 디자인 패턴을 알아보고 이러한 패턴의 일부 장단점을 설명합니다. 실제로 엔터티 쿼리에 최적화된 디자인은 대부분 엔터티를 수정하는 데에도 효율적입니다.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>삽입, 업데이트 및 삭제 작업 성능 최적화
엔터티를 업데이트하거나 삭제하려면 **PartitionKey** 및 **RowKey** 값을 사용하여 엔터티를 식별할 수 있어야 합니다. 이러한 점에서, 엔터티 수정을 위해 **PartitionKey** 및 **RowKey**를 선택할 때는 가급적 효율적으로 엔터티를 식별할 수 있어야 하므로 지점 쿼리를 지원하기 위해 선택한 것과 유사한 조건을 따라야 합니다. 업데이트하거나 삭제해야 하는 **PartitionKey** 및 **RowKey** 값을 찾기 위해 비효율적인 파티션 또는 테이블 검색을 사용하여 엔터티를 찾고 싶지는 않을 것입니다.  

테이블 디자인 패턴 섹션의 다음 패턴은 성능 또는 삽입, 업데이트, 삭제 작업의 최적화를 다룹니다.  

* [대용량 삭제 패턴](table-storage-design-patterns.md#high-volume-delete-pattern) - 동시 삭제할 모든 엔터티를 고유한 별도의 테이블에 저장하여 대용량 엔터티 삭제를 지원합니다. 테이블을 삭제하면 엔터티가 삭제됩니다.  
* [데이터 계열 패턴](table-storage-design-patterns.md#data-series-pattern) - 전체 데이터 계열을 단일 엔터티에 저장하여 요청 수를 최소화합니다.  
* [넓은 엔터티 패턴](table-storage-design-patterns.md#wide-entities-pattern) - 여러 실제 엔터티를 사용하여 속성이 252개가 넘는 논리적 엔터티를 저장합니다.  
* [큰 엔터티 패턴](table-storage-design-patterns.md#large-entities-pattern) - Blob Storage를 사용하여 큰 속성 값을 저장합니다.  

## <a name="ensure-consistency-in-your-stored-entities"></a>저장된 엔터티의 일관성 유지
데이터 수정을 최적화하기 위한 키 선택에 영향을 주는 다른 주요 요소는 원자성 트랜잭션을 사용하여 일관성을 유지하는 방법입니다. EGT는 동일한 파티션에 저장된 엔터티에서만 작동합니다.  

[테이블 디자인 패턴](table-storage-design-patterns.md) 아티클의 다음 패턴은 일관성 관리를 다룹니다.  

* [파티션 간 보조 인덱스 패턴](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - 서로 다른 **RowKey** 값을 사용하여 각 엔터티의 여러 복사본을 동일한 파티션에 저장하여 빠르고 효율적인 조회를 지원하며, 서로 다른 **RowKey** 값을 사용하여 대체 정렬 순서를 허용합니다.  
* [파티션 간 보조 인덱스 패턴](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - 서로 다른 **RowKey** 값을 사용하여 각 엔터티의 여러 복사본을 별도의 파티션과 별도의 테이블에 저장하여 빠르고 효율적인 조회를 지원하며, 서로 다른 RowKey 값을 사용하여 대체 정렬 순서를 허용합니다.  
* [결과적으로 일관성 있는 트랜잭션 패턴](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - Azure 큐를 사용하여 파티션 경계 또는 스토리지 시스템 경계 간에 결과적으로 일관성 있는 동작을 지원합니다.
* [인덱스 엔터티 패턴](table-storage-design-patterns.md#index-entities-pattern) -인덱스 엔터티를 유지 관리 하 여 엔터티 목록을 반환 하는 효율적인 검색을 가능 하 게 합니다.  
* [비정규화 패턴](table-storage-design-patterns.md#denormalization-pattern) - 관련 데이터를 단일 엔터티에 함께 통합하여 단일 지점 쿼리로 필요한 모든 데이터를 검색할 수 있습니다.  
* [데이터 계열 패턴](table-storage-design-patterns.md#data-series-pattern) - 전체 데이터 계열을 단일 엔터티에 저장하여 요청 수를 최소화합니다.  

엔터티 그룹 트랜잭션에 대 한 자세한 내용은 [엔터티 그룹 트랜잭션](table-storage-design.md#entity-group-transactions)섹션을 참조 하세요.  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>효율적인 수정을 위한 디자인이 효율적인 쿼리에도 유용
대부분의 경우 효율적인 쿼리를 위한 디자인은 효율적인 수정으로 이어지지만 항상 특정 시나리오에 이 사항이 적용되는지 평가해야 합니다. [테이블 디자인 패턴](table-storage-design-patterns.md) 아티클의 일부 패턴은 엔터티 쿼리와 수정 간의 장단점을 명시적으로 평가하므로 항상 각 작업 유형 수를 고려해야 합니다.  

[테이블 디자인 패턴](table-storage-design-patterns.md) 아티클의 다음 패턴은 효율적인 쿼리를 위한 디자인과 효율적인 데이터 수정을 위한 디자인 간의 장단점을 다룹니다.  

* [복합 키 패턴](table-storage-design-patterns.md#compound-key-pattern) - 복합 **RowKey** 키를 사용하여 클라이언트에서 단일 지점 쿼리로 관련 데이터를 조회하도록 할 수 있습니다.  
* [로그 테일 패턴](table-storage-design-patterns.md#log-tail-pattern) - 날짜 및 시간 역순으로 정렬된 *RowKey* 값을 사용하여 가장 최근에 파티션에 추가된 **n** 엔터티를 검색합니다.  
