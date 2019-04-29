---
title: Azure Data Lake Storage Gen2와 관련된 데이터 시나리오 | Microsoft Docs
description: Data Lake Storage Gen2(이전의 Azure Data Lake Store)에서 수집, 처리, 다운로드 및 시각화될 수 있는 데이터를 사용하는 다양한 시나리오와 도구를 알아봅니다.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: 9deaa2f1e381dffbd85b0ee150c5782098a9db6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628253"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>빅 데이터 요구 사항을 위한 Azure Data Lake Storage Gen2 사용

빅 데이터 처리에는 네 가지 주요 단계가 있습니다.

> [!div class="checklist"]
> * 실시간으로 또는 배치로 대량의 데이터를 데이터 저장소에 수집
> * 데이터 처리
> * 데이터 다운로드
> * 데이터 시각화

먼저 스토리지 계정과 파일 시스템을 만듭니다. 그런 다음, 데이터에 대한 액세스 권한을 부여합니다. 이 문서의 앞부분에 나오는 몇 개 섹션에서는 이러한 작업을 수행하는 방법을 설명합니다. 나머지 섹션에서는 각 처리 단계의 옵션 및 도구를 집중적으로 살펴봅니다.

## <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정 만들기

Data Lake Storage Gen2 계정은 계층 구조 네임스페이스가 있는 스토리지 계정입니다. 

