---
title: 지원되는 데이터 플랫폼
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine에 대해 지원 되는 데이터 플랫폼 및 도구에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 927e945a0d045abcd1caa2951dbd484224b2f425
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519544"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine에서 지원되는 데이터 플랫폼

Data Science Virtual Machine (DSVM)를 사용 하 여 광범위 한 데이터 플랫폼에 대해 분석을 빌드할 수 있습니다. 원격 데이터 플랫폼에 대 한 인터페이스를 외에도 DSVM 신속 하 게 개발 및 프로토타입 생성에 대 한 로컬 인스턴스를 제공합니다.

DSVM에서 지원 되는 데이터 플랫폼 도구는 다음과 같습니다.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 로컬 관계형 데이터베이스 인스턴스      |
| 지원 되는 DSVM 버전      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| 일반적인 사용 용도      | <ul><li>더 작은 데이터 세트를 사용하여 로컬에서 빠른 개발</li><li>In-database R 실행</li></ul> |
| 샘플에 대한 링크      | <ul><li>뉴욕 도시 데이터 집합의 작은 샘플은 SQL 데이터베이스에 로드 됩니다.<br/>  `nyctaxi`</li><li>Microsoft Machine Learning Server 및 데이터베이스 내 분석을 보여 주는 jupyter 샘플은 다음 위치에서 찾을 수 있습니다.<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| DSVM의 관련 도구       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC 드라이버</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> SQL Server Developer Edition은 개발 및 테스트 목적 으로만 사용할 수 있습니다. 프로덕션에서 실행하려면 라이선스나 SQL Server VM 중 하나가 필요합니다.


### <a name="setup"></a>설정

데이터베이스 서버는 이미 미리 구성 되어 있으며 SQL Server와 관련 된 Windows 서비스 (예: `SQL Server (MSSQLSERVER)` )가 자동으로 실행 되도록 설정 되어 있습니다. 수동 단계에서는 Microsoft Machine Learning Server를 사용 하 여 데이터베이스 내 분석을 설정 해야 합니다. SSMS (SQL Server Management Studio)에서 일회성 작업으로 다음 명령을 실행 하 여 분석을 사용 하도록 설정할 수 있습니다. 컴퓨터 관리자로 로그인 한 후에이 명령을 실행 하 여 SSMS에서 새 쿼리를 열고 선택한 데이터베이스가 다음과 같은지 확인 합니다 `master` .

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

% COMPUTERNAME%을 VM 이름으로 바꿉니다.

SQL Server Management Studio를 실행 하려면 프로그램 목록에서 "SQL Server Management Studio"를 검색 하거나 Windows 검색을 사용 하 여 검색 하 고 실행할 수 있습니다. 자격 증명을 입력 하 라는 메시지가 표시 되 면 **Windows 인증** 을 선택 하 고 컴퓨터 이름 또는 ```localhost``` **SQL Server 이름** 필드에 사용 합니다.

### <a name="how-to-use-and-run-it"></a>사용 및 실행 방법

기본적으로 기본 데이터베이스 인스턴스를 포함 하는 데이터베이스 서버는 자동으로 실행 됩니다. Microsoft SQL Server 데이터베이스에 로컬로 액세스하려면 VM에서 SQL Server Management Studio와 같은 도구를 사용할 수 있습니다. 로컬 관리자 계정에는 데이터베이스에 대 한 관리자 액세스 권한이 있습니다.

또한 DSVM은 Python 및 Machine Learning Server를 비롯 하 여 여러 언어로 작성 된 응용 프로그램에서 SQL Server, Azure SQL database 및 Azure Synapse Analytics와 통신 하기 위해 ODBC 및 JDBC 드라이버와 함께 제공 됩니다.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM에 구성 및 설치 하는 방법 

 SQL Server은 표준 방식으로 설치 됩니다. 그것은 `C:\Program Files\Microsoft SQL Server`에서 찾을 수 있습니다. 데이터베이스 내 Machine Learning Server 인스턴스는에 `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` 있습니다. 또한 DSVM에는에 설치 되는 별도의 독립 실행형 Machine Learning Server 인스턴스가 있습니다 `C:\Program Files\Microsoft\R Server\R_SERVER` . 이러한 두 Machine Learning Server 인스턴스는 라이브러리를 공유 하지 않습니다.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (독립 실행형)

