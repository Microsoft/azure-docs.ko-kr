---
title: Azure Stream Analytics의 출력 이해
description: 이 문서에서는 분석 결과에 대한 Power BI를 포함하여 Azure Stream Analytics에서 사용할 수 있는 데이터 출력 옵션을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 4e62ae47de95f95600faa3dc27f6867b065e117b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329972"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Azure Stream Analytics의 출력 이해

이 문서에서는 Azure Stream Analytics 작업에 사용할 수 있는 출력 유형을 설명 합니다. 출력을 사용하여 Stream Analytics 작업의 결과를 저장할 수 있습니다. 출력 데이터를 사용 하 여 수행할 수 있습니다 비즈니스 분석 및 데이터 웨어하우징 데이터를 추가 합니다.

Stream Analytics 쿼리를 디자인할 때 출력의 이름을 사용 하 여 참조를 [INTO 절](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics)합니다. 작업당 단일 출력 또는 쿼리에서 여러 INTO 절을 제공 하 여 작업 (필요) 하는 경우 스트리밍 작업당 여러 출력을 사용할 수 있습니다.

이전에 출력 하 만들기, 편집 및 Stream Analytics 작업을 테스트 하려면, 사용할 수는 [Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)를 [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)를 [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), 및 [Visual Studio](stream-analytics-quick-create-vs.md)합니다.

몇 가지 출력 형식 지원이 [분할](#partitioning)합니다. [출력 일괄 처리 크기](#output-batch-size) 처리량을 최적화 하는 달라 집니다.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics에서 지 원하는 [Azure Data Lake 저장소 Gen 1](../data-lake-store/data-lake-store-overview.md)합니다. Azure Data Lake Storage는 빅 데이터 분석 워크 로드에는 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. 모든 크기, 유형 및 운영 및 예비 분석에 대해 수집 속도의 데이터를 저장 하려면 Data Lake Storage를 사용할 수 있습니다. Stream Analytics Data Lake Storage에 액세스할 수 있는 권한이 필요 합니다.

Stream Analytics에서 azure Data Lake 저장소 출력은 현재 Azure 중국 21Vianet 및 Azure Germany (T-systems International) 지역에서 지원 되지.

다음 표에서 속성 이름 및 Data Lake 저장소 Gen 1 출력을 구성 하려면 해당 설명을 나열 합니다.   

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 | Data Lake Store에 쿼리 출력을 보내기 위해 쿼리에서 사용 되는 이름입니다. |
| 구독 | Azure Data Lake Storage 계정이 포함 된 구독입니다. |
| 계정 이름 | 출력을 전송 하는 위치는 Data Lake Store 계정의 이름입니다. 구독에서 사용할 수 있는 Data Lake Store 계정 드롭다운 목록이 제공 됩니다. |
| 경로 접두사 패턴 | 지정한 Data Lake Store 계정 내에서 파일을 작성 하는 데 사용 되는 파일 경로입니다. {Time} 변수 및 {date}의 하나 이상의 인스턴스를 지정할 수 있습니다.<br /><ul><li>예제 1: folder1/logs/{date}/{time}</li><li>예제 2: folder1/logs/{date}</li></ul><br />만든된 폴더 구조의 타임 스탬프는 UTC와 현지 시간이 아닌 따릅니다.<br /><br />파일 경로 패턴에 후행 슬래시 (/) 없으면 파일 경로의 마지막 패턴이 파일 이름 접두사로 처리 됩니다. <br /><br />이 경우 새 파일이 만들어집니다.<ul><li>출력 스키마의 변경</li><li>외부 또는 내부 작업 다시 시작</li></ul> |
| 날짜 형식 | 선택 사항입니다. 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예제: YYYY/MM/DD |
|시간 형식 | 선택 사항입니다. 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다.|
| Encoding | CSV 또는 JSON 형식을 사용 하는 경우 인코딩을 지정 해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.|
| 구분 기호 | CSV 직렬화에만 적용할 수 있습니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.|
| 형식 | JSON 직렬화에만 적용할 수 있습니다. **줄 바꿈을** 는 출력의 각 JSON 개체를 새 줄으로 구분 된 형식을 지정 합니다. **배열** JSON 개체 배열로 출력 형식이 있는지를 지정 합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 것이 좋습니다 줄으로 구분 된 JSON을 사용 하려면 출력 파일에 여전히 기록 되는 동안 특수 한 처리 필요가 없기 때문에.|
| 인증 모드 | 사용 하 여 Data Lake Storage 계정에 대 한 액세스 권한을 부여할 수 있습니다 [관리 Id](stream-analytics-managed-identities-adls.md) 또는 사용자 토큰입니다. 액세스를 부여 하면 사용자 계정 암호 변경,이 작업에 대 한 Data Lake 저장소 출력을 삭제 또는 Stream Analytics 작업을 삭제 하 여 액세스를 해지할 수 있습니다. |

## <a name="sql-database"></a>SQL Database

사용할 수 있습니다 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 사실상에서 관계형 인 데이터 또는 관계형 데이터베이스에서 호스트 되는 콘텐츠에 종속 되는 응용 프로그램에 대 한 출력으로 합니다. SQL Database의 기존 테이블에 Stream Analytics 작업 작성합니다. 테이블 스키마는 작업의 출력에 해당 형식과 필드에 정확히 일치 해야 합니다. 지정할 수 있습니다 [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) SQL Database를 통해 출력으로 옵션을 출력 합니다. 쓰기 처리량을 개선 하는 방법에 대 한 자세한 내용은 참조는 [Stream Analytics 출력으로 Azure SQL Database를 사용 하 여](stream-analytics-sql-output-perf.md) 문서.

다음 표에서 속성 이름 및 SQL Database 출력을 만들기 위한 해당 설명을 나열 합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 데이터베이스 | 출력 하는 보내는 데이터베이스의 이름입니다. |
| 서버 이름 | SQL Database 서버 이름입니다. |
| 사용자 이름 | 데이터베이스에 대 한 쓰기 권한이 있는 사용자 이름입니다. Stream Analytics는 SQL 인증만을 지원합니다. |
| 암호 | 데이터베이스에 연결하는 암호입니다. |
| 테이블 | 출력을 기록되는 테이블 이름입니다. 테이블 이름은 대/소문자 구분입니다. 이 테이블의 스키마 필드 및 작업 출력을 생성 하는 해당 형식 수가 정확히 일치 해야 합니다. |
|파티션 구성표 상속| 테이블에 여러 기록기를 사용 하 여 완전 한 병렬 토폴로지를 사용 하도록 설정 하 여 이전 쿼리 단계의 파티션 구성표를 상속 하는 옵션입니다. 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)을 참조하세요.|
|최대 일괄 처리 수| 권장 되는 모든 bulk와 함께 전달 된 레코드의 수에 상한값 트랜잭션을 삽입 합니다.|

