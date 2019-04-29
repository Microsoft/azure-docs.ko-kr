---
title: 빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드
description: Azure Data Lake Storage Gen2를 사용하기 위해 솔루션을 업그레이드합니다.
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 84e3aff9c1c8cb3e7fe399c861c2c7d58c278fed
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107876"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드

빅 데이터 분석 솔루션에서 Azure Data Lake Storage Gen1을 사용하는 경우 이 가이드는 Azure Data Lake Storage Gen2를 사용하기 위해 이러한 솔루션을 업그레이드하는 데 도움이 됩니다. 이 문서를 사용하여 Data Lake Storage Gen1에 대한 솔루션의 종속성을 평가할 수 있습니다. 또한 이 가이드에서는 업그레이드를 계획하고 수행하는 방법을 보여 줍니다.

다음 작업을 통해 알아봅니다.

:heavy_check_mark: 업그레이드 준비 평가

:heavy_check_mark: 업그레이드 계획

:heavy_check_mark: 업그레이드 수행

## <a name="assess-your-upgrade-readiness"></a>업그레이드 준비 평가

Data Lake Storage Gen1에 있는 모든 기능을 Data Lake Storage Gen2에서 사용할 수 있도록 하는 것이 목표입니다. 이러한 기능이 SDK, CLI 등에서 공개되는 방법은 Data Lake Storage Gen1과 Data Lake Storage Gen2 간에 다를 수 있습니다. Data Lake Storage Gen1에서 작동하는 애플리케이션과 서비스는 Data Lake Storage Gen2와 비슷하게 작동할 수 있어야 합니다. 마지막으로 Data Lake Storage Gen2에서 일부 기능은 즉시 사용할 수 없습니다. 이러한 기능이 사용할 수 있게 되면 이 문서에서 발표할 것입니다.

다음 섹션은 Data Lake Storage Gen2로 업그레이드하는 것이 가장 적합한지와 이를 수행하는 것이 합리적인 경우를 결정하는 데 도움이 됩니다.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 솔루션 구성 요소

분석 솔루션 또는 파이프라인에서 Data Lake Storage Gen1을 사용하면 전반적인 엔드투엔드 기능을 달성하기 위해 사용하는 많은 추가 기술이 있을 가능성이 높습니다. 이 [문서](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)에서는 데이터 수집, 처리 및 사용을 포함한 데이터 흐름의 다양한 구성 요소에 대해 설명합니다.

또한 이러한 구성 요소를 프로비전, 관리 및 모니터링하는 교차 구성 요소도 있습니다. 각 구성 요소는 가장 적합한 인터페이스를 사용하여 Data Lake Storage Gen1에서 작동합니다. 솔루션을 Data Lake Storage Gen2로 업그레이드하려면 사용되는 인터페이스를 알고 있어야 합니다. 각 인터페이스에는 고유한 요구 사항이 있으므로 관리 인터페이스와 데이터 인터페이스를 모두 업그레이드해야 합니다.

![Data Lake Storage 솔루션 구성 요소](./media/data-lake-storage-upgrade/solution-components.png)

위의 **그림 1**에서는 대부분의 분석 솔루션에서 볼 수 있는 기능 구성 요소를 보여 줍니다.

**그림 2**에서는 특정 기술을 사용하여 이러한 구성 요소를 구현하는 방법에 대한 예를 보여 줍니다.

**그림 1**의 저장 기능은 Data Lake Storage Gen1(**그림 2**)에서 제공됩니다. REST API 또는 Java SDK를 사용하여 데이터 흐름의 다양한 구성 요소에서 Data Lake Storage Gen1과 상호 작용하는 방법을 확인합니다. 또한 교차 기능 구성 요소에서 Data Lake Storage Gen1과 상호 작용하는 방법도 확인합니다. 프로 비전 구성 요소는 Azure Monitor 로그를 사용 하는 모니터링 구성 요소가 데이터 레이크 저장소 Gen1에서 제공 되는 운영 데이터를 활용 하는 반면 Azure 리소스 템플릿을 사용 합니다.

솔루션을 Data Lake Storage Gen1에서 Data Lake Storage Gen2로 업그레이드하려면 데이터와 메타데이터를 복사하고, 데이터 흐름을 다시 후크한 다음, 모든 구성 요소에서 Data Lake Storage Gen2를 사용할 수 있어야 합니다.

아래 섹션에서는 더 나은 결정을 내리는 데 도움이 되는 정보를 제공합니다.

:heavy_check_mark: 플랫폼 기능

:heavy_check_mark: 프로그래밍 인터페이스

