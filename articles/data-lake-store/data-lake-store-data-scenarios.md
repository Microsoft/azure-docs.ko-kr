---
title: Data Lake Storage Gen1과 관련된 데이터 시나리오 | Microsoft Docs
description: 데이터를 Data Lake Storage Gen1(이전에 Azure Data Lake Store로 알려짐)에 수집, 처리, 다운로드 및 시각화할 수 있는 다양한 시나리오 및 도구 이해
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0b16154edbda4bedfd4e9b680ba4311e7a235212
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879040"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>빅 데이터 요구 사항에 Azure Data Lake Storage Gen1 사용

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

빅 데이터 처리에는 네 가지 주요 단계가 있습니다.

* 실시간으로 또는 배치로 대량의 데이터를 데이터 저장소에 수집
* 데이터 처리
* 데이터 다운로드
* 데이터 시각화

이 문서에서는 Azure Data Lake Storage Gen1과 관련하여 이러한 단계를 살펴보고 빅 데이터 요구 사항을 충족하는 데 사용할 수 있는 옵션과 도구에 대해 알아봅니다.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 데이터 수집
이 섹션에서는 다양한 데이터 원본과 해당 데이터를 Data Lake Storage Gen1 계정에 수집할 수 있는 여러 가지 방법을 중점적으로 설명합니다.

![Data Lake Storage Gen1에 데이터 수집](./media/data-lake-store-data-scenarios/ingest-data.png "Data Lake Storage Gen1에 데이터 수집")

### <a name="ad-hoc-data"></a>임시 데이터
빅 데이터 애플리케이션의 프로토타입 제작에 사용되는 작은 데이터 집합을 나타냅니다. 데이터 원본에 따라 임시 데이터를 수집하는 여러 가지 방법이 있습니다.

| 데이터 원본 | 로컬 컴퓨터를 사용하여 |
| --- | --- |
| 수집 |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 만들기](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure 데이터 팩터리](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy 도구](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight 클러스터에서 실행되는 DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>스트리밍된 데이터
애플리케이션, 장치, 센서 등 다양한 원본을 통해 생성할 수 있는 데이터를 나타냅니다. 이 데이터는 다양한 도구를 통해 Data Lake Storage Gen1에 수집할 수 있습니다. 이러한 도구는 일반적으로 데이터를 이벤트별로 실시간으로 캡처하고 처리한 다음, 이벤트를 추가로 처리할 수 있도록 해당 이벤트를 일괄 처리 방식으로 Data Lake Storage Gen1에 씁니다.

다음은 사용할 수 있는 도구입니다.

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) - Azure Data Lake Storage Gen1 출력을 사용하여 Event Hubs에 수집된 이벤트를 Azure Data Lake Storage Gen1에 쓸 수 있습니다.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) - Storm 클러스터에서 Data Lake Storage Gen1에 직접 데이터를 쓸 수 있습니다.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – Event Hubs에서 이벤트를 받은 다음, [Data Lake Store Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)를 사용하여 Data Lake Storage Gen1에 해당 이벤트를 쓸 수 있습니다.

### <a name="relational-data"></a>관계형 데이터
관계형 데이터베이스의 데이터를 원본으로 사용할 수도 있습니다. 관계형 데이터베이스는 일정 기간 동안 엄청난 양의 데이터를 수집합니다. 이 데이터를 빅 데이터 파이프라인을 통해 처리하면 중요한 정보를 얻을 수 있습니다. 다음 도구를 사용하여 이러한 데이터를 Data Lake Storage Gen1로 이동할 수 있습니다.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure 데이터 팩터리](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>웹 서버 로그 데이터(사용자 지정 애플리케이션을 사용하여 업로드)
웹 서버 로그 데이터 분석은 빅 데이터 응용 프로그램에 대한 일반적인 사용 사례이며 대용량 로그 파일을 Data Lake Storage Gen1에 업로드해야 하므로 이러한 유형의 데이터 세트가 특별히 호출됩니다. 다음 중 아무 도구를 사용하여 이러한 데이터를 업로드하는 고유의 스크립트 또는 애플리케이션을 작성할 수 있습니다.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure 데이터 팩터리](../data-factory/copy-activity-overview.md)