> [!NOTE]
> Azure SQL Database 제공은 Azure 가상 머신에서 실행 중인 SQL Server 또는 SQL Azure 관리 되는 인스턴스에 연결 된 데이터베이스를 사용 하 여 아직 지원 되지 않습니다 하지만 Stream Analytics 작업 출력을 지원 합니다. 후속 릴리스에서는 변경될 수 있습니다.

## <a name="blob-storage"></a>Blob 저장소

Azure Blob storage는 클라우드에서 대량의 구조화 되지 않은 데이터를 저장 하기 위한 비용 효율적이 고 확장 가능한 솔루션을 제공 합니다. Blob 저장소 및 사용법에 대 한 소개를 참조 하세요 [업로드, 다운로드 및 Azure portal 사용 하 여 blob을 나열](../storage/blobs/storage-quickstart-blobs-portal.md)합니다.

다음 표에서 속성 이름 및 blob 출력을 만들기 위한 해당 설명을 나열 합니다.

| 속성 이름       | 설명                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 출력 별칭        | 쿼리 출력을 이 Blob Storage로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| Storage 계정     | 출력 하는 보내는 저장소 계정의 이름입니다.               |
| Storage 계정 키 | 저장소 계정과 연결된 비밀 키입니다.                              |
| 저장소 컨테이너   | Azure Blob service에 저장 된 blob에 대 한 논리적 그룹화입니다. Blob service에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다. |
| 경로 패턴 | 선택 사항입니다. 지정된 된 컨테이너 내에서 blob를 작성 하는 데 사용 되는 파일 경로 패턴입니다. <br /><br /> 경로 패턴에서 날짜 및 시간 변수의 인스턴스를 하나 이상의 blob에 기록 되는 빈도 지정 하는 데 선택할 수 있습니다. <br /> {date}, {time} <br /><br />사용자 지정 Blob 분할을 사용하여 이벤트 데이터에서 파티션 Blob까지 하나의 사용자 지정 {field} 이름을 지정할 수 있습니다. 필드 이름은 영숫자이며 공백, 하이픈 및 밑줄을 포함할 수 있습니다. 사용자 지정 필드에 대한 제한은 다음을 포함합니다. <ul><li>필드 이름은 대/소문자 구분 하지 않습니다. 예를 들어, 서비스와 구별할 수 없으므로 열 "ID" 열 "id입니다."</li><li>중첩 된 필드는 허용 되지 않습니다. 대신 "평면화" 필드에 작업 쿼리에서 별칭을 사용 합니다.</li><li>식은 필드 이름으로 사용할 수 없습니다.</li></ul> <br />이 기능을 사용하면 경로에 사용자 지정 날짜/시간 형식 지정자 구성을 사용할 수 있습니다. 사용자 지정 날짜 및 시간 형식은 {datetime:\<specifier>} 키워드로 묶어 한 번에 하나씩만 지정해야 합니다. 허용 되는 입력 \<지정자 > yyyy, MM, M, dd, d, HH, 시간, mm, m, ss, 또는 s 됩니다. {0} datetime:\<지정자 >} 키워드 수 여러 번 사용할 경로 사용자 지정 날짜/시간 구성. <br /><br />예제: <ul><li>예제 1: cluster1/logs/{date}/{time}</li><li>예제 2: cluster1/logs/{date}</li><li>예제 3: cluster1/{client_id}/{date}/{time}</li><li>예제 4: cluster1/{datetime:ss}/{myField} where the query is: SELECT data.myField AS myField FROM 입력</li><li>예제 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />만든된 폴더 구조의 타임 스탬프는 UTC와 현지 시간이 아닌 따릅니다.<br /><br />파일 명명은 다음 규칙을 사용합니다. <br /><br />{경로 접두사 패턴}/schemaHashcode_Guid_Number.extension<br /><br />예제 출력 파일:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />이 기능에 대 한 자세한 내용은 참조 하세요. [Azure Stream Analytics가 사용자 지정 blob 출력 분할](stream-analytics-custom-path-patterns-blob-storage-output.md)합니다. |
| 날짜 형식 | 선택 사항입니다. 접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예제: YYYY/MM/DD |
| 시간 형식 | 선택 사항입니다. 접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding    | CSV 또는 JSON 형식을 사용 하는 경우 인코딩을 지정 해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호   | CSV 직렬화에만 적용할 수 있습니다. Stream Analytics는 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식      | JSON 직렬화에만 적용할 수 있습니다. **줄 바꿈을** 는 출력의 각 JSON 개체를 새 줄으로 구분 된 형식을 지정 합니다. **배열** JSON 개체 배열로 출력 형식이 있는지를 지정 합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 것이 좋습니다 줄으로 구분 된 JSON을 사용 하려면 출력 파일에 여전히 기록 되는 동안 특수 한 처리 필요가 없기 때문에. |

