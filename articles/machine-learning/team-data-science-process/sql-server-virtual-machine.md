---
title: SQL Server 가상 머신에서 데이터 탐색 - Team Data Science Process
description: Azure의 SQL Server 가상 머신에서 데이터를 탐색하고 기능 생성
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7b3b4e0886f561cc66e2c02e4ea354c86b34453c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61044487"
---
# <a name="heading"></a>Azure의 SQL Server 가상 컴퓨터에서 데이터 처리
이 문서에서는 Azure의 SQL Server VM에 저장된 데이터를 탐색하고 데이터에 대한 기능을 생성하는 방법에 대해 알아봅니다. 이렇게 하려면 SQL을 사용하여 데이터 랭글링을 수행하거나 Python과 같은 프로그래밍 언어를 사용합니다.

> [!NOTE]
> 이 문서의 샘플 SQL 문에서는 데이터가 SQL Server에 있는 것으로 가정합니다. 그렇지 않은 경우 데이터를 SQL Server로 이동하는 방법은 클라우드 데이터 과학 프로세스 맵을 참조하세요.
> 
> 

## <a name="SQL"></a>SQL 사용
이 섹션에서는 SQL을 사용하여 다음과 같은 데이터 랭글링 작업을 수행하는 방법에 대해 설명합니다.

