---
title: Apache Hadoop용 Java MapReduce 만들기 - Azure HDInsight
description: Apache Maven을 사용하여 Java 기반 MapReduce 애플리케이션을 만든 다음 Azure HDInsight의 Hadoop과 함께 실행하는 방법을 알아봅니다.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: e4f567fe727a2ec308cef6f48e33728f7f31c217
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121999"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>HDInsight에서 Apache Hadoop용 Java MapReduce 프로그램 개발

Apache Maven을 사용하여 Java 기반 MapReduce 애플리케이션을 만든 다음, Azure HDInsight의 Hadoop으로 실행하는 방법을 알아봅니다.

> [!NOTE]
> 이 예제는 HDInsight 3.6에서 가장 최근에 테스트되었습니다.

## <a name="prerequisites"></a>필수 조건

* [Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/) 8 이상(또는 OpenJDK와 같은 이와 동등한 프로그램).
    
    > [!NOTE]
    > HDInsight 버전 3.4 이전은 Java 7을 사용합니다. HDInsight 3.5 이상은 Java 8을 사용합니다.

* [Apache Maven](https://maven.apache.org/)

## <a name="configure-development-environment"></a>개발 환경 구성

Java 및 JDK를 설치할 때 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* `JAVA_HOME` - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 OS X, Unix 또는 Linux 시스템에서는 `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`

* `PATH` - 다음 경로를 포함해야 합니다.
  
  * `JAVA_HOME`(또는 그와 동등한 경로)

  * `JAVA_HOME\bin`(또는 그와 동등한 경로)

  * Maven이 설치된 디렉터리

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

1. 터미널 세션 또는 개발 환경의 명령줄에서 이 프로젝트를 저장할 위치로 디렉터리를 변경합니다.

2. Maven과 함께 설치되는 `mvn` 명령을 사용하여 프로젝트에 대한 스캐폴딩을 생성합니다.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > PowerShell을 사용하는 경우 큰 따옴표로 `-D` 매개 변수를 묶어야 합니다.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    이 명령은 `artifactID` 매개 변수(이 예제에서는 **wordcountjava**)로 지정된 이름으로 디렉터리를 만듭니다. 이 디렉터리에는 다음과 같은 항목이 포함됩니다.

   * `pom.xml` - [프로젝트 개체 모델(POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.

   * `src` - 애플리케이션을 포함하는 디렉터리입니다.

3. `src/test/java/org/apache/hadoop/examples/apptest.java` 파일을 삭제합니다. 이 예제에서는 사용되지 않습니다.

## <a name="add-dependencies"></a>종속성 추가

1. `pom.xml` 파일을 편집하고 `<dependencies>` 섹션 내에 다음 텍스트를 추가합니다.
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    이 항목은 특정 버전(&lt;version\>에 나열됨)을 사용하는 필수 라이브러리(&lt;artifactId\> 내에 나열됨)를 정의합니다. 컴파일 시 이러한 종속성이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 리포지토리 검색](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) 을 사용하여 자세한 정보를 볼 수 있습니다.
   
    `<scope>provided</scope>`는 이러한 종속성은 런타임에 HDInsight 클러스터에서 제공되므로 애플리케이션과 함께 패키징해서는 안 된다는 점을 Maven에 알려 줍니다.

    > [!IMPORTANT]
    > 사용되는 버전은 클러스터에 있는 Hadoop 버전과 일치해야 합니다. 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전 관리](../hdinsight-component-versioning.md) 문서를 참조하세요.

2. `pom.xml` 파일에 다음을 추가합니다. 이 텍스트는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어 `</dependencies>`와 `</project>` 사이에 있어야 합니다.

   ```xml
    <build>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
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
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    첫 번째 플러그 인은 애플리케이션에 필요한 종속성을 포함하는 uberjar(fatjar이라고도 함)을 빌드하는 데 사용되는 [Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/)을 구성합니다. 또한 일부 시스템에서 문제를 일으킬 수 있는 jar 패키지 내 라이선스 중복을 방지합니다.

    두 번째 플러그 인은 대상 Java 버전을 구성합니다.

    > [!NOTE]
    > HDInsight 3.4 이전은 Java 7 사용합니다. HDInsight 3.5 이상은 Java 8을 사용합니다.

3. `pom.xml` 파일을 저장합니다.

## <a name="create-the-mapreduce-application"></a>MapReduce 애플리케이션 만들기

1. `wordcountjava/src/main/java/org/apache/hadoop/examples` 디렉터리로 이동하여 `App.java` 파일의 이름을 `WordCount.java`으로 지정합니다.

2. 텍스트 편집기에서 `WordCount.java` 파일을 열고 내용을 다음 텍스트로 바꿉니다.
   
    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```
   
    패키지 이름은 `org.apache.hadoop.examples`이며 클래스 이름은 `WordCount`입니다. MapReduce 작업을 제출할 때 이 이름을 사용합니다.

3. 파일을 저장합니다.

## <a name="build-the-application"></a>애플리케이션 빌드

1. 아직 이동하지 않은 경우 `wordcountjava` 디렉터리로 변경합니다.

2. 다음 명령을 사용하여 애플리케이션을 포함하는 JAR 파일을 빌드합니다.

   ```
   mvn clean package
   ```

    이 명령은 이전 빌드 아티팩트를 정리하고, 아직 설치되지 않은 모든 종속성을 다운로드한 후 애플리케이션을 빌드 및 패키지화합니다.

3. 명령이 마무리되면 `wordcountjava/target` 디렉터리는 `wordcountjava-1.0-SNAPSHOT.jar` 파일을 포함합니다.
   
   > [!NOTE]
   > `wordcountjava-1.0-SNAPSHOT.jar` 파일은 는 WordCount 작업뿐만 아니라 런타임 시 작업에서 필요로 하는 종속성을 포함하는 uberjar입니다.

## <a id="upload"></a>jar 업로드

다음 명령을 사용하여 HDInsight 헤드 노드에 jar 파일을 업로드합니다.

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

__USERNAME__을 클러스터의 SSH 사용자 이름으로 바꿉니다. __CLUSTERNAME__은 HDInsight 클러스터 이름으로 바꿉니다.

이 명령은 로컬 시스템에서 헤드 노드로 파일을 복사합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="run"></a>Hadoop에서 MapReduce 작업 실행

1. SSH를 사용하여 HDInsight에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. SSH 세션에서 다음 명령을 사용하여 MapReduce 애플리케이션을 실행합니다.
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    이 명령은 WordCount MapReduce 애플리케이션을 시작합니다. 입력된 파일은 `/example/data/gutenberg/davinci.txt`이며 출력 디렉터리는 `/example/data/wordcountout`입니다. 입력 파일과 출력 모두 클러스터의 기본 저장소에 저장됩니다.

3. 작업이 완료되면 다음 명령을 사용하여 결과를 확인합니다.
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    다음 텍스트와 유사한 값을 가진 단어 및 개수 목록이 표시됩니다.
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>다음 단계

이 문서에서는 Java MapReduce 작업을 개발하는 방법에 대해 알아보았습니다. HDInsight로 작업하는 다른 방법은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

자세한 내용은 [Java 개발자 센터](https://azure.microsoft.com/develop/java/)를 참조하세요.

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: https://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