| 범주 | 값 |
| ------------- | ------------- |
| 무엇인가요?   | 인기 있는 Apache Spark 플랫폼의 독립 실행형 (단일 노드 in-process) 인스턴스 고속, 대규모 데이터 처리 및 기계 학습을 위한 시스템     |
| 지원 되는 DSVM 버전      | Linux     |
| 일반적인 사용 용도      | <ul><li>더 작은 데이터 집합을 사용 하 여 로컬로 Spark/PySpark 응용 프로그램을 신속 하 게 개발 하 고 Azure HDInsight와 같은 대량 Spark 클러스터에서 나중에 배포</li><li>테스트 Microsoft Machine Learning Server Spark 컨텍스트</li><li>SparkML 또는 Microsoft의 오픈 소스 [Mmlspark](https://github.com/Azure/mmlspark) 라이브러리를 사용 하 여 ML 응용 프로그램 빌드</li></ul> |
| 샘플에 대한 링크      |    Jupyter 샘플:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark 컨텍스트):/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| DSVM의 관련 도구       | <ul><li>PySpark, Scala</li><li>Jupyter (Spark/PySpark 커널)</li><li>Microsoft Machine Learning Server, SparkR, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>사용 방법
또는 명령을 실행 하 여 명령줄에서 Spark 작업을 제출할 수 `spark-submit` 있습니다 `pyspark` . 또한 Spark 커널로 새 노트북을 만듦으로써 Jupyter 노트북을 만들 수 있습니다.

DSVM에서 사용할 수 있는 SparkR, Sparklyr 및 Microsoft Machine Learning Server와 같은 라이브러리를 사용 하 여 R에서 Spark를 사용할 수 있습니다. 앞의 표에서 샘플에 대한 포인터를 참조하세요.

### <a name="setup"></a>설정
Ubuntu Linux DSVM 버전에서 Microsoft Machine Learning Server Spark 컨텍스트를 실행 하기 전에 로컬 단일 노드 Hadoop HDFS 및 Yarn 인스턴스를 사용 하도록 설정 하려면 일회성 설치 단계를 완료 해야 합니다. 기본적으로 Hadoop 서비스는 설치되지만 DSVM에서 사용하지 않도록 설정됩니다. 이 기능을 사용 하도록 설정 하려면 처음에 루트로 다음 명령을 실행 합니다.

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

을 실행 하 여 더 이상 필요 하지 않은 경우 Hadoop 관련 서비스를 중지할 수 있습니다 ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

원격 Spark 컨텍스트 (DSVM의 독립 실행형 Spark 인스턴스)에서 MRS를 개발 하 고 테스트 하는 방법을 보여 주는 샘플은 디렉터리에서 제공 되 고 사용할 수 있습니다 `/dsvm/samples/MRS` .


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM에 구성 및 설치 하는 방법 
|플랫폼|설치 위치($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Microsoft MMLSpark 기계 학습 라이브러리를 사용 하 여 Azure Blob storage 또는 Azure Data Lake Storage에서 데이터에 액세스 하는 라이브러리는 $SPARK _HOME/jars.에 미리 설치 되어 있습니다. 이러한 JAR은 Apache Spark가 시작될 때 자동으로 로드됩니다. 기본적으로 Spark는 로컬 디스크의 데이터를 사용 합니다. 

Blob 저장소 또는 Azure Data Lake Storage에 저장 된 데이터에 액세스 하기 위해 DSVM의 Spark 인스턴스에서는 `core-site.xml` $SPARK _HOME/sv/core-site.xml 템플릿에 있는 템플릿을 기반으로 파일을 만들고 구성 해야 합니다. 또한 Blob storage에 액세스 하 고 Azure Data Lake Storage 하는 데 적절 한 자격 증명이 있어야 합니다. 템플릿 파일은 Blob 저장소 및 Azure Data Lake Storage 구성에 대 한 자리 표시자를 사용 합니다.

Azure Data Lake Storage 서비스 자격 증명을 만드는 방법에 대 한 자세한 내용은 [Azure Data Lake Storage Gen1 인증](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)을 참조 하세요. Blob 저장소 또는 Azure Data Lake Storage에 대 한 자격 증명을 core-site.xml 파일에 입력 한 후에는 wasb://또는 adl://의 URI 접두사를 통해 해당 원본에 저장 된 데이터를 참조할 수 있습니다.