Blob 저장소를 출력으로 사용 하는 경우 다음과 같은 경우 blob의 새 파일이 만들어집니다.

* 파일이 허용되는 최대 블록 수(현재 50,000)를 초과합니다. 허용 된 최대 blob 크기에 도달 하지 않고 허용 된 최대 블록 수에 도달할 수 있습니다. 예를 들어 출력 속도가 높으면 블록당 더 많은 바이트를 볼 수 있으며 파일 크기는 더 커집니다. 출력 속도가 낮으면 각 블록의 데이터가 줄어들고 파일 크기도 작아집니다.
* 출력 및 출력 형식에는 스키마 변경 사항이 있는 경우에 고정된 스키마 (CSV 및 Avro) 필요 합니다.
* 작업이 다시 시작되는 경우 사용자가 외부적으로 또는 시스템 유지 관리 또는 오류 복구의 경우 내부적으로 중지하고 시작합니다.
* 쿼리가 완전히 분할 되 고 하는 경우 각 출력 파티션에 대해 새 파일이 생성 됩니다.
* 파일 또는 저장소 계정의 컨테이너를 삭제 합니다.
* 출력 시간이 경로 접두사 패턴을 사용 하 여 분할 하 고 쿼리가 다음 시간으로 이동 하는 경우 새 blob에 사용 됩니다.
* 사용자 지정 필드에 따라 출력이 분할 되는 경우 파티션 키 당 새 blob을 만들 경우 존재 하지 않습니다.
* 출력에서 분할 된 경우 8000을 초과 하는 파티션 키 카디널리티가 사용자 지정 필드 및 파티션 키 당 새 blob이 생성 됩니다.

## <a name="event-hubs"></a>Event Hubs

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) 서비스는 확장성이 뛰어난 게시-구독 이벤트 수집기입니다. 초당 수 백만의 이벤트를 수집할 수 있습니다. 출력으로 이벤트 허브의 한 가지 용도 Stream Analytics 작업의 출력이 다른 스트리밍 작업의 입력 되 면 됩니다.