웹 서버 로그 데이터를 업로드하고 다른 종류의 데이터 (예: 소셜 정서 데이터)를 업로드하려는 경우 고유의 사용자 지정 스크립트/애플리케이션을 작성하는 것이 좋습니다. 데이터 업로드 구성 요소를 더 큰 빅 데이터 애플리케이션의 일부로 유연하게 포함할 수 있기 때문입니다. 어떤 경우에는 이 코드가 스크립트 형태 또는 간단한 명령줄 유틸리티의 형태를 취할 수 있습니다. 또 어떤 경우에는 빅 데이터 처리를 비즈니스 애플리케이션 또는 솔루션에 통합하는 데 이 코드가 사용될 수 있습니다.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터와 연결된 데이터
대부분의 HDInsight 클러스터 유형(Hadoop, HBase, Storm)은 Data Lake Storage Gen1을 데이터 저장소 리포지토리로 지원합니다. HDInsight 클러스터는 Azure Storage Blob(WASB)에서 데이터에 액세스합니다. WASB의 데이터를 클러스터와 연결된 Data Lake Storage Gen1 계정에 복사하면 성능을 향상시킬 수 있습니다. 다음 도구를 사용하여 데이터를 복사할 수 있습니다.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure 데이터 팩터리](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>온-프레미스 또는 IaaS Hadoop 클러스터에 저장된 데이터
HDFS를 사용하여 로컬 컴퓨터의 기존 Hadoop 클러스터에 대량의 데이터를 저장할 수 있습니다. Hadoop 클러스터는 온-프레미스 배포 또는 Azure의 IaaS 클러스터에 있을 수 있습니다. 이러한 데이터를 일회성 또는 반복적인 방식으로 Azure Data Lake Storage Gen1에 복사해야 할 수도 있습니다. 이 작업을 수행하는 방법은 다양한 옵션이 있습니다. 다음은 이를 대체할 수 있는 방법 목록과 관련 절충 사항입니다.

| 접근 방식 | 세부 정보 | 장점 | 고려 사항 |
| --- | --- | --- | --- |
| ADF(Azure Data Factory)를 사용하여 데이터를 Hadoop 클러스터에서 Azure Data Lake Storage Gen1로 직접 복사합니다. |[ADF는 데이터 원본으로 HDFS 지원](../data-factory/connector-hdfs.md) |ADF는 HDFS에 대한 기본 지원과 일등급 종단 간 관리 및 모니터링을 제공합니다. |온-프레미스 또는 IaaS 클러스터에 배포하려면 데이터 관리 게이트웨이가 필요합니다. |
| Hadoop에서 데이터를 파일로 내보냅니다. 그런 다음, 적절한 메커니즘을 사용하여 파일을 Azure Data Lake Storage Gen1에 복사합니다. |파일을 Azure Data Lake Storage Gen1에 복사하는 데 사용할 수 있는 도구는 다음과 같습니다. <ul><li>[Windows OS용 Azure PowerShell](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Data Lake Storage Gen1 SDK를 사용하는 사용자 지정 앱</li></ul> |빠르게 시작할 수 있습니다. 맞춤 업로드를 수행할 수 있습니다. |여러 기술을 사용하는 다단계 절차입니다. 도구를 맞춤화할 수 있으므로 시간이 지날수록 관리와 모니터링이 어려워집니다. |
| Distcp를 사용하여 Hadoop에서 Azure Storage로 데이터를 복사합니다. 그런 다음, 적절한 메커니즘을 사용하여 데이터를 Azure Storage에서 Data Lake Storage Gen1로 복사합니다. |데이터를 Azure Storage에서 Data Lake Storage Gen1에 복사하는 데 사용할 수 있는 도구는 다음과 같습니다. <ul><li>[Azure 데이터 팩터리](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy 도구](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight 클러스터에서 실행되는 Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |오픈 소스 도구를 사용할 수 있습니다. |여러 기술을 사용하는 다단계 절차입니다. |

### <a name="really-large-datasets"></a>매우 큰 데이터 세트
용량이 수 테라바이트에 달하는 데이터 세트를 업로드하는 경우 위에서 설명한 방법을 사용하면 속도가 느리고 비용이 많이 들 수 있습니다. 이러한 경우 아래의 옵션을 사용할 수 있습니다.

* **Azure ExpressRoute 사용**. Azure ExpressRoute를 사용하면 온-프레미스의 인프라와 Azure 데이터 센터 사이에 개인 연결을 만들 수 있습니다. 이렇게 하면 대용량 데이터를 안전하게 전송할 수 있습니다. 자세한 내용은 [Azure ExpressRoute 설명서](../expressroute/expressroute-introduction.md)를 참조하세요.
* **데이터를 "오프라인"으로 업로드**. Azure ExpressRoute를 사용할 수 없는 경우 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md) 를 사용하여 데이터가 들어 있는 하드 디스크를 Azure 데이터 센터로 보낼 수 있습니다. 데이터는 먼저 Azure Storage Blob에 업로드됩니다. 그러면 [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) 또는 [AdlCopy 도구](data-lake-store-copy-data-azure-storage-blob.md)를 사용하여 데이터를 Azure Storage Blob에서 Data Lake Store Gen1으로 복사할 수 있습니다.

  > [!NOTE]
  > Import/Export 서비스를 사용할 때 Azure 데이터 센터로 보내는 디스크의 파일 크기가 195GB를 초과하면 안 됩니다.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 저장된 데이터 처리
Data Lake Storage Gen1의 데이터를 사용할 수 있게 되면 지원되는 빅 데이터 애플리케이션을 사용하여 해당 데이터에 대한 분석을 실행할 수 있습니다. 현재 Azure HDInsight 및 Azure Data Lake Analytics를 사용하여 Azure Data Lake Storage Gen1에 저장된 데이터에 대한 데이터 분석 작업을 실행할 수 있습니다.

![Data Lake Storage Gen1의 데이터 분석](./media/data-lake-store-data-scenarios/analyze-data.png "Data Lake Storage Gen1의 데이터 분석")

다음 예를 살펴보세요.

* [Data Lake Storage Gen1를 저장소로 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Storage Gen1을 통해 Azure Data Lake Analytics 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 데이터 다운로드
다음과 같은 시나리오를 위해 Azure Data Lake Storage Gen1에서 데이터를 다운로드하거나 이동할 수도 있습니다.

* 기존 데이터 처리 파이프라인과 상호 작용하기 위해 다른 리포지토리로 데이터를 이동합니다. 예를 들어 Data Lake Storage Gen1에서 Azure SQL Database 또는 온-프레미스 SQL Server로 데이터를 이동하려는 경우가 있습니다.
* 애플리케이션 프로토타입을 빌드하는 동안 IDE 환경에서 데이터를 처리하기 위해 로컬 컴퓨터에 데이터를 다운로드 합니다.

![Data Lake Storage Gen1에서 데이터 송신](./media/data-lake-store-data-scenarios/egress-data.png "Data Lake Storage Gen1에서 데이터 송신")

이러한 경우 다음 옵션을 사용할 수 있습니다.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure 데이터 팩터리](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

또한 다음 방법을 사용하여 Data Lake Storage Gen1에서 데이터를 다운로드하는 사용자 고유의 스크립트/응용 프로그램을 작성할 수도 있습니다.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1의 데이터 시각화
여러 서비스를 혼합하여 Data Lake Storage Gen1에 저장된 데이터의 시각적 표현을 만들 수 있습니다.

![Data Lake Storage Gen1의 데이터 시각화](./media/data-lake-store-data-scenarios/visualize-data.png "Data Lake Storage Gen1의 데이터 시각화")

* 먼저 [Azure Data Factory를 사용하여 데이터를 Data Lake Store Gen1에서 Azure SQL Data Warehouse로 이동할 수 있습니다.](../data-factory/copy-activity-overview.md)
* 그 후에는 [Power BI를 Azure SQL Data Warehouse와 통합](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) 하여 데이터를 시각적으로 표현할 수 있습니다.
