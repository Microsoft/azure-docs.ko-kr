---
title: 수백 테라바이트의 데이터를 Azure Cosmos DB로 마이그레이션
description: 이 문서에서는 Cosmos DB로 100 테라바이트의 데이터를 마이그레이션하는 방법에 대해 설명 합니다.
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880215"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>수백 테라바이트의 데이터를 Azure Cosmos DB로 마이그레이션 

Azure Cosmos DB에 테라바이트의 데이터를 저장할 수 있습니다. 대규모 데이터 마이그레이션을 수행하여 프로덕션 워크로드를 Azure Cosmos DB로 이동할 수 있습니다. 이 문서에서는 대규모 데이터를 Azure Cosmos DB로 이동 하는 데 필요한 과제를 설명 하 고 문제를 해결 하 고 데이터를 Azure Cosmos DB으로 마이그레이션하는 데 도움이 되는 도구를 소개 합니다. 이 사례 연구에서 고객은 Cosmos DB SQL API를 사용 했습니다.  

전체 작업을 Azure Cosmos DB로 마이그레이션하기 전에 데이터의 하위 집합을 마이그레이션하여 파티션 키 선택, 쿼리 성능 및 데이터 모델링과 같은 일부 측면의 유효성을 검사할 수 있습니다. 개념 증명의 유효성을 검사 한 후에는 전체 작업을 Azure Cosmos DB로 이동할 수 있습니다.  

## <a name="tools-for-data-migration"></a>데이터 마이그레이션 도구 

Azure Cosmos DB 마이그레이션 전략은 현재 API 선택 및 데이터 크기에 따라 달라 집니다. 데이터 모델링, 쿼리 성능, 파티션 키 선택 등의 유효성을 검사 하기 위해 더 작은 데이터 집합을 마이그레이션하려면 [데이터 마이그레이션 도구](import-data.md) 또는 [Azure Data Factory의 Azure Cosmos DB 커넥터](../data-factory/connector-azure-cosmos-db.md)를 선택할 수 있습니다. Spark에 대해 잘 알고 있는 경우 [Azure Cosmos DB spark 커넥터](spark-connector.md) 를 사용 하 여 데이터를 마이그레이션하도록 선택할 수도 있습니다.

## <a name="challenges-for-large-scale-migrations"></a>대규모 마이그레이션에 대한 문제 

Azure Cosmos DB로 데이터를 마이그레이션하기 위한 기존 도구에는 다음과 같은 몇 가지 제한 사항이 있습니다.

 * **제한 된 규모 확장 기능**: 가능한 한 빨리 tb의 데이터를 Azure Cosmos DB으로 마이그레이션하고 프로 비전 된 전체 처리량을 효과적으로 사용 하기 위해 마이그레이션 클라이언트에서 무기한 규모를 확장할 수 있어야 합니다.  

* **진행률 추적 및 체크 인이 부족**합니다. 마이그레이션 진행 상황을 추적 하 고 큰 데이터 집합을 마이그레이션하는 동안 체크 가리키기를 수행 하는 것이 중요 합니다. 그렇지 않은 경우 마이그레이션 중에 발생 하는 모든 오류는 마이그레이션을 중지 하 고 처음부터 프로세스를 시작 해야 합니다. 99%가 이미 완료 되었을 때 전체 마이그레이션 프로세스를 다시 시작 하는 것은 생산적이 지 않습니다.  

* **배달 못한 편지 큐 부족**: 많은 데이터 집합 내에서 원본 데이터의 일부에 문제가 있을 수 있습니다. 또한 클라이언트 또는 네트워크에 일시적인 문제가 있을 수 있습니다. 이러한 경우 모두 전체 마이그레이션이 실패 하지는 않습니다. 대부분의 마이그레이션 도구는 일시적인 문제를 방지 하는 강력한 다시 시도 기능을 포함 하지만 항상 충분 하지는 않습니다. 예를 들어 원본 데이터 문서의 0.01% 미만이 크기가 2mb 보다 크면 문서 쓰기가 Azure Cosmos DB에서 실패 하 게 됩니다. 이상적인 경우 마이그레이션 도구를 통해 이러한 ' 실패 ' 문서를 다른 배달 못한 편지 큐에 보관 하 여 마이그레이션 후에 처리할 수 있는 것이 좋습니다. 

