---
title: Azure Stream Analytics의 출력 이해
description: 이 문서에서는 분석 결과에 대한 Power BI를 포함하여 Azure Stream Analytics에서 사용할 수 있는 데이터 출력 옵션을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: cfd4c113391f2ead238f5288c255b599e91b7e3a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201461"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Azure Stream Analytics의 출력 이해

이 문서에서는 Azure Stream Analytics 작업에 사용할 수 있는 출력 유형을 설명 합니다. 출력을 사용하여 Stream Analytics 작업의 결과를 저장할 수 있습니다. 출력 데이터를 사용 하 여 데이터의 비즈니스 분석과 데이터 웨어하우징을 추가로 수행할 수 있습니다.

Stream Analytics 쿼리를 디자인할 때 [INTO 절](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)을 사용 하 여 출력의 이름을 참조 합니다. 쿼리에 여러 INTO 절을 제공 하 여 작업당 단일 출력 또는 스트리밍 작업당 여러 출력 (필요한 경우)을 사용할 수 있습니다.

Stream Analytics 작업 출력을 만들고 편집 하 고 테스트 하려면 [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [AZURE POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)및 [Visual Studio](stream-analytics-quick-create-vs.md)를 사용할 수 있습니다.

일부 출력 형식은 [분할](#partitioning)을 지원 합니다. [출력 일괄 처리 크기](#output-batch-size) 는 처리량을 최적화 하는 데 차이가 있습니다.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics는 [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)을 지원 합니다. Azure Data Lake Storage는 빅 데이터 분석 워크 로드에 대 한 엔터프라이즈급의 대규모 리포지토리입니다. Data Lake Storage를 사용 하 여 운영 및 예비 분석에 대해 모든 크기, 유형 및 수집 속도의 데이터를 저장할 수 있습니다. Stream Analytics Data Lake Storage에 액세스할 수 있는 권한이 있어야 합니다.

Stream Analytics의 Azure Data Lake Storage 출력은 현재 Azure 중국 21Vianet 및 Azure 독일 (T-sql 국제) 지역에서 사용할 수 없습니다.

다음 표에서는 Data Lake Storage Gen 1 출력을 구성 하기 위한 속성 이름 및 해당 설명을 나열 합니다.   

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 | 쿼리 출력을 Data Lake Store으로 보내기 위해 쿼리에서 사용 되는 식별 이름입니다. |
| Subscription | Azure Data Lake Storage 계정을 포함 하는 구독입니다. |
| 계정 이름 | 출력을 전송 하는 Data Lake Store 계정의 이름입니다. 구독에서 사용할 수 있는 Data Lake Store 계정의 드롭다운 목록이 표시 됩니다. |
| 경로 접두사 패턴 | 지정 된 Data Lake Store 계정 내에서 파일을 작성 하는 데 사용 되는 파일 경로입니다. {Date} 및 {time} 변수의 인스턴스를 하나 이상 지정할 수 있습니다.<br /><ul><li>예제 1: folder1/logs/{date}/{time}</li><li>예제 2: folder1/logs/{date}</li></ul><br />만든 폴더 구조의 타임 스탬프는 현지 시간이 아닌 UTC를 따릅니다.<br /><br />파일 경로 패턴에 후행 슬래시 (/)가 포함 되어 있지 않으면 파일 경로의 마지막 패턴이 파일 이름 접두사로 처리 됩니다. <br /><br />이 경우 새 파일이 만들어집니다.<ul><li>출력 스키마의 변경</li><li>작업의 외부 또는 내부 다시 시작</li></ul> |
| 날짜 형식 | (선택 사항) 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD |
|시간 형식 | (선택 사항) 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다.|
| Encoding | CSV 또는 JSON 형식을 사용 하는 경우 인코딩을 지정 해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.|
| 구분 기호 | CSV serialization에만 적용 됩니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.|
| 형식 | JSON serialization에만 적용 됩니다. **줄 구분** 은 각 JSON 개체를 새 줄로 구분 하 여 출력 형식을 지정 하도록 지정 합니다. **배열은** 출력의 형식을 JSON 개체의 배열로 지정 합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로는 출력 파일을 쓰는 동안 특별 한 처리가 필요 하지 않으므로 줄로 구분 된 JSON을 사용 하는 것이 좋습니다.|
| 인증 모드 | [관리 id](stream-analytics-managed-identities-adls.md) 또는 사용자 토큰을 사용 하 여 Data Lake Storage 계정에 대 한 액세스 권한을 부여할 수 있습니다. 액세스 권한을 부여 하 고 나면 사용자 계정 암호를 변경 하거나이 작업에 대 한 Data Lake Storage 출력을 삭제 하거나 Stream Analytics 작업을 삭제 하 여 액세스를 해지할 수 있습니다. |

## <a name="sql-database"></a>SQL Database

관계형 데이터베이스에서 호스팅되는 콘텐츠를 사용 하는 응용 프로그램 또는 본질적으로 관계형 인 데이터에 대 한 출력으로 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 를 사용할 수 있습니다. Stream Analytics 작업은 SQL Database의 기존 테이블에 기록 합니다. 테이블 스키마는 작업 출력에서 필드 및 해당 형식과 정확히 일치 해야 합니다. SQL Database 출력 옵션을 통해 [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) 를 출력으로 지정할 수도 있습니다. 쓰기 처리량을 개선 하는 방법에 대 한 자세한 내용은 [Azure SQL Database를 사용 하 여 출력](stream-analytics-sql-output-perf.md) 에 대 한 Stream Analytics 문서를 참조 하세요.

[Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) 를 출력으로 사용할 수도 있습니다. [Azure SQL Database Managed Instance에서 공용 끝점을 구성](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) 하 고 Azure Stream Analytics에서 다음 설정을 수동으로 구성 해야 합니다. 아래 설정을 수동으로 구성 하 여 연결 된 데이터베이스와 함께 SQL Server를 실행 하는 Azure 가상 컴퓨터도 지원 됩니다.

다음 표에서는 SQL Database 출력을 만들기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 데이터베이스 | 출력을 보내는 데이터베이스의 이름입니다. |
| 서버 이름 | SQL Database 서버 이름입니다. Azure SQL Database Managed Instance의 경우 포트 3342를 지정 해야 합니다. 예를 들어 *sampleserver, 3342* 입니다. |
| 사용자 이름 | 데이터베이스에 대 한 쓰기 권한이 있는 사용자 이름입니다. Stream Analytics는 SQL 인증만 지원 합니다. |
| 암호 | 데이터베이스에 연결하는 암호입니다. |
| 테이블 | 출력을 기록되는 테이블 이름입니다. 테이블 이름은 대/소문자를 구분 합니다. 이 테이블의 스키마는 작업 출력에서 생성 하는 필드 수와 해당 형식에 정확히 일치 해야 합니다. |
|파티션 구성표 상속| 여러 작성기를 사용 하 여 테이블에 대 한 완전 한 병렬 토폴로지를 사용할 수 있도록 이전 쿼리 단계의 파티션 구성표를 상속 하는 옵션입니다. 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)을 참조하세요.|
|최대 일괄 처리 수| 모든 bulk insert 트랜잭션과 함께 전송 되는 레코드 수에 대 한 권장 상한입니다.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob storage 및 Azure Data Lake Gen2

Data Lake Storage Gen2는 Azure에서 Azure Storage를 엔터프라이즈 데이터 레이크를 구축하기 위한 기반으로 만듭니다. 페타바이트는 수백 가지 기가 비트의 처리량 Data Lake Storage Gen2을 유지 하는 동시에 여러 정보를 서비스 하는 데 사용 됩니다. Data Lake Storage Gen2의 기본 부분은 Blob 저장소에 대 한 계층적 네임 스페이스를 추가 하는 것입니다.

Azure Blob storage는 클라우드에 구조화 되지 않은 대량의 데이터를 저장 하기 위한 비용 효율적이 고 확장 가능한 솔루션을 제공 합니다. Blob storage 및 사용 현황에 대 한 소개는 [Azure Portal을 사용 하 여 Blob 업로드, 다운로드 및 나열](../storage/blobs/storage-quickstart-blobs-portal.md)을 참조 하세요.

다음 표에서는 blob 또는 ADLS Gen2 출력을 만들기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 출력 별칭        | 쿼리 출력을 이 Blob Storage로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 스토리지 계정     | 출력을 전송 하는 저장소 계정의 이름입니다.               |
| Storage 계정 키 | 스토리지 계정과 연결된 비밀 키입니다.                              |
| 저장소 컨테이너   | Azure Blob service에 저장 된 blob에 대 한 논리적 그룹화입니다. Blob service에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다. |
| 경로 패턴 | (선택 사항) 지정 된 컨테이너 내에서 blob를 작성 하는 데 사용 되는 파일 경로 패턴입니다. <br /><br /> 경로 패턴에서 하나 이상의 날짜 및 시간 변수 인스턴스를 사용 하 여 blob이 작성 되는 빈도를 지정 하도록 선택할 수 있습니다. <br /> {date}, {time} <br /><br />사용자 지정 Blob 분할을 사용하여 이벤트 데이터에서 파티션 Blob까지 하나의 사용자 지정 {field} 이름을 지정할 수 있습니다. 필드 이름은 영숫자이며 공백, 하이픈 및 밑줄을 포함할 수 있습니다. 사용자 지정 필드에 대한 제한은 다음을 포함합니다. <ul><li>필드 이름은 대/소문자를 구분 하지 않습니다. 예를 들어, 서비스는 열 "ID"와 열 "id"를 구분할 수 없습니다.</li><li>중첩 된 필드는 허용 되지 않습니다. 대신 작업 쿼리에 별칭을 사용 하 여 필드를 "평면화" 합니다.</li><li>식을 필드 이름으로 사용할 수 없습니다.</li></ul> <br />이 기능을 사용하면 경로에 사용자 지정 날짜/시간 형식 지정자 구성을 사용할 수 있습니다. 사용자 지정 날짜 및 시간 형식은 {datetime:\<specifier>} 키워드로 묶어 한 번에 하나씩만 지정해야 합니다. \<지정자 >에 허용 되는 입력은 yyyy, MM, M, dd, d, HH, H, MM, M, ss 또는 s입니다. {Datetime:\<지정자 >} 키워드는 경로에서 여러 번 사용 하 여 사용자 지정 날짜/시간 구성을 구성할 수 있습니다. <br /><br />예제: <ul><li>예제 1: cluster1/logs/{date}/{time}</li><li>예제 2: cluster1/logs/{date}</li><li>예제 3: cluster1/{client_id}/{date}/{time}</li><li>예제 4: cluster1/{datetime: ss}/{myField} 쿼리: SELECT FROM myField AS myField in Input;</li><li>예제 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />만든 폴더 구조의 타임 스탬프는 현지 시간이 아닌 UTC를 따릅니다.<br /><br />파일 이름 지정에는 다음 규칙이 사용 됩니다. <br /><br />{경로 접두사 패턴}/schemaHashcode_Guid_Number.extension<br /><br />예제 출력 파일:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />이 기능에 대 한 자세한 내용은 [Azure Stream Analytics 사용자 지정 blob 출력 분할](stream-analytics-custom-path-patterns-blob-storage-output.md)을 참조 하세요. |
| 날짜 형식 | (선택 사항) 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD |
| 시간 형식 | (선택 사항) 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, Avro 및 Parquet가 지원 됩니다. |
|최소 행 (Parquet에만 해당)|일괄 처리당 최소 행 수입니다. Parquet의 경우 모든 일괄 처리는 새 파일을 만듭니다. 현재 기본값은 2000 행이 고 허용 되는 최대 값은 1만 행입니다.|
|최대 시간 (Parquet에만 해당)|일괄 처리당 최대 대기 시간입니다. 이 시간 후에는 최소 행 요구 사항이 충족 되지 않은 경우에도 일괄 처리가 출력에 기록 됩니다. 현재 기본값은 1 분이 고 허용 되는 최대값은 2 시간입니다. Blob 출력에 경로 패턴 빈도가 있는 경우 대기 시간은 파티션 시간 범위 보다 높을 수 없습니다.|
| Encoding    | CSV 또는 JSON 형식을 사용 하는 경우 인코딩을 지정 해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호   | CSV serialization에만 적용 됩니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식      | JSON serialization에만 적용 됩니다. **줄 구분** 은 각 JSON 개체를 새 줄로 구분 하 여 출력 형식을 지정 하도록 지정 합니다. **배열은** 출력의 형식을 JSON 개체의 배열로 지정 합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로는 출력 파일을 쓰는 동안 특별 한 처리가 필요 하지 않으므로 줄로 구분 된 JSON을 사용 하는 것이 좋습니다. |

Blob 저장소를 출력으로 사용 하는 경우 다음과 같은 경우에 새 파일이 blob에 만들어집니다.

* 파일이 허용되는 최대 블록 수(현재 50,000)를 초과합니다. 허용 되는 최대 blob 크기에 도달 하지 않고 허용 되는 최대 블록 수에 도달할 수 있습니다. 예를 들어 출력 속도가 높으면 블록당 더 많은 바이트를 볼 수 있으며 파일 크기는 더 커집니다. 출력 속도가 낮으면 각 블록의 데이터가 줄어들고 파일 크기도 작아집니다.
* 출력에 스키마가 변경 되 고 출력 형식에 고정 스키마 (CSV 및 Avro)가 필요한 경우
* 작업이 다시 시작되는 경우 사용자가 외부적으로 또는 시스템 유지 관리 또는 오류 복구의 경우 내부적으로 중지하고 시작합니다.
* 쿼리가 완전히 분할 되 고 각 출력 파티션에 대해 새 파일이 만들어집니다.
* 사용자가 저장소 계정의 파일 또는 컨테이너를 삭제 하는 경우
* 출력이 경로 접두사 패턴을 사용 하 여 분할 된 시간이 며 쿼리가 다음 시간으로 이동할 때 새 blob이 사용 됩니다.
* 출력이 사용자 지정 필드에 의해 분할 되 고 파티션 키가 없는 경우 새 blob이 생성 되 면이 고,
* 출력이 파티션 키 카디널리티가 8000를 초과 하는 사용자 지정 필드에 의해 분할 되 고 파티션 키 당 새 blob이 생성 되는 경우

## <a name="event-hubs"></a>Event Hubs

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) 서비스는 확장성이 뛰어난 게시-구독 이벤트 수집기입니다. 초당 수 백만의 이벤트를 수집할 수 있습니다. 출력으로 이벤트 허브를 사용 하는 한 가지 방법은 Stream Analytics 작업의 출력이 다른 스트리밍 작업의 입력이 되는 경우입니다. 최대 메시지 크기 및 일괄 처리 크기 최적화에 대 한 자세한 내용은 [출력 일괄 처리 크기](#output-batch-size) 섹션을 참조 하세요.

이벤트 허브에서 출력으로 데이터 스트림을 구성 하는 몇 가지 매개 변수가 필요 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 | 쿼리 출력을이 이벤트 허브로 보내기 위해 쿼리에서 사용 되는 식별 이름입니다. |
| 이벤트 허브 네임스페이스 | 메시징 엔터티 집합에 대 한 컨테이너입니다. 새 이벤트 허브를 만들 때 event hub 네임 스페이스도 만들었습니다. |
| 이벤트 허브 이름 | 이벤트 허브 출력의 이름입니다. |
| 이벤트 허브 정책 이름 | 이벤트 허브의 **구성** 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 이벤트 허브 정책 키 | 이벤트 허브 네임 스페이스에 대 한 액세스를 인증 하는 데 사용 되는 공유 액세스 키입니다. |
| 파티션 키 열 | (선택 사항) 이벤트 허브 출력에 대 한 파티션 키를 포함 하는 열입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding | CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 | CSV serialization에만 적용 됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 | JSON serialization에만 적용 됩니다. **줄 구분** 은 각 JSON 개체를 새 줄로 구분 하 여 출력 형식을 지정 하도록 지정 합니다. **배열은** 출력의 형식을 JSON 개체의 배열로 지정 합니다.  |
| 속성 열 | (선택 사항) 페이로드가 아닌 보내는 메시지의 사용자 속성으로 연결 해야 하는 쉼표로 구분 된 열입니다. 이 기능에 대 한 자세한 내용은 [출력에 대 한 사용자 지정 메타 데이터 속성](#custom-metadata-properties-for-output)섹션에 있습니다. |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) 를 Stream Analytics 작업에 대 한 출력으로 사용 하 여 분석 결과에 대 한 풍부한 시각화 환경을 제공할 수 있습니다. 운영 대시보드, 보고서 생성 및 메트릭 기반 보고에이 기능을 사용할 수 있습니다.

Stream Analytics의 Power BI 출력은 현재 Azure 중국 21Vianet 및 Azure 독일 (T-sql 국제) 지역에서 사용할 수 없습니다.

다음 표에서는 Power BI 출력을 구성 하기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을이 Power BI 출력으로 보내기 위해 쿼리에서 사용 되는 친숙 한 이름을 제공 합니다. |
| 그룹 작업 영역 |다른 Power BI 사용자와 데이터를 공유할 수 있도록 하려면 Power BI 계정 내에서 그룹을 선택 하거나 그룹에 쓰지 않으려는 경우 **내 작업 영역** 을 선택 합니다. 기존 그룹을 업데이트하려면 Power BI 인증을 갱신해야 합니다. |
| 데이터 세트 이름 |Power BI 출력에 사용할 데이터 집합 이름을 제공 합니다. |
| 테이블 이름 |Power BI 출력의 데이터 세트 아래에 테이블 이름을 제공합니다. 현재, Stream Analytics 작업의 Power BI 출력에는 하나의 데이터 세트에 하나의 테이블만 있을 수 있습니다. |
| 연결 권한 부여 | 출력 설정을 구성 하려면 Power BI를 사용 하 여 권한을 부여 해야 합니다. 이 출력에 Power BI 대시보드에 대 한 액세스 권한을 부여 하면 사용자 계정 암호를 변경 하거나, 작업 출력을 삭제 하거나, Stream Analytics 작업을 삭제 하 여 액세스를 해지할 수 있습니다. | 

Power BI 출력 및 대시보드를 구성 하는 연습은 [Azure Stream Analytics 및 Power BI](stream-analytics-power-bi-dashboard.md) 자습서를 참조 하세요.

> [!NOTE]
> Power BI 대시보드에서 데이터 집합 및 테이블을 명시적으로 만들지 마세요. 작업을 시작 하 고 작업에서 출력을 Power BI로 펌프 하기 시작 하면 데이터 집합 및 테이블은 자동으로 채워집니다. 작업 쿼리가 결과를 생성 하지 않으면 데이터 집합 및 테이블이 생성 되지 않습니다. 이 Stream Analytics 작업에서 제공 된 것과 동일한 이름의 데이터 집합과 테이블이 이미 Power BI 경우 기존 데이터를 덮어씁니다.
>

### <a name="create-a-schema"></a>스키마 만들기
Azure Stream Analytics는 사용자에 대 한 Power BI 데이터 집합 및 테이블 스키마가 아직 없는 경우 새로 만듭니다. 다른 모든 경우에는 테이블이 새 값으로 업데이트됩니다. 현재 데이터 집합 내에는 하나의 테이블만 있을 수 있습니다. 

Power BI은 FIFO (선입 선출) 보존 정책을 사용 합니다. 데이터는 20만 행에 도달할 때까지 테이블에서 수집 됩니다.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Stream Analytics에서 Power BI로 데이터 형식 변환
Azure Stream Analytics는 출력 스키마가 변경되면 런타임 시 동적으로 데이터 모델을 업데이트합니다. 열 이름 변경, 열 형식 변경 및 열 추가 또는 제거 작업이 모두 추적됩니다.

이 표에서는 Power BI 데이터 형식 및 테이블이 없는 경우 [Stream Analytics 데이터 형식](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) 에서 [EDM (Power BI 엔터티 데이터 모델) 형식](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)으로의 데이터 형식 변환에 대해 설명 합니다.

Stream Analytics에서 | Power BI로
-----|-----
bigint | Int64
nvarchar(max) | String
Datetime | DateTime
float | Double
레코드 배열 | 문자열 형식, 상수 값 "IRecord" 또는 "Irecord"

### <a name="update-the-schema"></a>스키마 업데이트
Stream Analytics는 출력의 첫 번째 이벤트 집합을 기반으로 데이터 모델 스키마를 유추합니다. 나중에 필요한 경우 원본 스키마에 맞지 않을 수 있는 들어오는 이벤트를 수용 하도록 데이터 모델 스키마가 업데이트 됩니다.

행 간 동적 스키마 업데이트를 방지 하기 위해 `SELECT *` 쿼리를 사용 하지 않습니다. 잠재적 성능 영향 외에도 결과에 소요 되는 시간이 불확실 해질 수 있습니다. Power BI 대시보드에 표시 해야 하는 정확한 필드를 선택 합니다. 또한 데이터 값은 선택한 데이터 형식을 준수해야 합니다.


이전/현재 | Int64 | String | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
DateTime | String | String |  DateTime | String

## <a name="table-storage"></a>Table Storage

[Azure Table Storage](../storage/common/storage-introduction.md)는 가용성이 높고 확장성이 큰 스토리지를 제공하므로, 애플리케이션이 사용자 요구에 맞게 자동으로 확장할 수 있습니다. 테이블 저장소는 Microsoft의 NoSQL 키/특성 저장소로, 스키마에 대 한 제약 조건이 더 작은 구조화 된 데이터에 사용할 수 있습니다. Azure Table Storage는 지속적이고 효율적인 검색을 위해 데이터를 저장하는 데 사용할 수 있습니다.

다음 표에서는 테이블 출력을 만들기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 Table Storage로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| 스토리지 계정 |출력을 전송 하는 저장소 계정의 이름입니다. |
| Storage 계정 키 |스토리지 계정과 연결된 선택키입니다. |
| 테이블 이름 |테이블의 이름입니다. 테이블이 존재 하지 않는 경우 생성 됩니다. |
| 파티션 키 |파티션 키를 포함 하는 출력 열의 이름입니다. 파티션 키는 엔터티의 기본 키의 첫 번째 부분을 형성 하는 테이블 내의 파티션에 대 한 고유 식별자입니다. 크기는 최대 1kb 일 수 있는 문자열 값입니다. |
| 행 키. |행 키를 포함 하는 출력 열의 이름입니다. 행 키는 파티션 내의 엔터티에 대 한 고유 식별자입니다. 엔터티의 기본 키에서 두 번째 부분을 형성합니다. 행 키는 최대 1kb의 크기를 가질 수 있는 문자열 값입니다. |
| Batch 크기 |배치 작업에 대한 레코드 수입니다. 기본값(100)은 대부분의 작업에 충분합니다. 이 설정을 수정 하는 방법에 대 한 자세한 내용은 [Batch 작업 사양 표](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) 를 참조 하세요. |

## <a name="service-bus-queues"></a>Service Bus 큐

[Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) 는 하나 이상의 경쟁 소비자에 게 FIFO 메시지 배달을 제공 합니다. 일반적으로 메시지는 큐에 추가 된 임시 순서로 수신자에 의해 수신 되 고 처리 됩니다. 각 메시지는 하나의 메시지 소비자에 의해서만 수신 및 처리 됩니다.

[호환성 수준 1.2](stream-analytics-compatibility-level.md)에서 Azure Stream Analytics는 [Amqp (Advanced Message queuing protocol)](../service-bus-messaging/service-bus-amqp-overview.md) 메시징 프로토콜을 사용 하 여 Service Bus 큐 및 토픽에 기록 합니다. AMQP를 사용하여 여러 플랫폼 간에 개방형 표준 프로토콜을 사용하는 하이브리드 애플리케이션을 빌드할 수 있습니다.

다음 표에서는 큐 출력을 만들기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을이 Service Bus 큐로 보내기 위해 쿼리에서 사용 되는 식별 이름입니다. |
| Service Bus 네임스페이스 |메시징 엔터티 집합에 대 한 컨테이너입니다. |
| 큐 이름 |Service Bus 큐의 이름입니다. |
| 큐 정책 이름 |큐를 만들 때 큐의 **구성** 탭에서 공유 액세스 정책을 만들 수도 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 큐 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 이벤트 직렬화 형식 |출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 |CSV serialization에만 적용 됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 |JSON 형식에만 적용할 수 있습니다. **줄 구분** 은 각 JSON 개체를 새 줄로 구분 하 여 출력 형식을 지정 하도록 지정 합니다. **배열은** 출력의 형식을 JSON 개체의 배열로 지정 합니다. |
| 속성 열 | (선택 사항) 페이로드가 아닌 보내는 메시지의 사용자 속성으로 연결 해야 하는 쉼표로 구분 된 열입니다. 이 기능에 대 한 자세한 내용은 [출력에 대 한 사용자 지정 메타 데이터 속성](#custom-metadata-properties-for-output)섹션에 있습니다. |
| 시스템 속성 열 | (선택 사항) 페이로드 대신 나가는 메시지에 연결 해야 하는 시스템 속성 및 해당 열 이름에 대 한 키 값 쌍입니다. 이 기능에 대 한 자세한 내용은 [Service Bus 큐 및 토픽 출력의 시스템 속성](#system-properties-for-service-bus-queue-and-topic-outputs) 섹션에 있습니다.  |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.

## <a name="service-bus-topics"></a>Service Bus 토픽
Service Bus 큐는 보낸 사람에서 받는 사람으로의 일대일 통신 방법을 제공 합니다. [Service Bus 항목](https://msdn.microsoft.com/library/azure/hh367516.aspx) 에서는 일 대 다 형태의 통신을 제공 합니다.

다음 표에서는 Service Bus 토픽 출력을 만들기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을이 Service Bus 토픽으로 보내기 위해 쿼리에서 사용 되는 식별 이름입니다. |
| Service Bus 네임스페이스 |메시징 엔터티 집합에 대 한 컨테이너입니다. 새 이벤트 허브를 만들 때 Service Bus 네임스페이스도 만들었습니다. |
| 항목 이름 |토픽은 이벤트 허브 및 큐와 유사한 메시징 엔터티입니다. 장치 및 서비스에서 이벤트 스트림을 수집 하도록 설계 되었습니다. 항목을 만들 때 특정 이름도 지정 됩니다. 구독을 만들지 않으면 토픽으로 전송 된 메시지를 사용할 수 없으므로 토픽 아래에 구독이 하나 이상 있는지 확인 합니다. |
| 토픽 정책 이름 |Service Bus 토픽을 만들 때 항목의 **구성** 탭에서 공유 액세스 정책을 만들 수도 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 토픽 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 이벤트 직렬화 형식 |출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 또는 JSON 형식을 사용 하는 경우 인코딩을 지정 해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호 |CSV serialization에만 적용 됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 속성 열 | (선택 사항) 페이로드가 아닌 보내는 메시지의 사용자 속성으로 연결 해야 하는 쉼표로 구분 된 열입니다. 이 기능에 대 한 자세한 내용은 [출력에 대 한 사용자 지정 메타 데이터 속성](#custom-metadata-properties-for-output)섹션에 있습니다. |
| 시스템 속성 열 | (선택 사항) 페이로드 대신 나가는 메시지에 연결 해야 하는 시스템 속성 및 해당 열 이름에 대 한 키 값 쌍입니다. 이 기능에 대 한 자세한 내용은 [Service Bus 큐 및 토픽 출력의 시스템 속성](#system-properties-for-service-bus-queue-and-topic-outputs) 섹션에 있습니다. |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대 한 고유한 정수 값입니다.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 은 전 세계적으로 분산 된 데이터베이스 서비스 이며 스키마를 알 수 없는 데이터 모델에 대 한 다양 한 쿼리 및 자동 인덱싱을 제공 합니다. Stream Analytics에 대 한 Azure Cosmos DB 컨테이너 옵션에 대 한 자세한 내용은 [Stream Analytics Azure Cosmos DB 출력](stream-analytics-documentdb-output.md) 문서를 참조 하세요.

Stream Analytics의 Azure Cosmos DB 출력은 현재 Azure 중국 21Vianet 및 Azure 독일 (T-sql 국제) 지역에서 사용할 수 없습니다.

> [!Note]
> 현재 Azure Stream Analytics는 SQL API를 사용 하 여 Azure Cosmos DB에 대 한 연결만 지원 합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. Azure Stream Analytics를 다른 API로 만든 Azure Cosmos DB 계정에 지정한 경우 데이터는 올바르게 저장되지 않을 수도 있습니다.

다음 표에서는 Azure Cosmos DB 출력을 만드는 속성에 대해 설명합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 | Stream Analytics 쿼리에서 이 출력을 참조할 별칭입니다. |
| sink | Azure Cosmos DB. |
| 가져오기 옵션 | **구독에서 Cosmos DB 선택** 또는 **Cosmos DB 설정 수동으로 제공**을 선택 합니다.
| 계정 ID | Azure Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다. |
| 계정 키 | Azure Cosmos DB 계정에 대한 공유 액세스 키입니다. |
| 데이터베이스 | Azure Cosmos DB 데이터베이스 이름입니다. |
| 컨테이너 이름 | 사용할 컨테이너 이름 Cosmos DB에 존재 해야 합니다. 예제:  <br /><ul><li> _Mycontainer_: "mycontainer" 라는 컨테이너가 있어야 합니다.</li>|
| 문서 ID |(선택 사항) 삽입 또는 업데이트 작업의 기반이 되는 기본 키를 지정 하는 데 사용 되는 출력 이벤트의 필드 이름입니다.

## <a name="azure-functions"></a>Azure 기능
Azure Functions는 인프라를 명시적으로 프로 비전 하거나 관리 하지 않고도 요청 시 코드를 실행 하는 데 사용할 수 있는 서버를 사용 하지 않는 계산 서비스입니다. 이를 통해 Azure 또는 파트너 서비스에서 발생 하는 이벤트에 의해 트리거되는 코드를 구현할 수 있습니다. 트리거에 응답 하는 이러한 Azure Functions 기능을 사용 하면 Azure Stream Analytics에 대 한 자연 스러운 출력이 됩니다. 이 출력 어댑터를 사용 하 여 Stream Analytics Azure Functions에 연결 하 고 다양 한 이벤트에 대 한 응답으로 스크립트 또는 코드 조각을 실행할 수 있습니다.

Stream Analytics의 Azure Functions 출력은 현재 Azure 중국 21Vianet 및 Azure 독일 (T-sql 국제) 지역에서 사용할 수 없습니다.

Azure Stream Analytics는 HTTP 트리거를 통해 Azure Functions를 호출합니다. 다음 구성 가능한 속성을 사용 하 여 Azure Functions 출력 어댑터를 사용할 수 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 함수 앱 |Azure Functions 앱의 이름입니다. |
| 함수 |Azure Functions 앱의 함수 이름입니다. |
| 키 |다른 구독에서 Azure 함수를 사용 하려는 경우 함수에 액세스 하기 위한 키를 제공 하 여이 작업을 수행할 수 있습니다. |
| 최대 일괄 처리 크기 |Azure 함수로 전송 되는 각 출력 일괄 처리의 최대 크기를 설정할 수 있는 속성입니다. 입력 단위는 바이트입니다. 기본적으로이 값은 262144 바이트 (256 KB)입니다. |
| 최대 일괄 처리 수  |Azure Functions로 전송 되는 각 일괄 처리의 최대 이벤트 수를 지정할 수 있는 속성입니다. 기본값은 100입니다. |

성공적으로 처리 된 일괄 처리에 대해서는 함수 앱의 HTTP 상태 200가 필요 Azure Stream Analytics.

Azure Stream Analytics는 Azure 함수에서 413 ("http 요청 엔터티 너무 큼") 예외를 수신 하면 Azure Functions 전송 되는 일괄 처리 크기를 줄입니다. Azure 함수 코드에서 이 예외를 사용하여 Azure Stream Analytics가 너무 큰 일괄 처리를 전송하지 않도록 합니다. 또한 함수에 사용 되는 최대 일괄 처리 수 및 크기 값이 Stream Analytics 포털에 입력 한 값과 일치 하는지 확인 합니다.

> [!NOTE]
> 연결을 테스트 하는 동안 Stream Analytics는 Azure Functions에 대 한 빈 일괄 처리를 보내 두 작업 간의 연결이 작동 하는지 테스트 합니다. 함수 앱이 빈 일괄 처리 요청을 처리 하 여 연결 테스트에 통과 하는지 확인 합니다.

또한 시간 창에 이벤트가 발생 하지 않는 경우 출력이 생성 되지 않습니다. 따라서 **computeResult** 함수는 호출 되지 않습니다. 이 동작은 기본 제공 기간 이동 집계 함수와 일치합니다.

## <a name="custom-metadata-properties-for-output"></a>출력에 대 한 사용자 지정 메타 데이터 속성 

보내는 메시지에 쿼리 열을 사용자 속성으로 연결할 수 있습니다. 이러한 열은 페이로드로 이동 하지 않습니다. 속성은 출력 메시지에 대 한 사전 형식으로 제공 됩니다. *Key* 는 열 이름이 고 *값* 은 속성 사전의 열 값입니다. 레코드와 배열을 제외 하 고 모든 Stream Analytics 데이터 형식이 지원 됩니다.  

지원 되는 출력: 
* Service Bus 큐 
* Service Bus 항목 
* 이벤트 허브 

다음 예제에서는 `DeviceId` 두 필드를 추가 하 고 메타 데이터에 `DeviceStatus` 합니다. 
* 쿼리: `select *, DeviceId, DeviceStatus from iotHubInput`
* 출력 구성: `DeviceId,DeviceStatus`

![속성 열](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

다음 스크린샷은 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer)를 통해 EventHub에서 검사 된 출력 메시지 속성을 보여 줍니다.

![이벤트 사용자 지정 속성](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Service Bus 큐 및 항목 출력에 대 한 시스템 속성 
보내는 service bus 큐 또는 토픽 메시지에 쿼리 열을 [시스템 속성](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) 으로 연결할 수 있습니다. 이러한 열은 페이로드로 이동 하지 않습니다. 대신 해당 BrokeredMessage [system 속성이](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) 쿼리 열 값으로 채워집니다.
이러한 시스템 속성은 `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`지원 됩니다.
이러한 열의 문자열 값은 해당 시스템 속성 값 형식으로 구문 분석 되 고 구문 분석 오류는 데이터 오류로 처리 됩니다.
이 필드는 JSON 개체 형식으로 제공 됩니다. 이 형식에 대 한 자세한 내용은 다음과 같습니다.
* 중괄호 {}로 둘러싸여 있습니다.
* 키/값 쌍으로 작성 됩니다.
* 키와 값은 문자열 이어야 합니다.
* 키는 시스템 속성 이름 이며 값은 쿼리 열 이름입니다.
* 키와 값은 콜론으로 구분 됩니다.
* 각 키/값 쌍은 쉼표로 구분 됩니다.

이 속성을 사용 하는 방법을 보여 줍니다.

* 쿼리: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 시스템 속성 열: `{ "MessageId": "column1", "PartitionKey": "column2"}`

이렇게 하면 `column1`값이 있는 service bus 큐 메시지의 `MessageId` 설정 되 고 PartitionKey가 `column2`의 값으로 설정 됩니다.

## <a name="partitioning"></a>분할

다음 표에서는 각 출력 유형에 대한 출력 기록기 수 및 파티션 지원이 요약되어 있습니다.

| 출력 형식 | 분할 지원 | 파티션 키  | 출력 기록기 수 |
| --- | --- | --- | --- |
| Azure Data Lake Store | yes | 경로 접두사 패턴에 {date} 및 {time} 토큰을 사용 합니다. 날짜 형식 (예: YYYY/MM/DD, DD/MM/YYYY 또는 MM-DD-YYYY)을 선택 합니다. HH는 시간 형식에 사용 됩니다. | [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure SQL Database | 예, 사용 하도록 설정 해야 합니다. | 쿼리의 PARTITION BY 절을 기반으로 합니다. | 분할 상속 옵션을 사용 하도록 설정 하면는 [완전히 병렬화 쿼리에](stream-analytics-scale-jobs.md)대 한 입력 분할을 따릅니다. Azure SQL Database으로 데이터를 로드할 때 더 나은 쓰기 처리량을 달성 하는 방법에 대 한 자세한 내용은 [Azure SQL Database Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)을 참조 하세요. |
| Azure BLOB 스토리지 | yes | 경로 패턴에서 이벤트 필드의 {date} 및 {time} 토큰을 사용 합니다. 날짜 형식 (예: YYYY/MM/DD, DD/MM/YYYY 또는 MM-DD-YYYY)을 선택 합니다. HH는 시간 형식에 사용 됩니다. 단일 사용자 지정 이벤트 특성 {fieldname} 또는 {datetime:\<specifier>}로 Blob 출력을 분할할 수 있습니다. | [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure Event Hubs | yes | yes | 파티션 맞춤에 따라 달라집니다.<br /> 이벤트 허브 출력에 대 한 파티션 키가 업스트림 (이전) 쿼리 단계와 동일 하 게 정렬 된 경우 작성기의 수는 이벤트 허브 출력의 파티션 수와 동일 합니다. 각 작성자는 [EventHubSender 클래스](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) 를 사용 하 여 이벤트를 특정 파티션으로 보냅니다. <br /> 이벤트 허브 출력에 대 한 파티션 키가 업스트림 (이전) 쿼리 단계와 맞지 않는 경우 작성자 수는 이전 단계에서의 파티션 수와 동일 합니다. 각 작성자는 **EventHubClient** 의 [sendbatchasync 클래스](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) 를 사용 하 여 모든 출력 파티션에 이벤트를 보냅니다. |
| Power BI | 예 | None | 해당 사항 없음 |
| Azure Table Storage | yes | 모든 출력 열입니다.  | [완전히 병렬 처리된 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure Service Bus 항목 | yes | 자동으로 선택됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대 한 고유한 정수 값입니다.| 출력 항목의 파티션 수와 동일합니다.  |
| Azure Service Bus 큐 | yes | 자동으로 선택됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대 한 고유한 정수 값입니다.| 출력 큐의 파티션 수와 동일합니다. |
| Azure Cosmos DB | yes | 쿼리의 PARTITION BY 절을 기반으로 합니다. | [완전히 병렬 처리된 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure 기능 | yes | 쿼리의 PARTITION BY 절을 기반으로 합니다. | [완전히 병렬 처리된 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |

쿼리에서 `INTO <partition count>` ( [INTO INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) 절을 사용 하 여 출력 기록기의 수를 제어할 수도 있습니다 .이는 원하는 작업 토폴로지를 달성 하는 데 도움이 될 수 있습니다. 출력 어댑터가 분할되지 않은 경우 한 입력 파티션에 데이터가 없으면 지연 도착 시간까지 지연될 수 있습니다. 이러한 경우 출력이 단일 기록기에 병합 되어 파이프라인에서 병목 현상이 발생할 수 있습니다. 지연 도착 정책에 대해 자세히 알아보려면 [Azure Stream Analytics 이벤트 순서 고려 사항](stream-analytics-out-of-order-and-late-events.md)을 참조 하세요.

## <a name="output-batch-size"></a>출력 일괄 처리 크기
Azure Stream Analytics는 가변 크기 일괄 처리를 사용 하 여 이벤트를 처리 하 고 출력에 씁니다. 일반적으로 Stream Analytics 엔진은 한 번에 하나의 메시지를 작성 하지 않으며 효율성을 위해 일괄 처리를 사용 합니다. 들어오는 이벤트와 나가는 이벤트의 비율이 높으면 Stream Analytics 큰 일괄 처리를 사용 합니다. 송신 속도가 낮은 경우 대기 시간을 낮게 유지하기 위해 더 작은 일괄 처리를 사용합니다.

다음 표에서는 출력 일괄 처리에 대 한 몇 가지 고려 사항을 설명 합니다.

| 출력 형식 | 최대 메시지 크기 | 일괄 처리 크기 최적화 |
| :--- | :--- | :--- |
| Azure Data Lake Store | [Data Lake Storage 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)을 참조 하세요. | 쓰기 작업당 최대 4mb를 사용 합니다. |
| Azure SQL Database | 최대 일괄 처리 수를 사용 하 여 구성할 수 있습니다. 1만 기본적으로 단일 대량 삽입 당 최대 및 100의 최소 행 수입니다.<br />[AZURE SQL 제한](../sql-database/sql-database-resource-limits.md)을 참조 하세요. |  모든 일괄 처리는 처음에 최대 일괄 처리 수로 대량 삽입 됩니다. 일괄 처리는 SQL에서 재시도 가능한 오류를 기준으로 절반 (최소 일괄 처리 수까지)으로 분할 됩니다. |
| Azure BLOB 스토리지 | [Azure Storage 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)을 참조 하세요. | 최대 blob 블록 크기는 4mb입니다.<br />최대 blob bock 수는 5만입니다. |
| Azure Event Hubs  | 메시지 당 256 KB 또는 1mb입니다. <br />[Event Hubs 제한](../event-hubs/event-hubs-quotas.md)을 참조 하세요. |  입력/출력 분할이 정렬 되지 않은 경우 각 이벤트는 `EventData`에 개별적으로 압축 되 고 최대 메시지 크기까지 일괄 처리로 전송 됩니다. 이는 [사용자 지정 메타 데이터 속성이](#custom-metadata-properties-for-output) 사용 되는 경우에도 마찬가지입니다. <br /><br />  입력/출력 분할이 정렬 된 경우 여러 이벤트는 단일 `EventData` 인스턴스로 압축 되며 최대 메시지 크기까지 전송 됩니다. |
| Power BI | [Power BI REST API 제한](https://msdn.microsoft.com/library/dn950053.aspx)을 참조 하세요. |
| Azure Table Storage | [Azure Storage 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)을 참조 하세요. | 기본값은 단일 트랜잭션 당 100 엔터티입니다. 필요에 따라 더 작은 값으로 구성할 수 있습니다. |
| Azure Service Bus 큐   | 표준 계층의 경우 메시지당 256, 프리미엄 계층의 경우 1MB입니다.<br /> [Service Bus 제한](../service-bus-messaging/service-bus-quotas.md)을 참조 하세요. | 메시지당 단일 이벤트를 사용 합니다. |
| Azure Service Bus 항목 | 표준 계층의 경우 메시지당 256, 프리미엄 계층의 경우 1MB입니다.<br /> [Service Bus 제한](../service-bus-messaging/service-bus-quotas.md)을 참조 하세요. | 메시지당 단일 이벤트를 사용 합니다. |
| Azure Cosmos DB   | [Azure Cosmos DB 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)을 참조 하세요. | 일괄 처리 크기와 쓰기 빈도는 Azure Cosmos DB 응답에 따라 동적으로 조정 됩니다. <br /> Stream Analytics에서 결정 한 제한 사항은 없습니다. |
| Azure 기능   | | 기본 일괄 처리 크기는 262144 바이트 (256 KB)입니다. <br /> 일괄 처리당 기본 이벤트 수는 100입니다. <br /> 일괄 처리 크기는 구성 가능하며 Stream Analytics [출력 옵션](#azure-functions)에서 증가 또는 감소될 수 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> 
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
