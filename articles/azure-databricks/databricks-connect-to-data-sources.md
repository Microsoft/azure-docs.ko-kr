---
title: 'Azure Databricks에서 다른 데이터 원본에 연결 '
description: Azure SQL Data Warehouse에서 Azure SQL Database, Azure Data Lake Store, blob storage, Cosmos DB, Event Hubs 및 Azure Databricks에 연결 하는 방법에 대해 알아봅니다.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129393"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Azure Databricks에서 데이터 원본에 연결

이 문서에서는 Azure Databricks에 연결할 수 있는 Azure의 다른 모든 데이터 원본에 대한 링크를 제공합니다. 이러한 링크의 예제를 따라 Azure 데이터 원본(예: Azure Blob Storage, Azure Event Hubs 등)의 데이터를 Azure Databricks 클러스터로 추출하고 데이터에 대한 분석 작업을 실행합니다. 

## <a name="prerequisites"></a>선행 조건

* Azure Databricks 작업 영역 및 Spark 클러스터가 있어야 합니다. [Azure Databricks 시작](quickstart-create-databricks-workspace-portal.md)의 지침을 따르세요.

## <a name="data-sources-for-azure-databricks"></a>Azure Databricks의 데이터 원본

다음 목록은 Azure Databricks에 사용할 수 있는 Azure의 데이터 원본을 보여 줍니다. Azure Databricks에 사용할 수 있는 데이터 원본의 전체 목록은 [Azure Databricks의 데이터 원본](/azure/databricks/data/data-sources/index)을 참조하세요.

- [Azure SQL 데이터베이스](/azure/databricks/data/data-sources/sql-databases)

    이 링크는 JDBC를 사용하여 SQL 데이터베이스에 연결하기 위한 DataFrame API 및 JDBC 인터페이스를 통해 읽기의 병렬 처리를 제어하는 방법을 제공합니다. 이 항목에서는 Scala API를 사용하여 자세한 예제를 제공하며, 끝에 축약된 Python 및 Spark SQL 예제가 나와 있습니다.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    이 링크는 Azure Active Directory 서비스 주체를 사용 하 여 Azure Data Lake Storage 인증 하는 방법에 대한 예제를 제공 합니다. 또한 Azure Databricks에서 Azure Data Lake Storage 데이터에 액세스 하는 방법에 대한 지침을 제공 합니다.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    이 링크는 지정된 컨테이너의 SAS 또는 액세스 키를 사용하여 Azure Databricks에서 Azure Blob Storage에 직접 액세스하는 방법의 예제를 제공합니다. 또한 RDD API를 사용하여 Azure Databricks에서 Azure Blob Storage에 액세스하는 방법에 대한 정보를 제공합니다.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    이 링크는 Azure Databricks에서 [Azure Cosmos DB Spark 커넥터](https://github.com/Azure/azure-cosmosdb-spark)를 사용하여 Azure Cosmos DB의 데이터에 액세스하는 방법에 대한 지침을 제공합니다.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    이 링크는 Azure Databricks에서 [Azure Event Hubs Spark 커넥터](https://github.com/Azure/azure-event-hubs-spark)를 사용하여 Azure Event Hubs의 데이터에 액세스하는 방법에 대한 지침을 제공합니다.

- [Azure SQL Data Warehouse](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    이 링크는 Azure SQL Data Warehouse 커넥터를 사용하여 Azure Databricks에서 연결하는 방법에 대한 지침을 제공합니다.
    

## <a name="next-steps"></a>다음 단계

Azure Databricks로 데이터를 가져올 수 있는 원본에 대해 알아보려면 [Azure Databricks의 데이터 원본](/azure/databricks/data/data-sources/index)을 참조하세요.


