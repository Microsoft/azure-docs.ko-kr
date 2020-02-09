---
title: Azure Cosmos DB의 기본 제공 노트북 명령 및 기능 사용 (미리 보기)
description: 기본 제공 명령 및 기능을 사용 하 여 Azure Cosmos DB의 기본 제공 노트북을 사용 하는 일반적인 작업을 수행 하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513402"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>Azure Cosmos DB의 기본 제공 노트북 명령 및 기능 사용 (미리 보기)

Azure Cosmos DB의 기본 제공 Jupyter 노트북을 사용 하 여 Azure Portal에서 데이터를 분석 하 고 시각화할 수 있습니다. 이 문서에서는 기본 제공 Notebook 명령 및 기능을 사용하여 일반적인 작업을 수행하는 방법을 설명합니다.

## <a name="install-a-new-package"></a>새 패키지 설치
Azure Cosmos 계정에 대해 노트북 지원을 사용 하도록 설정한 후 새 노트북을 열고 패키지를 설치할 수 있습니다.

새 코드 셀에서 다음 코드를 삽입 하 고 실행 하 여 ``PackageToBeInstalled``를 원하는 Python 패키지로 바꿉니다.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
이 패키지는 Azure Cosmos 계정 작업 영역에 있는 모든 노트북에서 사용할 수 있습니다. 