만드는 방법은 [빠른 시작: Azure Data Lake Storage Gen2 스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

## <a name="create-a-file-system"></a>파일 시스템 만들기

*파일 시스템*은 폴더와 파일을 담는 컨테이너입니다. 스토리지 계정에서 데이터 수집을 시작하려면 적어도 하나 이상의 파일 시스템이 필요합니다.  다음은 파일 시스템을 만드는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure Storage 탐색기 | [Storage 탐색기를 사용하여 파일 시스템 만들기](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-filesystem) |
|AzCopy | [AzCopyV10을 사용하여 Blob 컨테이너 또는 파일 공유 만들기](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-blob-container-or-file-share)|
|HDInsight가 포함된 HDFS(Hadoop File System) CLI(명령줄 인터페이스) |[HDInsight가 포함된 HDFS를 사용하여 파일 시스템 만들기](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Azure Databricks Notebook의 코드|[스토리지 계정 파일 시스템 만들기(Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [파일 시스템을 만들고 탑재(Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

Storage 탐색기 또는 AzCopy를 사용하여 파일 시스템을 만드는 방법이 가장 쉽습니다. HDInsight 및 Databricks를 사용하여 파일 시스템을 만들려면 좀 더 많은 작업이 필요합니다. 하지만 HDInsight 또는 Databricks 클러스터를 사용하여 데이터를 처리할 계획이라면 클러스터를 먼저 만들고, HDFS CLI를 사용하여 파일 시스템을 만들어도 됩니다.  

## <a name="grant-access-to-the-data"></a>데이터에 대한 액세스 권한 부여

데이터 수집을 시작하려면 계정 및 계정의 데이터에 대한 적절한 액세스 권한을 설정해야 합니다.

다음과 같은 세 가지 방법으로 액세스 권한을 부여할 수 있습니다.

* 다음 역할 중 하나를 사용자, 그룹, 사용자 관리형 ID 또는 서비스 주체에 할당합니다.

  [Storage Blob 데이터 읽기 권한자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)

  [Storage Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)

  [Storage Blob 데이터 소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)

* SAS(공유 액세스 서명) 토큰을 사용합니다.

* 스토리지 계정 키를 사용합니다.

이 표는 각 Azure 서비스 또는 도구에 대한 액세스 권한을 부여하는 방법을 보여줍니다.

|도구 | 액세스 권한을 부여하려면 | 지침 |
|---|--|---|
|Storage 탐색기| 사용자 및 그룹에 역할 할당 | [Azure Active Directory를 사용하여 사용자에게 관리자 및 비관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| 사용자 및 그룹에 역할 할당 <br>**or**<br> SAS 토큰 사용| [Azure Active Directory를 사용하여 사용자에게 관리자 및 비관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[간편하게 Azure Storage 탐색기를 사용하여 Azure Storage에서 파일을 다운로드하는 SAS 만들기](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | 사용자 할당 관리 ID에 역할 할당 | [Data Lake Storage Gen2를 사용하여 HDInsight 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| 사용자 할당 관리형 ID에 역할 할당<br>**or**<br> 서비스 주체에 역할 할당<br>**or**<br> 스토리지 계정 키 사용 | [연결된 서비스 속성](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| 사용자 할당 관리 ID에 역할 할당 | [Data Lake Storage Gen2를 사용하여 HDInsight 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| 서비스 주체에 역할 할당 | [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

특정 파일 및 폴더에 대한 액세스 권한을 부여하려면 다음 문서를 참조하세요.

* [Azure Data Lake Storage Gen2와 함께 Azure Storage 탐색기를 사용하여 파일 및 디렉터리 수준 권한 설정](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [파일 및 디렉터리에 대한 액세스 제어 목록](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

보안의 다른 측면을 설정하는 방법은 [Azure Data Lake Storage Gen2 보안 가이드](https://review.docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

## <a name="ingest-the-data"></a>데이터 수집

이 섹션에서는 다양한 데이터 원본과 해당 데이터를 Data Lake Storage Gen2 계정에 수집할 수 있는 여러 가지 방법을 중점적으로 설명합니다.

![Data Lake Storage Gen2에 데이터 수집](./media/data-lake-storage-data-scenarios/ingest-data.png "Data Lake Storage Gen2에 데이터 수집")

### <a name="ad-hoc-data"></a>임시 데이터

빅 데이터 애플리케이션의 프로토타입 제작에 사용되는 작은 데이터 집합을 나타냅니다. 데이터 원본에 따라 임시 데이터를 수집하는 여러 가지 방법이 있습니다. 

다음은 임시 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

| 데이터 원본 | 로컬 컴퓨터를 사용하여 |
| --- | --- |
| 수집 |[Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy 도구](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure 데이터 팩터리](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy 도구](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight 클러스터에서 실행되는 DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>스트리밍된 데이터

애플리케이션, 장치, 센서 등 다양한 원본을 통해 생성할 수 있는 데이터를 나타냅니다. 이 데이터는 다양한 도구를 통해 Data Lake Storage Gen2에 수집할 수 있습니다. 이러한 도구는 일반적으로 데이터를 이벤트별로 실시간으로 캡처하고 처리한 다음, 이벤트를 추가로 처리할 수 있도록 해당 이벤트를 Data Lake Storage Gen2에 일괄적으로 씁니다.

다음은 스트리밍된 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure HDInsight Storm | [HDInsight의 Apache Storm에서 Apache Hadoop HDFS에 쓰기](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>관계형 데이터

관계형 데이터베이스의 데이터를 원본으로 사용할 수도 있습니다. 관계형 데이터베이스는 일정 기간 동안 엄청난 양의 데이터를 수집합니다. 이 데이터를 빅 데이터 파이프라인을 통해 처리하면 중요한 정보를 얻을 수 있습니다. 다음 도구를 사용하여 이러한 데이터를 Data Lake Storage Gen2로 이동할 수 있습니다.

다음은 관계형 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure Data Factory | [Azure Data Factory의 복사 작업](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>웹 서버 로그 데이터(사용자 지정 애플리케이션을 사용하여 업로드)

웹 서버 로그 데이터의 분석은 빅 데이터 애플리케이션에 대한 일반적인 사용 사례이며 대용량 로그 파일을 Data Lake Storage Gen2에 업로드해야 하므로 이러한 유형의 데이터 세트가 특별히 호출됩니다. 다음 중 아무 도구를 사용하여 이러한 데이터를 업로드하는 고유의 스크립트 또는 애플리케이션을 작성할 수 있습니다.

다음은 웹 서버 로그 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure Data Factory | [Azure Data Factory의 복사 작업](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

웹 서버 로그 데이터를 업로드하고 다른 종류의 데이터 (예: 소셜 정서 데이터)를 업로드하려는 경우 고유의 사용자 지정 스크립트/애플리케이션을 작성하는 것이 좋습니다. 데이터 업로드 구성 요소를 더 큰 빅 데이터 애플리케이션의 일부로 유연하게 포함할 수 있기 때문입니다. 어떤 경우에는 이 코드가 스크립트 형태 또는 간단한 명령줄 유틸리티의 형태를 취할 수 있습니다. 또 어떤 경우에는 빅 데이터 처리를 비즈니스 애플리케이션 또는 솔루션에 통합하는 데 이 코드가 사용될 수 있습니다.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터와 연결된 데이터

대부분의 HDInsight 클러스터 유형(Hadoop, HBase, Storm)은 Data Lake Storage Gen2를 데이터 스토리지 리포지토리로 지원합니다. HDInsight 클러스터는 Azure Storage Blob(WASB)에서 데이터에 액세스합니다. WASB의 데이터를 클러스터와 연결된 Data Lake Storage Gen2 계정에 복사하면 성능을 향상시킬 수 있습니다. 다음 도구를 사용하여 데이터를 복사할 수 있습니다.

다음은 HDInsight 클러스터와 연결된 데이터를 수집하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Apache DistCp | [DistCp를 사용하여 Azure Storage Blob과 Azure Data Lake Storage Gen2 간에 데이터 복사](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy 도구 | [AzCopy를 사용하여 데이터 전송](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Azure Data Factory를 사용 하 여 Azure Data Lake 저장소 Gen2 간에 데이터 복사](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>온-프레미스 또는 IaaS Hadoop 클러스터에 저장된 데이터

HDFS를 사용하여 로컬 컴퓨터의 기존 Hadoop 클러스터에 대량의 데이터를 저장할 수 있습니다. Hadoop 클러스터는 온-프레미스 배포 또는 Azure의 IaaS 클러스터에 있을 수 있습니다. 이러한 데이터를 Azure Data Lake Storage Gen2에 일회성 또는 반복적인 방식으로 복사해야 할 수도 있습니다. 이 작업을 수행하는 방법은 다양한 옵션이 있습니다. 다음은 이를 대체할 수 있는 방법 목록과 관련 절충 사항입니다.

| 접근 방식 | 세부 정보 | 장점 | 고려 사항 |
| --- | --- | --- | --- |
| ADF(Azure Data Factory)를 사용하여 데이터를 Hadoop 클러스터에서 Azure Data Lake Storage Gen2로 직접 복사합니다. |[ADF는 데이터 원본으로 HDFS 지원](../../data-factory/connector-hdfs.md) |ADF는 HDFS에 대한 기본 지원과 일등급 종단 간 관리 및 모니터링을 제공합니다. |온-프레미스 또는 IaaS 클러스터에 배포하려면 데이터 관리 게이트웨이가 필요합니다. |
| Distcp를 사용하여 Hadoop에서 Azure Storage로 데이터를 복사합니다. 그런 다음, 적절한 메커니즘을 사용하여 데이터를 Azure Storage에서 Data Lake Storage Gen2로 복사합니다. |데이터를 Azure Storage에서 Data Lake Storage Gen2에 복사하는 데 사용할 수 있는 도구는 다음과 같습니다. <ul><li>[Azure 데이터 팩터리](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy 도구](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight 클러스터에서 실행되는 Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |오픈 소스 도구를 사용할 수 있습니다. |여러 기술을 사용하는 다단계 절차입니다. |

### <a name="really-large-datasets"></a>매우 큰 데이터 세트

용량이 수 테라바이트에 달하는 데이터 세트를 업로드하는 경우 위에서 설명한 방법을 사용하면 속도가 느리고 비용이 많이 들 수 있습니다. 이러한 경우 Azure ExpressRoute를 사용할 수 있습니다.  

Azure ExpressRoute를 사용하면 온-프레미스의 인프라와 Azure 데이터 센터 사이에 비공개 연결을 만들 수 있습니다. 이렇게 하면 대용량 데이터를 안전하게 전송할 수 있습니다. 자세한 내용은 [Azure ExpressRoute 설명서](../../expressroute/expressroute-introduction.md)를 참조하세요.

## <a name="process-the-data"></a>데이터 처리

Data Lake Storage Gen2의 데이터를 사용할 수 있게 되면 지원되는 빅 데이터 애플리케이션을 사용하여 해당 데이터에 대한 분석을 실행할 수 있습니다. 

![Data Lake Storage Gen2의 데이터 분석](./media/data-lake-storage-data-scenarios/analyze-data.png "Data Lake Storage Gen2의 데이터 분석")

다음은 Data Lake Storage Gen2에 저장된 데이터에 대한 데이터 분석 작업을 실행하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure HDInsight | [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[빠른 시작: Azure Databricks를 사용하여 Azure Data Lake Storage Gen2의 데이터 분석](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[자습서: Azure Databricks를 사용하여 데이터 추출, 변환 및 로드](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>데이터 시각화

다양한 서비스를 사용하여 Data Lake Storage Gen2에 저장된 데이터를 시각적으로 표현할 수 있습니다.

![Data Lake Storage Gen2의 데이터 시각화](./media/data-lake-storage-data-scenarios/visualize-data.png "Data Lake Storage Gen2의 데이터 시각화")

* 먼저 [Azure Data Factory를 사용하여 데이터를 Data Lake Store Gen2에서 Azure SQL Data Warehouse로 이동할 수 있습니다](../../data-factory/copy-activity-overview.md).
* 그 후에는 [Power BI를 Azure SQL Data Warehouse와 통합](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) 하여 데이터를 시각적으로 표현할 수 있습니다.

## <a name="download-the-data"></a>데이터 다운로드

다음과 같은 시나리오를 위해 Azure Data Lake Storage Gen2에서 데이터를 다운로드하거나 이동할 수도 있습니다.

* 기존 데이터 처리 파이프라인과 상호 작용하기 위해 다른 리포지토리로 데이터를 이동합니다. 예를 들어 데이터를 Data Lake Storage Gen2에서 Azure SQL Database 또는 온-프레미스 SQL Server로 이동하려고 할 수 있습니다.

* 애플리케이션 프로토타입을 빌드하는 동안 IDE 환경에서 데이터를 처리하기 위해 로컬 컴퓨터에 데이터를 다운로드 합니다.

![Data Lake Storage Gen2에서 데이터 송신](./media/data-lake-storage-data-scenarios/egress-data.png "Data Lake Storage Gen2에서 데이터 송신")

다음은 Data Lake Storage Gen2에서 데이터를 다운로드하는 데 사용할 수 있는 도구 목록입니다.

|도구 | 지침 |
|---|--|
|Azure Data Factory | [Azure Data Factory의 복사 작업](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCop | [DistCp를 사용하여 Azure Storage Blob과 Azure Data Lake Storage Gen2 간에 데이터 복사](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
