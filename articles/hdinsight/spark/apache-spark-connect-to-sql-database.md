---
title: Apache Spark를 사용하여 Azure SQL Database에서 데이터 읽기 및 쓰기
description: HDInsight Spark 클러스터와 Azure SQL Database 간에 연결을 설정하는 방법을 알아봅니다. SQL 데이터베이스에서 데이터를 읽고 쓰고 스트리밍하려면
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 0c9c3b2d915e54cf954703c56c2087637cc80aa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864621"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>HDInsight Spark 클러스터를 사용하여 Azure SQL Database에서 데이터 읽기 및 쓰기

Azure HDInsight의 Apache Spark 클러스터를 Azure SQL Database에 연결하는 방법을 알아봅니다. 그런 다음 SQL 데이터베이스에서 데이터를 읽고 쓰고 스트리밍합니다. 이 문서의 지침은 Jupyter Notebook을 사용하여 Scala 코드 조각을 실행합니다. 그러나 Scala 또는 Python에 독립 실행형 애플리케이션을 만들고 동일한 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure HDInsight Spark 클러스터 -  [HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)의 지침을 따르세요.

* Azure SQL Database. [Azure SQL Database에서 데이터베이스 만들기](../../azure-sql/database/single-database-create-quickstart.md)의 지침을 따릅니다. 샘플 **AdventureWorksLT** 스키마와 데이터를 사용해서 데이터베이스를 만들어야 합니다. 또한 클라이언트의 IP 주소가 SQL 데이터베이스에 액세스할 수 있도록 서버 수준 방화벽 규칙을 만들어야 합니다. 방화벽 규칙을 추가하는 지침은 같은 문서에 제공됩니다. SQL 데이터베이스를 만든 후 다음 값을 준비해야 합니다. Spark 클러스터에서 데이터베이스에 연결할 때 필요합니다.

    * 서버 이름입니다.
    * 데이터베이스 이름
    * Azure SQL Database 관리 사용자 이름/암호.

* SSMS(SQL Server Management Studio). [SSMS를 사용하여 데이터 연결 및 쿼리](../../azure-sql/database/connect-query-ssms.md)의 지침을 따릅니다.

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook 만들기

먼저 Spark 클러스터와 연결된 Jupyter Notebook을 만듭니다. 이 노트를 사용해서 이 문서에 사용된 코드 조각을 실행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 클러스터를 엽니다.
1. 오른쪽에 있는 **클러스터 대시보드** 아래에서 **Jupyter Notebook** 을 선택합니다.  **클러스터 대시보드** 가 표시되지 않으면 왼쪽 메뉴에서 **개요** 를 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png " alt-text="Apache Spark의 Jupyter Notebook" border="true":::

   > [!NOTE]  
   > 또한 브라우저에서 다음 URL을 열어 Spark 클러스터의 Jupyter Notebook에 액세스할 수도 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Jupyter Notebook의 오른쪽 상단에서 **새로 만들기** 를 클릭하고 **Spark** 를 클릭하여 Scala 노트를 만듭니다. HDInsight Spark 클러스터의 Jupyter Notebook은 Python2 애플리케이션에 대한 **PySpark** 커널과 Python3 애플리케이션에 대한 **PySpark3** 커널도 제공합니다. 이 문서에서는 Scala 노트를 만듭니다.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png " alt-text="Spark의 Jupyter Notebook에 대한 커널" border="true":::

    커널에 대한 자세한 내용은 [HDInsight에서 Apache Spark 클러스터와 함께 Jupyter Notebook 커널 사용](apache-spark-jupyter-notebook-kernels.md)을 참조하세요.

   > [!NOTE]  
   > 이 문서에서는 Spark에서 SQL 데이터베이스로의 데이터 스트리밍이 현재 Scala 및 Java에서만 지원되므로 Spark(Scala) 커널을 사용합니다. SQL에서 읽고 쓰는 작업은 Python을 사용해서 수행할 수 있지만, 이 문서에서는 일관성을 위해 3가지 작업 모두에 Scala를 사용합니다.

1. 기본 이름이 **Untitled** 인 새 노트가 열립니다. 노트 이름을 클릭하고 원하는 이름을 입력합니다.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png " alt-text="노트북에 대한 이름 제공" border="true":::

이제 애플리케이션 만들기를 시작할 수 있습니다.

## <a name="read-data-from-azure-sql-database"></a>Azure SQL Database에서 데이터 읽기

이 섹션에서는 AdventureWorks 데이터베이스에 존재하는 테이블(예: **SalesLT.Address**)에서 데이터를 읽습니다.

1. 새 Jupyter Notebook 코드 셀에서 다음 코드 조각을 붙여넣고 자리 표시자 값을 데이터베이스에 대한 값으로 바꿉니다.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    **Shift+Enter** 를 눌러 코드 셀을 실행합니다.  

1. 아래 코드 조각을 사용하여 Spark 데이터 프레임 API에 전달할 수 있는 JDBC URL을 빌드합니다. 코드는 `Properties` 매개 변수를 보유하는 개체를 만듭니다. 이 코드 조각을 코드 셀에 붙여 넣고 **Shift+Enter** 를 눌러 실행합니다.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 다음 코드 조각을 사용하여 데이터베이스의 테이블에 있는 데이터로 데이터 프레임을 만듭니다. 이 코드 조각에서는 **AdventureWorksLT** 데이터베이스의 일부로 제공되는 `SalesLT.Address` 테이블을 사용합니다. 이 코드 조각을 코드 셀에 붙여 넣고 **Shift+Enter** 를 눌러 실행합니다.

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. 이제 데이터 스키마를 가져오는 것과 같은 작업을 데이터 프레임에서 수행할 수 있습니다.

    ```scala
    sqlTableDF.printSchema
    ```

    다음 이미지와 유사한 결과가 표시됩니다.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png " alt-text="스키마 출력" border="true":::