이러한 제한 사항 중 상당수는 Azure data factory, Azure Data Migration services와 같은 도구에 대해 수정 되 고 있습니다. 

## <a name="custom-tool-with-bulk-executor-library"></a>대량 실행자 라이브러리를 사용 하는 사용자 지정 도구 

위의 섹션에서 설명 하는 문제는 여러 인스턴스에서 쉽게 확장할 수 있는 사용자 지정 도구를 사용 하 여 해결할 수 있으며 일시적인 오류에 대해 복원 력이 있습니다. 또한 사용자 지정 도구는 다양 한 검사점에서 마이그레이션을 일시 중지 하 고 다시 시작할 수 있습니다. Azure Cosmos DB은 이러한 기능 중 일부를 통합 하는 [대량 실행자 라이브러리](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) 를 이미 제공 합니다. 예를 들어 대량 실행자 라이브러리에는 일시적인 오류를 처리 하는 기능이 이미 포함 되어 있으며, 노드당 약 500 K RUs를 사용 하도록 단일 노드의 스레드를 확장할 수 있습니다. 또한 bulk executor 라이브러리는 검사점의 형태로 독립적으로 작동 하는 마이크로 일괄 처리로 원본 데이터 집합을 분할 합니다.  

사용자 지정 도구는 대량 실행자 라이브러리를 사용 하 고 여러 클라이언트에서 확장 하 고 수집 프로세스 중에 오류를 추적할 수 있도록 지원 합니다. 이 도구를 사용 하려면 서로 다른 마이그레이션 작업 자가 각 파일을 선택 하 여 Azure Cosmos DB으로 수집할 수 있도록 Azure Data Lake Storage (ADLS)에서 원본 데이터를 개별 파일로 분할 해야 합니다. 사용자 지정 도구는 ADLS의 개별 원본 파일 각각에 대한 마이그레이션 진행률에 대한 메타 데이터를 저장 하 고 이와 관련 된 모든 오류를 추적 하는 별도의 컬렉션을 사용 합니다.  

