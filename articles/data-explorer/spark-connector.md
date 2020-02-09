---
title: Apache Spark Azure 데이터 탐색기 커넥터를 사용 하 여 Azure 데이터 탐색기와 Spark 클러스터 간에 데이터를 이동 합니다.
description: 이 항목에서는 Azure 데이터 탐색기와 Apache Spark 클러스터 간에 데이터를 이동 하는 방법을 보여 줍니다.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 868e9e068244af91e218d906bee115b58906152f
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027938"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Apache Spark 용 Azure 데이터 탐색기 커넥터 (미리 보기)

[Apache Spark](https://spark.apache.org/) 는 대규모 데이터 처리를 위한 통합 분석 엔진입니다. Azure 데이터 탐색기는 대량의 데이터를 실시간으로 분석할 수 있는 빠르고 완전히 관리 되는 데이터 분석 서비스입니다. 

Spark 용 azure 데이터 탐색기 커넥터는 데이터 원본 및 데이터 싱크를 구현 하 여 Azure 데이터 탐색기 및 Spark 클러스터 간에 데이터를 이동 하 여 두 기능을 모두 사용 합니다. Azure Data Explorer와 Apache Spark를 사용하면 ML(기계 학습), ETL(추출-변환-로드) 및 Log Analytics와 같은 데이터 기반 시나리오를 대상으로 빠르고 확장성 있는 애플리케이션을 구축할 수 있습니다. 일괄 처리 및 스트리밍 모드에서 Azure 데이터 탐색기에 대한 쓰기를 수행할 수 있습니다.
Azure 데이터 탐색기에서 읽으면 열 정리 및 조건자 푸시 다운이 지원 되므로 Azure 데이터 탐색기에서 데이터를 필터링 하 여 전송 되는 데이터의 양을 줄일 수 있습니다.

Azure 데이터 탐색기 Spark 커넥터는 모든 Spark 클러스터에서 실행할 수 있는 [오픈 소스 프로젝트](https://github.com/Azure/azure-kusto-spark) 입니다. Azure 데이터 탐색기 Spark 커넥터를 사용 하면 Azure 데이터 탐색기는 표준 Spark 원본 및 싱크 작업 (예: write, read 및 writeStream)에 대한 유효한 데이터 저장소입니다. 

> [!NOTE]
> 아래 예제 중 일부는 [Azure Databricks](https://docs.azuredatabricks.net/) spark 클러스터를 참조 하지만, Azure 데이터 탐색기 spark 커넥터는 Databricks 또는 다른 spark 배포에 대한 직접적인 종속성을 취하지 않습니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Data Explorer 클러스터 및 데이터베이스 만들기](/azure/data-explorer/create-cluster-database-portal) 
* Spark 클러스터 만들기
* 다음 [Kusto JAVA SDK](/azure/kusto/api/java/kusto-java-client-library) 라이브러리를 포함 하 여 Azure 데이터 탐색기 커넥터 라이브러리와 [종속성](https://github.com/Azure/azure-kusto-spark#dependencies) 에 나열 된 라이브러리를 설치 합니다.
    * [Kusto 데이터 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 수집 클라이언트](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 및 [Maven 리포지토리의](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector) 미리 빌드된 라이브러리

## <a name="how-to-build-the-spark-connector"></a>Spark 커넥터를 빌드하는 방법

아래에 설명 된 대로 Spark 커넥터를 [원본](https://github.com/Azure/azure-kusto-spark) 에서 빌드할 수 있습니다.

> [!NOTE]
> 이 단계는 선택 사항입니다. 미리 빌드된 라이브러리를 사용 하는 경우 [Spark 클러스터 설정](#spark-cluster-setup)으로 이동 합니다.

### <a name="build-prerequisites"></a>빌드 필수 구성 요소

* Java 1.8 SDK 설치 됨
* [Maven](https://maven.apache.org/download.cgi) 3.x 설치 됨
* Apache Spark 버전 2.4.0 이상

> [!TIP]
> 2.3. x 버전도 지원 되지만 pom .xml 종속성의 일부를 변경 해야 할 수도 있습니다.

Maven 프로젝트 정의를 사용 하는 Scala/Java 응용 프로그램의 경우 다음 아티팩트를 사용 하 여 응용 프로그램을 연결 합니다 (최신 버전은 다를 수 있음).

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>빌드 명령

jar를 빌드하고 모든 테스트를 실행하려면:

```
mvn clean package
```

Jar을 빌드하려면 모든 테스트를 실행 하 고 로컬 Maven 리포지토리에 jar을 설치 합니다.

```
mvn clean install
```

자세한 내용은 [커넥터 사용](https://github.com/Azure/azure-kusto-spark#usage)을 참조 하세요.

## <a name="spark-cluster-setup"></a>Spark 클러스터 설정

> [!NOTE]
> 다음 단계를 수행 하는 경우 최신 Azure 데이터 탐색기 Spark 커넥터 릴리스를 사용 하는 것이 좋습니다.

1. Spark 2.4.4 및 Scala 2.11를 사용 하 여 Azure Databricks 클러스터에 따라 다음 Spark 클러스터 설정을 설정 합니다. 

    ![Databricks 클러스터 설정](media/spark-connector/databricks-cluster.png)
    
1. Maven에서 최신 spark-kusto 커넥터 라이브러리를 설치 합니다.

    ![Azure 데이터 탐색기 라이브러리 가져오기](media/spark-connector/db-create-library.png)

1. 모든 필수 라이브러리가 설치 되어 있는지 확인 합니다.

    ![설치 된 라이브러리 확인](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>인증

Azure 데이터 탐색기 Spark 커넥터를 사용 하면 azure ad [응용 프로그램](#azure-ad-application-authentication), [azure ad 액세스 토큰](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [장치 인증](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (비프로덕션 시나리오의 경우) 또는 [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)를 사용 하 여 Azure Active Directory (azure ad)에 인증할 수 있습니다. 사용자는 azure-keyvault 패키지를 설치 하 고 Key Vault 리소스에 액세스 하기 위한 응용 프로그램 자격 증명을 제공 해야 합니다.

### <a name="azure-ad-application-authentication"></a>Azure AD 응용 프로그램 인증

가장 간단한 일반 인증 방법입니다. 이 방법은 Azure 데이터 탐색기 Spark 커넥터 사용에 권장 됩니다.

|속성  |Description  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 응용 프로그램 (클라이언트) 식별자입니다.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 인증 기관. Azure AD 디렉터리 (테넌트) ID입니다.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    클라이언트의 Azure AD 응용 프로그램 키입니다.     |

### <a name="azure-data-explorer-privileges"></a>Azure 데이터 탐색기 권한

Azure 데이터 탐색기 클러스터에서 다음 권한을 부여 해야 합니다.

* 읽기 (데이터 원본)의 경우 Azure AD 응용 프로그램에 대상 데이터베이스에 대한 *뷰어* 권한 또는 대상 테이블에 대한 *관리자* 권한이 있어야 합니다.
* 쓰기 (데이터 싱크)의 경우 Azure AD 응용 프로그램에는 대상 데이터베이스에 대한 *수집기* 권한이 있어야 합니다. 또한 새 테이블을 만들려면 대상 데이터베이스에 대한 *사용자* 권한이 있어야 합니다. 대상 테이블이 이미 있는 경우 대상 테이블에 대한 *관리자* 권한을 구성할 수 있습니다.
 
Azure 데이터 탐색기 주 역할에 대한 자세한 내용은 [역할 기반 권한 부여](/azure/kusto/management/access-control/role-based-authorization)를 참조 하세요. 보안 역할 관리에 대한는 [보안 역할 관리](/azure/kusto/management/security-roles)를 참조 하세요.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark 싱크: Azure 데이터 탐색기에 쓰기

1. 싱크 매개 변수 설정:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Azure 데이터 탐색기 클러스터에 batch로 Spark 데이터 프레임을 씁니다.

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   또는 간단한 구문을 사용 합니다.
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. 스트리밍 데이터를 씁니다.

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 원본: Azure 데이터 탐색기에서 읽기

1. 소량의 데이터를 읽을 때 데이터 쿼리를 정의 합니다.

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. 많은 양의 데이터를 읽을 때 임시 blob 저장소를 제공 해야 합니다. 저장소 컨테이너 SAS 키, 저장소 계정 이름, 계정 키 및 컨테이너 이름을 제공 합니다. 이 단계는 Spark 커넥터의 현재 미리 보기 릴리스에만 필요 합니다.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    위의 예제에서는 커넥터 인터페이스를 사용 하 여 Key Vault에 액세스 하지 않습니다. 또는 Databricks 암호를 사용 하는 간단한 방법을 사용 합니다.

1. Azure 데이터 탐색기에서 읽기:

    ```scala
     val conf3 = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
          KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 Spark 커넥터](https://github.com/Azure/azure-kusto-spark/tree/master/docs) 에 대한 자세한 정보
* [샘플 코드](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)

