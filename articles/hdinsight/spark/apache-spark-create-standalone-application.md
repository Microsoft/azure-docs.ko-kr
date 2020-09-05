---
title: '자습서: Spark 및 IntelliJ용 Scala Maven 앱 - Azure HDInsight'
description: 자습서 - Apache Maven을 빌드 시스템으로 사용하여 Scala로 작성된 Spark 애플리케이션을 만듭니다. 그리고 IntelliJ IDEA에서 제공하는 Scala용 기존 Maven 원형을 만듭니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: contperfq1
ms.date: 08/21/2020
ms.openlocfilehash: cfdb02b0a5942536a608fbc957ed69d1efd97e8e
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756897"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>자습서: IntelliJ를 사용하여 HDInsight의 Apache Spark용 Scala Maven 애플리케이션 만들기

이 자습서에서는 IntelliJ IDEA에서 Apache Maven을 사용하여 Scala로 작성한 Apache Spark 애플리케이션을 만드는 방법을 알아봅니다. 이 문서에서는 Apache Maven을 빌드 시스템으로 사용합니다. 그리고 IntelliJ IDEA에서 제공하는 Scala용 기존 Maven 원형으로 시작합니다.  IntelliJ IDEA에서 Scala 애플리케이션 만들기는 다음 단계를 포함합니다.

* 빌드 시스템으로 Maven을 사용합니다.
* POM(프로젝트 개체 모델) 파일을 업데이트하여 Spark 모듈 종속성을 해결합니다.
* Scala에서 애플리케이션을 작성합니다.
* HDInsight Spark 클러스터에 제출할 수 있는 jar 파일을 생성합니다.
* Livy를 사용하여 Spark 클러스터에서 애플리케이션을 실행합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * IntelliJ IDEA용 Scala 플러그 인 설치
> * IntelliJ를 사용 하 여 Scala Maven 애플리케이션 개발
> * 독립 실행형 Scala 프로젝트 만들기

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Spark. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

