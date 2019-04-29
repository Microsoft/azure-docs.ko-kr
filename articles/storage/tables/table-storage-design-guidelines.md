---
title: Azure Storage 테이블 디자인의 지침 | Microsoft Docs
description: 읽기 작업을 효율적으로 지원하도록 Azure Table service를 디자인합니다.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 04/23/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.component: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269850"
---
# <a name="guidelines-for-table-design"></a>테이블 디자인 지침

Azure Storage Table service와 함께 사용할 테이블을 디자인하는 것은 관계형 데이터베이스에 대한 디자인 고려 사항과 많이 다릅니다. 이 아티클에서는 효율적으로 읽고 쓰도록 Table service 솔루션을 디자인하는 지침을 설명합니다.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>효율적으로 읽을 수 있도록 Table service 솔루션 디자인

* ***읽기 작업이 많은 애플리케이션의 쿼리를 위해 디자인합니다.*** 테이블을 디자인할 때는 엔터티 업데이트 방법을 고려하기 전에 먼저 쿼리(특히 대기 시간이 중요한 쿼리)를 고려해야 합니다. 이는 일반적으로 솔루션의 효율성 및 성능에 영향을 줍니다.  
* ***쿼리에서 PartitionKey와 RowKey 둘 다 지정합니다.*** *지점 쿼리* 는 가장 효율적인 테이블 서비스 쿼리입니다.  
* ***엔터티의 중복 복사본을 저장하는 것이 좋습니다.*** Table Storage는 저렴하므로 여러 번 저장(다른 키를 사용하여)하여 쿼리의 효율성을 높이는 것이 좋습니다.  
* ***데이터를 비정규화하는 것이 좋습니다.*** Table Storage는 저렴하기 때문에 데이터를 비정규화하는 것이 좋습니다. 예를 들어 집계 데이터에 대한 쿼리에서 단일 엔터티에만 액세스하면 되도록 요약 엔터티를 저장합니다.  
* ***복합 키 값을 사용하는 것이 좋습니다.*** 사용하는 키는 **PartitionKey**와 **RowKey**뿐입니다. 예를 들어 복합 키 값을 사용하여 엔터티에 대한 대체 키 액세스 경로를 사용하도록 설정할 수 있습니다.  
* ***쿼리 프로젝션을 사용합니다.*** 필요한 필드만 선택하는 쿼리를 사용하여 네트워크를 통해 전송하는 데이터 양을 줄일 수 있습니다.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>효율적으로 쓸 수 있도록 Table service 솔루션 디자인  

* ***핫 파티션을 만들지 마세요.*** 언제든 여러 파티션으로 요청을 분산할 수 있는 키를 선택합니다.  
* ***트래픽 급증을 방지합니다.*** 적절한 기간에 걸쳐 트래픽을 원활하게 유지하고 트래픽 급증을 방지합니다.
* ***각 엔터티 유형에 대한 별도의 테이블을 만들 필요가 없습니다.*** 엔터티 유형 간에 원자성 트랜잭션이 필요한 경우 이러한 여러 엔터티 유형을 동일한 테이블의 동일한 파티션에 저장할 수 있습니다.
* ***달성해야 하는 최대 처리량을 고려합니다.*** Table service의 확장성 목표를 알고 디자인으로 인해 이러한 목표가 초과되지 않도록 해야 합니다.  

이 가이드에서는 이 모든 원칙을 실습해 볼 수 있는 예제를 제공합니다. 

## <a name="next-steps"></a>다음 단계

- [테이블 디자인 패턴](table-storage-design-patterns.md)
- [쿼리를 위한 디자인](table-storage-design-for-query.md)
- [테이블 데이터 암호화](table-storage-design-encrypt-data.md)
- [데이터 수정을 위한 디자인](table-storage-design-for-modification.md)