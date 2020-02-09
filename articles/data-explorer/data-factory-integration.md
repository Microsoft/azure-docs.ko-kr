---
title: Azure Data Factory와 Azure 데이터 탐색기 통합
description: 이 항목에서는 Azure 데이터 탐색기를 Azure Data Factory와 통합 하 여 복사, 조회 및 명령 작업을 사용 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293626"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Azure Data Factory와 Azure 데이터 탐색기 통합

ADF ( [Azure Data Factory](/azure/data-factory/) )는 다양 한 데이터 저장소를 통합 하 고 데이터에 대한 작업을 수행할 수 있는 클라우드 기반 데이터 통합 서비스입니다. ADF를 사용 하면 데이터 이동 및 데이터 변환을 오케스트레이션 하 고 자동화 하기 위한 데이터 기반 워크플로를 만들 수 있습니다. Azure 데이터 탐색기는 Azure Data Factory에서 지원 되는 [데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) 중 하나입니다. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure 데이터 탐색기에 대한 Azure Data Factory 활동

Azure 데이터 탐색기 사용자는 Azure Data Factory와 다양 한 통합을 사용할 수 있습니다.

### <a name="copy-activity"></a>복사 활동
 
Azure Data Factory 복사 작업은 데이터 저장소 간에 데이터를 전송 하는 데 사용 됩니다. Azure 데이터 탐색기는 데이터를 Azure 데이터 탐색기에서 지원 되는 데이터 저장소로 복사 하는 원본으로 지원 되며, 지원 되는 모든 데이터 저장소에서 Azure 데이터 탐색기로 데이터가 복사 됩니다. 자세한 내용은 [Azure Data Factory를 사용 하 여 Azure 데이터 탐색기 간에 데이터 복사](/azure/data-factory/connector-azure-data-explorer)를 참조 하세요. 자세한 연습을 보려면 [Azure 데이터 탐색기에 Azure Data Factory에서 데이터 로드](data-factory-load-data.md)를 참조 하세요.
Azure 데이터 탐색기는 azure 내에서 데이터를 복사할 때 사용 되는 Azure IR (Integration Runtime)에서 지원 되며, 온-프레미스 또는 Azure Virtual Network와 같은 액세스 제어를 사용 하는 네트워크에 있는 데이터 저장소 간에 데이터를 복사할 때 사용 됩니다. 자세한 내용은 [사용할 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use) 을 참조 하세요.
 
> [!TIP]
> 복사 작업을 사용 하 고 연결 된 **서비스** 또는 **데이터 집합**을 만들 때 이전 데이터 저장소가 아닌 **Azure 데이터 탐색기 (kusto)** **데이터 저장소를 선택 합니다.**  

### <a name="lookup-activity"></a>조회 작업
 
조회 작업은 Azure 데이터 탐색기에서 쿼리를 실행 하는 데 사용 됩니다. 쿼리 결과는 조회 활동의 출력으로 반환 되며 [ADF 조회 설명서](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)에 설명 된 대로 파이프라인의 다음 활동에서 사용할 수 있습니다.  
5000 행 및 2mb의 응답 크기 제한 외에도 작업의 쿼리 제한 시간 제한은 1 시간입니다.

### <a name="command-activity"></a>명령 작업