* [Oracle Java Development 키트](https://www.azul.com/downloads/azure-only/zulu/).  이 자습서에서는 Java 버전 8.0.202를 사용합니다.

* Java IDE. 이 문서에서는 [IntelliJ IDEA 커뮤니티 버전  2018.3.4](https://www.jetbrains.com/idea/download/)를 사용합니다.

* Azure Toolkit for IntelliJ.  [Azure Toolkit for IntelliJ 설치](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in)를 참조하세요.

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA용 Scala 플러그 인 설치

Scala 플러그 인을 설치하려면 다음 단계를 수행합니다.

1. IntelliJ IDEA를 엽니다.

2. 시작 화면에서 **구성** > **플러그인**으로 이동하여 **플러그인** 창을 엽니다.

    ![`scala 플러그 인을 사용하도록 설정하는 IntelliJ IDEA`](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. 새 창에 제공되는 Scala 플러그인에 대해 **설치**를 선택합니다.  

    ![`scala 플러그 인을 설치하는 IntelliJ IDEA`](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. 플러그 인이 성공적으로 설치된 후에 IDE를 다시 시작해야 합니다.

## <a name="use-intellij-to-create-application"></a>IntelliJ를 사용하여 애플리케이션 만들기

1. IntelliJ IDEA를 시작하고 **새 프로젝트 만들기**를 선택하여 **새 프로젝트** 창을 엽니다.

2. 왼쪽 창에서 **Apache Spark/HDInsight**를 선택합니다.

3. 주 창에서 **Spark 프로젝트(Scala)** 를 선택합니다.

4. **빌드 도구** 드롭다운 목록에서 다음 값 중 하나를 선택합니다.
      * **Maven**: Scala 프로젝트 만들기 마법사 지원의 경우
      * **SBT** - 종속성 관리 및 Scala 프로젝트용 빌드의 경우

   ![IntelliJ 새 프로젝트 대화 상자](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. **다음**을 선택합니다.

6. **새 프로젝트** 창에서 다음 정보를 제공합니다.  

  	|  속성   | Description   |  
  	| ----- | ----- |  
  	|프로젝트 이름| 이름을 입력합니다.|  
  	|프로젝트&nbsp;위치| 프로젝트를 저장할 위치를 입력합니다.|
  	|프로젝트 SDK| IDEA를 처음 사용하는 경우에는 이 필드가 비어 있습니다.  **새로 만들기...** 를 만들기 JDK로 이동합니다.|
  	|Spark 버전|만들기 마법사는 Spark SDK 및 Scala SDK에 대해 적합한 버전을 통합합니다. Spark 클러스터 버전이 2.0 이전인 경우 **Spark 1.x**를 선택합니다. 그렇지 않으면 **Spark2.x**를 선택합니다. 이 예제에서는 **Spark 2.3.0(Scala 2.11.8)** 을 사용합니다.|

    ![Spark SDK를 선택하는 IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. **마침**을 선택합니다.

## <a name="create-a-standalone-scala-project"></a>독립 실행형 Scala 프로젝트 만들기

1. IntelliJ IDEA를 시작하고 **새 프로젝트 만들기**를 선택하여 **새 프로젝트** 창을 엽니다.

2. 왼쪽 창에서 **Maven**을 선택합니다.

3. **프로젝트 SDK**를 지정합니다. 비어 있을 경우 **새로 만들기...** 를 선택하고 Java 설치 디렉터리로 이동합니다.

4. **원형으로부터 만들기** 확인란을 선택합니다.  

5. 원형 목록에서 **`org.scala-tools.archetypes:scala-archetype-simple`** 을 선택합니다. 이 원형은 올바른 디렉터리 구조를 만들고 Scala 프로그램 작성에 필요한 기본 종속성을 다운로드합니다.

    ![`Maven 프로젝트를 만드는 IntelliJ IDEA`](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. **다음**을 선택합니다.

7. **아티팩트 좌표**를 확장합니다. **GroupId** 및 **ArtifactId**에 관련 값을 제공합니다. **이름** 및 **위치**는 자동으로 채워집니다. 이 자습서에서는 다음 값이 사용됩니다.

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

    ![`Maven 프로젝트를 만드는 IntelliJ IDEA`](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. **다음**을 선택합니다.

9. 설정을 검토한 후 **다음**을 선택합니다.

10. 프로젝트 이름과 위치를 확인하고 **마침**을 선택합니다.  이 프로젝트는 가져오는 데 몇 분 정도 걸립니다.

11. 프로젝트를 가져왔으면 왼쪽 창에서 **SparkSimpleApp** > **src** > **test** > **scala** > **com** > **microsoft** > **spark** > **example**로 이동합니다.  **MySpec**을 마우스 오른쪽 단추로 클릭한 다음, **삭제...** 를 선택합니다. 애플리케이션에 이 파일이 필요하지는 않습니다.  대화 상자에서 **확인**을 선택합니다.
  
12. 이후 단계에서는 **pom.xml**을 업데이트하여 Spark Scala 애플리케이션에 대한 종속성을 정의합니다. 다운로드되고 자동으로 해결되는 이러한 종속성의 경우 Maven을 구성해야 합니다.

13. **파일** 메뉴에서 **설정**을 선택하여 **설정** 창을 엽니다.

14. **설정** 창에서 **빌드, 실행, 배포** > **빌드 도구** > **Maven** > **가져오기**로 이동합니다.

15. **Maven 프로젝트 자동으로 가져오기** 확인란을 선택합니다.

16. **적용**을 선택한 다음 **확인**을 선택합니다.  그러면 프로젝트 창으로 돌아갑니다.

    ![자동 다운로드를 위해 Maven 구성](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. 왼쪽 창에서 **src** > **main** > **scala** > **com.microsoft.spark.example**로 이동하고 **앱**을 두 번 클릭하여 App.scala를 엽니다.

18. 기존 샘플 코드를 다음 코드로 바꾸고 변경 내용을 저장합니다. 이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽습니다. 여섯 번째 열에 한 자리 수만 있는 행을 검색합니다. 그리고 클러스터의 기본 스토리지 컨테이너 아래에 **/HVACOut**에 출력을 씁니다.

    ```scala
    package com.microsoft.spark.example

    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    /**
      * Test IO to wasb
      */
    object WasbIOTest {
        def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACout")
        }
    }
    ```

19. 왼쪽 창에서 **pom.xml**을 두 번 클릭합니다.  

20. `<project>\<properties>`에 다음 세그먼트를 추가합니다.

    ```xml
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11.8</scala.compat.version>
    <scala.binary.version>2.11</scala.binary.version>
    ```

21. `<project>\<dependencies>`에 다음 세그먼트를 추가합니다.

    ```xml
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_${scala.binary.version}</artifactId>
        <version>2.3.0</version>
    </dependency>
    ```

    pom.xml에 변경 내용을 저장합니다.

22. .jar 파일을 만듭니다. IntelliJ IDEA는 프로젝트의 아티팩트로 JAR을 작성할 수 있습니다. 다음 단계를 수행합니다.

    1. **파일** 메뉴에서 **프로젝트 구조...** 를 선택합니다.

    2. **프로젝트 구조** 창에서 **아티팩트** > **더하기 기호 +**  > **JAR** > **종속 항목이 있는 모듈에서...** 로 이동합니다.

        ![`jar를 추가하는 IntelliJ IDEA 프로젝트 구조`](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. **모듈에서 JAR 만들기** 창의 **주 클래스** 텍스트 상자에서 폴더 아이콘을 선택합니다.

    4. **주 클래스 선택** 창에서 기본적으로 표시되는 클래스를 선택한 다음, **확인**을 선택합니다.

        ![`클래스를 선택하는 IntelliJ IDEA 프로젝트 구조`](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. **모듈에서 JAR 만들기** 창에서 **대상 JAR에 추출** 옵션이 선택되었는지 확인한 다음, **확인**을 선택합니다.  이 설정을 사용하면 모든 종속성이 있는 단일 JAR이 만들어집니다.

        ![모듈의 IntelliJ IDEA 프로젝트 구조 jar](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. **출력 레이아웃** 탭에는 Maven 프로젝트의 일부분으로 포함된 jar이 모두 나열됩니다. 직접 종속성이 없는 Scala 애플리케이션을 선택하고 삭제할 수 있습니다. 여기에서 만드는 애플리케이션의 경우 마지막 것(**SparkSimpleApp 컴파일 출력**)을 제외한 모두를 제거할 수 있습니다. jar을 선택하여 삭제한 다음, 빼기 기호 **-** 를 선택합니다.

        ![`출력을 삭제하는 IntelliJ IDEA 프로젝트 구조`](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        **프로젝트 빌드에 포함** 확인란이 선택되어 있는지 확인합니다. 이 옵션을 사용하면 프로젝트를 빌드하거나 업데이트할 때마다 jar이 생성됩니다. **적용**을 선택한 다음, **확인**을 선택합니다.

    7. jar을 만들려면 **빌드** > **빌드 아티팩트** > **빌드**로 이동합니다. 프로젝트가 약 30초 후에 컴파일됩니다.  **\out\artifacts** 아래에 출력 jar이 만들어집니다.

        ![IntelliJ IDEA 프로젝트 아티팩트 출력](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Apache Spark 클러스터에서 애플리케이션 실행

클러스터에서 애플리케이션을 실행하려면 다음 방법을 사용할 수 있습니다.

* 클러스터와 연결된 **Azure Storage Blob에 애플리케이션 jar을 복사**합니다. **AzCopy** 명령줄 유틸리티를 사용하면 이 작업을 수행할 수 있습니다. 데이터를 업로드하는 데 사용할 수 있는 다른 클라이언트도 많이 있습니다. [HDInsight에서 Apache Hadoop 작업용 데이터 업로드](../hdinsight-upload-data.md)에서 자세한 정보를 찾을 수 있습니다.

* **Apache Livy를 사용하여 애플리케이션 작업을 원격으로** Spark 클러스터에 제출합니다. HDInsight의 Spark 클러스터에는 Spark 작업을 원격으로 제출하는 REST 엔드포인트를 노출하는 Livy가 포함됩니다. 자세한 내용은 [HDInsight의 Spark 클러스터와 함께 Apache Livy를 사용하여 원격으로 Apache Spark 작업 제출](apache-spark-livy-rest-interface.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 생성된 클러스터를 삭제합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 맨 위에 있는 **검색** 상자에 **HDInsight**를 입력합니다.

1. **서비스**에서 **HDInsight 클러스터**를 선택합니다.

1. 표시되는 HDInsight 클러스터 목록에서 이 자습서용으로 만든 클러스터 옆에 있는 **...** 를 선택합니다.

1. **삭제**를 선택합니다. **예**를 선택합니다.

![`클러스터를 삭제하는 HDInsight Azure Portal`](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "HDInsight 클러스터 삭제")

## <a name="next-step"></a>다음 단계

이 문서에서는 Apache Spark Scala 애플리케이션을 만드는 방법을 배웠습니다. 다음 문서를 진행하여 Livy를 사용하여 HDInsight Spark 클러스터에서 이 애플리케이션을 실행하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
>[Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](./apache-spark-livy-rest-interface.md)
