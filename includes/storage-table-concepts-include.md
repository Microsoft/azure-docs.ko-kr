---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 042aedf1a043cd89d74ff099554642d38a3c7dd3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122984"
---
## <a name="what-is-table-storage"></a>Table Storage란?
Azure Table Storage는 많은 양의 구조화된 데이터를 저장합니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다. Table Storage의 일반적인 용도는 다음과 같습니다.

* 웹 규모 애플리케이션을 처리할 수 있는 구조화된 데이터 TB 저장
* 복합 조인, 외래 키 또는 저장 프로시저가 필요하지 않고 빠른 액세스를 위해 역정규화할 수 있는 데이터 세트 저장
* 클러스터형 인덱스를 사용하여 신속하게 데이터 쿼리
* WCF 데이터 서비스 .NET 라이브러리와 함께 OData 프로토콜 및 LINQ 쿼리를 사용하여 데이터 액세스

Table Storage를 사용하여 매우 큰 구조화된 비관계형 데이터 집합을 저장하고 쿼리할 수 있으며, 수요가 증가함에 따라 테이블의 크기가 조정됩니다.

## <a name="table-storage-concepts"></a>Table Storage 개념
Table Storage에 포함되는 구성 요소는 다음과 같습니다.

![테이블 저장소 구성 요소 다이어그램][Table1]

* **URL 형식:** Azure Table Storage 계정은 이 형식을 사용합니다. `http://<storage account>.table.core.windows.net/<table>`

  Azure Cosmos DB Table API 계정은 이 형식을 사용합니다. `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  OData 프로토콜과 함께 이 주소를 사용하여 Azure 테이블 주소를 직접 지정할 수 있습니다. 자세한 내용은 [OData.org][OData.org]를 참조하세요.
* **계정:** Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. Storage 계정 용량에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](../articles/storage/common/storage-scalability-targets.md) (영문)를 참조하십시오. 

    Azure Cosmos DB에 대한 모든 액세스는 Table API 계정을 통해 수행됩니다. Table API 계정 만들기에 관한 자세한 내용은 [Table API 계정 만들기](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account)를 참조하세요.
* **테이블**: 테이블은 엔터티 컬렉션입니다. 테이블은 엔터티에 스키마를 적용하지 않으므로 단일 테이블에 각기 다른 속성 집합을 가진 엔터티가 포함될 수 있습니다.  
* **엔터티**: 엔터티는 데이터베이스 행과 유사한 속성 집합입니다. Azure Storage 엔터티의 크기는 최대 1MB일 수 있습니다. Azure Cosmos DB 엔터티의 크기는 최대 2MB일 수 있습니다.
* **속성**: 속성은 이름 값 쌍입니다. 각 엔터티에 데이터를 저장할 속성을 최대 252개까지 포함할 수 있습니다. 또한 각 엔터티에는 파티션 키, 행 키 및 타임스탬프를 지정하는 세 가지 시스템 속성이 있습니다. 동일한 파티션 키를 가진 엔터티는 보다 신속하게 쿼리할 수 있으며 원자성 작업으로 삽입/업데이트할 수 있습니다. 엔터티의 행 키는 파티션 내의 고유 식별자입니다.

테이블 및 속성의 명명에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)를 참조하세요.

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