명령 작업을 통해 Azure 데이터 탐색기 [제어 명령을](/azure/kusto/concepts/#control-commands)실행할 수 있습니다. 쿼리와 달리 제어 명령은 잠재적으로 데이터 또는 메타 데이터를 수정할 수 있습니다. 일부 제어 명령은 데이터를 Azure 데이터 탐색기에 수집 하거나 `.ingest`또는 `.set-or-append`등의 명령을 사용 하 여 Azure 데이터 탐색기에서 `.export`등의 명령을 사용 하 여 외부 데이터 저장소로 데이터를 복사 하는 것을 목표로 합니다.
명령 활동에 대한 자세한 연습을 보려면 [Azure Data Factory 명령 활동을 사용 하 여 Azure 데이터 탐색기 제어 명령 실행](data-factory-command-activity.md)을 참조 하세요.  Ctrl 명령을 사용 하 여 데이터를 복사 하는 작업은 복사 작업 보다 빠르고 저렴 한 옵션 일 수 있습니다. 명령 활동과 복사 활동을 사용할 시기를 결정 하려면 데이터를 복사할 [때 복사 및 명령 활동 중에서 선택](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)을 참조 하세요.

### <a name="copy-in-bulk-from-a-database-template"></a>데이터베이스 템플릿에서 대량 복사

[Azure Data Factory 템플릿을 사용 하 여 데이터베이스에서 Azure 데이터 탐색기로 대량 복사](data-factory-template.md) 는 미리 정의 된 Azure Data Factory 파이프라인입니다. 템플릿은 데이터베이스 또는 테이블당 많은 파이프라인을 만드는 데 사용 되며 더 빠른 데이터 복사를 위해 사용 됩니다. 

### <a name="mapping-data-flows"></a>데이터 흐름 매핑 

데이터 [흐름 매핑 Azure Data Factory](/azure/data-factory/concepts-data-flow-overview) 는 데이터 엔지니어가 코드를 작성 하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있도록 시각적으로 디자인 된 데이터 변환입니다. 데이터 흐름을 만들고 Azure 데이터 탐색기에 데이터를 수집 하려면 다음 방법을 사용 합니다.

1. [매핑 데이터 흐름](/azure/data-factory/data-flow-create)을 만듭니다.
1. [데이터를 Azure Blob으로 내보냅니다](/azure/data-factory/data-flow-sink). 
1. 데이터를 Azure 데이터 탐색기에 수집 하는 [Event Grid](/azure/data-explorer/ingest-data-event-grid) 또는 [ADF 복사 작업](/azure/data-explorer/data-factory-load-data) 을 정의 합니다.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>데이터 복사 시 복사와 Azure 데이터 탐색기 명령 활동 중에서 선택 

이 섹션에서는 데이터 복사 요구에 맞는 올바른 활동을 선택 하는 데 도움이 됩니다.

Azure 데이터 탐색기 간에 데이터를 복사 하는 경우 Azure Data Factory에서 사용할 수 있는 두 가지 옵션이 있습니다.
* 복사 작업입니다.
* Azure 데이터 탐색기에서 데이터를 전송 하는 제어 명령 중 하나를 실행 하는 azure 데이터 탐색기 명령 작업입니다. 

### <a name="copy-data-from-azure-data-explorer"></a>Azure 데이터 탐색기에서 데이터 복사
  
복사 작업 또는 [`.export`](/azure/kusto/management/data-export/) 명령을 사용 하 여 Azure 데이터 탐색기에서 데이터를 복사할 수 있습니다. `.export` 명령은 쿼리를 실행 한 다음 쿼리 결과를 내보냅니다. 

Azure 데이터 탐색기에서 데이터를 복사 하는 복사 작업 및 `.export` 명령의 비교는 다음 표를 참조 하세요.

| | 복사 활동 | . export 명령 |
|---|---|---|
| **흐름 설명** | ADF는 Kusto에 대해 쿼리를 실행 하 고 결과를 처리 한 다음 대상 데이터 저장소에 보냅니다. <br>(**Adx > ADF > 싱크 데이터 저장소**) | ADF는 명령을 실행 하 고 데이터를 대상 데이터 저장소로 직접 전송 하는 Azure 데이터 탐색기에 `.export` 제어 명령을 보냅니다. <br>(**Adx > 싱크 데이터 저장소**) |
| **지원 되는 대상 데이터 저장소** | 지원 되는 다양 한 [데이터 저장소](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure Blob SQL Database |
| **성능** | 중앙 | <ul><li>분산 (기본값), 여러 노드에서 동시에 데이터 내보내기</li><li>더 빠르고 COGS 효율적입니다.</li></ul> |
| **서버 제한** | [쿼리 제한은](/azure/kusto/concepts/querylimits) 확장/비활성화할 수 있습니다. 기본적으로 ADF 쿼리는 다음을 포함 합니다. <ul><li>50만 레코드 또는 64 MB의 크기 제한</li><li>제한 시간은 10 분입니다.</li><li>`noTruncation` false로 설정 합니다.</li></ul> | 기본적으로는 쿼리 제한을 확장 하거나 해제 합니다. <ul><li>크기 제한이 사용 되지 않습니다.</li><li>서버 제한 시간은 1 시간으로 연장 됩니다.</li><li>`MaxMemoryConsumptionPerIterator` 및 `MaxMemoryConsumptionPerQueryPerNode`은 max (5gb, Totalgb Memory/2)로 확장 됩니다.</li></ul>

> [!TIP]
> 복사 대상이 `.export` 명령에서 지 원하는 데이터 저장소 중 하나이 고 복사 작업 기능이 요구 사항에 중요 하지 않은 경우에는 `.export` 명령을 선택 합니다.

### <a name="copying-data-to-azure-data-explorer"></a>Azure 데이터 탐색기에 데이터 복사

[쿼리 수집](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`및 [저장소에서 수집](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`)과 같은 수집 명령과 복사 작업을 사용 하 여 Azure 데이터 탐색기에 데이터를 복사할 수 있습니다. 

Azure 데이터 탐색기로 데이터를 복사 하기 위한 수집 명령과 복사 작업의 비교는 다음 표를 참조 하세요.

| | 복사 활동 | 쿼리에서 수집<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | 스토리지에서 수집 <br> `.ingest` |
|---|---|---|---|
| **흐름 설명** | ADF는 원본 데이터 저장소에서 데이터를 가져와 테이블 형식으로 변환 하 고 필요한 스키마 매핑을 변경 합니다. 그런 다음 ADF는 데이터를 Azure blob에 업로드 하 고 청크로 분할 한 다음 blob을 다운로드 하 여 ADX 테이블로 수집 합니다. <br> (**원본 데이터 저장소 > ADF > Azure blob > ADX**) | 이러한 명령은 쿼리 또는 `.show` 명령을 실행 하 고 쿼리 결과를 테이블에 수집할 수 있습니다 (**adx > adx**). | 이 명령은 하나 이상의 클라우드 저장소 아티팩트에서 데이터를 "풀링" 하 여 테이블에 데이터를 수집 합니다. |
| **지원 되는 원본 데이터 저장소** |  [다양 한 옵션](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (sql_request 플러그 인 사용), Cosmos (cosmosdb_sql_request 플러그 인 사용) 및 HTTP 또는 Python Api를 제공 하는 기타 데이터 저장소를 제공 합니다. | Filesystem, Azure Blob Storage, ADLS Gen 1, ADLS Gen 2 |
| **성능** | Ingestions는 큐에 대기 되 고 관리 되므로, 작은 크기의 Ingestions를 보장 하 고 부하 분산, 다시 시도 및 오류 처리를 제공 하 여 고가용성을 보장 합니다. | <ul><li>이러한 명령은 대량 데이터 가져오기를 위해 설계 되지 않았습니다.</li><li>예상 및 저렴 하 게 작동 합니다. 그러나 프로덕션 시나리오의 경우와 트래픽 속도와 데이터 크기가 크면 복사 작업을 사용 합니다.</li></ul> |
| **서버 제한** | <ul><li>크기 제한이 없습니다.</li><li>최대 시간 제한: 수집 blob 당 1 시간입니다. |<ul><li>쿼리 파트에는 크기 제한이 있으므로 `noTruncation=true`을 지정 하 여 건너뛸 수 있습니다.</li><li>최대 제한 시간 제한: 1 시간</li></ul> | <ul><li>크기 제한이 없습니다.</li><li>최대 제한 시간 제한: 1 시간</li></ul>|

> [!TIP]
> * ADF에서 Azure 데이터 탐색기 데이터를 복사 하는 경우 `ingest from query` 명령을 사용 합니다.  
> * 대량 데이터 집합 (> 1GB)의 경우 복사 작업을 사용 합니다.  

## <a name="required-permissions"></a>필요한 사용 권한

다음 표에서는 Azure Data Factory와의 통합에서 다양 한 단계에 필요한 사용 권한을 나열 합니다.

| 단계 | 작업 | 최소 권한 수준 | 메모 |
|---|---|---|---|
| **연결 된 서비스 만들기** | 데이터베이스 탐색 | *데이터베이스 뷰어* <br>ADF를 사용 하는 로그인 한 사용자에 게는 데이터베이스 메타 데이터를 읽을 수 있는 권한이 있어야 합니다. | 사용자는 데이터베이스 이름을 수동으로 입력할 수 있습니다. |
| | 연결을 테스트 | *데이터베이스 모니터* 또는 *테이블 수집기* <br>서비스 주체에는 데이터베이스 수준 `.show` 명령 또는 테이블 수준 수집을 실행할 수 있는 권한이 있어야 합니다. | <ul><li>TestConnection은 데이터베이스가 아니라 클러스터에 대한 연결을 확인 합니다. 데이터베이스가 존재 하지 않더라도 성공할 수 있습니다.</li><li>테이블 관리자 권한이 충분 하지 않습니다.</li></ul>|
| **데이터 집합 만들기** | 테이블 탐색 | *데이터베이스 모니터* <br>ADF를 사용 하 여 로그인 한 사용자는 데이터베이스 수준 `.show` 명령을 실행할 수 있는 권한이 있어야 합니다. | 사용자는 테이블 이름을 수동으로 입력할 수 있습니다.|
| **데이터 집합** 또는 **복사 작업** 만들기 | 데이터 미리 보기 | *데이터베이스 뷰어* <br>서비스 주체는 데이터베이스 메타 데이터를 읽을 수 있는 권한이 있어야 합니다. | | 
|   | 스키마 가져오기 | *데이터베이스 뷰어* <br>서비스 주체는 데이터베이스 메타 데이터를 읽을 수 있는 권한이 있어야 합니다. | ADX가 테이블 형식에서 테이블 형식 복사의 원본인 경우에는 사용자가 명시적으로 스키마를 가져오지 않더라도 ADF가 자동으로 스키마를 가져옵니다. |
| **ADX를 싱크로** | 이름으로 열 매핑 만들기 | *데이터베이스 모니터* <br>서비스 주체는 데이터베이스 수준 `.show` 명령을 실행할 수 있는 권한이 있어야 합니다. | <ul><li>모든 필수 작업은 *table 수집기*와 함께 작동 합니다.</li><li> 일부 선택적 작업은 실패할 수 있습니다.</li></ul> |
|   | <ul><li>테이블에 CSV 매핑 만들기</li><li>매핑 삭제</li></ul>| *테이블 수집기* 또는 *데이터베이스 관리자* <br>테이블을 변경 하려면 서비스 사용자에 게 권한이 있어야 합니다. | |
|   | 데이터 수집 | *테이블 수집기* 또는 *데이터베이스 관리자* <br>테이블을 변경 하려면 서비스 사용자에 게 권한이 있어야 합니다. | | 
| **ADX를 원본으로** | 쿼리 실행 | *데이터베이스 뷰어* <br>서비스 주체는 데이터베이스 메타 데이터를 읽을 수 있는 권한이 있어야 합니다. | |
| **Kusto 명령** | | 각 명령의 권한 수준에 따라 |

## <a name="performance"></a>성능 중심 

Azure 데이터 탐색기가 원본인 경우 쿼리를 포함 하는 조회, 복사 또는 명령 작업을 사용 하는 경우 성능 정보에 대한 [쿼리 모범 사례](/azure/kusto/query/best-practices) 및 [복사 작업을 위한 ADF 설명서](/azure/data-factory/copy-activity-performance)를 참조 하세요.
  
이 섹션에서는 Azure 데이터 탐색기 싱크로 복사 작업을 사용 하는 방법을 설명 합니다. Azure 데이터 탐색기 싱크에 대한 예상 처리량은 11-13 MBps입니다. 다음 표에서는 Azure 데이터 탐색기 싱크의 성능에 영향을 주는 매개 변수를 자세히 설명 합니다.

| 매개 변수 | 메모 |
|---|---|
| **구성 요소 지리적 근접성** | 모든 구성 요소를 동일한 지역에 저장 합니다.<ul><li>원본 및 싱크 데이터 저장소.</li><li>ADF 통합 런타임.</li><li>ADX 클러스터.</li></ul>하나 이상의 통합 런타임이 ADX 클러스터와 동일한 지역에 있는지 확인 합니다. |
| **DIUs 수** | ADF에서 사용 되는 4 개의 DIUs 마다 하나의 VM <br>파일이 여러 파일을 사용 하는 파일 기반 저장소 인 경우에만 DIUs를 늘릴 수 있습니다. 그러면 각 VM은 서로 다른 파일을 병렬로 처리 합니다. 따라서 하나의 큰 파일을 복사 하는 경우 여러 개의 작은 파일을 복사 하는 것 보다 대기 시간이 더 깁니다.|
|**ADX 클러스터의 금액 및 SKU** | ADX 노드 수가 많으면 수집 처리 시간이 향상 됩니다.|
| **Parallelism** | 데이터베이스에서 대량의 데이터를 복사 하려면 데이터를 분할 한 다음 각 파티션을 병렬로 복사 하는 ForEach 루프를 사용 하거나 [데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사](data-factory-template.md)를 사용 합니다. 참고: 복사 작업의 **병렬 처리 수준** **설정** > adx와 관련이 없습니다. |
| **데이터 처리 복잡성** | 대기 시간은 원본 파일 형식, 열 매핑 및 압축에 따라 다릅니다.|
| **Integration runtime을 실행 하는 VM** | <ul><li>Azure 복사의 경우 ADF Vm 및 컴퓨터 Sku를 변경할 수 없습니다.</li><li> 온-프레미스에서 Azure로 복사 하는 경우 자체 호스팅 IR을 호스트 하는 VM이 충분히 강력한 지 확인 합니다.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>팁 및 일반적인 문제

### <a name="monitor-activity-progress"></a>작업 진행률 모니터링

* 데이터 *읽기* 는 이진 파일 크기에 따라 계산 되 *는 반면, 데이터는 직렬화* 및 압축을 푼 후 메모리 내 크기에 따라 계산 되므로 작업 진행률을 모니터링할 때 데이터 *쓰기* 속성은 *데이터 읽기* 속성 보다 훨씬 클 수 있습니다.

* 작업 진행률을 모니터링할 때 데이터가 Azure 데이터 탐색기 싱크에 기록 되는 것을 볼 수 있습니다. Azure 데이터 탐색기 테이블을 쿼리하면 데이터가 도착 하지 않은 것을 볼 수 있습니다. 이는 Azure 데이터 탐색기에 복사할 때 두 단계가 있기 때문입니다. 
    * 첫 번째 단계에서는 원본 데이터를 읽고, 900-MB 청크로 분할 하 고, 각 청크를 Azure Blob에 업로드 합니다. 첫 번째 단계는 ADF 활동 진행률 보기에서 볼 수 있습니다. 
    * 두 번째 단계는 모든 데이터가 Azure Blob에 업로드 된 후에 시작 됩니다. Azure 데이터 탐색기 engine 노드는 blob을 다운로드 하 고 데이터를 싱크 테이블에 수집 합니다. 그러면 데이터가 Azure 데이터 탐색기 테이블에 표시 됩니다.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>잘못 된 이스케이프로 인해 CSV 파일을 수집 하지 못했습니다.

Azure 데이터 탐색기에는 CSV 파일이 [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt)와 일치 하는 것으로 예상 됩니다.
다음이 필요 합니다.
* 이스케이프를 필요로 하는 문자를 포함 하는 필드 (예: "및 새 줄)는 공백 없이 **"** 문자로 시작 하 고 끝나야 합니다. "문자 ( **" "** )를 사용 하 여 필드 *내의* 모든 **"** **문자를** 이스케이프 합니다. 예를 들어 _"hello," "_ " ""는 단일 레코드를 포함 하는 유효한 CSV 파일이 며 내용 _Hello, "세계"_ 가 있는 단일 열 또는 필드가 있습니다.
* 파일의 모든 레코드는 동일한 수의 열과 필드를 포함 해야 합니다.

Azure Data Factory 백슬래시 (이스케이프) 문자를 허용 합니다. Azure Data Factory를 사용 하 여 백슬래시 문자를 사용 하 여 CSV 파일을 생성 하는 경우 파일을 Azure 데이터 탐색기 수집 하지 못합니다.

#### <a name="example"></a>예

다음 텍스트 값: Hello, "세계"<br/>
ABC DEF<br/>
"ABC\D" EF<br/>
"ABC DEF<br/>

다음과 같이 적절 한 CSV 파일에 표시 되어야 합니다. "Hello," "" ""<br/>
"ABC DEF"<br/>
"" "ABC DEF"<br/>
"" "ABC\D" "EF"<br/>

기본 이스케이프 문자 (백슬래시)를 사용 하면 Azure 데이터 탐색기에서 다음 CSV가 작동 하지 않습니다. "Hello, \"세계\""<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>중첩 된 JSON 개체

JSON 파일을 Azure 데이터 탐색기에 복사할 때 다음 사항에 유의 하세요.
* 배열은 지원 되지 않습니다.
* JSON 구조에 개체 데이터 형식이 포함 된 경우 Azure Data Factory는 개체의 자식 항목을 평면화 하 고 각 자식 항목을 Azure 데이터 탐색기 테이블의 다른 열에 매핑하려고 합니다. 전체 개체 항목이 Azure 데이터 탐색기의 단일 열에 매핑되도록 하려면 다음을 수행 합니다.
    * 전체 JSON 행을 Azure 데이터 탐색기의 단일 동적 열에 수집 합니다.
    * Azure Data Factory의 JSON 편집기를 사용 하 여 파이프라인 정의를 수동으로 편집 합니다. **매핑**
       * 각 자식 항목에 대해 만들어진 여러 매핑을 제거 하 고, 개체 유형을 테이블 열에 매핑하는 단일 매핑을 추가 합니다.
       * 닫는 대괄호 뒤에 쉼표를 추가 하 고 다음을 추가 합니다.<br/>
       `"mapComplexValuesToString": true`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Azure 데이터 탐색기에 복사할 때 AdditionalProperties 지정

> [!NOTE]
> 이 기능은 현재 JSON 페이로드를 수동으로 편집 하 여 사용할 수 있습니다. 

복사 작업의 "싱크" 섹션 아래에 다음과 같이 단일 행을 추가 합니다.

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

값의 이스케이프는 어려울 수 있습니다. 다음 코드 조각을 참조로 사용 합니다.

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

인쇄 된 값:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory를 사용 하 여 Azure 데이터 탐색기에 데이터를 복사](data-factory-load-data.md)하는 방법을 알아봅니다.
* [데이터베이스에서 Azure 데이터 탐색기 대량 복사를 위해 Azure Data Factory 템플릿](data-factory-template.md)사용에 대해 알아봅니다.
* [Azure Data Factory 명령 작업을 사용 하 여 Azure 데이터 탐색기 제어 명령을 실행](data-factory-command-activity.md)하는 방법을 알아봅니다.
* 데이터 쿼리를 위한 [Azure 데이터 탐색기 쿼리](/azure/data-explorer/web-query-data) 에 대해 알아봅니다.



