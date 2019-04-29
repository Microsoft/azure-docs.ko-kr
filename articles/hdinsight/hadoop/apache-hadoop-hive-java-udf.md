---
title: HDInsight에서 Apache Hive와 함께 사용할 Java UDF(사용자 정의 함수) 만들기 - Azure
description: Apache Hive와 함께 사용할 Java 기반 UDF(사용자 정의 함수)를 만드는 방법을 알아봅니다. 이 예제 UDF는 텍스트 문자열 테이블을 소문자로 변환합니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 8d4f8efe-2f01-4a61-8619-651e873c7982
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 03/21/2019
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: 24c2e8b9600b3d622d3d6b42b3bc3615a87ff853
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122033"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>HDInsight에서 Apache Hive와 함께 Java UDF 사용

Apache Hive와 함께 사용할 Java 기반 UDF(사용자 정의 함수)를 만드는 방법을 알아봅니다. 이 예제의 Java UDF는 텍스트 문자열 테이블을 모두 소문자로 변환합니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight에서 Hadoop 클러스터입니다. 참조 [Linux에서 HDInsight 시작](./apache-hadoop-linux-tutorial-get-started.md)합니다.
* [JDK(Java Developer Kit) 버전 8](https://aka.ms/azure-jdks)
* Apache에 따라 올바르게 [설치된](https://maven.apache.org/install.html) [Apache Maven](https://maven.apache.org/download.cgi)  Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.
* 합니다 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 클러스터 기본 저장소에 대 한 합니다. Wasb 하는 것: / / Azure storage의 경우 abfs: / / Azure Data Lake 저장소 Gen2 또는 adl: / / Azure Data Lake 저장소 Gen1에 대 한 합니다. URI wasbs 것에 대해 Azure Storage 또는 Data Lake 저장소 Gen2 전송 보안을 사용 하는 경우: / / 또는 abfss: / / 각각도 참조 하세요 [보안 전송](../../storage/common/storage-require-secure-transfer.md)합니다.

* 텍스트 편집기 또는 Java IDE

    > [!IMPORTANT]  
    > Windows 클라이언트에서 Python 파일을 만드는 경우 LF를 줄 끝으로 사용하는 편집기를 이용해야 합니다. 편집기에서 LF 또는 CRLF를 사용하는지 여부가 확실하지 않은 경우 CR 문자를 제거하는 단계는 [문제 해결](#troubleshooting) 섹션을 참조하세요.

## <a name="test-environment"></a>테스트 환경
이 문서에 사용 되는 환경에서 Windows 10을 실행 하는 컴퓨터.  명령 프롬프트에서 실행 된 명령 및 다양 한 파일을 메모장으로 편집 합니다. 그에 따라 수정 사용자 환경에 대 한 합니다.

명령 프롬프트에서 작업 환경을 만들려면 아래 명령을 입력 합니다.

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>예제 Java UDF 만들기

1. 다음 명령을 입력 하 여 새 Maven 프로젝트를 만듭니다.

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    이 명령은 라는 디렉터리를 만듭니다 `exampleudf`를 Maven 프로젝트를 포함 하는 합니다.

2. 프로젝트를 만든 후 삭제를 `exampleudf/src/test` 다음 명령을 입력 하 여 프로젝트의 일부로 만들어진 디렉터리:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. 열기 `pom.xml` 아래 명령을 입력 하 여:

    ```cmd
    notepad pom.xml
    ```

    그런 다음 기존 `<dependencies>` 다음 XML 사용 하 여 항목:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    이러한 항목은 HDInsight 3.6과 함께 포함된 Hadoop 및 Hive의 버전을 지정합니다. [HDInsight 구성 요소 버전 관리](../hdinsight-component-versioning.md) 문서에서 HDInsight를 제공하는 Hadoop 및 Hive의 버전에 대한 정보를 찾을 수 있습니다.

    파일 끝의 `</project>` 줄 앞에 `<build>` 섹션을 추가합니다. 이 섹션에는 다음 XML이 포함되어야 합니다.

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    이러한 항목은 프로젝트를 빌드하는 방법을 정의합니다. 특히, 프로젝트에서 사용하는 Java 버전과 클러스터로의 배포를 위한 uberjar를 빌드하는 방법이 유용합니다.

    변경이 완료되면 파일을 저장합니다.

4. 만들고 새 파일을 열고 아래 명령을 입력 `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    복사 하 고 새 파일에 다음 java 코드를 붙여 넣습니다. 그런 다음 파일을 닫습니다.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    이 코드는 문자열 값을 허용하고 문자열의 소문자 버전을 반환하는 UDF를 구현합니다.

## <a name="build-and-install-the-udf"></a>UDF 빌드 및 설치

아래 명령에서 대체 `sshuser` 와 다른 경우 실제 사용자 이름입니다. 대체 `mycluster` 실제 클러스터 이름입니다.

1. 컴파일 및 다음 명령을 입력 하 여 UDF를 패키지 합니다.

    ```cmd
    mvn compile package
    ```

    이 명령은 `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` 파일에 UDF를 빌드하고 패키지합니다.

2. 사용 된 `scp` 다음 명령을 입력 하 여 HDInsight 클러스터에 파일을 복사 하는 명령:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight
    ```

3. 다음 명령을 입력 하 여 SSH를 사용 하 여 클러스터에 연결 합니다.

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.cn
    ```

4. 열린 SSH 세션에서 HDInsight 저장소에 jar 파일을 복사 합니다.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Hive에서 UDF 사용

1. 다음 명령을 입력 하 여 SSH 세션에서 Beeline 클라이언트를 시작 합니다.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    이 명령에서는 클러스터에 대한 로그인 계정에 **admin** 의 기본값을 사용했다고 가정합니다.

2. `jdbc:hive2://localhost:10001/>` 프롬프트가 표시되면 다음을 입력하여 Hive에 UDF를 추가하고 함수로 노출합니다.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. UDF를 사용하여 검색한 값을 테이블에서 소문자 문자열로 변환합니다.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    이 쿼리는 테이블에서 상태를 선택 하 고 이하인 경우 하 고 수정 되지 않은 이름과 함께 표시할 문자열을 변환 합니다. 출력은 다음 텍스트와 유사합니다.

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>문제 해결

하이브 작업 실행 중 다음 텍스트와 유사한 오류가 발생할 수 있습니다.

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

이 문제는 Python 파일의 줄 끝 때문에 발생할 수 있습니다. 많은 Windows 편집기에서는 기본적으로 CRLF를 줄 끝으로 사용하지만 Linux 애플리케이션에서는 보통 LF를 사용합니다.

파일을 HDInsight로 업로드하기 전에 CR 문자를 제거하기 위해 다음 PowerShell 문을 사용할 수 있습니다.

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>다음 단계

Hive로 작업하는 다른 방법은 [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)을 참조하세요.

Hive 사용자 정의 함수에 대한 자세한 내용은 apache.org의 Hive wiki에서 [Apache Hive 연산자 및 사용자 정의 함수](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) 섹션을 참조하세요.
