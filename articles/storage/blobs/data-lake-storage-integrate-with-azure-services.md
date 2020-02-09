---
title: Azure 서비스와 Azure Data Lake Storage 통합 | Microsoft Docs
description: Azure Data Lake Storage Gen2와 통합 하는 Azure 서비스에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: 7f43f69ebdac05b8f99739ea6b51453671b9a70a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024554"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Azure 서비스와 Azure Data Lake Storage 통합

Azure 서비스를 사용 하 여 데이터를 수집 하 고 분석을 수행 하 고 시각적 표현을 만들 수 있습니다. 이 문서는 지원 되는 Azure 서비스 목록을 제공 하 고 이러한 서비스를 Azure Data Lake Storage Gen2 사용 하는 데 도움이 되는 문서에 대 한 링크를 제공 합니다.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2를 지 원하는 Azure 서비스

다음 표에서는 Azure Data Lake Storage Gen2을 지 원하는 Azure 서비스를 나열 합니다.

| Azure 서비스 |  관련 문서 |
|---------------|-------------------|
|Azure Data Factory | [Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Azure Databricks와 함께 사용](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [빠른 시작: Azure Databricks를 사용 하 여 Azure Data Lake Storage Gen2에서 데이터 분석](data-lake-storage-quickstart-create-databricks-account.md) <br>[자습서: Azure Databricks을 사용 하 여 데이터 추출, 변환 및 로드](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[자습서: Spark를 사용 하 여 Azure Databricks으로 데이터 Data Lake Storage Gen2 액세스](data-lake-storage-use-databricks-spark.md) |
|Azure Event Hubs 캡처| [Azure Blob Storage 또는 Azure Data Lake Storage에서 Azure Event Hubs를 통해 이벤트를 캡처합니다.](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [개요-Azure Logic Apps 이란?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Azure storage 서비스의 데이터에 액세스](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Cognitive Search | [문서 Azure Data Lake Storage Gen2 인덱스 및 검색 (미리 보기)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Stream Analytics| [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Azure Data Lake Gen2로 송신](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Azure Data Box를 사용 하 여 온-프레미스 HDFS 저장소에서 Azure Storage 데이터를 마이그레이션합니다.](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Data Lake Storage Gen2에서 HDFS CLI 사용](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[자습서: Azure HDInsight에서 Apache Hive를 사용 하 여 데이터 추출, 변환 및 로드](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|IoT Hub | [IoT Hub 메시지 라우팅을 사용 하 여 다른 엔드포인트으로 장치-클라우드 메시지 보내기](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Power BI를 사용 하 여 Data Lake Storage Gen2 데이터 분석](https://docs.microsoft.com/power-query/connectors/datalakestorage) |
|SQL Data Warehouse | [Azure SQL Data Warehouse와 함께 사용](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SSIS(SQL Server Integration Services) | [Azure Storage 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>다음 단계

- Data lake의 데이터를 처리 하는 데 사용할 수 있는 도구에 대해 자세히 알아보세요. [빅 데이터 요구 사항은 Azure Data Lake Storage Gen2 사용을](data-lake-storage-data-scenarios.md)참조 하세요.

- Data Lake Storage Gen2 및 시작 하는 방법에 대해 자세히 알아보세요. [Azure Data Lake Storage Gen2 소개를](data-lake-storage-introduction.md) 참조 하세요.
