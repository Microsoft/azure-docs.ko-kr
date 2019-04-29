---
title: Hortonworks 샌드박스에서 Azure Toolkit for IntelliJ 사용
description: Hortonworks 샌드박스에서 IntelliJ용 Azure 도구 키트의 HDInsight Tools 사용 방법을 알아봅니다.
keywords: hadoop 도구,hive 쿼리,intellij,hortonworks 샌드박스,intellij용 azure 도구 키트
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 6ae271fc464e2a5735ef95a428b3070066058ddc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129232"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용

IntelliJ용 HDInsight 도구를 사용하여 컴퓨터에서 실행되는 [Hortonworks 샌드박스](https://hortonworks.com/products/sandbox/)에서 Apache Scala 애플리케이션을 개발한 다음, 애플리케이션을 테스트하는 방법을 알아봅니다. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/)는 컴퓨터 소프트웨어를 개발하기 위한 Java IDE(통합 개발 환경)입니다. Hortonworks 샌드박스에서 애플리케이션을 개발하고 테스트한 후에 [Azure HDInsight](apache-hadoop-introduction.md)로 애플리케이션을 이동할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 항목이 있어야 합니다.

- 로컬 컴퓨터에서 실행되는 Hortonworks 샌드박스의 HDP(Hortonworks Data Platform) 2.4. HDP를 설정하려면 [가상 머신에서 Hadoop 샌드박스를 사용하여 Apache Hadoop 에코시스템 시작](apache-hadoop-emulator-get-started.md)을 참조하세요. 
    > [!NOTE]
    > IntelliJ용 HDInsight Tools는 HDP 2.4에서만 테스트되었습니다. HDP 2.4를 가져오려면 [Hortonworks 샌드박스 다운로드 사이트](https://hortonworks.com/downloads/#sandbox)에서 **Hortonworks 샌드박스 보관**을 확장합니다.

- [JDK(Java Developer Kit) 버전 1.8 이상](https://aka.ms/azure-jdks) IntelliJ용 Azure 도구 키트에 JDK가 필요합니다.

- [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download)과 [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) 플러그 인 및 [IntelliJ용 Azure 도구 키트](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) 플러그 인. IntelliJ용 HDInsight 도구는 IntelliJ용 Azure 도구 키트의 일부로 사용할 수 있습니다. 

플러그 인을 설치하려면:

  1. IntelliJ IDEA를 엽니다.
  2. **시작** 페이지에서 **구성**을 선택한 다음 **플러그 인**을 선택합니다.
  3. 왼쪽 아래 모서리에서 **JetBrains 플러그 인 설치**를 선택합니다.
  4. 검색 기능을 사용하여 **Scala**를 검색한 후 **설치**를 선택합니다.
  5. 설치를 완료하려면 **IntelliJ IDEA 다시 시작**을 선택합니다.
  6. 4~5단계를 반복하여 **IntelliJ용 Azure 도구 키트**를 설치합니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트 설치](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)를 참조하세요.

## <a name="create-an-apache-spark-scala-application"></a>Apache Spark Scala 애플리케이션 만들기

이 섹션에서는 IntelliJ IDEA를 사용하여 샘플 Scala 프로젝트를 만듭니다. 다음 섹션에서는 프로젝트를 제출하기 전에 Hortonworks 샌드박스(에뮬레이터)에 IntelliJ IDEA를 연결합니다.

1. 컴퓨터에서 IntelliJ IDEA를 엽니다. **새 프로젝트** 대화 상자에서 다음 단계를 완료합니다.

   1. **HDInsight** > **HDInsight의 Spark(Scala)** 를 선택합니다.
   2. **빌드 도구** 목록에서 시나리오에 따라 다음 중 하나를 선택합니다.

      * **Maven**: Scala 프로젝트 만들기 마법사 지원의 경우
      * **SBT**: 종속성 관리 및 Scala 프로젝트에 대한 빌드의 경우

   ![새 프로젝트 대화 상자](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. **다음**을 선택합니다.
3. 다음 **새 프로젝트** 대화 상자에서 다음 단계를 완료합니다.

   1. **프로젝트 이름** 상자에 프로젝트 이름을 입력합니다.
   2. **프로젝트 위치** 상자에 프로젝트 위치를 입력합니다.
   3. **프로젝트 SDK** 드롭다운 목록 옆의 **새로 만들기**를 선택하고 **JDK**를 선택한 다음 Java JDK 버전 1.7 이상의 폴더를 지정합니다. Spark 2.x 클러스터에 대해 **Java 1.8**을 선택합니다. Spark 1.x 클러스터에 대해 **Java 1.7**을 선택합니다. 기본 위치는 C:\Program Files\Java\jdk1.8.x_xxx입니다.
   4. **Spark 버전** 드롭다운 목록에서 Scala 프로젝트 생성 마법사는 Spark SDK 및 Scala SDK에 대한 올바른 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 **Spark 1.x**를 선택합니다. 그렇지 않은 경우 **Spark 2.x**를 선택합니다. 이 예제에서는 Spark 1.6.2(Scala 2.10.5)를 사용합니다. **Scala 2.10.x**가 표시된 리포지토리를 사용하고 있는지 확인합니다. Scala 2.11.x가 표시된 리포지토리를 사용하지 마십시오.
    
      ![IntelliJ Scala 프로젝트 속성 만들기](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. **마침**을 선택합니다.
5. **프로젝트** 뷰가 열려 있지 않은 경우 **Alt + 1** 키를 눌러 엽니다.
6. **프로젝트 탐색기**에서 프로젝트를 확장한 다음 **src**를 선택합니다.
7. **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **Scala 클래스**를 선택합니다.
8. **이름** 상자에 이름을 입력합니다. **종류** 상자에서 **개체**를 선택합니다. 그런 다음 **확인**을 선택합니다.

    ![새 Scala 클래스 만들기 대화 상자](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. .scala 파일에서 다음 코드를 붙여 넣습니다.

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. **빌드** 메뉴에서 **프로젝트 빌드**를 선택합니다. 컴파일이 성공적으로 완료되었는지 확인합니다.


## <a name="link-to-the-hortonworks-sandbox"></a>Hortonworks 샌드박스에 연결

Hortonworks 샌드박스(에뮬레이터)에 연결하려면 기존 IntelliJ 애플리케이션이 있어야 합니다.

에뮬레이터에 연결하려면:

1. IntelliJ에서 프로젝트를 엽니다.
2. **보기** 메뉴에서 **도구 창**, **Azure 탐색기**를 차례로 선택합니다.
3. **Azure**를 확장하고 **HDInsight**를 마우스 오른쪽 단추로 클릭한 다음 **에뮬레이터에 연결**을 선택합니다.
4. **새 에뮬레이터 연결** 대화 상자에 Hortonworks 샌드박스의 루트 계정에 대해 설정한 암호를 입력합니다. 다음으로, 다음 스크린샷에 사용된 것과 유사한 값을 입력합니다. 그런 다음 **확인**을 선택합니다. 

   ![새 에뮬레이터 연결 대화 상자](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. 에뮬레이터를 구성하려면 **예**를 선택합니다.

에뮬레이터가 성공적으로 연결되면 HDInsight 노드에 에뮬레이터(Hortonworks 샌드박스)가 나열됩니다.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Hortonworks 샌드박스에 Spark Scala 애플리케이션 제출

IntelliJ IDEA를 에뮬레이터에 연결한 후에는 프로젝트를 제출할 수 있습니다.

에뮬레이터에 프로젝트를 제출하려면:

1. **프로젝트 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **HDInsight에 Spark 애플리케이션 제출**을 선택합니다.
2. 다음 단계를 완료합니다.

    1. **Spark 클러스터(Linux만 해당)** 드롭다운 목록에서 로컬 Hortonworks 샌드박스를 선택합니다.
    2. **기본 클래스 이름** 상자에서 기본 클래스 이름을 선택하거나 입력합니다. 이 자습서의 경우 이름은 **GroupByTest**입니다.

3. **제출**을 선택합니다. 작업 제출 로그가 Spark 제출 도구 창에 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Toolkit for IntelliJ의 HDInsight 도구를 사용하여 HDInsight Spark Linux 클러스터용 Apache Spark 애플리케이션을 만드는](../spark/apache-spark-intellij-tool-plugin.md) 방법을 알아봅니다.

- IntelliJ용 HDInsight 도구에 대한 비디오는 [Apache Spark 개발을 위한 IntelliJ용 HDInsight 도구 소개](https://www.youtube.com/watch?v=YTZzYVgut6c)를 참조하세요.

- [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 HDInsight 클러스터에서 원격으로 Apache Spark 애플리케이션을 디버그](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)하는 방법을 알아봅니다.

- [HDInsight Spark Linux 클러스터에서 Azure Toolkit for IntelliJ의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)하는 방법을 알아봅니다.

- [Azure Toolkit for Eclipse의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션을 만드는](../spark/apache-spark-eclipse-tool-plugin.md) 방법을 알아봅니다.

- Eclipse용 HDInsight 도구에 대한 비디오는 [Eclipse용 HDInsight 도구를 사용하여 Spark 애플리케이션 만들기](https://mix.office.com/watch/1rau2mopb6fha)를 참조하세요.