출력으로 event hubs에서 데이터 스트림을 구성 하는 몇 가지 매개 변수가 필요 합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 | 쿼리 출력을이 이벤트 허브로 보내기 위해 쿼리에서 사용 되는 이름입니다. |
| 이벤트 허브 네임스페이스 | 메시징 엔터티 집합에 대 한 컨테이너입니다. 새 이벤트 허브를 만들 때에 이벤트 허브 네임 스페이스를 만들었습니다. |
| 이벤트 허브 이름 | 이벤트 허브 출력의 이름입니다. |
| 이벤트 허브 정책 이름 | 이벤트 허브에서 만들 수 있는 공유 액세스 정책 **구성** 탭 합니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 이벤트 허브 정책 키 | 이벤트 허브 네임 스페이스에 대 한 액세스를 인증 하는 데 사용 되는 공유 액세스 키입니다. |
| 파티션 키 열 | 선택 사항입니다. 이벤트 허브 출력에 대 한 파티션 키가 포함 된 열입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding | CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 | CSV 직렬화에만 적용할 수 있습니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 | JSON 직렬화에만 적용할 수 있습니다. **줄 바꿈을** 는 출력의 각 JSON 개체를 새 줄으로 구분 된 형식을 지정 합니다. **배열** JSON 개체 배열로 출력 형식이 있는지를 지정 합니다. 이 배열은 작업이 중지되거나 Stream Analytics가 다음 시간 범위로 이동되었을 때만 닫힙니다. 일반적으로 것이 좋습니다 줄으로 구분 된 JSON을 사용 하려면 출력 파일에 여전히 기록 되는 동안 특수 한 처리 필요가 없기 때문에. |
| 속성 열 | 선택 사항입니다. 페이로드는 대신 보내는 메시지의 사용자 속성으로 연결 해야 하는 쉼표로 구분 된 열입니다. 섹션에는이 기능에 대 한 자세한 내용은 [출력에 대 한 사용자 지정 메타 데이터 속성](#custom-metadata-properties-for-output)합니다. |

## <a name="power-bi"></a>Power BI

사용할 수 있습니다 [Power BI](https://powerbi.microsoft.com/) 분석 결과 풍부한 시각화 환경을 제공 하는 Stream Analytics 작업에 대 한 출력으로 합니다. 운영 대시보드, 보고서 생성 및 메트릭 제어 보고에 대 한이 기능을 사용할 수 있습니다.

Stream Analytics에서 power BI 출력은 현재 Azure 중국 21Vianet 및 Azure Germany (T-systems International) 지역에서 지원 되지.

다음 표에서 속성 이름 및 Power BI 출력을 구성 하려면 해당 설명을 나열 합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 |쿼리 출력을이 Powerbi 출력으로 보내기 위해 쿼리에서 사용 되는 친숙 한 이름을 제공 합니다. |
| 그룹 작업 영역 |다른 Power BI 사용자와 공유 데이터를 사용 하려면 Power BI 계정 내에서 그룹을 선택 하거나 선택할 수 있습니다 **내 작업 영역** 그룹에 작성 하지 않으려는 경우. 기존 그룹을 업데이트하려면 Power BI 인증을 갱신해야 합니다. |
| 데이터 세트 이름 |사용 하 여 Power BI 출력 데이터 집합 이름을 제공 합니다. |
| 테이블 이름 |Power BI 출력의 데이터 세트 아래에 테이블 이름을 제공합니다. 현재, Stream Analytics 작업의 Power BI 출력에는 하나의 데이터 세트에 하나의 테이블만 있을 수 있습니다. |
| 연결 권한 부여 | 출력 설정을 구성 하려면 Power BI를 사용 하 여 권한을 부여 해야 합니다. Power BI 대시보드에이 출력 액세스를 부여 하면 사용자 계정 암호 변경, 삭제 작업 출력을 Stream Analytics 작업을 삭제 하 여 액세스를 해지할 수 있습니다. | 

Power BI 출력 및 대시보드를 구성 하는 연습을 참조 하세요. 합니다 [Azure Stream Analytics 및 Power BI](stream-analytics-power-bi-dashboard.md) 자습서입니다.

> [!NOTE]
> Power BI 대시보드에서 데이터 집합 및 테이블을 만드는 명시적으로 하지 마십시오. 데이터 집합 및 테이블을 자동으로 채워집니다 작업이 시작 되 고 작업이 Power BI로 출력을 시작 하는 경우. 작업 쿼리에서 결과 생성 하지 않습니다, 경우에 데이터 집합 및 테이블 생성 되지 않습니다. Power BI에는 이미이 Stream Analytics 작업에서 제공한 것과 동일한 이름 가진 테이블과 데이터 집합, 기존 데이터를 덮어씁니다.
>

### <a name="create-a-schema"></a>스키마 만들기
Azure Stream Analytics는 아직 존재 하지 않는 경우 사용자에 대 한 Power BI 데이터 집합 및 테이블 스키마를 만듭니다. 다른 모든 경우에는 테이블이 새 값으로 업데이트됩니다. 현재 데이터 집합 내에서 하나의 테이블만 있을 수 있습니다. 

Power BI는 선입 선출 (FIFO) 보존 정책을 사용 합니다. 200,000 개 행에 도달할 때까지 테이블의 데이터를 수집 합니다.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Stream Analytics에서 Power BI는 데이터 형식 변환
Azure Stream Analytics는 출력 스키마가 변경되면 런타임 시 동적으로 데이터 모델을 업데이트합니다. 열 이름 변경, 열 형식 변경 및 열 추가 또는 제거 작업이 모두 추적됩니다.

데이터 형식 변환 표 [Stream Analytics 데이터 형식](https://msdn.microsoft.com/library/azure/dn835065.aspx) Power bi [엔터티 데이터 모델 (EDM) 형식을](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)이면 Power BI 데이터 집합 및 테이블을 존재 하지 않습니다.

Stream Analytics에서 | Power BI로
-----|-----
bigint | Int64
nvarchar(max) | 문자열
Datetime | DateTime
float | Double
레코드 배열 | 문자열 형식, 상수 값 "IRecord" 또는 "IArray"

### <a name="update-the-schema"></a>스키마 업데이트
Stream Analytics는 출력의 첫 번째 이벤트 집합을 기반으로 데이터 모델 스키마를 유추합니다. 나중에 필요한 경우 데이터 모델 스키마는 원래 스키마에 맞지 않는 들어오는 이벤트에 맞게 업데이트 됩니다.

방지는 `SELECT *` 행에서 동적 스키마 업데이트를 방지 하기 위해 쿼리 합니다. 잠재적인 성능에 미치는 영향 외에도 발생할 수 있습니다 결과 대 한 시간이 불확실 합니다. Power BI 대시보드에서 표시 해야 하는 정확한 필드를 선택 합니다. 또한 데이터 값은 선택한 데이터 형식을 준수해야 합니다.


이전/현재 | Int64 | 문자열 | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | 문자열 | 문자열 | Double
Double | Double | 문자열 | 문자열 | Double
문자열 | 문자열 | 문자열 | 문자열 | 문자열 
DateTime | 문자열 | 문자열 |  DateTime | String

## <a name="table-storage"></a>테이블 저장소

[Azure Table Storage](../storage/common/storage-introduction.md)는 가용성이 높고 확장성이 큰 스토리지를 제공하므로, 응용 프로그램이 사용자 요구에 맞게 자동으로 확장할 수 있습니다. Table storage는 스키마에 대 한 제약 조건이 적은 구조화 된 데이터에 대해 사용할 수 있는 Microsoft의 NoSQL 키/특성 저장소입니다. Azure Table Storage는 지속적이고 효율적인 검색을 위해 데이터를 저장하는 데 사용할 수 있습니다.

다음 표에서 속성 이름 및 테이블 출력 만들기에 대 한 해당 설명을 나열 합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 Table Storage로 보내기 위해 쿼리에서 사용되는 이름입니다. |
| Storage 계정 |출력 하는 보내는 저장소 계정의 이름입니다. |
| Storage 계정 키 |저장소 계정과 연결된 선택키입니다. |
| 테이블 이름 |테이블의 이름입니다. 테이블에 존재 하지 않는 경우 만들어집니다. |
| 파티션 키 |파티션 키가 포함 된 출력 열의 이름입니다. 파티션 키는 엔터티의 기본 키의 첫 번째 부분을 구성 하는 테이블 내 파티션에 대 한 고유 식별자입니다. 크기가 최대 1KB 일 수 있는 문자열 값입니다. |
| 행 키. |행 키가 포함 된 출력 열의 이름입니다. 행 키는 파티션 내 엔터티의 고유 식별자입니다. 엔터티의 기본 키에서 두 번째 부분을 형성합니다. 행 키는 크기가 최대 1KB 일 수 있습니다 하는 문자열 값입니다. |
| Batch 크기 |배치 작업에 대한 레코드 수입니다. 기본값(100)은 대부분의 작업에 충분합니다. 참조 된 [테이블 Batch 작업 사양](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) 이 설정을 수정 하는 대 한 자세한 내용은 합니다. |

## <a name="service-bus-queues"></a>Service Bus 큐

[Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) 하나 이상의 경쟁 소비자에 게 FIFO 메시지 배달을 제공 합니다. 일반적으로 메시지를 받아서 큐에 추가 된 임시 순서로 수신자에에서 의해 처리 됩니다. 각 메시지를 받고 하나의 메시지 소비자에 의해 처리 됩니다.

다음 표에서 속성 이름 및은 큐 출력을 만들기에 대 한 해당 설명을 나열 합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 |쿼리 출력을이 Service Bus 큐로 보내기 위해 쿼리에서 사용 되는 이름입니다. |
| Service Bus 네임스페이스 |메시징 엔터티 집합에 대 한 컨테이너입니다. |
| 큐 이름 |Service Bus 큐의 이름입니다. |
| 큐 정책 이름 |큐의 공유 액세스 정책을 만들 수도 있습니다 큐를 만들 때 **구성** 탭 합니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 큐 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 이벤트 직렬화 형식 |출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 |CSV 직렬화에만 적용할 수 있습니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 |JSON 형식에 대해서만 적용할 수 있습니다. **줄 바꿈을** 는 출력의 각 JSON 개체를 새 줄으로 구분 된 형식을 지정 합니다. **배열** JSON 개체 배열로 출력 형식이 있는지를 지정 합니다. |
| 속성 열 | 선택 사항입니다. 페이로드는 대신 보내는 메시지의 사용자 속성으로 연결 해야 하는 쉼표로 구분 된 열입니다. 섹션에는이 기능에 대 한 자세한 내용은 [출력에 대 한 사용자 지정 메타 데이터 속성](#custom-metadata-properties-for-output)합니다. |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.

## <a name="service-bus-topics"></a>Service Bus 토픽
Service Bus 큐는 수신자에 게 보낸에서 일대일 통신 방법을 제공합니다. [Service Bus 토픽](https://msdn.microsoft.com/library/azure/hh367516.aspx) 에 일 대 다 형태의 통신을 제공 합니다.

다음 표에서 속성 이름 및 Service Bus 토픽 출력을 만들기 위한 해당 설명을 나열 합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 |쿼리 출력을이 Service Bus 토픽을 보내기 위해 쿼리에서 사용 되는 이름입니다. |
| Service Bus 네임스페이스 |메시징 엔터티 집합에 대 한 컨테이너입니다. 새 이벤트 허브를 만들 때 Service Bus 네임스페이스도 만들었습니다. |
| 항목 이름 |토픽은 이벤트 허브 및 큐와 유사한 메시징 엔터티입니다. 장치 및 서비스에서 이벤트 스트림을 수집 하도록 설계 되었습니다. 토픽을 만들 때 특정 이름도 지정에 해당 합니다. 구독을 만들지 않으면 토픽에 전송 된 메시지를 사용할 수 없습니다, 있도록 토픽 아래에 있는 하나 이상의 구독이 있습니다. |
| 토픽 정책 이름 |공유 액세스 정책 항목의에서 만들 수 Service Bus 토픽을 만들면 **구성** 탭 합니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 토픽 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 이벤트 직렬화 형식 |출력 데이터에 대 한 serialization 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 또는 JSON 형식을 사용 하는 경우 인코딩을 지정 해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호 |CSV 직렬화에만 적용할 수 있습니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 속성 열 | 선택 사항입니다. 페이로드는 대신 보내는 메시지의 사용자 속성으로 연결 해야 하는 쉼표로 구분 된 열입니다. 섹션에는이 기능에 대 한 자세한 내용은 [출력에 대 한 사용자 지정 메타 데이터 속성](#custom-metadata-properties-for-output)합니다. |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대 한 고유 정수 값입니다.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 는 전 세계, 풍부한 쿼리 및 스키마 제약 없는 데이터 모델을 통해 자동 인덱싱 제한 없는 탄력적인 확장을 제공 하는 전역으로 분산된 데이터베이스 서비스입니다. Stream Analytics에 대 한 Azure Cosmos DB 컬렉션 옵션에 대 한 자세한 내용은 참조는 [Stream Analytics 출력으로 Azure Cosmos DB를 사용 하 여](stream-analytics-documentdb-output.md) 문서.

Stream Analytics에서 azure Cosmos DB 출력은 현재 Azure 중국 21Vianet 및 Azure Germany (T-systems International) 지역에서 지원 되지.

> [!Note]
> 이때 Azure Stream Analytics SQL API를 사용 하 여 Azure Cosmos DB에 연결만을 지원 합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. Azure Stream Analytics를 다른 API로 만든 Azure Cosmos DB 계정에 지정한 경우 데이터는 올바르게 저장되지 않을 수도 있습니다.

다음 표에서는 Azure Cosmos DB 출력을 만드는 속성에 대해 설명합니다.

| 속성 이름 | 설명 |
| --- | --- |
| 출력 별칭 | Stream Analytics 쿼리에서 이 출력을 참조할 별칭입니다. |
| 싱크 | Azure Cosmos DB. |
| 가져오기 옵션 | 선택할 **구독에서 Cosmos DB 선택** 또는 **Cosmos DB 제공 설정을 수동으로**합니다.
| 계정 ID | Azure Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다. |
| 계정 키 | Azure Cosmos DB 계정에 대한 공유 액세스 키입니다. |
| 데이터베이스 | Azure Cosmos DB 데이터베이스 이름입니다. |
| 컬렉션 이름 | Azure Cosmos DB 컬렉션의 이름입니다. Azure Cosmos DB 워크 로드에 따라 파티션 확장 자동으로 Azure Cosmos DB로 데이터를 분할에 대 한 무제한 컨테이너는 권장 되는 접근 방법입니다. |
| 문서 ID |선택 사항입니다. 작업 하는 삽입 또는 업데이트에 기반한 기본 키를 지정 하는 데 사용 되는 출력 이벤트의 필드의 이름입니다.

## <a name="azure-functions"></a>Azure 기능
Azure Functions는 인프라를 명시적으로 프로 비전 또는 관리 하지 않고도 요청 시 코드를 실행 하는 데 사용할 수 있는 서버 리스 계산 서비스입니다. Azure 또는 파트너 서비스에서 발생 하는 이벤트로 트리거되는 코드를 구현할 수 있습니다. 트리거에 응답 하는 Azure Functions의이 기능 수 Azure Stream Analytics에 대 한 출력이 자연스럽 게 있습니다. 이 출력 어댑터를 사용 하면 Azure Functions를 Stream Analytics를 연결 하 고 다양 한 이벤트에 대 한 응답으로 스크립트 또는 코드 조각을 실행할 수 있습니다.

Stream Analytics에서 azure Functions 출력은 현재 Azure 중국 21Vianet 및 Azure Germany (T-systems International) 지역에서 지원 되지.

Azure Stream Analytics는 HTTP 트리거를 통해 Azure Functions를 호출합니다. Azure Functions 출력 어댑터는 다음 구성 가능한 속성으로 제공 됩니다.

| 속성 이름 | 설명 |
| --- | --- |
| 함수 앱 |Azure Functions 앱의 이름입니다. |
| 함수 |Azure Functions 앱의 함수 이름입니다. |
| 키 |다른 구독에서 Azure 함수를 사용 하려는 경우 함수에 액세스 하는 키를 제공 하 여 이렇게 수 있습니다. |
| 최대 일괄 처리 크기 |속성을 사용 하면 Azure 함수에 전송 되는 각 출력 일괄 처리에 대 한 최대 크기를 설정 합니다. 입력 단위는 바이트입니다. 기본적으로이 값에는 262,144 바이트 (256 KB)입니다. |
| 최대 일괄 처리 수  |속성 수 있는 Azure Functions로 전송 되는 각 일괄 처리에서 이벤트의 최대 수를 지정 합니다. 기본값은 100입니다. |

Azure Stream Analytics는 413 ("http 요청 엔터티 너무 큼")를 수신 하는 경우 Azure 함수에서 예외를 Azure Functions로 전송 하는 일괄 처리의 크기를 줄입니다. Azure 함수 코드에서 이 예외를 사용하여 Azure Stream Analytics가 너무 큰 일괄 처리를 전송하지 않도록 합니다. 또한 함수에 사용 되는 최대 일괄 처리 수 및 크기 값은 Stream Analytics 포털에 입력 된 값과 일치 해야 합니다.

상황에서는 또한 기간에 이벤트가 있는 출력이 생성 되지 않습니다. 결과적으로 **computeResult** 함수가 호출 되지 않습니다. 이 동작은 기본 제공 기간 이동 집계 함수와 일치합니다.

## <a name="custom-metadata-properties-for-output"></a>출력에 대 한 사용자 지정 메타 데이터 속성 

보내는 메시지에 사용자 속성으로 쿼리 열을 연결할 수 있습니다. 이러한 열 페이로드에 이동 하지 않습니다. 속성은 출력 메시지에는 사전 형식의에 나타납니다. *키* 은 열 이름 및 *값* 속성 사전에 있는 열 값입니다. 레코드 및 배열을 제외한 모든 Stream Analytics 데이터 형식이 지원 됩니다.  

지원 되는 출력: 
* Service Bus 큐 
* Service Bus 토픽 
* 이벤트 허브 

다음 예제에서 두 필드를 추가 했습니다 `DeviceId` 고 `DeviceStatus` 메타 데이터입니다. 
* 쿼리: `select *, DeviceId, DeviceStatus from iotHubInput`
* 출력 구성: `DeviceId,DeviceStatus`

![속성 열](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

다음 스크린샷은 출력 메시지 속성을 통해 EventHub에서 검사할 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer)합니다.

![사용자 지정 이벤트 속성](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>분할

다음 표에서는 각 출력 유형에 대한 출력 기록기 수 및 파티션 지원이 요약되어 있습니다.

| 출력 형식 | 분할 지원 | 파티션 키  | 출력 기록기 수 |
| --- | --- | --- | --- |
| Azure Data Lake Store | 예 | 경로 접두사 패턴에 {time} 토큰을 사용 하 여 {date} 날짜 형식은 YYYY/MM/DD, DD/MM/YYYY 또는 MM-DD-YYYY 등을 선택 합니다. HH는 시간 형식에 사용 됩니다. | [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure SQL Database | 예, 사용 하도록 설정 해야 합니다. | PARTITION BY 절을 쿼리의 기반으로 합니다. | 상속 분할 옵션을 설정 하는 경우에 대 한 입력 분할을 따릅니다 [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)합니다. 달성 하는 방법에 대 한 자세한 내용은 더 쓰기 처리량 성능을 Azure SQL Database로 데이터를 로드 하는 경우에 대해 알아보려면 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)합니다. |
| Linux에서 File Storage 사용에 대한 자세한 내용은 Linux에서 Azure 파일 스토리지 사용 방법을 참조하세요. | 예 | 경로 패턴에서 이벤트 필드에서 {time} 토큰을 사용 하 여 {date} 날짜 형식은 YYYY/MM/DD, DD/MM/YYYY 또는 MM-DD-YYYY 등을 선택 합니다. HH는 시간 형식에 사용 됩니다. 단일 사용자 지정 이벤트 특성 {fieldname} 또는 {datetime:\<specifier>}로 Blob 출력을 분할할 수 있습니다. | [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure Event Hubs | 예 | 예 | 파티션 맞춤에 따라 달라집니다.<br /> 이벤트 허브 출력에 대 한 파티션 키가 업스트림 (이전의) 쿼리 단계로 동일 하 게 맞춰질, 작성자 수 때 이벤트 허브 출력에서 파티션 수와 동일 합니다. 각 작성기를 사용 합니다 [EventHubSender 클래스](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) 특정 파티션에 이벤트를 보내도록 합니다. <br /> 이벤트 허브 출력에 대 한 파티션 키가 업스트림 (이전의) 쿼리 단계로 사용 하 여 정렬 되지 않은 경우 작성기의 수는는 이전 단계의 파티션 수와 동일 합니다. 각 작성기를 사용 하는 [SendBatchAsync 클래스](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) 에 **EventHubClient** 모든 출력 파티션에 이벤트를 보내도록 합니다. |
| Power BI | 아닙니다. | 없음 | 사용할 수 없습니다. |
| Azure Table Storage | 예 | 모든 출력 열입니다.  | [완전히 병렬 처리된 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure Service Bus 항목 | 예 | 자동으로 선택됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대 한 고유 정수 값입니다.| 출력 항목의 파티션 수와 동일합니다.  |
| Azure Service Bus 큐 | 예 | 자동으로 선택됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대 한 고유 정수 값입니다.| 출력 큐의 파티션 수와 동일합니다. |
| Azure Cosmos DB | 예 | 컬렉션 이름 패턴에서 {partition} 토큰을 사용 합니다. {Partition} 값은 쿼리의 PARTITION BY 절을 기반으로 합니다. | [완전히 병렬 처리된 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. |
| Azure 기능 | 아닙니다. | 없음 | 사용할 수 없습니다. |

출력 어댑터가 분할되지 않은 경우 한 입력 파티션에 데이터가 없으면 지연 도착 시간까지 지연될 수 있습니다. 이러한 경우 출력은 파이프라인에서 병목 현상이 발생할 수 있는 단일 작성기에 병합 됩니다. 지연 도착 정책에 대 한 자세한 내용은 참조 하세요 [Azure Stream Analytics 이벤트 순서 고려 사항](stream-analytics-out-of-order-and-late-events.md)합니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기
Azure Stream Analytics는 이벤트를 처리 하 고 출력을 쓰는 변수 크기 일괄 처리를 사용 합니다. 일반적으로 Stream Analytics 엔진은 한 번에 하나의 메시지를 기록 하지 않습니다 하 고 효율성을 위해 일괄 처리를 사용 합니다. 들어오고 나가는 이벤트 비율이 높은 경우 Stream Analytics는 더 큰 일괄 처리를 사용 합니다. 송신 속도가 낮은 경우 대기 시간을 낮게 유지하기 위해 더 작은 일괄 처리를 사용합니다.

다음 표에서 출력 일괄 처리에 대 한 고려 사항 중 일부를 설명합니다.

| 출력 형식 | 최대 메시지 크기 | 일괄 처리 크기 최적화 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 참조 [Data Lake Storage 제한](../azure-subscription-service-limits.md#data-lake-store-limits)합니다. | 쓰기 작업당 최대 4MB를 사용 합니다. |
| Azure SQL Database | 최대 일괄 처리 수를 사용 하 여 구성할 수 있습니다. 기본적으로 10,000 최대값과 100 행 그룹당 최대 행 단일 대량 삽입합니다.<br />참조 [Azure SQL 한도](../sql-database/sql-database-resource-limits.md)합니다. |  처음에 모든 일괄 처리는 대량 삽입 최대 일괄 처리 수입니다. 일괄 처리의 절반 (최소 일괄 처리 수) 될 때까지 SQL에서 재시도 가능한 오류를 기반으로 분할 됩니다. |
| Linux에서 File Storage 사용에 대한 자세한 내용은 Linux에서 Azure 파일 스토리지 사용 방법을 참조하세요. | 참조 [Azure Storage 용량 한도](../azure-subscription-service-limits.md#storage-limits)합니다. | 최대 blob 블록 크기는 4MB입니다.<br />최대 blob 블록 개수는 50,000입니다. |
| Azure Event Hubs  | 256KB 또는 메시지당 1MB입니다. <br />참조 [Event Hubs 제한](../event-hubs/event-hubs-quotas.md)합니다. |  각 이벤트에 개별적으로 압축 된 입/출력 분할 정렬 되지 경우 `EventData` 최대 메시지 크기의 일괄 처리에 전송 합니다. 이 경우에 발생 [사용자 지정 메타 데이터 속성](#custom-metadata-properties-for-output) 사용 됩니다. <br /><br />  여러 이벤트를 단일 압축 되는 입/출력 분할 aligned 인 경우 `EventData` 최대 메시지 크기, 최대 인스턴스 및 전송 합니다. |
| Power BI | 참조 [Power BI Rest API 제한](https://msdn.microsoft.com/library/dn950053.aspx)합니다. |
| Azure Table Storage | 참조 [Azure Storage 용량 한도](../azure-subscription-service-limits.md#storage-limits)합니다. | 기본값은 단일 트랜잭션당 100 개의 엔터티가 있습니다. 필요에 따라 작은 값으로 구성할 수 있습니다. |
| Azure Service Bus 큐   | 표준 계층에서 프리미엄 계층에 대 한 1MB 메시지당 256KB입니다.<br /> 참조 [Service Bus 한도](../service-bus-messaging/service-bus-quotas.md)합니다. | 메시지당 단일 이벤트를 사용 합니다. |
| Azure Service Bus 항목 | 표준 계층에서 프리미엄 계층에 대 한 1MB 메시지당 256KB입니다.<br /> 참조 [Service Bus 한도](../service-bus-messaging/service-bus-quotas.md)합니다. | 메시지당 단일 이벤트를 사용 합니다. |
| Azure Cosmos DB   | 참조 [Azure Cosmos DB 제한](../azure-subscription-service-limits.md#azure-cosmos-db-limits)합니다. | 일괄 처리 크기 및 쓰기 빈도 Azure Cosmos DB 응답에 따라 동적으로 조정 됩니다. <br /> Stream Analytics에서 미리 정해진 제한은 없습니다. |
| Azure 기능   | | 기본 배치 크기는 262,144 바이트 (256 KB)입니다. <br /> 일괄 처리당 기본 이벤트 수는 100입니다. <br /> 일괄 처리 크기는 구성 가능하며 Stream Analytics [출력 옵션](#azure-functions)에서 증가 또는 감소될 수 있습니다.

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