다음 이미지는이 사용자 지정 도구를 사용 하는 마이그레이션 프로세스를 설명 합니다. 이 도구는 가상 머신 집합에서 실행 되 고 있으며 각 가상 머신은 Azure Cosmos DB의 추적 컬렉션을 쿼리하여 원본 데이터 파티션 중 하나에 대한 임대를 획득 합니다. 이 작업이 완료 되 면 도구에서 원본 데이터 파티션을 읽고 대량 실행자 라이브러리를 사용 하 여 Azure Cosmos DB에 수집. 그런 다음, 데이터 수집의 진행률과 발생 한 오류를 기록 하도록 추적 컬렉션이 업데이트 됩니다. 데이터 파티션이 처리 된 후이 도구는 사용 가능한 다음 원본 파티션에 대한 쿼리를 시도 합니다. 모든 데이터가 마이그레이션될 때까지 계속 해 서 다음 원본 파티션을 처리 합니다. 이 도구에 대한 소스 코드는 [여기](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)에서 사용할 수 있습니다.  

 
![마이그레이션 도구 설치](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

추적 컬렉션은 다음 예제와 같이 문서를 포함 합니다. 이러한 문서는 원본 데이터의 각 파티션에 대해 하나씩 표시 됩니다.  각 문서에는 원본 데이터 파티션에 대한 메타 데이터 (예: 위치, 마이그레이션 상태 및 오류 (있는 경우))가 포함 됩니다.  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>데이터 마이그레이션에 대한 필수 조건 

데이터 마이그레이션을 시작 하기 전에 고려해 야 할 몇 가지 필수 구성 요소가 있습니다.  

#### <a name="estimate-the-data-size"></a>데이터 크기를 예측 합니다.  

원본 데이터 크기가 Azure Cosmos DB의 데이터 크기와 정확 하 게 매핑되지 않을 수 있습니다. 원본에서 몇 가지 샘플 문서를 삽입 하 여 Azure Cosmos DB에서 데이터 크기를 확인할 수 있습니다. 샘플 문서 크기에 따라 마이그레이션 후 Azure Cosmos DB의 총 데이터 크기를 예상할 수 있습니다. 

예를 들어 Azure Cosmos DB의 마이그레이션 후 각 문서가 약 1KB이 고 원본 데이터 집합에 600억 문서가 있는 경우 Azure Cosmos DB의 예상 크기는 60 TB에 가깝습니다. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>충분 한 RUs를 사용 하 여 컨테이너 미리 만들기: 

저장소를 자동으로 확장할 Azure Cosmos DB 있지만 가장 작은 컨테이너 크기에서 시작 하지 않는 것이 좋습니다. 컨테이너 크기가 작을수록 처리량 가용성이 낮아집니다. 즉, 마이그레이션을 완료 하는 데 더 많은 시간이 소요 됩니다. 대신 최종 데이터 크기를 사용 하 여 컨테이너를 만들고 (이전 단계에서 예상) 마이그레이션 작업에서 프로 비전 된 처리량을 완전히 소비 하는지 확인 하는 것이 유용 합니다.  

이전 단계에서. 데이터 크기가 60 TB를 예상 하기 때문에 전체 데이터 집합을 수용 하려면 최소 2.4 M RUs의 컨테이너가 필요 합니다.  

 

#### <a name="estimate-the-migration-speed"></a>마이그레이션 속도 예측: 

마이그레이션 워크 로드가 전체 프로 비전 된 처리량을 소비할 수 있는 것으로 가정 하 고, 전체에서 프로 비전 된는 마이그레이션 속도의 예측을 제공 합니다. 이전 예제를 계속 하 고 Azure Cosmos DB SQL API 계정에 1kb 문서를 작성 하기 위해 5 개의 RUs가 필요 합니다.  240만 RUs는 초당 48만 문서를 전송할 수 있습니다 (또는 480 m b/초). 즉, 60 TB의 전체 마이그레이션은 125000 초 또는 약 34 시간을 사용 합니다.  

하루 내에 마이그레이션을 완료 하려는 경우 프로 비전 된 처리량을 500만 RUs로 늘려야 합니다. 

 

#### <a name="turn-off-the-indexing"></a>인덱싱 해제:  

가능한 한 빨리 마이그레이션을 완료 해야 하므로 각 문서 수집에 대한 인덱스를 만드는 데 소요 되는 시간과 RUs를 최소화 하는 것이 좋습니다.  모든 속성을 자동으로 인덱싱하 Azure Cosmos DB 선택한 몇 가지 용어에 대한 인덱싱을 최소화 하거나 마이그레이션 과정을 위해 완전히 해제 하는 것이 유용 합니다. 아래와 같이 됨를 none으로 변경 하 여 컨테이너의 인덱싱 정책을 해제할 수 있습니다.  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

마이그레이션이 완료 되 면 인덱싱을 업데이트할 수 있습니다.  

## <a name="migration-process"></a>마이그레이션 프로세스 

필수 구성 요소가 완료 되 면 다음 단계에 따라 데이터를 마이그레이션할 수 있습니다.  

1. 먼저 원본에서 Azure Blob Storage 데이터를 가져옵니다. 마이그레이션의 속도를 높이려면 개별 원본 파티션 간에 병렬 처리 하는 것이 좋습니다. 마이그레이션을 시작 하기 전에 원본 데이터 집합은 크기가 200 인 파일로 분할 되어야 합니다.   

2. 대량 실행자 라이브러리는 단일 클라이언트 VM에서 50만 RUs를 사용 하도록 확장할 수 있습니다. 사용 가능한 처리량은 500만 RUs 이므로 Azure Cosmos 데이터베이스가 있는 동일한 지역에 10 Ubuntu 16.04 Vm (Standard_D32_v3)을 프로 비전 해야 합니다. 마이그레이션 도구와 해당 설정 파일을 사용 하 여 이러한 Vm을 준비 해야 합니다.  

3. 클라이언트 가상 컴퓨터 중 하나에서 큐 단계를 실행 합니다. 이 단계에서는 ADLS 컨테이너를 검색 하 고 각 원본 데이터 집합의 파티션 파일에 대해 진행률 추적 문서를 만드는 추적 컬렉션을 만듭니다.  

4. 다음으로 모든 클라이언트 Vm에서 가져오기 단계를 실행 합니다. 각 클라이언트는 원본 파티션에 대한 소유권을 가져오고 해당 데이터를 Azure Cosmos DB으로 수집할 수 있습니다. 작업이 완료 되 고 추적 컬렉션에서 상태가 업데이트 되 면 클라이언트는 추적 컬렉션에서 사용 가능한 다음 원본 파티션을 쿼리할 수 있습니다.  

5. 이 프로세스는 전체 원본 파티션 집합이 수집 될 때까지 계속 됩니다. 모든 원본 파티션이 처리 되 면 동일한 추적 컬렉션의 오류 수정 모드에서 도구를 다시 실행 해야 합니다. 이 단계는 오류로 인해 다시 처리 해야 하는 원본 파티션을 식별 하는 데 필요 합니다.  

6. 이러한 오류 중 일부는 원본 데이터의 잘못 된 문서 때문에 발생할 수 있습니다. 이를 식별 하 고 수정 해야 합니다. 그런 다음 실패 한 파티션에서 가져오기 단계를 다시 실행 하 여 다시 수집 해야 합니다. 

마이그레이션이 완료 되 면 Azure Cosmos DB의 문서 수가 원본 데이터베이스의 문서 수와 동일한 지 확인할 수 있습니다. 이 예에서는 Azure Cosmos DB의 총 크기가 65 테라바이트로 설정 되어 있습니다. 마이그레이션 후에는 인덱싱을 선택적으로 설정 하 고 작업의 작업에 필요한 수준으로 RUs를 낮출 수 있습니다.

## <a name="contact-the-azure-cosmos-db-team"></a>Azure Cosmos DB 팀에 문의
이 가이드를 따라 대규모 마이그레이션에 대해 대규모 데이터 집합을 Azure Cosmos DB로 마이그레이션할 수 있지만, Azure Cosmos DB 제품 팀에 연락 하 여 데이터 모델링 및 일반 아키텍처 검토의 유효성을 검사 하는 것이 좋습니다. 제품 팀은 데이터 집합 및 워크 로드에 따라 사용자에 게 적용할 수 있는 다른 성능 및 비용 최적화를 제안할 수도 있습니다. 대규모 마이그레이션에 대한 지원을 위해 Azure Cosmos DB 팀에 문의 하려면 아래와 같이 "일반 권고" 문제 유형 및 "큼 (TB +) 마이그레이션" 문제 하위 유형 아래에서 지원 티켓을 열 수 있습니다.

![마이그레이션 지원 항목](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>다음 단계

* [.Net](bulk-executor-dot-net.md) 및 [Java](bulk-executor-java.md)에서 대량 실행자 라이브러리를 사용 하는 샘플 응용 프로그램을 사용해 보세요. 
* 대량 실행자 라이브러리는 Cosmos DB Spark 커넥터에 통합 되어 있습니다. 자세한 내용은 [Azure Cosmos DB spark 커넥터](spark-connector.md) 문서를 참조 하세요.  
* 대규모 마이그레이션에 대한 추가 도움말을 보려면 "일반 권고" 문제 유형 및 "대규모 (TB +) 마이그레이션" 문제 하위 유형에 서 지원 티켓을 열어 Azure Cosmos DB 제품 팀에 문의 하세요. 