1. 상위 10개 행을 검색하는 등의 작업을 수행할 수도 있습니다.

    ```scala
    sqlTableDF.show(10)
    ```

1. 또는 데이터 세트에서 특정 열을 검색합니다.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Azure SQL Database에 데이터 쓰기

이 섹션에서는 클러스터에서 사용할 수 있는 샘플 CSV 파일을 사용하여 데이터베이스에서 테이블을 만들고 데이터를 채웁니다. 샘플 CSV 파일(**HVAC.csv**)은 `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`의 모든 HDInsight 클러스터에서 사용할 수 있습니다.

1. 새 Jupyter Notebook 코드 셀에서 다음 코드 조각을 붙여넣고 자리 표시자 값을 데이터베이스에 대한 값으로 바꿉니다.

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    **Shift+Enter** 를 눌러 코드 셀을 실행합니다.  

1. 다음 코드 조각은 Spark 데이터 프레임 API에 전달할 수 있는 JDBC URL을 빌드합니다. 코드는 `Properties` 매개 변수를 보유하는 개체를 만듭니다. 이 코드 조각을 코드 셀에 붙여 넣고 **Shift+Enter** 를 눌러 실행합니다.

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 다음 코드 조각을 사용하여 HVAC.csv에 있는 데이터의 스키마를 추출하고, 이 스키마를 사용해서 CSV의 데이터를 데이터 프레임 `readDf`로 로드합니다. 이 코드 조각을 코드 셀에 붙여 넣고 **Shift+Enter** 를 눌러 실행합니다.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. `readDf` 데이터 프레임을 사용하여 임시 테이블 `temphvactable`을 만듭니다. 그런 후 임시 테이블을 사용하여 hive 테이블 `hvactable_hive`를 만듭니다.

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. 마지막으로 하이브 테이블을 사용하여 데이터베이스에 테이블을 만듭니다. 다음 코드 조각은 Azure SQL Database에 `hvactable`을 만듭니다.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. SSMS를 사용하여 Azure SQL Database에 연결하고 `dbo.hvactable`이 표시되는지 확인합니다.

    a. SSMS를 시작한 후 아래 스크린샷에 표시된 대로 연결 세부 정보를 입력하여 Azure SQL Database에 연결합니다.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png " alt-text="SSMS1을 사용하여 SQL 데이터베이스에 연결" border="true":::

    b. **개체 탐색기** 에서 데이터베이스 및 테이블 노드를 확장하여  **dbo.hvactable** 이 생성되었는지 확인합니다.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png " alt-text="SSMS2를 사용하여 SQL 데이터베이스에 연결" border="true":::

1. SSMS에서 쿼리를 실행하여 테이블에 열을 표시합니다.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Azure SQL Database로 데이터 스트리밍

이 섹션에서는 데이터를 이전 섹션에서 만든 `hvactable`로 스트리밍합니다.

1. 첫 번째 단계로, `hvactable`에 레코드가 없는지 확인합니다. SSMS를 사용하여 테이블에 대해 다음 쿼리를 실행합니다.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. HDInsight Spark 클러스터에서 새 Jupyter Notebook을 만듭니다. 빈 셀에서 다음 코드 조각을 붙여넣고 **Shift+Enter** 를 누릅니다.

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. **HVAC.csv** 의 데이터를 `hvactable`로 스트리밍합니다. HVAC.csv 파일은 `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`의 클러스터에서 사용할 수 있습니다. 다음 코드 조각에서는 먼저 스트리밍할 데이터의 스키마를 가져옵니다. 그런 다음 해당 스키마를 사용하여 스트리밍 데이터 프레임을 만듭니다. 이 코드 조각을 코드 셀에 붙여 넣고 **Shift+Enter** 를 눌러 실행합니다.

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. 출력에는 **HVAC.csv** 의 스키마가 표시됩니다. `hvactable` 또한 동일한 스키마를 갖습니다. 출력에는 테이블의 열이 표시됩니다.

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png " alt-text="‘HDInsight Apache Spark 스키마 테이블’" border="true":::

1. 마지막으로 다음 코드 조각을 사용하여 HVAC.csv에서 데이터를 읽은 후 데이터베이스의 `hvactable`에 스트리밍합니다. 코드 셀에 코드 조각을 붙여넣고 자리 표시자 값을 데이터베이스에 대한 값으로 바꾼 다음 **SHIFT + ENTER** 를 눌러 실행합니다.

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. SSMS(SQL Server Management Studio)에서 다음 쿼리를 실행하여 데이터가 `hvactable`로 스트리밍되고 있는지 확인합니다. 쿼리를 실행할 때마다 테이블의 행 수가 증가합니다.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>다음 단계

* [HDInsight Spark 클러스터를 사용하여 Data Lake Storage의 데이터 분석](apache-spark-use-with-data-lake-store.md)
* [Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행](apache-spark-load-data-run-query.md)
* [HDInsight에서 Apache Kafka의 Apache Spark 정형 스트림 사용](../hdinsight-apache-kafka-spark-structured-streaming.md)
