---
title: Azure Stream Analytics에서 조회에 대한 참조 데이터 사용
description: 이 문서에서는 Azure Stream Analytics 작업의 쿼리 디자인에서 조회 또는 상관 관계 데이터에 대한 참조 데이터를 사용하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 4ddbec6b163a939c1663630e39e89140ac6f7efe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761491"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Stream Analytics에서 조회에 대한 참조 데이터 사용
참조 데이터(조회 테이블이라고도 함)는 정적이거나 느리게 변경되는 특성을 지닌 한정된 데이터 집합으로, 데이터 스트림을 조회하거나 상관 관계를 지정하는 데 사용됩니다. 예를 들어 IoT 시나리오에서는 센서에 대한 메타데이터를 참조 데이터에 저장하고(보통 변경하지 않음) 실시간 IoT 데이터 스트림과 조인할 수 있습니다. Azure Stream Analytics는 메모리에서 참조 데이터를 로드하여 대기 시간이 짧은 스트림 프로세스를 달성합니다. Azure Stream Analytics 작업에서 참조 데이터를 사용하려면 일반적으로 쿼리에서 [참조 데이터 조인](https://msdn.microsoft.com/library/azure/dn949258.aspx)을 사용합니다. 

Stream Analytics는 참조 데이터에 대한 스토리지 계층으로 Azure Blob 스토리지 및 Azure SQL Database를 지원합니다. 또한 참조 데이터를 Azure Data Factory에서 Blob Storage로 변환 및/또는 복사하여 [여러 클라우드 기반 및 온-프레미스 데이터 저장소](../data-factory/copy-activity-overview.md)를 사용할 수 있습니다.

## <a name="azure-blob-storage"></a>Linux에서 File Storage 사용에 대한 자세한 내용은 Linux에서 Azure 파일 스토리지 사용 방법을 참조하세요.

참조 데이터는 BLOB 이름에서 지정한 날짜/시간의 오름차순에 따라 BLOB의 시퀀스(입력 구성에서 정의)로 모델링됩니다. 시퀀스의 마지막 BLOB에서 지정한 것보다 **이후인** 날짜/시간을 사용하여 시퀀스의 마지막에 추가하는 것**만** 지원됩니다.

### <a name="configure-blob-reference-data"></a>Blob 참조 데이터 구성

참조 데이터를 구성하려면 먼저 **참조 데이터**형식의 입력을 만들어야 합니다. 다음 표에서 설명과 함께 참조 데이터 입력을 만드는 동안 제공해야 하는 각 속성을 설명합니다.

|**속성 이름**  |**설명**  |
|---------|---------|
|입력 별칭   | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다.   |
|Storage 계정   | Blob이 위치한 저장소 계정의 이름입니다. Stream Analytics 작업과 동일한 구독에 있으면 드롭다운에서 선택할 수 있습니다.   |
|Storage 계정 키   | 저장소 계정과 연결된 비밀 키입니다. 저장소 계정이 Stream Analytics 작업과 동일한 구독에 있으면 자동으로 채워집니다.   |
|저장소 컨테이너   | 컨테이너는 Microsoft Azure Blob service에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Blob service에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다.   |
|경로 패턴   | 지정된 컨테이너 내에서 Blob을 찾는 데 사용되는 경로입니다. 경로 내에서 다음 두 변수의 인스턴스 중 하나 이상을 지정하도록 선택할 수도 있습니다.<BR>{date}, {time}<BR>예 1: products/{date}/{time}/product-list.csv<BR>예 2: products/{date}/product-list.csv<BR>예 3: product-list.csv<BR><br> Blob이 지정된 경로에 없는 경우, Stream Analytics 작업은 Blob이 사용 가능해질 때까지 무기한 대기합니다.   |
|날짜 형식[선택 사항]   | 지정한 경로 패턴 내에서 {date}를 사용한 경우 Blob이 구성되는 날짜 형식을 지원되는 형식 드롭다운에서 선택할 수 있습니다.<BR>예제: YYYY/MM/DD, MM/DD/YYYY 등   |
|시간 형식[선택 사항]   | 지정한 경로 패턴 내에서 {time}을 사용한 경우 Blob이 구성되는 시간 형식을 지원되는 형식 드롭다운에서 선택할 수 있습니다.<BR>예제: HH, HH/mm 또는 HH-mm  |
|이벤트 직렬화 형식   | 쿼리가 예상대로 작동하는지 확인하려면 Stream Analytics은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식을 알고 있어야 합니다. 참조 데이터에 대해 지원되는 형식은 CSV 및 JSON입니다.  |
|Encoding   | 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.  |

### <a name="static-reference-data"></a>정적 참조 데이터

참조 데이터가 변경될 필요가 없으면 입력 구성에 정적 경로를 지정하여 정적 참조 데이터에 대한 지원을 사용하도록 설정할 수 있습니다. Azure Stream Analytics는 지정된 경로에서 Blob을 선택합니다. {date} 및 {time} 대체 토큰이 필요하지 않습니다. 참조 데이터는 Stream Analytics에서 변경할 수 없습니다. 따라서 정적 참조 데이터 Blob을 덮어쓰지 않는 것이 좋습니다.

### <a name="generate-reference-data-on-a-schedule"></a>일정에 따라 참조 데이터 생성

참조 데이터가 느리게 변경되는 데이터 집합인 경우 {date} 및 {time} 대체 토큰을 사용하여 입력 구성의 경로 패턴을 지정하여 참조 데이터 새로 고침 지원을 사용하도록 설정할 수 있습니다. Stream Analytics이 이 경로 패턴에 따라 업데이트된 참조 데이터 정의를 선택합니다. 예를 들어 날짜 형식 **“YYYY-MM-DD”** 및 시간 형식 **“HH-mm”** 의 `sample/{date}/{time}/products.csv` 패턴은 UTC 표준 시간대 2015년 4월 16일 오후 5시 30분에 Stream Analytics에서 업데이트된 Blob `sample/2015-04-16/17-30/products.csv`을 선택하도록 지시합니다.

Azure Stream Analytics는 1분 간격으로 새로 고친 참조 데이터 Blob을 자동으로 검색합니다.

> [!NOTE]
> 현재 Stream Analytics 작업은 컴퓨터 시간이 Blob 이름에 인코딩된 시간으로 진행하는 경우에만 Blob 새로 고침을 찾습니다. 예를 들어 작업은 가능한 빨리 `sample/2015-04-16/17-30/products.csv`를 찾지만 표준 시간대 2015년 4월 16일 오후 5시 30분보다 이르지 않습니다. 마지막으로 검색된 것보다 이전에 인코딩된 시간으로 Blob을 찾지 *않습니다* .
> 
> 예: 작업이 Blob `sample/2015-04-16/17-30/products.csv`를 발견하면 2015년 4월 16일 오후 5시 30분 이전에 인코딩된 날짜의 모든 파일을 무시합니다. 따라서 늦게 도착하는 `sample/2015-04-16/17-25/products.csv` Blob이 동일한 컨테이너에 만들어지는 경우 작업은 이를 사용하지 않습니다.
> 
> 마찬가지로 `sample/2015-04-16/17-30/products.csv`가 2015년 4월 16일 오후 10시 3분에 생성되었지만 컨테이너에 이전 날짜의 Blob이 없는 경우 작업은 2015년 4월 16일 오후 10시 3분에 시작하는 이 파일을 사용하고 그때까지 이전 참조 데이터를 사용합니다.
> 
> 이에 대한 예외는 시간을 거슬러 데이터를 재처리해야 하는 작업이거나 작업을 최초로 시작할 때입니다. 시작 시점에 작업은 지정된 작업 시작 시간 이전에 생성된 가장 최근 Blob을 찾습니다. 이렇게 하면 작업을 시작할 때 **비어 있지 않은** 참조 데이터가 설정됩니다. 찾을 수 없는 경우 작업은 다음 진단 `Initializing input without a valid reference data blob for UTC time <start time>`을 표시합니다.

Stream Analytics에서 참조 데이터 정의를 업데이트하는 데 필요한 업데이트된 Blob을 만드는 작업을 오케스트레이션하는 데 [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)를 사용할 수 있습니다. 데이터 팩터리는 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 데이터 팩터리는 [많은 수의 클라우드 기반 및 온-프레미스 데이터 저장소 연결](../data-factory/copy-activity-overview.md) 을 지원하고 사용자가 지정한 정기적인 일정으로 데이터를 쉽게 이동할 수 있도록 지원합니다. 미리 정의된 일정에 따라 새로 고쳐지는 Stream Analytics를 위한 참조 데이터를 생성하는 데이터 팩터리 파이프라인 설정 방법에 대한 단계별 지침과 자세한 내용은 이 [GitHub 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)을 확인하세요.

### <a name="tips-on-refreshing-blob-reference-data"></a>Blob 참조 데이터 새로 고침 팁

1. 참조 데이터 Blob은 변경할 수 없으므로 덮어쓰지 않습니다.
2. 참조 데이터를 새로 고치는 데 권장되는 방법은 다음과 같습니다.
    * 경로 패턴에 {date}/{time}을 사용합니다.
    * 작업 입력에 정의된 동일한 컨테이너와 경로 패턴을 사용하여 새 Blob을 추가합니다.
    * 시퀀스의 마지막 Blob에서 지정된 날짜/시간보다 **큰** 날짜/시간을 사용합니다.
3. 참조 데이터 Blob은 Blob의 “마지막 수정” 시간 순서가 **아니라** {date} 및 {time}을 대체하여 Blob 이름에 지정한 날짜와 시간으로만 순서가 지정됩니다.
3. 많은 수의 BLOB을 표시하지 않으려면 더 이상 처리 작업이 수행되지 않는 아주 오래된 BLOB을 삭제합니다. 재시작 같은 일부 시나리오에서는 ASA가 소량을 다시 처리해야 할 수도 있습니다.

## <a name="azure-sql-database-preview"></a>Azure SQL Database(미리 보기)

Azure SQL Database 참조 데이터는 Stream Analytics 작업에서 검색되고 처리를 위해 메모리에 스냅숏으로 저장됩니다. 또한 참조 데이터의 스냅숏은 구성 설정에서 지정하는 스토리지 계정의 컨테이너에 저장됩니다. 컨테이너는 작업을 시작할 때 자동 생성됩니다. 작업이 중지되거나 실패 상태가 되면, 작업을 다시 시작할 때 자동 생성된 컨테이너가 삭제됩니다.  

참조 데이터가 느리게 변화하는 데이터 세트인 경우 작업에서 사용되는 스냅숏을 정기적으로 새로 고쳐야 합니다. Stream Analytics를 사용하면 Azure SQL Database 입력 연결을 구성할 때 새로 고침 빈도를 설정할 수 있습니다. Stream Analytics 런타임은 새로 고침 빈도에서 지정된 간격으로 Azure SQL Database를 쿼리합니다. 지원되는 가장 빠른 새로 고침 빈도는 1분에 한 번씩입니다. 각 새로 고침의 경우 Stream Analytics는 제공된 스토리지 계정에 새 스냅숏을 저장합니다.

Stream Analytics는 Azure SQL Database를 쿼리하기 위한 두 가지 옵션을 제공합니다. 스냅숏 쿼리는 필수이며 각 작업에 포함되어야 합니다. Stream Analytics는 새로 고침 간격에 따라 주기적으로 스냅숏 쿼리를 실행하고 쿼리의 결과(스냅숏)를 참조 데이터 세트로 사용합니다. 스냅숏 쿼리는 대부분의 시나리오에 적합해야 하지만, 데이터 세트가 크고 새로 고침 주기가 빠른 성능 문제가 발생하는 경우 델타 쿼리 옵션을 사용할 수 있습니다. 참조 데이터 집합을 반환할 60 초 이상 걸리는 쿼리 시간 초과가 발생 합니다.

델타 쿼리 옵션을 사용하면 Stream Analytics는 초기에 스냅숏 쿼리를 실행하여 기본 참조 데이터 세트를 가져옵니다. 그 후에 Stream Analytics는 새로 고침 간격에 따라 주기적으로 델타 쿼리를 실행하여 증분 변경 내용을 검색합니다. 이러한 증분 변경 내용은 지속적으로 참조 데이터 세트에 적용되어 업데이트를 최신으로 유지합니다. 델타 쿼리를 사용하면 스토리지 비용 및 네트워크 I/O 작업을 줄이는 데 도움이 될 수 있습니다.

### <a name="configure-sql-database-reference"></a>SQL Database 참조 구성

SQL Database 참조 데이터를 구성하려면 먼저 **참조 데이터** 입력을 만들어야 합니다. 다음 표에는 참조 데이터 입력을 만드는 동안 제공해야 하는 각 속성이 해당 설명과 함께 나와 있습니다. 자세한 내용은 [Azure Stream Analytics 작업에 SQL Database의 참조 데이터 사용](sql-reference-data.md)을 참조하세요.

|**속성 이름**|**설명**  |
|---------|---------|
|입력 별칭|이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다.|
|구독|구독 선택|
|데이터베이스|참조 데이터가 포함된 Azure SQL Database입니다.|
|사용자 이름|Azure SQL Database와 연결된 사용자 이름입니다.|
|암호|Azure SQL Database와 연결된 암호입니다.|
|주기적으로 새로 고침|이 옵션을 사용하면 새로 고침 빈도를 선택할 수 있습니다. “On”을 선택하면 DD:HH:MM 형식으로 새로 고침 빈도를 지정할 수 있습니다.|
|스냅숏 쿼리|이는 SQL Database에서 참조 데이터를 검색하는 기본 쿼리 옵션입니다.|
|델타 쿼리|데이터 세트가 크고 새로 고침 빈도가 짧은 고급 시나리오의 경우 델타 쿼리를 추가하도록 선택합니다.|

## <a name="size-limitation"></a>크기 제한

Stream Analytics는 **최대 300MB 크기**의 참조 데이터를 지원합니다. 참조 데이터의 최대 크기인 300MB 제한은 간단한 쿼리만으로도 달성할 수 있습니다. 기간 이동 집계, temporal 조인 및 temporal 분석 함수와 같은 상태 프로세싱을 포함할 정도로 쿼리의 복잡성이 증가하면 참조 데이터의 지원되는 최대 크기는 감소하게 됩니다. Azure Stream Analytics가 참조 데이터를 로드하여 복잡한 작업을 수행할 수 없는 경우 작업에 메모리가 부족하여 실패하게 됩니다. 그러한 경우 SU % 사용률 메트릭은 100%에 도달합니다.    

|**스트리밍 단위의 수**  |**지원되는 최대 근사값 크기(MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 이상   |300   |

6 이상 작업의 스트리밍 단위 수가 증가하면 참조 데이터의 지원되는 최대 크기는 증가하지 않습니다.

참조 데이터에는 압축이 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