:heavy_check_mark: Azure 에코시스템

:heavy_check_mark: 파트너 에코시스템

:heavy_check_mark: 작동 정보

각 섹션에서 업그레이드를 위한 "필수 요소(must-have)"를 결정할 수 있습니다. 기능을 사용할 수 있거나 합리적인 해결 방법이 있다고 확신할 수 있으면 이 가이드의 [업그레이드 계획](#planning-for-an-upgrade) 섹션으로 진행합니다.

### <a name="platform-capabilities"></a>플랫폼 기능

이 섹션에서는 현재 Data Lake Storage Gen2에서 사용할 수 있는 Data Lake Storage Gen1 플랫폼 기능에 대해 설명합니다.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - 목표 | Data Lake Storage Gen2 - 가용성 상태  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| 데이터 구성| 폴더 및 파일로 저장된 데이터 지원 | 폴더/파일 외에도 개체/Blob으로 저장된 데이터 지원 - [링크](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | 폴더 및 파일로 저장된 데이터 지원 – *지금 사용 가능* <br><br> 개체/Blob으로 저장된 데이터 지원 - *아직 사용할 수 없음* |
| 네임스페이스| 계층적 | 계층적 |  *지금 사용 가능*  |
| API  | HTTPS를 통한 REST API | HTTP/HTTPS를 통한 REST API| *지금 사용 가능* |
| 서버 쪽 API| [WebHDFS 호환 REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob Service REST API, [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *지금 사용 가능* <br><br> Azure Blob Service REST API – *아직 사용할 수 없음*       |
| Hadoop 파일 시스템 클라이언트 | 예([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | 예([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *지금 사용 가능*  |  
| 데이터 작업 – 권한 부여  | Azure Active Directory ID 기반의 파일 및 폴더 수준 POSIX ACL(액세스 제어 목록)  | Azure Active Directory ID 기반의 파일 및 폴더 수준 POSIX ACL(액세스 제어 목록), 컨테이너에 액세스하기 위한 계정 수준 권한 부여 [RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)(역할 기반 액세스 제어)에 대한 [공유 키](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) | *지금 사용 가능* |
| 데이터 작업 – 로그  | 예 | 지원 티켓을 사용하여 특정 기간에 대한 일회성 로그 요청, Azure 모니터링 통합 | 지원 티켓을 사용하여 특정 기간에 대한 일회성 로그 요청 – *지금 사용 가능*<br><br> Azure Monitoring 통합 – *아직 사용할 수 없음* |
| 미사용 암호화 데이터 | 투명, 서비스 관리 키 및 Azure KeyVault의 고객 관리 키를 통한 서버 쪽 암호화 | 투명, 서비스 관리 키 및 Azure KeyVault의 고객 관리 키를 통한 서버 쪽 암호화 | 서비스 관리형 키 – *지금 사용 가능*<br><br> 고객 관리형 키 – *지금 사용 가능*  |
| 관리 작업(예: 계정 만들기) | [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview) ) | [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/overview) ) | *지금 사용 가능*|
| 개발자 SDK | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *아직 사용할 수 없음* |
| |병렬 분석 워크로드에 대해 최적화된 성능입니다. 높은 처리량 및 IOPS. | 병렬 분석 워크로드에 대해 최적화된 성능입니다. 높은 처리량 및 IOPS. | *지금 사용 가능* |
| 가상 네트워크(VNet) 지원  | [Virtual Network 통합 사용](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Azure Storage에 서비스 엔드포인트 사용](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *지금 사용 가능* |
| 크기 한도 | 계정 크기, 파일 크기 또는 파일 수에 한도가 없음 | 계정 크기 또는 파일 수에 제한이 없습니다. 파일 크기는 5TB로 제한됩니다. | *지금 사용 가능*|
| 지리적 중복| LRS(로컬 중복) | 로컬 중복(LRS), 영역 중복(ZRS), 글로벌 중복(GRS), 읽기 액세스 글로벌 중복(RA-GRS). 자세한 내용은 [여기](https://docs.microsoft.com/azure/storage/common/storage-redundancy)를 참조하세요.| *지금 사용 가능* |
| 국가별 가용성 | 자세한 내용은 [여기](https://azure.microsoft.com/regions/) | 모든 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *지금 사용 가능*                                                                                                                           |
| 가격                                       | 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| 가용성 SLA                            | [SLA 참조](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [SLA 참조](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *지금 사용 가능*                                                                                                                           |
| 데이터 관리                             | 파일 만료 기간                                                                                                                                        | 수명 주기 정책                                                                                                                                                                                                                                                                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                       |

### <a name="programming-interfaces"></a>프로그래밍 인터페이스

다음 표에서는 사용자 지정 애플리케이션에 사용할 수 있는 API 세트에 대해 설명합니다. 좀 더 명확 하 게 작업을 하려면 이러한 API 집합 2 형식으로 분리 했습니다: 관리 Api 및 파일 시스템 Api.

관리 Api에는 파일 시스템 Api를 사용 하면 파일 및 폴더에 대해 작동 하는 데 도움이 하는 동안 계정을 관리할 수 있습니다.

|  API 세트                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Data Lake Storage Gen2 가용성 - 공유 키 인증 사용 | Data Lake Storage Gen2 가용성 - OAuth 인증 사용                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK - 관리                  | [링크](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *지원되지 않음*                                                      | *지금 사용 가능 -* [링크](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK-파일 시스템                  | [링크](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *아직 사용할 수 없음*                                                | *아직 사용할 수 없음*                                                                                                                                             |
| Java SDK - 관리                  | [링크](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *지원되지 않음*                                                      | *지금 사용 가능 –* [링크](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – 파일 시스템                  | [링크](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *아직 사용할 수 없음*                                                | *아직 사용할 수 없음*                                                                                                                                             |
| Node.js - 관리                   | [링크](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | 지원되지 않음                                                      | *지금 사용 가능 -* [링크](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js-파일 시스템                   | [링크](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *아직 사용할 수 없음*                                                | *아직 사용할 수 없음*                                                                                                                                             |
| Python - 관리                    | [링크](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *지원되지 않음*                                                      | *지금 사용 가능 -* [링크](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – 파일 시스템                    | [링크](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *아직 사용할 수 없음*                                                | *아직 사용할 수 없음*                                                                                                                                             |
| REST API - 관리                  | [링크](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *지원되지 않음*                                                      | *지금 사용 가능*                                                                                                                                               |
| REST API-파일 시스템                  | [링크](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *지금 사용 가능*                                                    | *지금 사용 가능 -* [링크](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell-관리 및 파일 시스템 | [링크](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | 관리 – 지원 되지 않습니다. 파일 시스템- *아직 사용할 수 없음*        | 관리 – *지금 사용 가능 -* [링크](https://docs.microsoft.com/powershell/module/az.storage) <br><br>파일 시스템- *아직 사용할 수 없음* |
| CLI - 관리                       | [링크](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *지원되지 않음*                                                      | *지금 사용 가능 -* [링크](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI-파일 시스템                       | [링크](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *아직 사용할 수 없음*                                                | *아직 사용할 수 없음*                                                                                                                                             |
| Azure Resource Manager 템플릿 - 관리             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/),   [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/),   [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *지원되지 않음*                                                      | *지금 사용 가능 -* [링크](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure 에코시스템

Data Lake Storage Gen1을 사용하면 엔드투엔드 파이프라인에서 다양한 Microsoft 서비스 및 제품을 사용할 수 있습니다. 이러한 서비스 및 제품은 Data Lake Storage Gen1에서 직접 또는 간접적으로 작동합니다. 다음 표에서는 Data Lake Storage Gen1에서 작동하도록 수정한 서비스 목록 및 현재 Data Lake Storage Gen2와 호환되는 서비스 목록을 보여 줍니다.

| **영역**             | **Data Lake Storage Gen1 가용성**                                                                                                                                    | **Data Lake Storage Gen2 가용성 - 공유 키 인증 사용**                                                                                                           | **Data Lake Storage Gen2 가용성 - OAuth 사용**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| 분석 프레임워크  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *지금 사용 가능*                                                                                                                                                              | *지금 사용 가능*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *지금 사용 가능*, HDInsight 4.0 - *아직 사용할 수 없음*      | HDInsight 3.6 ESP – *지금 사용 가능* <br><br>  HDInsight 4.0 ESP - *아직 사용할 수 없음*                                                                 |
|                      | Databricks Runtime 3.1 이상                                                                                                                                               | [Databricks Runtime 5.1 이상](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – ‘지금 사용 가능’ | [Databricks Runtime 5.1 이상](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) - ‘지금 사용 가능’                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *지원되지 않음*                                                                                                                                                              | ‘지금 사용 가능’ [링크](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| 데이터 통합     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [버전 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – ‘지금 사용 가능’, 버전 1 – ‘지원되지 않음’                               | [버전 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *지금 사용 가능* <br><br> 버전 1 – *지원되지 않음*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *지원되지 않음*                                                                                                                                                              | *지원되지 않음*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
| IoT                  | [Event Hubs – 캡처](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
| Consumption          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
| 생산성         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *지원되지 않음*                                                                                                                                                              | 계정 관리 *– 지금 사용 가능* <br><br>데이터 작업 *–*  *아직 사용할 수 없음*                                                                   |
|                      | [Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |
|                      | [Azure Storage 탐색기](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *지금 사용 가능*                                                                                                                                                              | *지금 사용 가능*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *아직 사용할 수 없음*                                                                                                                                                          | *아직 사용할 수 없음*                                                                                                                             |

### <a name="partner-ecosystem"></a>파트너 에코시스템

다음 표에서는 Data Lake Storage Gen1에서 작동하도록 수정된 타사 서비스 및 제품 목록을 보여 줍니다. 또한 현재 Data Lake Storage Gen2와 호환되는 서비스 및 제품 목록도 보여 줍니다.

| 영역            | 파트너  | 제품/서비스  | Data Lake Storage Gen1 가용성                                                                                                                                               | Data Lake Storage Gen2 가용성 - 공유 키 인증 사용                                                   | Data Lake Storage Gen2 가용성 - OAuth 사용                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| 분석 프레임워크 | Cloudera     | CDH                  | [링크](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *아직 사용할 수 없음*                                                                                                  | [링크](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [링크](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *지원되지 않음*                                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [링크](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *아직 사용할 수 없음*                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
|                     | Qubole       |                      | [링크](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *아직 사용할 수 없음*                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
| ETL                 | StreamSets   |                      | [링크](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *아직 사용할 수 없음*                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
|                     | Informatica  |                      | [링크](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *아직 사용할 수 없음*                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
|                     | Attunity     |                      | [링크](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *아직 사용할 수 없음*                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
|                     | Alteryx      |                      | [링크](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *아직 사용할 수 없음*                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
|                     | ImanisData   |                      | [링크](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *아직 사용할 수 없음*                                                                                                  | *아직 사용할 수 없음*                                                                                                  |
|                     | WANdisco     |                      | [링크](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [링크](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [링크](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>작동 정보

Data Lake Storage Gen1은 파이프라인을 운영하는 데 도움이 되는 특정 정보와 데이터를 다른 서비스에 푸시합니다. 다음 표에서는 해당 지원에 대한 Data Lake Storage Gen2의 가용성을 보여 줍니다.

| 데이터 형식                                                                                       | Data Lake Storage Gen1 가용성                                                                 | Data Lake Storage Gen2 가용성                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| 청구 데이터 - 대금 청구를 위해 상거래 팀에 전송된 후 고객에게 제공되는 측정값  | *지금 사용 가능*                                                                                             | *지금 사용 가능*                                                                                                                           |
| 활동 로그                                                                                          | [링크](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | 지원 티켓을 사용하여 특정 기간에 대한 일회성 로그 요청 - *지금 사용 가능*, Azure 모니터링 통합 – *아직 사용할 수 없음* |
| 진단 로그                                                                                        | [링크](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | 지원 티켓을 사용하여 특정 기간에 대한 일회성 로그 요청 - *지금 사용 가능*, Azure 모니터링 통합 – *아직 사용할 수 없음* |
| 메트릭                                                                                                | *지원되지 않음*                                                                                               | *지금 사용 가능 -* [링크](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>업그레이드 계획

이 섹션에서는 이 가이드의 [업그레이드 준비 평가](#assess-your-upgrade-readiness) 섹션을 검토하고 모든 종속성이 충족되었다고 가정합니다. Data Lake Storage Gen2에서 아직 사용할 수 없는 기능이 있는 경우 해당 해결 방법을 알고 있는 경우에만 진행하세요. 여기서는 파이프라인 업그레이드를 계획하는 방법에 대한 지침을 제공합니다. 실제로 업그레이드를 수행하는 방법은 이 가이드의 [업그레이드 수행](#performing-the-upgrade) 섹션에서 설명하고 있습니다.

### <a name="upgrade-strategy"></a>업그레이드 전략

업그레이드의 가장 중요한 부분은 전략을 결정하는 것입니다. 이 결정에 따라 사용할 수 있는 선택 항목이 결정됩니다.

다음 표에는 데이터베이스, Hadoop 클러스터 등을 마이그레이션하는 데 사용되어 잘 알려진 몇 가지 전략이 나와 있습니다. 이러한 지침에서 비슷한 전략을 채택하여 해당 컨텍스트에 적용합니다.

| 전략                   | 장점                                                                                  | 단점                                                           | 사용하는 경우                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 리프트 앤 시프트                 | 가장 간단합니다.                                                                                 | 데이터 복사, 작업 이동, 수신 및 송신 이동을 위한 가동 중지 시간이 필요합니다.                                             | 간단한 솔루션의 경우 동일한 Gen1 계정에 액세스하는 여러 솔루션이 없으므로 빠르게 제어되는 방식으로 함께 이동할 수 있습니다.                                                  |
| 한 번 복사 및 증분 복사 | 원본 시스템이 아직 활성 상태인 동안 백그라운드에서 복사를 수행하여 가동 중지 시간을 줄여줍니다. | 수신 및 송신 이동을 위해 가동 중지 시간이 필요합니다.                   | 복사할 데이터의 양이 많고, 리프트 앤 시프트와 관련된 가동 중지 시간이 허용되지 않습니다. 전송하기 전에 대상 시스템에서 중요한 프로덕션 데이터를 사용하여 테스트할 필요가 있습니다. |
| 병렬 도입              | 애플리케이션의 자체 결정에 따라 최소 가동 중지 시간을 통해 마이그레이션할 수 있습니다.           | 두 시스템 간에 양방향 동기화가 필요하므로 가장 정교합니다. | 복잡한 시나리오의 경우 Data Lake Storage Gen1에 기반하여 구축된 애플리케이션에서 한 번에 모두 중단할 수 없으므로 증분 방식으로 이동해야 합니다.                                                      |

각 전략에 관련된 단계에 대한 자세한 내용은 다음과 같습니다. 단계에서는 업그레이드와 관련된 구성 요소를 사용하여 수행할 작업을 나열합니다. 여기에는 전체 원본 시스템, 전체 대상 시스템, 원본 시스템의 수신 원본, 원본 시스템의 송신 대상 및 원본 시스템에서 실행되는 작업이 포함됩니다.

이러한 단계는 규범적인 내용이 아닙니다. 이는 각 전략에 대해 생각하는 방법에 대한 프레임워크를 설정하기 위한 것입니다. 각 전략이 구현됨에 따라 해당 사례 연구를 제공할 것입니다.

#### <a name="lift-and-shift"></a>리프트 앤 시프트

1. 원본 시스템, 즉 수신 원본, 작업, 송신 대상을 일시 중지합니다.
2. 모든 데이터를 원본 시스템에서 대상 시스템으로 복사합니다.
3. 모든 수신 원본을 대상 시스템으로 가리킵니다. 대상 시스템에서 송신 대상을 가리킵니다.
4. 모든 작업을 대상 시스템으로 이동, 수정, 실행합니다.
5. 원본 시스템을 해제합니다.

#### <a name="copy-once-and-copy-incremental"></a>한 번 복사 및 증분 복사

1. 데이터를 원본 시스템에서 대상 시스템으로 복사합니다.
2. 증분 데이터를 원본 시스템에서 대상 시스템으로 정기적으로 복사합니다.
3. 대상 시스템에서 송신 대상을 가리킵니다.
4. 모든 작업을 대상 시스템에서 이동, 수정, 실행합니다.
5. 사용자의 편의에 따라 증분 방식으로 수신 원본을 대상 시스템으로 가리킵니다.
6. 모든 수신 원본에서 대상 시스템만 가리킵니다.
    1. 증분 복사를 해제합니다.
    2. 원본 시스템을 해제합니다.

#### <a name="parallel-adoption"></a>병렬 도입

1. 대상 시스템을 설정합니다.
2. 원본 시스템과 대상 시스템 간에 양방향 복제를 설정합니다.
3. 수신 원본을 대상 시스템으로 가리킵니다.
4. 작업을 대상 시스템으로 증분 방식으로 이동, 수정, 실행합니다.
5. 증분 방식으로 송신 원본을 대상 시스템으로 가리킵니다.
6. 원래 수신 원본, 작업 및 송신 대상이 모두 대상 시스템에서 작동하면 원본 시스템을 해제합니다.

### <a name="data-upgrade"></a>데이터 업그레이드

업그레이드를 수행하는 데 사용하는 전체 전략(이 가이드의 [업그레이드 전략](#upgrade-strategy) 섹션에서 설명함)에 따라 데이터 업그레이드에 사용할 수 있는 도구가 결정됩니다. 아래에 나열된 도구는 현재 정보를 기반으로 하며 제안 사항입니다. 

#### <a name="tools-guidance"></a>도구 지침

| 전략                       | 도구                                                                                                             | 장점                                                                                                                             | 고려 사항                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **리프트 앤 시프트**                 | [Azure 데이터 팩터리](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | 관리형 클라우드 서비스                                                                                                                | 데이터만 복사합니다. ACL은 현재 복사할 수 없습니다.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | 잘 알려진 Hadoop 제공 도구 권한 - 이 도구를 사용하여 ACL을 복사할 수 있음                                                   | Data Lake Storage Gen1과 Gen2 모두에 동시에 연결할 수 있는 클러스터가 필요합니다.                                                                                                                                                                                   |
| **한 번 복사 및 증분 복사** | Azure Data Factory                                                                                                    | 관리형 클라우드 서비스                                                                                                                | ADF에서 증분 복사를 지원하려면 데이터를 시계열 방식으로 구성해야 합니다. 증분 복사 간의 가장 짧은 간격은 [15분](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger)입니다. 간격이 더 짧으면 ADF가 작동하지 않습니다. ACL은 현재 복사할 수 없습니다. |
| **병렬 도입**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | 일관된 복제 지원, Azure Data Lake Storage에 연결된 순수 Hadoop 환경을 사용하는 경우 양방향 복제 지원 | 순수 Hadoop 환경을 사용하지 않는 경우 복제가 지연될 수 있습니다.                                                                                                                                                                                                                                                  |

위의 데이터/메타데이터 복사 도구를 사용하지 않고 Data Lake Storage Gen1에서 Data Lake Storage Gen2로 업그레이드할 수 있는 타사 제품(예: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/))이 있습니다. 데이터 마이그레이션뿐만 아니라 워크로드 마이그레이션도 수행하는 "원스톱 쇼핑(one-stop shop)" 환경을 제공합니다. 에코시스템 외부에 있는 도구에 대해서는 대역 외 업그레이드를 수행해야 합니다.

#### <a name="considerations"></a>고려 사항

* 현재 지침에는 Gen1 계정 정보를 기반으로 한 자동 Gen2 계정 프로세스가 포함되어 있지 않으므로 업그레이드의 일부를 시작하기 전에 먼저 Data Lake Storage Gen2 계정을 수동으로 만들어야 합니다. [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) 및 [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account) 계정 만들기 프로세스를 비교합니다.

* Data Lake Storage Gen2는 최대 5TB의 파일만 지원합니다. Data Lake Storage Gen2로 업그레이드하려면 Data Lake Storage Gen1의 파일 크기를 5TB보다 작게 조정해야 합니다.

* ACL을 복사하지 않거나 ACL을 통해 복사하지 않으려면 대상의 ACL을 적절한 최상위 수준에서 수동으로 설정해야 합니다. 이 작업은 Storage 탐색기를 사용하여 수행할 수 있습니다. 이러한 ACL이 기본 ACL이므로 복사한 파일 및 폴더에서 해당 ACL을 상속합니다.

* Data Lake Storage Gen1에서 ACL을 설정할 수 있는 최상위 수준은 계정의 루트에 있습니다. 하지만 Data Lake 저장소 Gen2 가장 높은 수준 Acl을 설정할 수 있습니다는 루트 폴더에 파일 시스템에서 전체 계정이 아닙니다. 따라서 계정 수준에서 기본 ACL을 설정하려면 Data Lake Storage Gen2 계정의 모든 파일 시스템에서 해당 ACL을 복제해야 합니다.

* 파일 명명 제한 사항은 두 스토리지 시스템 간에 서로 다릅니다. 이러한 차이는 특히 Data Lake Storage Gen2에서 Data Lake Storage Gen1로 복사할 때 발생합니다. 이는 Data Lake Storage Gen1에 더 강제적인 제한 사항이 있기 때문입니다.

### <a name="application-upgrade"></a>애플리케이션 업그레이드

Data Lake Storage Gen1 또는 Data Lake Storage Gen2에서 애플리케이션을 빌드해야 하는 경우 먼저 적절한 프로그래밍 인터페이스를 선택해야 합니다. 해당 인터페이스에서 API를 호출할 때는 적절한 URI와 자격 증명을 제공해야 합니다. 이러한 세 가지 요소, 즉 API, URI 및 자격 증명 제공 방법은 Data Lake Storage Gen1과 Data Lake Storage Gen2 간에 서로 다릅니다. 따라서 애플리케이션 업그레이드의 일환으로 이러한 세 가지 구조를 적절히 매핑해야 합니다.

#### <a name="uri-changes"></a>URI 변경

URI의 접두사를 가진를 변환 하는 주요 작업을 여기 `adl://` URI의 있는에 `abfss://` 접두사입니다.

Data Lake Storage Gen1의 URI 체계는 [여기](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store)서 자세히 설명되지만, 대체로 *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>입니다.*

Data Lake 저장소 Gen2 파일에 액세스 하기 위한 URI 체계는 설명 [여기](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) 세부 정보, 하지만 대체로에서는 `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>`합니다.

기존 애플리케이션을 살펴보고 Data Lake Storage Gen2를 가리키도록 URI를 적절히 변경했는지 확인해야 합니다. 또한 적절한 자격 증명도 추가해야 합니다. 마지막으로, 원래 애플리케이션을 사용 중지하고 새 애플리케이션으로 교체하는 방법은 전체 업그레이드 전략과 긴밀하게 연계되어야 합니다.

#### <a name="custom-applications"></a>사용자 지정 애플리케이션

애플리케이션이 Data Lake Storage Gen1에서 사용하는 인터페이스에 따라 Data Lake Storage Gen2에 맞게 해당 애플리케이션을 수정해야 합니다.

##### <a name="rest-apis"></a>REST API

애플리케이션에서 Data Lake Storage REST API를 사용하는 경우 Data Lake Storage Gen2 REST API를 사용하도록 애플리케이션을 수정해야 합니다. 링크는 *프로그래밍 인터페이스* 섹션에 나와 있습니다.

##### <a name="sdks"></a>SDK

*업그레이드 준비 평가* 섹션에서 설명한 대로 SDK는 현재 사용할 수 없습니다. Data Lake Storage Gen2로 애플리케이션을 이동하려는 경우에는 지원되는 SDK를 사용할 수 있을 때까지 기다리는 것이 좋습니다.

##### <a name="powershell"></a>PowerShell

업그레이드 준비 평가 섹션에서 설명한 대로 PowerShell 지원은 현재 데이터 평면에서 사용할 수 없습니다.

Data Lake Storage Gen2에서 관리 평면 commandlet을 적절한 명령으로 바꿀 수 있습니다. 링크는 *프로그래밍 인터페이스* 섹션에 나와 있습니다.

##### <a name="cli"></a>CLI

*업그레이드 준비 평가* 섹션에서 설명한 대로 CLI 지원은 현재 데이터 평면에서 사용할 수 없습니다.

Data Lake Storage Gen2에서 관리 평면 명령을 적절한 명령으로 바꿀 수 있습니다. 링크는 *프로그래밍 인터페이스* 섹션에 나와 있습니다.

### <a name="analytics-frameworks-upgrade"></a>분석 프레임워크 업그레이드

애플리케이션에서 명시적 파일 및 폴더 경로와 같은 저장소의 정보에 대한 메타데이터를 만드는 경우 저장소 데이터/메타데이터를 업그레이드한 후에 추가 작업을 수행해야 합니다. 이는 일반적으로 저장소 데이터에 카탈로그 데이터를 만드는 Azure HDInsight, Databricks 등과 같은 분석 프레임워크에서 특히 그렇습니다.

분석 프레임워크는 Data Lake Storage Gen1 및 Gen2와 같은 원격 스토리지에 저장된 데이터 및 메타데이터를 사용합니다. 따라서 이론적으로는 엔진이 사용 후 삭제될 수 있으며, 저장된 데이터에 대한 작업을 실행해야 하는 경우에만 작동할 수 있습니다.

그러나 성능을 최적화하기 위해 분석 프레임워크는 원격 저장소에 저장된 파일 및 폴더에 대한 명시적 참조를 만든 다음, 이를 보관할 캐시를 만들 수 있습니다. 원격 데이터의 URI가 변경되면, 예를 들어 이전에 Data Lake Storage Gen1에 데이터를 저장하고 있던 클러스터에서 이제는 Data Lake Storage Gen2에 저장하려는 경우 동일한 복사된 콘텐츠에 대한 URI가 달라질 수 있습니다. 따라서 데이터와 메타데이터를 업그레이드한 후에는 이러한 엔진의 캐시도 업데이트하거나 다시 초기화해야 합니다.

계획 프로세스의 일환으로, 애플리케이션을 식별하고, 현재 Data Lake Storage Gen2에 저장된 데이터를 가리키도록 메타데이터 정보를 다시 초기화할 수 있는 방법을 파악해야 합니다. 일반적으로 채택되는 분석 프레임워크의 업그레이드 단계를 지원하기 위한 지침은 다음과 같습니다.

#### <a name="azure-databricks"></a>Azure Databricks

선택한 업그레이드 전략에 따라 단계가 달라집니다. 이 섹션에서는 "리프트 앤 시프트" 전략을 선택했다고 가정합니다. 또한 Data Lake Storage Gen1 계정의 데이터에 액세스하는 데 사용한 기존 Databricks 작업 영역에서는 Data Lake Storage Gen2 계정에 복사된 데이터를 사용해야 합니다.

먼저 Gen2 계정을 만든 다음, 적절한 도구를 사용하여 데이터와 메타데이터를 Gen1에서 Gen2로 복사했는지 확인합니다. 이러한 도구는 이 가이드의 [데이터 업그레이드](#data-upgrade) 섹션에 나와 있습니다.

그런 다음, Data Lake Storage Gen2를 지원하는 Databricks 런타임 5.1 이상을 사용할 수 있도록 기존 Databricks 클러스터를 [업그레이드](https://docs.azuredatabricks.net/user-guide/clusters/index.html)합니다.

이후 단계는 기존 Databricks 작업 영역에서 Data Lake Storage Gen1 계정의 데이터에 액세스하는 방법을 기반으로 합니다. 이 작업은 adl:// URI를 Notebook에서 [직접](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) 호출하거나 [탑재 지점](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)을 통해 호출하여 수행할 수 있습니다.

전체 adl:// URI를 제공하여 Notebook에서 직접 액세스하는 경우 각 Notebook을 살펴보고 해당 Data Lake Storage Gen2 URI에 액세스하도록 구성을 변경해야 합니다.

향후에는 Data Lake Storage Gen2 계정을 가리키도록 재구성해야 합니다. 그러면 더 이상 변경할 필요가 없으며, Notebook은 이전과 같이 작동할 수 있습니다.

다른 업그레이드 전략을 사용하는 경우 요구 사항을 충족시키기 위해 위의 단계를 변경할 수 있습니다.

### <a name="azure-ecosystem-upgrade"></a>Azure 에코시스템 업그레이드

이 가이드의 [Azure 에코시스템](#azure-ecosystem) 섹션에 나와 있는 각 도구와 서비스는 Data Lake Storage Gen2에서 작동하도록 구성해야 합니다.

먼저 Data Lake Storage Gen2와 통합할 수 있는지 확인합니다.

그런 다음, 위에서 명시한 요소(예: URI 및 자격 증명)를 변경해야 합니다. Data Lake Storage Gen1에서 작동하는 기존 인스턴스를 수정하거나 Data Lake Storage Gen2에서 작동하는 새 인스턴스를 만들 수 있습니다.

### <a name="partner-ecosystem-upgrade"></a>파트너 에코시스템 업그레이드

구성 요소와 도구를 제공하는 파트너와 협력하여 Data Lake Storage Gen2에서 작동할 수 있도록 합니다. 

## <a name="performing-the-upgrade"></a>업그레이드 수행

### <a name="pre-upgrade"></a>업그레이드 전

이 프로세스의 일환으로, 이 가이드의 *업그레이드 준비 평가* 섹션 및 [업그레이드 계획](#planning-for-an-upgrade) 섹션을 수행하여 필요한 모든 정보를 얻었고 요구 사항에 맞는 계획을 만들었습니다. 이 단계에서는 테스트 작업을 수행하게 됩니다.

### <a name="in-upgrade"></a>업그레이드 중

선택한 전략과 솔루션의 복잡성에 따라 이 단계는 증분 방식으로 Data Lake Storage Gen2로 이동할 때까지 기다리는 여러 워크로드가 있는 간단하거나 확장된 단계일 수 있습니다. 이는 업그레이드에서 가장 중요한 부분입니다.

### <a name="post-upgrade"></a>업그레이드 후

전환 작업이 완료되면 마지막 단계로 철저히 확인해야 합니다. 이 포함 하지만에 제한 되지는지 않습니다 확인 Acl 올바르게 설정 되었는지, 확인 엔드-투-엔드 파이프라인 올바르게 작동 하는 등 데이터를 확인 하는 복사 된 안정적으로. 확인이 완료되면 이제 이전 파이프라인을 해제하고, 원본 Data Lake Storage Gen1 계정을 삭제하고, Data Lake Storage Gen2 기반 솔루션을 최대한 활용할 수 있습니다.

## <a name="conclusion"></a>결론

이 문서에 제공된 지침은 Data Lake Storage Gen2를 사용하기 위해 솔루션을 업그레이드하는 데 도움이 됩니다. 

추가 질문이 있거나 의견이 있으면 아래 피드백에서 의견을 제출하거나 [Azure 피드백 포럼](https://feedback.azure.com/forums/327234-data-lake)에서 피드백을 제출하세요.
