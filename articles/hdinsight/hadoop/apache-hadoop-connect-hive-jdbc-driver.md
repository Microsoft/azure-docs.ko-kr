---
title: JDBC 드라이버를 통해 Apache Hive 쿼리 - Azure HDInsight
description: Java 애플리케이션에서 JDBC 드라이버를 사용하여 HDInsight의 Hadoop에 Apache Hive 쿼리를 제출합니다. 프로그래밍 방식으로 SQuirrel SQL 클라이언트에서 연결합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: hrasheed
ms.openlocfilehash: aaf54907ca0cf218b126fc9fc74080c02ef6cf6c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122118"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>HDInsight에서 JDBC 드라이버를 통해 Apache Hive 쿼리

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Java 애플리케이션에서 JDBC 드라이버를 사용하여 Azure HDInsight의 Apache Hadoop에 Apache Hive 쿼리를 제출하는 방법을 알아봅니다. 이 문서에서는 SQuirreL SQL 클라이언트에서 프로그래밍 방식으로 연결 하는 방법에 설명 합니다.

Hive JDBC 인터페이스에 대한 자세한 내용은 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* HDInsight Hadoop 클러스터. 만들려면 [Azure HDInsight 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
* 합니다 [Java 개발자 키트 (JDK) 버전 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) 이상.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL은 JDBC 클라이언트 애플리케이션입니다.


## <a name="jdbc-connection-string"></a>JDBC 연결 문자열

Azure에서 HDInsight 클러스터에 대 한 JDBC 연결은 포트 443 통해 수행 하 고는 트래픽이 SSL을 사용 하 여 보호 됩니다. 클러스터가 뒤에 있는 공용 게이트웨이는 HiveServer2에서 실제로 수신하는 포트로 트래픽을 리디렉션합니다. 다음 연결 문자열은 HDInsight에 사용할 형식을 보여줍니다.

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

`CLUSTERNAME`을 HDInsight 클러스터 이름으로 바꿉니다.

## <a name="authentication"></a>Authentication

연결을 설정할 때 HDInsight 클러스터 관리자 이름 및 암호를 사용하여 클러스터 게이트웨이에 대해 인증해야 합니다. SQuirreL SQL 등의 JDBC 클라이언트에서 연결할 때 클라이언트 설정에 관리자 이름 및 암호를 입력해야 합니다.

Java 애플리케이션에서 연결을 설정할 때 이름 및 암호를 사용해야 합니다. 예를 들어 다음 Java 코드는 연결 문자열, 관리자 이름 및 암호를 사용하여 새 연결을 엽니다.

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>SQuirreL SQL 클라이언트를 사용하여 연결

SQuirreL SQL은 HDInsight 클러스터와 함께 Hive 쿼리를 원격으로 실행하는 데 사용할 수 있는 JDBC 클라이언트입니다. 다음 단계에서는 SQuirreL SQL을 이미 설치했다고 가정합니다.

1. 클러스터에서 복사할 특정 파일을 포함할 디렉터리를 만듭니다.

2. 다음 스크립트에서 바꾸기 `sshuser` 클러스터에 대 한 SSH 사용자 계정 이름입니다.  `CLUSTERNAME`은 HDInsight 클러스터 이름으로 바꿉니다.  명령줄에서 HDInsight 클러스터에서 파일을 복사 하려면 다음 명령을 입력 합니다.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-codec*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    ```

3. SQuirreL SQL 애플리케이션을 시작합니다. 왼쪽 창에서 **드라이버**를 선택합니다.

    ![창 왼쪽의 드라이버 탭](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. **드라이버** 대화 상자 위쪽의 아이콘에서 **+** 아이콘을 선택하여 드라이버를 만듭니다.

    ![드라이버 아이콘](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. 드라이버 추가 대화 상자에 다음 정보를 추가합니다.

    * **이름**: Hive
    * **예제 URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **추가 클래스 경로**: 추가 단추를 사용하여 이전에 다운로드한 jar 파일을 모두 추가합니다.
    * **클래스 이름**: org.apache.hive.jdbc.HiveDriver

   ![드라이버 추가 대화 상자](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   선택 **확인** 이러한 설정을 저장 합니다.

6. SQuirreL SQL 창의 왼쪽에서 **별칭**을 선택합니다. 다음을 선택 합니다 **+** 연결 별칭을 만들려면 아이콘.

    ![새 별칭 추가](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. **별칭 추가** 대화 상자에서 다음 값을 사용합니다.

    * **이름**: HDInsight의 Hive

    * **드라이버**: 드롭다운을 사용 하 여 선택 합니다 **Hive** 드라이버

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        **CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.

    * **사용자 이름**: HDInsight 클러스터의 클러스터 로그인 계정 이름입니다. 기본값은 `admin`입니다.

    * **암호**: 클러스터 로그인 계정의 암호입니다.

   ![별칭 추가 대화 상자](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > **테스트** 단추를 사용하여 연결이 작동하는지 확인합니다. **연결 대상: Hive on HDInsight** 대화 상자가 나타나면 **연결**을 선택하여 테스트를 수행합니다. 테스트가 성공하면 **연결 성공** 대화 상자가 표시됩니다. 오류가 발생하는 경우 [문제 해결](#troubleshooting)을 참조하세요.

    연결 별칭을 저장하려면 **별칭 추가** 대화 상자의 아래쪽에 있는 **확인** 단추를 사용합니다.

8. SQuirreL SQL 위쪽의 **연결 대상** 드롭다운에서 **Hive on HDInsight**를 선택합니다. 메시지가 표시되면 **연결**을 선택합니다.

    ![연결 대화 상자](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. 연결 되 면 SQL 쿼리 대화 상자에 다음 쿼리를 입력 한 다음 선택 합니다 **실행** 아이콘 (실행 중인 person). 결과 영역에 쿼리 결과가 표시됩니다.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![결과를 포함한 sql 쿼리 대화 상자](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Java 애플리케이션 예제에서 연결

Java 클라이언트를 사용하여 HDInsight에서 Hive를 쿼리하는 예제는 [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)에 있습니다. 리포지토리의 지침에 따라 샘플을 빌드하고 실행합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>SQL 연결을 열 때 예기치 않은 오류가 발생했습니다

**증상**: 버전 3.3 이상인 HDInsight 클러스터에 연결할 때 예기치 않은 오류가 발생할 수 있습니다. 이 오류에 대한 스택 추적은 다음 줄로 시작합니다.

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**원인**: SQuirreL에 포함된 이전 버전 commons-codec.jar 파일에서 이 오류가 발생됩니다.

**해결 방법**: 이 오류를 해결하려면 다음 단계를 수행합니다.

1. SQuirreL을 종료한 다음 시스템에서 SQuirreL이 설치된 디렉터리로 이동합니다. SquirreL 디렉터리의 `lib` 디렉터리에서 기존 common-codec.jar 파일을 HDInsight 클러스터에서 다운로드한 파일로 바꿉니다.

2. SQuirreL을 다시 시작합니다. HDInsight에서 Hive에 연결할 때 오류가 더 이상 발생하지 않아야 합니다.

## <a name="next-steps"></a>다음 단계

JDBC를 사용하여 Hive와 함께 작업하는 방법을 살펴보았으므로 이제 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](./../hdinsight-connect-hive-zeppelin.md)
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](../hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](../hdinsight-upload-data.md)
* [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)