> [!TIP]
> 노트북에 사용자 지정 패키지가 필요한 경우 [작업 영역을 다시 설정](#reset-notebooks-workspace)하는 경우 패키지가 제거 되므로 노트북에 셀을 추가 하 여 패키지를 설치 하는 것이 좋습니다.  

## <a name="run-a-sql-query"></a>SQL 쿼리 실행

``%%sql`` magic 명령을 사용 하 여 계정의 모든 컨테이너에 대해 [SQL 쿼리](sql-query-getting-started.md) 를 실행할 수 있습니다. 다음 구문을 사용 합니다.

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- ``{database_id}`` 및 ``{container_id}``을 Cosmos 계정의 데이터베이스 및 컨테이너 이름으로 바꿉니다. ``--database`` 및 ``--container`` 인수를 제공 하지 않으면 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에서 쿼리가 실행 됩니다.
- Azure Cosmos DB에서 유효한 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다.

예: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
셀에서 ```%%sql?```를 실행 하 여 노트북의 sql 매직 명령에 대한 도움말 설명서를 확인 합니다.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>SQL 쿼리를 실행 하 고 Pandas 데이터 프레임에 출력 합니다.

``%%sql`` 쿼리의 결과를 [Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)에 출력할 수 있습니다. 다음 구문을 사용 합니다. 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- ``{database_id}`` 및 ``{container_id}``을 Cosmos 계정의 데이터베이스 및 컨테이너 이름으로 바꿉니다. ``--database`` 및 ``--container`` 인수를 제공 하지 않으면 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에서 쿼리가 실행 됩니다.
- ``{outputDataFrameVar}``를 결과를 포함 하는 데이터 프레임 변수 이름으로 바꿉니다.
- Azure Cosmos DB에서 유효한 SQL 쿼리를 실행할 수 있습니다. 쿼리 텍스트는 새 줄에 있어야 합니다. 

예:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>컨테이너에 JSON 항목 업로드
``%%upload`` magic 명령을 사용 하 여 JSON 파일에서 지정 된 Azure Cosmos 컨테이너로 데이터를 업로드할 수 있습니다. 다음 명령을 사용 하 여 항목을 업로드 합니다.

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}`` 및 ``{container_id}``를 Azure Cosmos 계정에 있는 데이터베이스 및 컨테이너의 이름으로 바꿉니다. ``--database`` 및 ``--container`` 인수를 제공 하지 않으면 [기본 데이터베이스 및 컨테이너](#set-default-database-for-queries)에서 쿼리가 실행 됩니다.
- ``{url_location_of_file}``을 JSON 파일의 위치로 바꿉니다. 파일은 유효한 JSON 개체의 배열이 며 공용 인터넷을 통해 액세스할 수 있어야 합니다.

예:

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
출력 통계를 사용 하 여 항목을 업로드 하는 데 사용 되는 유효 r u/s를 계산할 수 있습니다. 예를 들어 38 초를 초과 하 여 25000 RUs를 사용 하는 경우 적용 되는 것은 25000 RUs/38 seconds = 658 r u/초입니다.

## <a name="set-default-database-for-queries"></a>쿼리에 대한 기본 데이터베이스 설정
명령에서 노트북에 사용할 기본 데이터베이스 ```%%sql``` 설정할 수 있습니다. ```{database_id}```를 해당 데이터베이스의 이름으로 바꿉니다.

```bash
%database {database_id}
```
셀에서 ```%database?```를 실행 하 여 노트북의 설명서를 확인 합니다.

## <a name="set-default-container-for-queries"></a>쿼리에 대한 기본 컨테이너 설정
명령에서 노트북에 사용할 기본 컨테이너 ```%%sql``` 설정할 수 있습니다. ```{container_id}```를 컨테이너의 이름으로 바꿉니다.

```bash
%container {container_id}
```
셀에서 ```%container?```를 실행 하 여 노트북의 설명서를 확인 합니다.

## <a name="use-built-in-nteract-data-explorer"></a>기본 제공 nteract 데이터 탐색기 사용
기본 제공 [nteract 데이터 탐색기](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) 를 사용 하 여 데이터 프레임를 필터링 하 고 시각화할 수 있습니다. 이 기능을 사용 하도록 설정 하려면 ``pd.options.display.html.table_schema`` 옵션을 ``True``로 설정 하 고 원하는 값으로 ``pd.options.display.max_rows`` 합니다. 모든 결과를 표시 하도록 ``pd.options.display.max_rows``를 ``None``로 설정할 수 있습니다.

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract 데이터 탐색기](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>기본 제공 Python SDK 사용
[SQL API 용 Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 버전 4가 설치 되어 Azure Cosmos 계정에 대한 노트북 환경에 포함 되어 있습니다.

기본 제공 ``cosmos_client`` 인스턴스를 사용 하 여 모든 SDK 작업을 실행할 수 있습니다. 

예:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
[PYTHON SDK 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)을 참조 하세요. 

> [!IMPORTANT]
> 기본 제공 Python SDK는 SQL (Core) API 계정에 대해서만 지원 됩니다. 다른 Api의 경우 API에 해당 하는 [관련 Python 드라이버를 설치](#install-a-new-package) 해야 합니다. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>``cosmos_client``의 사용자 지정 인스턴스를 만듭니다.
더 많은 유연성을 위해 다음과 같은 작업을 위해 ``cosmos_client``의 사용자 지정 인스턴스를 만들 수 있습니다.

- [연결 정책](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview) 사용자 지정
- 사용자가 아닌 다른 Azure Cosmos 계정에 대해 작업을 실행 합니다.

[환경 변수](#access-the-account-endpoint-and-primary-key-env-variables)를 통해 현재 계정의 연결 문자열 및 기본 키에 액세스할 수 있습니다. 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>계정 엔드포인트 및 기본 키 env 변수에 액세스
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` 및 ``COSMOS_KEY`` 환경 변수는 SQL API에만 적용 됩니다. 다른 Api의 경우 Azure Cosmos 계정의 **연결 문자열** 또는 **키** 블레이드에서 엔드포인트 및 키를 찾습니다.  

## <a name="reset-notebooks-workspace"></a>전자 필기장 작업 영역 다시 설정
전자 필기장 작업 영역을 기본 설정으로 다시 설정 하려면 명령 모음에서 **작업 영역 다시 설정** 을 선택 합니다. 그러면 설치 된 모든 사용자 지정 패키지가 제거 되 고 Jupyter 서버가 다시 시작 됩니다. 노트북, 파일 및 Azure Cosmos 리소스는 영향을 받지 않습니다.  

![전자 필기장 작업 영역 다시 설정](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>다음 단계

- [Jupyter 노트북 Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) 의 이점에 대해 알아봅니다.
- [SQL API 용 PYTHON SDK Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 에 대해 알아보기
