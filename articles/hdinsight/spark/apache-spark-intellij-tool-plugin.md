---
title: 'Azure Toolkit for IntelliJ: HDInsight 클러스터용 Spark 애플리케이션 만들기 '
description: Azure Toolkit for IntelliJ를 사용하여 Scala로 작성된 Spark 애플리케이션을 개발한 후 HDInsight Spark 클러스터로 제출합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 682b2266442f3aba6696385862844e1b4b93aa3d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124257"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터용 Apache Spark 애플리케이션 만들기

Azure Toolkit for IntelliJ 플러그 인을 사용하여 [Scala](https://www.scala-lang.org/)로 작성된 [Apache Spark](https://spark.apache.org/) 애플리케이션을 개발한 후 IntelliJ IDE(통합 개발 환경)에서 직접 HDInsight Spark 클러스터로 제출합니다. 다음과 같은 몇 가지 방식으로 플러그 인을 사용할 수 있습니다.

* HDInsight Spark 클러스터에서 Scala Spark 애플리케이션을 개발 및 제출합니다.
* Azure HDInsight Spark 클러스터 리소스에 액세스합니다.
* Scala Spark 애플리케이션을 로컬로 개발 및 실행합니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* [Oracle Java Development 키트](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  이 자습서에서는 Java 버전 8.0.202를 사용합니다.
* IntelliJ IDEA. 이 문서에서는 [IntelliJ IDEA 커뮤니티 버전  2018.3.4](https://www.jetbrains.com/idea/download/)를 사용합니다.
* Azure Toolkit for IntelliJ.  [Azure Toolkit for IntelliJ 설치](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)를 참조하세요.
* WINUTILS.EXE.  [Windows의 Hadoop 실행 문제](https://wiki.apache.org/hadoop/WindowsProblems)를 참조하세요.

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA용 Scala 플러그 인 설치
Scala 플러그인을 설치하려면 다음 단계를 수행하세요.

1. IntelliJ IDEA를 엽니다.

2. 시작 화면에서 **구성** > **플러그인**으로 이동하여 **플러그인** 창을 엽니다.
   
    ![scala 플러그 인 활성화](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. 새 창에 제공되는 Scala 플러그인에 대해 **설치**를 선택합니다.  

    ![scala 플러그 인 설치](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. 플러그 인이 성공적으로 설치된 후에 IDE를 다시 시작해야 합니다.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에서 Spark Scala 애플리케이션 만들기

1. IntelliJ IDEA를 시작하고 **새 프로젝트 만들기**를 선택하여 **새 프로젝트** 창을 엽니다.

2. 왼쪽 창에서 **Azure Spark/HDInsight**를 선택합니다.

3. 주 창에서 **Spark 프로젝트(Scala)** 를 선택합니다.

4. **빌드 도구** 드롭다운 목록에서 다음 중 하나를 선택합니다.
   * **Maven**: Scala 프로젝트 만들기 마법사 지원의 경우
   * **SBT** - 종속성 관리 및 Scala 프로젝트용 빌드의 경우

     ![새 프로젝트 대화 상자](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. **다음**을 선택합니다.

6. **새 프로젝트** 창에서 다음 정보를 제공합니다.  

    |  자산   | 설명   |  
    | ----- | ----- |  
    |프로젝트 이름| 이름을 입력합니다.  이 자습서에서는 `myApp`를 사용합니다.|  
    |프로젝트&nbsp;위치| 프로젝트를 저장하기를 원하는 위치를 입력합니다.|
    |프로젝트 SDK| IDEA를 처음 사용하는 경우에는 비어 있을 수 있습니다.  **새로 만들기...** 를 만들기 JDK로 이동합니다.|
    |Spark 버전|만들기 마법사는 Spark SDK 및 Scala SDK에 대해 적합한 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 **Spark 1.x**를 선택합니다. 그렇지 않은 경우 **Spark2.x**를 선택합니다. 이 예제에서는 **Spark 2.3.0(Scala 2.11.8)** 을 사용합니다.|

    ![Spark SDK 선택](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. **마침**을 선택합니다.  프로젝트를 사용할 수 있게 되기까지 몇 분 정도 걸릴 수 있습니다.

8. Spark 프로젝트가 자동으로 아티팩트를 만듭니다. 이 아티팩트를 보려면 다음을 수행합니다.

   a. 메뉴 모음에서 **파일** > **프로젝트 구조...** 로 이동합니다.

   b. **프로젝트 구조** 창에서 **아티팩트**를 선택합니다.  

   다. 아티팩트를 확인한 후 **취소**를 선택합니다.

      ![대화 상자의 아티팩트 정보](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. 다음을 수행하여 애플리케이션 소스 코드를 추가합니다.

    a. 프로젝트에서 **myApp** > **src** > **main** > **scala**로 이동합니다.  

    b. 마우스 오른쪽 단추로 **scala**를 클릭하고 **새로 만들기** > **Scala 클래스**로 이동합니다.

   ![프로젝트에서 Scala 클래스를 만들기 위한 명령](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   다. **새 Scala 클래스 만들기** 대화 상자에서 이름을 제공하고 **종류** 드롭다운 목록에서 **개체**를 선택한 다음, **확인**을 선택합니다.

     ![새 Scala 클래스 만들기 대화 상자](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. 그러면 **myApp.scala** 파일이 기본 보기에서 열립니다. 기본 코드를 아래 코드로 바꿉니다.  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    이 코드는 HVAC.csv(모든 HDInsight Spark 클러스터에서 사용 가능)에서 데이터를 읽고, CSV 파일의 일곱 번째 열에 한 자리 수만 있는 행을 검색하고, 출력을 클러스터의 기본 저장 컨테이너 아래의 `/HVACOut`에 씁니다.

## <a name="connect-to-your-hdinsight-cluster"></a>HDInsight 클러스터 연결
[Azure 구독에 로그인](#sign-in-to-your-azure-subscription)하거나 Ambari 사용자 이름/암호 또는 도메인 가입 자격 증명을 사용하여 [HDInsight 클러스터에 연결](#link-a-cluster)할 수 있습니다.

### <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

1. 메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기**로 이동합니다.
       
   ![Azure 탐색기 링크](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Azure 탐색기에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음, **로그인**을 선택합니다.

3. **Azure 로그인** 대화 상자에서 **로그인**을 선택하고 Azure 자격 증명을 입력합니다.

    ![Azure 로그인 대화 상자](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. 로그인하고 나면 **구독 선택** 대화 상자에 자격 증명과 연결된 모든 Azure 구독의 목록이 표시됩니다. 구독을 선택한 후 **선택** 단추를 선택합니다.

    ![구독 선택 대화 상자](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. **Azure 탐색기**에서 **HDInsight**를 확장하여 구독에 포함된 HDInsight Spark 클러스터를 표시합니다.

    ![Azure 탐색기의 HDInsight Spark 클러스터](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. 클러스터와 연결된 리소스(예: 저장소 계정)를 표시하려면 클러스터 이름 노드를 더 확장할 수 있습니다.

    ![확장된 클러스터-이름 노드](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>클러스터 연결
Apache Ambari 관리 사용자 이름을 사용하여 HDInsight 클러스터에 연결할 수 있습니다. 마찬가지로, 도메인에 가입된 HDInsight 클러스터의 경우, user1@contoso.com과 같이 도메인 및 사용자 이름을 사용하여 연결할 수 있습니다. 또한 Livy 서비스 클러스터를 연결할 수 있습니다.

1. 메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기**로 이동합니다.

2. Azure 탐색기에서 **HDInsight** 노드를 마우스 오른쪽 단추로 클릭한 다음, **클러스터 연결**을 선택합니다.

   ![클러스터 연결 상황에 맞는 메뉴](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. **클러스터 연결** 창에 표시되는 사용 가능한 옵션은 **링크 리소스 유형** 드롭다운 목록에서 선택한 값에 따라 다릅니다.  값을 입력하고 **확인**을 선택합니다.

    * **HDInsight 클러스터**  
  
        |자산 |값 |
        |----|----|
        |링크 리소스 유형|드롭다운 목록에서 **HDInsight 클러스터**를 선택합니다.|
        |클러스터 이름/URL| 클러스터 이름을 입력합니다.|
        |인증 유형| **기본 인증** 상태로 유지|
        |사용자 이름| 클러스터 사용자 이름을 입력합니다. 기본값은 admin입니다.|
        |암호| 사용자 이름에 대한 암호를 입력합니다.|
    
        ![hdinsight 클러스터 연결 대화 상자](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy 서비스**  
  
        |자산 |값 |
        |----|----|
        |링크 리소스 유형|드롭다운 목록에서 **Livy 서비스**를 선택합니다.|
        |Livy 엔드포인트| Livy 엔드포인트를 입력합니다.|
        |클러스터 이름| 클러스터 이름을 입력합니다.|
        |Yarn 엔드포인트|선택 사항입니다.|
        |인증 유형| **기본 인증** 상태로 유지|
        |사용자 이름| 클러스터 사용자 이름을 입력합니다. 기본값은 admin입니다.|
        |암호| 사용자 이름에 대한 암호를 입력합니다.|

        ![livy 클러스터 연결 대화 상자](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. **HDInsight** 노드에서 연결된 클러스터를 볼 수 있습니다.

   ![연결된 클러스터](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. **Azure Explorer**에서 클러스터 연결을 해제할 수도 있습니다.

   ![연결되지 않은 클러스터](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에서 Spark Scala 애플리케이션 실행
Scala 애플리케이션을 작성한 후 이를 클러스터에 제출할 수 있습니다.

1. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp**으로 이동합니다.  **myApp**을 마우스 오른쪽 단추로 클릭하고 **Spark 애플리케이션 제출**(일반적으로 목록 맨 아래에 표시됨)을 선택합니다.
    
      ![HDInsight에 Spark 애플리케이션 제출 명령](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. **Spark 애플리케이션 제출** 대화 상자 창에서 **1. HDInsight의 Spark**를 선택합니다.

3. **구성 편집** 창에서 다음 값을 제공하고 **확인**을 선택합니다.

    |자산 |값 |
    |----|----|
    |Spark 클러스터(Linux 전용)|애플리케이션을 실행하려는 HDInsight Spark 클러스터를 선택합니다.|
    |제출할 아티팩트를 선택합니다.|기본 설정을 유지합니다.|
    |기본 클래스 이름|기본값은 선택한 파일의 기본 클래스입니다. 줄임표(**...**)를 선택하고 다른 클래스를 선택하여 클래스를 변경할 수 있습니다.|
    |작업 구성|기본 키 및/또는 값을 변경할 수 있습니다. 자세한 내용은 [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html)를 참조하세요.|
    |명령줄 인수|필요한 경우 기본 클래스에 대해 공백으로 구분된 인수를 입력할 수 있습니다.|
    |참조된 Jar 및 참조된 파일|참조되는 Jar 및 파일(있는 경우)의 경로를 입력할 수 있습니다. 자세한 내용은 다음을 참조하세요. [Apache Spark 구성](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)  [클러스터에 리소스를 업로드하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)을 참조하세요.|
    |작업 업로드 스토리지|추가 옵션을 표시하려면 확장합니다.|
    |저장소 유형|드롭다운 목록에서 **Azure Blob을 사용하여 업로드**를 선택합니다.|
    |Storage 계정|스토리지 계정을 입력합니다.|
    |스토리지 키|스토리지 키를 입력합니다.|
    |저장소 컨테이너|**스토리지 계정** 및 **스토리지 키**를 입력했으면 드롭다운 목록에서 스토리지 컨테이너를 선택합니다.|

    ![Spark 제출 대화 상자](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. **SparkJobRun**을 선택하여 선택된 클러스터에 프로젝트를 제출합니다. 맨 아래의 **Remote Spark Job in Cluster**(클러스터의 원격 Spark 작업) 탭은 작업 실행 진행 상황을 표시합니다. 빨간색 단추를 클릭하여 애플리케이션을 중지할 수 있습니다. 작업 출력에 액세스하는 방법을 알아보려면 이 문서의 뒷부분에 나오는 "IntelliJ용 Azure 도구 키트를 사용하여 HDInsight Spark 클러스터 액세스 및 관리" 섹션을 참조하세요.  
      
    ![Spark 제출 창](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>HDInsight 클러스터에서 로컬 또는 원격으로 Apache Spark 애플리케이션 디버그 
Spark 애플리케이션을 클러스터에 제출하는 또 다른 권장되는 방법이 있습니다. **구성 실행/디버그** IDE에서 매개 변수를 설정하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 HDInsight 클러스터에서 로컬 또는 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-debug-remotely-through-ssh.md)를 참조하세요.

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트를 사용하여 HDInsight Spark 클러스터 액세스 및 관리
IntelliJ용 Azure 도구 키트를 사용하여 다양한 작업을 수행할 수 있습니다.  대부분의 작업은 **Azure 탐색기**에서 시작합니다.  메뉴 모음에서 **보기** > **도구 창** > **Azure 탐색기**로 이동합니다.

### <a name="access-the-job-view"></a>작업 보기 액세스

1. Azure 탐색기에서 **HDInsight** > \<클러스터 >> **작업**으로 이동합니다.

    ![작업 보기 노드](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. 오른쪽 창의 **Spark 작업 보기** 탭에는 클러스터에서 실행된 모든 애플리케이션이 표시됩니다. 자세한 내용을 보려면 원하는 애플리케이션 이름을 선택합니다.

    ![애플리케이션 세부 정보](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. 실행 중인 기본 작업 정보를 표시하려면 작업 그래프 위로 마우스를 가져갑니다. 모든 작업이 생성하는 단계 그래프 및 정보를 보려면 작업 그래프에서 노드를 선택합니다.

    ![작업 단계 세부 정보](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. *드라이버 Stderr*, *드라이버 Stdout* 및 *디렉터리 정보*와 같은 자주 사용되는 로그를 보려면 **로그** 탭을 선택합니다.

    ![로그 세부 정보](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. 창 맨 위에 있는 링크를 클릭하여 Spark 기록 UI 및 YARN UI(애플리케이션 수준)를 볼 수도 있습니다.

### <a name="access-the-spark-history-server"></a>Spark 기록 서버 액세스

1. Azure 탐색기에서 **HDInsight**를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음, **Spark 기록 UI 열기**를 선택합니다.  
2. 메시지가 나타나면 클러스터를 설정할 때 지정한 클러스터의 관리자 자격 증명을 입력합니다.

3. Spark 기록 서버 대시보드에서 애플리케이션 이름을 사용하여 방금 실행을 마친 애플리케이션을 찾을 수 있습니다. 위의 코드에서 `val conf = new SparkConf().setAppName("myApp")`을 사용하여 애플리케이션 이름을 설정합니다. 따라서 Spark 애플리케이션 이름은 **myApp**입니다.

### <a name="start-the-ambari-portal"></a>Ambari 포털 시작

1. Azure 탐색기에서 **HDInsight**를 확장하고, 마우스 오른쪽 단추로 Spark 클러스터 이름을 클릭한 다음, **클러스터 관리 포털(Ambari) 열기**를 선택합니다.  

2. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 클러스터 설치 프로세스 동안 이러한 자격 증명을 지정했을 것입니다.

### <a name="manage-azure-subscriptions"></a>Azure 구독 관리
기본적으로 IntelliJ용 Azure 도구 키트에는 모든 Azure 구독의 Spark 클러스터가 나열됩니다. 필요한 경우 액세스하려는 구독을 지정할 수 있습니다.  

1. Azure 탐색기에서 **Azure** 루트 노드를 마우스 오른쪽 단추로 클릭한 다음, **구독 선택**을 선택합니다.  

2. **구독 선택** 창에서 액세스하지 않으려는 구독 옆의 확인란을 선택 취소하고 **닫기**를 선택합니다.

## <a name="spark-console"></a>Spark 콘솔
Spark 로컬 콘솔(Scala)을 실행하거나 Spark Livy 대화형 세션 콘솔(Scala)을 실행할 수 있습니다.

### <a name="spark-local-consolescala"></a>Spark 로컬 콘솔(Scala)
WINUTILS.EXE 필수 구성 요소를 충족했는지 확인합니다.

1. 메뉴 모음에서 **실행** > **구성 편집...** 으로 이동합니다.

2. **구성 실행/디버그** 창의 왼쪽 분할 창에서 **HDInsight의 Apache Spark** > **[HDInsight의 Spark] myApp**으로 이동합니다.

3. 기본 창에서 **로컬로 실행** 탭을 선택합니다.

4. 다음 값을 제공하고 **확인**을 선택합니다.

    |자산 |값 |
    |----|----|
    |작업 기본 클래스|기본값은 선택한 파일의 기본 클래스입니다. 줄임표(**...**)를 선택하고 다른 클래스를 선택하여 클래스를 변경할 수 있습니다.|
    |환경 변수|HADOOP_HOME 값이 올바른지 확인합니다.|
    |WINUTILS.exe 위치|경로가 올바른지 확인합니다.|

    ![로컬 콘솔 구성 설정](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp**으로 이동합니다.  

6. 메뉴 모음에서 **도구** > **Spark 콘솔** > **Spark 로컬 콘솔(Scala) 실행**으로 이동합니다.

7. 그러면 종속성을 자동 수정할 것인지 묻는 두 개의 대화 상자가 표시됩니다. 이러한 대화 상자가 표시되면 **자동 수정**을 선택합니다.

    ![Spark 자동 수정1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark 자동 수정2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. 콘솔은 아래 그림과 유사합니다. 콘솔 창에 `sc.appName`을 입력하고 Ctrl+Enter를 누릅니다.  결과가 나타납니다. 빨간색 단추를 클릭하여 로컬 콘솔을 종료할 수 있습니다.

    ![로컬 콘솔 결과](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy 대화형 세션 콘솔(Scala)
IntelliJ 2018.2 및 2018.3에서만 지원됩니다.

1. 메뉴 모음에서 **실행** > **구성 편집...** 으로 이동합니다.

2. **구성 실행/디버그** 창의 왼쪽 분할 창에서 **HDInsight의 Apache Spark** > **[HDInsight의 Spark] myApp**으로 이동합니다.

3. 주 창에서 **클러스터에서 원격으로 실행** 탭을 선택합니다.

4. 다음 값을 제공하고 **확인**을 선택합니다.

    |자산 |값 |
    |----|----|
    |Spark 클러스터(Linux 전용)|애플리케이션을 실행하려는 HDInsight Spark 클러스터를 선택합니다.|
    |기본 클래스 이름|기본값은 선택한 파일의 기본 클래스입니다. 줄임표(**...**)를 선택하고 다른 클래스를 선택하여 클래스를 변경할 수 있습니다.|

    ![대화형 콘솔 구성 설정](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. 프로젝트에서 **myApp** > **src** > **main** > **scala** > **myApp**으로 이동합니다.  

6. 메뉴 모음에서 **도구** > **Spark 콘솔** > **Spark Livy 대화형 세션 콘솔(Scala) 실행**으로 이동합니다.

7. 콘솔은 아래 그림과 유사합니다. 콘솔 창에 `sc.appName`을 입력하고 Ctrl+Enter를 누릅니다.  결과가 나타납니다. 빨간색 단추를 클릭하여 로컬 콘솔을 종료할 수 있습니다.

    ![대화형 콘솔 결과](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Spark 콘솔로 선택 내용 보내기

일부 코드를 로컬 콘솔 또는 Livy 대화식 세션 콘솔(Scala)로 보내면 편리하게 스크립트 결과를 예측할 수 있습니다. Scala 파일의 일부 코드를 강조 표시하고 **Spark 콘솔로 선택 내용 보내기**를 마우스 오른쪽 단추로 클릭합니다. 선택한 코드가 콘솔로 전송되고 실행됩니다. 결과는 콘솔의 코드 뒤에 표시됩니다. 콘솔에서 오류가 있는지 검사합니다.  

   ![Spark 콘솔로 선택 내용 보내기](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ를 사용하도록 기존 IntelliJ IDEA 애플리케이션 변환

IntelliJ IDEA에서 만든 기존 Spark Scala 애플리케이션을 Azure Toolkit for IntelliJ와 호환되도록 변환할 수 있습니다. 이렇게 하면 플러그 인을 사용하여 HDInsight Spark 클러스터에 애플리케이션을 제출할 수 있습니다.

1. IntelliJ IDEA를 통해 만들어진 기존 Spark Scala 애플리케이션의 경우 관련된 .iml 파일을 엽니다.

1. 루트 수준에 다음과 같은 **module** 요소가 표시됩니다.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   **module** 요소가 다음과 같이 표시되도록 해당 요소를 편집하여 `UniqueKey="HDInsightTool"`을 추가합니다.
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. 변경 내용을 저장합니다. 이제 애플리케이션이 Azure Toolkit for IntelliJ와 호환됩니다. 프로젝트에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하여 이 테스트를 수행할 수 있습니다. 이제 팝업 메뉴에 **HDInsight에 Spark 애플리케이션 제출** 옵션이 표시됩니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="error-in-local-run-use-a-larger-heap-size"></a>로컬 실행의 오류: *더 큰 힙 크기를 사용하세요.*
Spark 1.6에서 로컬 실행 동안 32비트 Java SDK를 사용하는 경우 다음과 같은 오류가 발생할 수 있습니다.

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

이러한 오류는 힙 크기가 실행할 Spark에 대해 충분히 크지 않기 때문에 발생합니다. Spark에는 적어도 471MB가 필요합니다. 자세한 내용은 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)을 참조하세요. 한 가지 간단한 해결 방법은 64비트 Java SDK를 사용하는 것입니다. 또한 다음 옵션을 추가하여 IntelliJ의 JVM 설정을 변경할 수도 있습니다.

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![IntelliJ의 "VM 옵션" 상자에 옵션 추가](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>FAQ
클러스터가 사용 중인 경우 아래 오류가 발생할 수 있습니다.

![클러스터가 사용 중인 경우 Intellij에 오류가 발생합니다.](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![클러스터가 사용 중인 경우 Intellij에 오류가 발생합니다.](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>사용자 의견 및 알려진 문제
현재 직접 Spark 출력 보기는 지원되지 않습니다.

제안 또는 피드백이 있거나 이 플러그 인을 사용할 때 문제가 발생하는 경우 hdivstool@microsoft.com으로 메일을 보내 주시기 바랍니다.

## <a name="seealso"></a>다음 단계
* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>데모
* Scala 프로젝트(비디오) 작성: [Apache Spark Scala 애플리케이션 만들기](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* 원격 디버그(비디오): [Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터에서 원격으로 Apache Spark 애플리케이션 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Apache Spark 및 Machine Learning: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 및 Machine Learning: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>애플리케이션 만들기 및 실행
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션 만들기](apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
