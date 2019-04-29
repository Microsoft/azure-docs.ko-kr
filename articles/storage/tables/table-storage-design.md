---
title: Azure Table Storage에서 확장형 및 영구 테이블을 디자인합니다. | Microsoft Docs
description: Azure Table Storage에서 확장형 및 영구 테이블을 디자인합니다.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848285"
---
# <a name="design-scalable-and-performant-tables"></a>확장 가능하고 성능이 우수한 테이블 설계

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

확장형 및 영구 테이블을 디자인하려면 성능, 확장성, 비용 등의 요소를 고려해야 합니다. 이전에 관계형 데이터베이스의 스키마를 디자인해 본 적이 있는 경우 이러한 고려 사항에 익숙하겠지만 Azure Table service 저장소 모델과 관계형 모델 간에는 일부 유사한 점이 있는 반면 중요한 차이점도 있습니다. 이러한 차이점으로 인해 일반적으로 관계형 데이터베이스에 익숙한 사용자에게는 직관에 반하거나 잘못된 것으로 보일 수 있지만 Azure Table service와 같은 NoSQL 키/값 저장소를 디자인하는 경우에는 적절한 다른 디자인이 도출됩니다. 디자인 차이점의 대부분은 Table service는 수십억 개의 데이터 엔터티(관계 데이터베이스 용어로는 행)를 포함할 수 있거나 대용량 트랜잭션을 지원해야 하는 데이터 집합에 사용되는 클라우드 규모의 애플리케이션을 지원하도록 디자인된다는 사실을 반영합니다. 따라서 데이터를 저장하는 방법을 다르게 생각하고 Table service의 작동 방식을 이해해야 합니다. 잘 디자인된 NoSQL 데이터 저장소는 관계형 데이터베이스를 사용하는 솔루션보다 적은 비용으로 훨씬 뛰어난 확장성을 제공할 수 있습니다. 이 가이드에서는 이러한 항목에 대해 설명합니다.  

## <a name="about-the-azure-table-service"></a>Azure Table service 정보
이 섹션에서는 특히 성능 및 확장성 디자인과 관련이 있는 Table service의 몇 가지 주요 기능을 중점적으로 살펴봅니다. Azure Storage와 Table service를 처음 접하는 경우 이 아티클의 나머지 내용을 진행하기 전에 먼저 [Microsoft Azure Storage 소개](../../storage/common/storage-introduction.md) 및 [.NET을 사용하여 Azure Table Storage 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)을 읽어 보시기 바랍니다. 이 가이드는 Table service에 중점을 두지만 Azure 큐 및 Blob service를 설명하고 Table service에서 사용할 수 있는 방법에 대해서도 다룹니다.  

Table service란? 이름에서 알 수 있듯이, Table service에서는 테이블 형식을 사용하여 데이터를 저장합니다. 표준 용어로, 테이블의 각 행은 엔터티를 나타내고 행은 해당 엔터티의 여러 속성을 저장합니다. 모든 엔터티에는 고유하게 식별하는 키 쌍 및 Table service가 엔터티를 마지막으로 수정한 시간을 추적하는 데 사용하는 타임스탬프 열이 있습니다. 타임스탬프는 자동으로 적용되고 임의의 값으로 타임스탬프를 수동으로 덮어쓸 수 없습니다. Table service에서는 LMT(마지막 수정 타임스탬프)를 사용하여 낙관적 동시성을 관리합니다.  

> [!NOTE]
> 또한 Table service REST API 작업은 LMT에서 파생되는 **ETag** 값도 반환합니다. 이 문서에서는 ETag와 LMT를 상호 교환적으로 사용합니다. 이러한 용어는 동일한 기본 데이터를 의미하기 때문입니다.  
> 
> 

다음 예제에서는 직원 및 부서 엔터티를 저장하는 간단한 테이블 디자인을 보여 줍니다. 이 가이드의 뒷부분에 나오는 예제는 대부분 이 간단한 디자인을 기반으로 합니다.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>타임 스탬프</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>department</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