1. [데이터 탐색](#sql-dataexploration)
2. [기능 생성](#sql-featuregen)

### <a name="sql-dataexploration"></a>데이터 탐색
SQL Server에서 데이터 저장소를 탐색하는 데 사용할 수 있는 몇 가지 샘플 SQL 스크립트는 다음과 같습니다.

> [!NOTE]
> 실용적인 예제에는 [NYC Taxi 데이터 세트](https://www.andresmh.com/nyctaxitrips/)를 사용할 수 있으며, 종단 간 연습에 [IPython Notebook 및 SQL Server를 사용한 NYC 데이터 랭글링](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)이라는 IPNB를 참조할 수 있습니다.
> 
> 

1. 일별 관찰 수 가져오기
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. 범주 열의 수준 가져오기
   
    `select  distinct <column_name> from <databasename>`
3. 두 범주 열 조합의 수준 수 가져오기 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. 숫자 열의 분포 가져오기
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>기능 생성
이 섹션에서는 SQL을 사용하여 기능을 생성하는 방법에 대해 설명합니다.  

1. [개수 기반 기능 생성](#sql-countfeature)
2. [범주화 기능 생성](#sql-binningfeature)
3. [단일 열에서 기능 롤아웃](#sql-featurerollout)

> [!NOTE]
> 추가 기능을 생성한 후 이를 기존 테이블에 열로 추가하거나, 추가 기능 및 기본 키를 사용하여 새 테이블을 만들어 원래 테이블에 조인할 수 있습니다. 
> 
> 

### <a name="sql-countfeature"></a>개수 기반 기능 생성
다음의 예제에서는 개수 기능을 생성하는 두 가지 방법을 보여 줍니다. 첫 번째 방법에서는 조건부 합계를 사용하고, 두 번째 방법에서는 'where' 절을 사용합니다. 그런 다음 원래 데이터와 함께 개수 기능을 유지하도록 원래 테이블에 조인할 수 있습니다(기본 키 열 사용).

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>범주화 기능 생성
다음 예제에서는 기능으로 사용할 수 있는 숫자 열을 범주화하여(다섯 개의 bin 사용) 범주화된 기능을 생성하는 방법을 보여 줍니다.

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>단일 열에서 기능 롤아웃
이 섹션에서는 테이블의 단일 열을 롤아웃하여 추가 기능을 생성하는 방법을 보여 줍니다. 이 예제에서는 기능을 생성하려는 테이블에 위도 또는 경도 열이 있는 것으로 가정합니다.

다음은 위도/경도 위치 데이터에 대한 간략한 기초 정보입니다(stackoverflow [위도 및 경도의 정확도를 측정하는 방법](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)에서 발췌). 위치 필드를 기능화하기 전에 이 정보를 이해하는 것이 좋습니다.

* 부호는 지구에서 현재 위치의 방위(북쪽, 남쪽, 동쪽 또는 서쪽)를 알려 줍니다.
* 0이 아닌 100자리수는 위도가 아니라 경도를 사용하고 있음을 알려 줍니다.
* 10자리수는 약 1,000km까지의 위치를 제공합니다. 현재 위치의 대륙 또는 대양에 대한 유용한 정보를 제공합니다.
* 단위 자리수(하나의 도 단위)는 최대 111km(60해리, 약 69마일)까지의 위치를 제공합니다. 이는 현재 위치의 주 또는 국가를 대략적으로 알려 줍니다.
* 첫 번째 소수 자릿수는 11.1km까지 적용되며, 하나의 대도시를 인접한 대도시와 구분할 수 있습니다.
* 두 번째 소수 자릿수는 1.1km까지 적용되며, 하나의 마을을 다음 마을과 구분할 수 있습니다.
* 세 번째 소수 자릿수는 110m까지 적용되며, 대규모 농경지 또는 기업 부지를 식별할 수 있습니다.
* 네 번째 소수 자릿수는 11m까지 적용되며, 하나의 필지를 식별할 수 있습니다. 이는 간섭 없이 보정되지 않은 GPS 장치의 일반적인 정확도에 해당됩니다.
* 다섯 번째 소수 자릿수는 1.1m까지 적용되며, 수목을 서로 구분할 수 있습니다. 상용 GPS 장치에서는 미분 보정을 통해서만 이 수준의 정확도를 실현할 수 있습니다.
* 여섯 번째 소수 자릿수는 0.11m까지 적용되며, 상세 구조물 배치, 조경 설계, 도로 건설 등에 사용될 수 있습니다. 빙하 및 강의 이동을 추적하는 데 매우 적합합니다. 미분 보정된 GPS와 같은 GPS로 세밀히 측정하여 이를 수행할 수 있습니다.

위치 정보는 다음과 같이 지역, 위치 및 도시 정보를 구분하여 기능화할 수 있습니다. 또한 Bing Maps API( [지점별 위치 찾기](https://msdn.microsoft.com/library/ff701710.aspx) )와 같은 REST 끝점을 호출하여 지역/구역 정보를 가져올 수 있습니다.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

이러한 위치 기반 기능을 사용하여 앞서 설명한 대로 추가 개수 기능을 생성할 수도 있습니다. 

> [!TIP]
> 선택한 언어를 사용하여 프로그래밍 방식으로 레코드를 삽입할 수 있습니다. 쓰기 효율성을 개선하기 위해 청크에 데이터를 삽입해야 할 수도 있습니다. pyodbc를 사용하여 이 작업을 수행하는 방법에 대한 예제는 [Python을 사용하여 SQL Server에 액세스하는 HelloWorld 샘플](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)을 참조하세요. 또 다른 방법은 [BCP 유틸리티](https://msdn.microsoft.com/library/ms162802.aspx)를 사용하여 데이터베이스에 데이터를 삽입하는 것입니다.
> 
> 

### <a name="sql-aml"></a>Azure 기계 학습에 연결
새로 생성한 기능을 기존 테이블에 열로 추가하거나, 새 테이블에 저장하여 기계 학습을 위해 원래 테이블과 조인할 수 있습니다. Azure Machine Learning에서는 아래 표시된 대로 [데이터 가져오기][import-data] 모듈을 사용하여 기능을 생성하거나 액세스(이미 만든 경우)할 수 있습니다.

![azureml 판독기][1] 

## <a name="python"></a>Python과 같은 프로그래밍 언어 사용
데이터가 SQL Server에 있는 경우 Python을 사용하여 데이터를 탐색하고 기능을 생성하는 작업은 [데이터 과학 환경에서 Azure Blob 데이터 처리](data-blob.md)에 설명된 대로 Python을 사용하여 Azure Blob의 데이터를 처리하는 것과 유사합니다. 데이터베이스에서 pandas 데이터 프레임으로 데이터를 로드해야 하며, 그런 다음 데이터를 추가로 처리할 수 있습니다. 데이터베이스에 연결하여 데이터 프레임으로 데이터를 로드하는 프로세스는 이 섹션에 설명되어 있습니다.

다음 연결 문자열 형식은 pyodbc를 사용(servername, dbname, username 및 password를 특정 값으로 대체)하여 Python에서 SQL Server 데이터베이스 연결하는 데 사용될 수 있습니다.

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python의 [Pandas 라이브러리](https://pandas.pydata.org/) 에서는 Python 프로그래밍용 데이터 조작을 위한 다양한 데이터 구조 및 데이터 분석 도구 집합을 제공합니다. 아래 코드는 SQL Server 데이터베이스에서 Pandas 데이터 프레임으로 반환되는 결과를 읽습니다.

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

이제 [데이터 과학 환경에서 Azure Blob 데이터 처리](data-blob.md) 문서에 설명된 대로 Pandas 데이터 프레임으로 작업할 수 있습니다.

## <a name="azure-data-science-in-action-example"></a>Azure 데이터 과학 작동 예제
공용 데이터 세트를 사용한 Azure 데이터 과학 프로세스의 종단 간 연습 예제는 [Azure에서 Azure 데이터 과학 프로세스](sql-walkthrough.md)를 참조하세요.

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

