---
title: Azure의 Jupyter Notebook에서 데이터 리소스 액세스
description: Jupyter Notebook에서 파일, REST API, 데이터베이스 및 다른 Azure Storage 리소스에 액세스하는 방법입니다.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 14a4191612a5d42836ae4be3ff902ca47a6b06d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634349"
---
# <a name="access-cloud-data-in-a-notebook"></a>Notebook에서 클라우드 데이터 액세스

Jupyter Notebook에서 흥미로운 작업을 하려면 데이터가 필요합니다. 데이터는 실제로 Notebook의 핵심 요소입니다.

Notebook 내에서 `curl`과 같은 명령을 사용하여 파일을 직접 다운로드할 수 있지만 [데이터 파일을 프로젝트로 가져올](work-with-project-data-files.md) 수 있습니다. 그러나 REST API, 관계형 데이터베이스 및 Azure 테이블과 같은 클라우드 스토리지와 같은 파일이 아닌 원본에서 사용할 수 있는 훨씬 더 광범위한 데이터를 사용해야 합니다.

이 문서에는 간단히 이러한 다양한 옵션을 설명합니다. 데이터 액세스는 작업에서 가장 잘 표시되므로 [Azure Notebooks 샘플 - 데이터 액세스](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb)에서 실행 가능한 코드를 찾을 수 있습니다.

## <a name="rest-apis"></a>REST API

일반적으로 인터넷에서 사용 가능한 방대한 양의 데이터는 파일이 아닌 REST API를 통해 액세스합니다. 다행스럽게도 Notebook 셀에서 원하는 모든 코드를 포함할 수 있으므로 코드를 사용해서 요청을 보내거나 JSON 데이터를 받을 수 있습니다. 해당 JSON을 Pandas 데이터 프레임 같이 사용하려는 형식에 관계 없이 변환할 수 있습니다.

REST API를 사용하여 데이터에 액세스하려면 다른 애플리케이션에서 사용하는 Notebook의 코드 셀에 동일한 코드를 사용합니다. 요청 라이브러리를 사용하는 일반적인 구조체는 다음과 같습니다.

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL 데이터베이스

pyodbc 또는 pymssql 라이브러리 지원으로 SQL Server 데이터베이스를 액세스할 수 있습니다.

[Python을 사용하여 Azure SQL 데이터베이스 쿼리](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python)는 AdventureWorks 데이터를 포함하는 데이터베이스를 만드는 방법에 대한 지침을 제공하고 해당 데이터를 쿼리하는 방법을 보여 줍니다. 동일한 코드는 이 문서에 대한 샘플 Notebook에 표시됩니다.

## <a name="azure-storage"></a>Azure Storage

Azure Storage는 데이터 형식 및 액세스해야 하는 방법에 따라 다양한 형식의 비관계형 스토리지를 제공합니다.

- Table Storage: 수집된 센서 로그, 진단 로그 등과 같은 표 형식 데이터에 대한 저렴한 대용량 스토리지를 제공합니다.
- Blob Storage: 모든 종류의 데이터에 대한 파일 형식의 스토리지를 제공합니다.

공유 액세스 서명을 사용하여 Blob에 대한 읽기 전용 액세스를 허용하는 방법을 포함하여 테이블 및 Blob을 사용한 작업을 보여줍니다.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB는 JSON 문서에 대해 완벽하게 인덱싱된 NoSQL 스토리지를 제공합니다.) 다음 문서에서는 Python에서 Cosmos DB를 사용하는 다양한 방법을 제공합니다.

- [Python 사용하여 SQL API 앱 빌드](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Azure Cosmos DB의 MongoDB API를 사용하여 Flask 앱 빌드](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Python 및 Gremlin API를 사용하여 그래프 데이터베이스 만들기](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Python 및 Azure Cosmos DB를 사용하여 Cassandra 앱 빌드](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Python 및 Azure Cosmos DB를 사용하여 Table API 앱 빌드](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Cosmos DB로 작업할 때 [Azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) 라이브러리를 사용할 수 있습니다.

## <a name="other-azure-databases"></a>다른 Azure 데이터베이스

Azure는 사용할 수 있는 많은 다른 데이터베이스 형식을 제공 합니다. 아래 문서에서는 Python에서 해당 데이터베이스에 액세스하기 위한 지침을 제공합니다.

- [Azure Database for PostgreSQL Python을 사용하여 데이터 연결 및 쿼리](https://docs.microsoft.com/azure/postgresql/connect-python)
- [빠른 시작: Python으로 Azure Redis Cache사용](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: Python을 사용하여 데이터 연결 및 쿼리](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure 데이터 팩터리](https://azure.microsoft.com/services/data-factory/)
  - [Azure Data Factory 복사 마법사](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>다음 단계

- [방법: 프로젝트 데이터 파일을 사용하여 작업](work-with-project-data-files.md)