지금까지 이 데이터는 필수 열이 있고 여러 엔터티 유형을 동일한 테이블에 저장할 수 있다는 점을 제외하고는 관계형 데이터베이스의 테이블과 유사해 보입니다. 또한 **FirstName** 또는 **Age**와 같은 각 사용자 정의 속성에 관계형 데이터베이스의 열과 마찬가지로 정수 또는 문자열과 같은 데이터 형식이 있습니다. 관계형 데이터베이스와 달리 Table service는 스키마 없음 속성을 갖추고 있기 때문에 각 엔터티의 데이터 유형이 동일하지 않아도 됩니다. 복잡한 데이터 형식을 단일 속성에 저장하려면 JSON 또는 XML과 같은 직렬화된 형식을 사용해야 합니다. 지원되는 데이터 형식, 지원되는 날짜 범위, 명명 규칙, 크기 제약 조건 등 테이블 서비스에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해](https://msdn.microsoft.com/library/azure/dd179338.aspx)를 참조하세요.

**PartitionKey** 및 **RowKey** 선택은 적절한 테이블 디자인의 기본 사항입니다. 테이블에 저장된 모든 엔터티에는 고유하게 조합된 **PartitionKey**와 **RowKey**가 있어야 합니다. 관계형 데이터베이스 테이블의 키와 마찬가지로 **PartitionKey** 및 **RowKey** 값을 인덱싱하여 빠른 조회를 사용하도록 설정하기 위해 클러스터형 인덱스를 만듭니다. 그러나 Table service는 보조 인덱스를 만들지 않습니다. 따라서 **PartitionKey** 및 **RowKey**는 인덱싱된 속성입니다. [테이블 디자인 패턴](table-storage-design-patterns.md)에 설명된 일부 패턴은 이 명백한 제한 사항을 해결할 수 있는 방법을 보여줍니다.  

테이블이 하나 이상의 파티션으로 구성되므로 디자인 의사 결정은 대부분 적절한 **PartitionKey** 및 **RowKey**를 선택하여 솔루션을 최적화하는 데 중점을 둡니다. 솔루션은 파티션으로 구성된 모든 엔터티를 포함하는 단일 테이블로 구성될 수 있지만 일반적으로 솔루션에는 여러 테이블이 있습니다. 테이블을 사용하면 엔터티를 논리적으로 구성하고, 액세스 제어 목록을 사용하여 데이터 액세스를 보다 쉽게 관리하며, 단일 저장소 작업을 사용하여 전체 테이블을 삭제할 수 있습니다.  

## <a name="table-partitions"></a>테이블 파티션
계정 이름, 테이블 이름 및 **PartitionKey**는 Table service가 엔터티를 저장하는 저장소 서비스 내에서 파티션을 식별합니다. 파티션은 엔터티의 주소 지정 체계의 일부일 뿐만 아니라 트랜잭션의 범위를 정의(아래의 [EGT(엔터티 그룹 트랜잭션)](#entity-group-transactions) 참조)하며, 테이블 서비스를 확장하는 방법의 기초가 됩니다. 파티션에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../../storage/common/storage-scalability-targets.md)를 참조하세요.  

Table service에서 개별 노드는 하나 이상의 전체 파티션을 지원하며, 서비스는 노드 간에 파티션 부하를 동적으로 분산하여 크기가 조정됩니다. 하나의 노드에 부하가 걸려 있는 경우 Table service는 해당 노드가 지원하는 파티션 범위를 여러 노드로 *분할*할 수 있습니다. 트래픽이 진정되면 서비스는 안정된 노드의 파티션 범위를 단일 노드로 다시*병합*할 수 있습니다.  

Table service의 내부 세부 정보, 특히 서비스에서 파티션을 관리하는 방법에 대한 자세한 내용은 [Microsoft Azure Storage: 강력한 일관성과 함께 항상 사용 가능한 클라우드 스토리지 서비스](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) 문서를 참조하세요.  

## <a name="entity-group-transactions"></a>EGT(엔터티 그룹 트랜잭션)
Table service에서 EGT(엔터티 그룹 트랜잭션)는 여러 엔터티 간에 원자성 업데이트를 수행하기 위한 유일한 기본 제공 메커니즘입니다. EGT를 *일괄 처리 트랜잭션*이라고도 합니다. EGT는 동일한 파티션에 저장된 엔터티에서만 작동할 수 있습니다(즉, 지정된 테이블에서 동일한 파티션 키를 공유함). 따라서 여러 엔터티 간에 원자성 트랜잭션 동작이 필요하면 언제든지 해당 엔터티가 동일한 파티션에 있도록 해야 합니다. 따라서 서로 다른 엔터티 유형에 여러 테이블을 사용하지 말고 여러 엔터티 유형을 동일한 테이블(및 파티션)에 유지하는 것이 좋습니다. 단일 EGT는 최대 100개의 엔터티에서 작동할 수 있습니다.  처리하기 위해 여러 개의 동시에 발생하는 EGT를 제출하는 경우 해당 EGT가 EGT에서 공통되는 엔터티에서 작동하지 않도록 해야 합니다. 그렇지 않으면 처리가 지연될 수 있습니다.

EGT는 디자인을 평가하기 위해 잠재적인 장단점에 대해서도 소개합니다. 즉, Azure가 노드에 대한 요청의 부하를 용이하게 분산할 수 있기 때문에 더 많은 파티션을 사용하면 애플리케이션의 확장성을 증가시킵니다. 하지만 많은 파티션을 사용하면 원자성 트랜잭션을 수행하고 데이터에 강력한 일관성을 유지하는 애플리케이션의 기능을 제한할 수 있습니다. 또한 단일 노드에서 예상할 수 있는 트랜잭션 처리량을 제한할 수 있는 파티션 수준에서 특정 확장성 목표가 있습니다. Azure Storage 계정 및 Table service의 확장성 목표에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../../storage/common/storage-scalability-targets.md)를 참조하세요.   

## <a name="capacity-considerations"></a>용량 고려 사항
다음 표에서는 Table service 솔루션을 디자인할 때 알아야 할 몇 가지 키 값을 설명합니다.  

| Azure 저장소 계정의 총 용량 | 500TB |
| --- | --- |
| Azure 저장소 계정에서 테이블의 수 |저장소 계정의 용량에 의해서만 제한됨 |
| 테이블에 있는 파티션 수 |저장소 계정의 용량에 의해서만 제한됨 |
| 파티션의 엔터티 수 |저장소 계정의 용량에 의해서만 제한됨 |
| 개별 엔터티의 크기 |최대 255개 속성을 포함하여 최대 1MB(**PartitionKey**, **RowKey** 및 **타임스탬프** 포함) |
| **PartitionKey** |최대 1KB의 크기 문자열 |
| **RowKey** |최대 1KB의 크기 문자열 |
| 엔터티 그룹 트랜잭션의 크기 |한 개 트랜잭션에는 최대 100개의 엔터티가 포함될 수 있고, 페이로드 크기는 4MB 미만이어야 합니다. EGT는 한 번에 하나의 엔터티만 업데이트할 수 있음 |

자세한 내용은 [테이블 서비스 데이터 모델 이해](https://msdn.microsoft.com/library/azure/dd179338.aspx)를 참조하세요.  

## <a name="cost-considerations"></a>비용 고려 사항
Table Storage는 비교적 저렴하지만 용량 사용량 및 트랜잭션 수량 모두에 대한 예상 비용을 Table service 솔루션 평가의 일부로 포함시켜야 합니다. 그러나 대부분의 시나리오에서는 비정규화되거나 중복된 데이터를 저장하여 솔루션의 성능 또는 확장성을 개선하는 것이 유효한 접근 방식입니다. 가격 책정에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.  

## <a name="next-steps"></a>다음 단계

- [테이블 디자인 패턴](table-storage-design-patterns.md)
- [관계 모델링](table-storage-design-modeling.md)
- [쿼리를 위한 디자인](table-storage-design-for-query.md)
- [테이블 데이터 암호화](table-storage-design-encrypt-data.md)
- [데이터 수정을 위한 디자인](table-storage-design-for-modification.md)
