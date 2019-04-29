---
title: 'Azure Toolkit for Eclipse: HDInsight Spark용 Scala 애플리케이션 만들기 '
description: Azure Toolkit for Eclipse의 HDInsight Tools를 사용하여 Scala로 작성된 Spark 애플리케이션을 개발한 후에 Eclipse IDE에서 직접 HDInsight Spark 클러스터로 제출합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 1ae585322316a9c215fc32cc2f8ffba2f332ff61
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124376"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for Eclipse를 사용하여 HDInsight 클러스터용 Apache Spark 애플리케이션 만들기

Azure Toolkit for [Eclipse](https://www.eclipse.org/)의 HDInsight Tools를 사용하여 [Scala](https://www.scala-lang.org/)로 작성된 [Apache Spark](https://spark.apache.org/) 애플리케이션을 개발하여 Eclipse IDE에서 직접 Azure HDInsight Spark 클러스터에 제출합니다. 다음과 같은 방법으로 HDInsight Tools 플러그 인을 사용할 수 있습니다.

* HDInsight Spark 클러스터에서 Scala Spark 애플리케이션을 개발 및 제출하려면
* Azure HDInsight Spark 클러스터 리소스에 액세스하려면
* Scala Spark 애플리케이션을 로컬로 개발 및 실행하려면

> [!IMPORTANT]  
> 이 도구를 사용하면 Linux의 HDInsight Spark 클러스터용 애플리케이션만 만들고 제출할 수 있습니다.
> 
> 

## <a name="prerequisites"></a>필수 조건

* HDInsight의 Apache Spark 클러스터. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.
* Eclipse IDE 런타임에 사용되는 Oracle Java Development 키트 버전 8입니다. [Oracle 웹 사이트](https://aka.ms/azure-jdks)에서 다운로드할 수 있습니다.
* Eclipse IDE. 이 문서에서는 Eclipse Neon을 사용합니다. [Eclipse 웹 사이트](https://www.eclipse.org/downloads/)에서 설치할 수 있습니다.



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Eclipse용 Azure 도구 키트의 HDInsight 도구 및 Scala 플러그 인 설치

### <a name="install-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트 설치
Eclipse용 HDInsight Tools는 Eclipse용 Azure 도구 키트의 일부분으로 제공됩니다. 설치 지침은 [Eclipse용 Azure 도구 키트 설치](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation)를 참조하세요.

### <a name="install-the-scala-plug-in"></a>Scala 플러그 인 설치
Eclipse를 열면 HDInsight Tools는 Scala 플러그 인을 설치했는지 여부를 자동으로 검색합니다. **확인**을 선택하여 계속 진행한 다음 Eclipse Marketplace에서 해당 플러그 인을 설치하는 지침을 따릅니다.

![Scala 플러그 인 자동 설치](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

[Azure 구독에 로그인](#sign-in-to-your-azure-subscription)하거나 Ambari 사용자 이름/암호 또는 도메인 가입 자격 증명을 사용하여 [HDInsight 클러스터를 연결하여](#link-a-cluster) 시작할 수 있습니다. 

## <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.
1. Eclipse IDE를 시작하고 Azure 탐색기를 엽니다. **창** 메뉴에서 **보기 표시**를 선택한 다음 **기타**를 선택합니다. 열린 대화 상자에서 **Azure**를 확장하고 **Azure 탐색기**를 선택한 다음 **확인**을 선택합니다.

   ![보기 표시 대화 상자](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음 **로그인**을 선택합니다.
1. **Azure 로그인** 대화 상자에서 인증 모드를 선택하고 **로그인**을 선택하고 Azure 자격 증명을 입력합니다.
   
   ![Azure 로그인 대화 상자](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. 로그인하고 나면 **구독 선택** 대화 상자에 자격 증명과 연결된 모든 Azure 구독의 목록이 표시됩니다. **선택**을 클릭하여 대화 상자를 닫습니다.

   ![구독 선택 대화 상자](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. **Azure 탐색기** 탭에서 **HDInsight**를 확장하여 구독에서 HDInsight Spark 클러스터를 표시합니다.
   
   ![Azure 탐색기의 HDInsight Spark 클러스터](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. 클러스터 이름 노드를 더 확장하여 클러스터와 연결된 리소스(예: 저장소 계정)를 표시할 수 있습니다.
   
   ![클러스터 이름을 확장하여 리소스 표시](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>클러스터 연결
Ambari 관리 사용자 이름을 사용하여 일반 클러스터를 연결할 수 있습니다. 마찬가지로, 도메인에 가입된 HDInsight 클러스터의 경우, user1@contoso.com과 같이 도메인 및 사용자 이름을 사용하여 연결할 수 있습니다.

1. **Azure Explorer**에서 **클러스터 연결**을 클릭합니다.

   ![클러스터 연결 상황에 맞는 메뉴](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. **클러스터 이름**, **사용자 이름** 및 **암호**를 입력한 다음, 확인 단추를 클릭하여 클러스터에 연결합니다. 선택적으로 저장소 계정, 저장소 키를 입력한 다음, 저장소 탐색기의 저장소 컨테이너를 선택하여 왼쪽 트리 뷰에서 작업합니다.
   
   ![클러스터 연결 대화 상자](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > 클러스터가 Azure 구독 및 연결된 클러스터 모두에 로그인되어 있으면, 연결된 저장소 키, 사용자 이름 및 암호를 사용합니다.
   > ![Eclipse의 저장소 탐색기](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. 입력 정보가 올바르면 확인 단추를 클릭한 후 **HDInsight** 노드에 연결된 클러스터가 표시됩니다. 이제 애플리케이션을 연결된 클러스터에 제출할 수 있습니다.

   ![연결된 클러스터](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. **Azure Explorer**에서 클러스터 연결을 해제할 수도 있습니다.
   
   ![연결되지 않은 클러스터](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에 Spark Scala 프로젝트 설정

1. Eclipse IDE 작업 영역에서 **파일**을 선택하고 **새로 만들기**를 선택한 후 **프로젝트**를 선택합니다. 
1. 새 프로젝트 마법사에서 **HDInsight**를 확장하고 **HDInsight의 Spark(Scala)** 를 선택한 후 **다음**을 선택합니다.

   ![HDInsight의 Spark(Scala) 프로젝트 선택](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. Scala 프로젝트 생성 마법사는 Scala 플러그 인이 설치되어 있는지 여부를 자동으로 검색합니다. **확인**을 선택하여 Scala 플러그 인 다운로드를 계속 진행한 후 지침에 따라 Eclipse를 다시 시작합니다.

   ![Scala 확인](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. **새 HDInsight Scala 프로젝트** 대화 상자에서 다음 값을 입력한 후 **다음**을 선택합니다.
   * 프로젝트의 이름을 입력합니다.
   * **JRE** 영역에서 **JRE 실행 환경 사용**을 **JavaSE-1.7** 이상으로 설정해야 합니다.
   * **Spark 라이브러리** 영역에서 **Maven을 사용하여 Spark SDK 구성** 옵션을 선택할 수 있습니다.  이 도구는 Spark SDK 및 Scala SDK에 대해 적합한 버전을 통합합니다. **Spark SDK를 수동으로 추가** 옵션을 선택하고 수동으로 Spark SDK를 다운로드하여 추가할 수 있습니다.

   ![새 HDInsight Scala 프로젝트 대화 상자](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. 다음 대화 상자에서 **마침**을 선택합니다. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark 클러스터에 대한 Scala 애플리케이션 만들기

1. Eclipse IDE의 패키지 탐색기에서 앞서 만든 프로젝트를 확장하고 **src**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 가리킨 다음 **기타**를 선택합니다.
1. **마법사 선택** 대화 상자에서 **Scala 마법사**를 확장하고 **Scala 개체**, **다음**을 차례로 선택합니다.
   
   ![마법사 대화 상자 선택](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. **새 파일 만들기** 대화 상자에서 개체 이름을 입력한 다음 **마침**을 선택합니다.
   
   ![새 파일 대화 상자 만들기](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. 텍스트 편집기에 다음 코드를 붙여 넣습니다.
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
1. HDInsight Spark 클러스터에서 애플리케이션 실행:
   
   a. 패키지 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 다음 **HDInsight에 Spark 애플리케이션 제출**을 선택합니다.        
   b. **Spark 제출** 대화 상자에 다음 값을 제공한 다음 **제출**을 선택합니다.
      
   * **클러스터 이름**의 경우 애플리케이션을 실행하려는 HDInsight Spark 클러스터를 선택합니다.
   * Eclipse 프로젝트에서 아티팩트를 선택하거나 하드 디스크에서 아티팩트를 선택합니다. 기본값은 패키지 탐색기에서 마우스 오른쪽 단추로 클릭한 항목에 따라 달라집니다.
   * **기본 클래스 이름** 드롭다운 목록에서 제출 마법사에는 프로젝트의 모든 개체 이름이 표시됩니다. 실행하려는 이름을 선택하거나 입력합니다. 하드 드라이브에서 아티팩트를 선택한 경우 주 클래스 이름을 수동으로 입력해야 합니다. 
   * 이 예제의 애플리케이션 코드에는 명령줄 인수가 필요하지 않고 JAR 또는 파일을 참조하지 않으므로 나머지 텍스트 상자를 비워둘 수 있습니다.
        
     ![Spark 제출 대화 상자](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. **Spark 제출** 탭에 진행 상태가 표시되기 시작합니다. **Spark 제출** 창에서 빨간색 단추를 선택하여 애플리케이션을 중지할 수 있습니다. 구형 아이콘(이미지에 파란색 상자로 표시됨)을 선택하여 특정 애플리케이션을 실행하는 로그를 볼 수도 있습니다.
      
   ![Spark 제출 창](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트의 HDInsight Tools를 사용하여 HDInsight Spark 클러스터 액세스 및 관리
HDInsight Tools를 사용하여 작업 출력에 액세스를 포함한 다양한 작업을 수행할 수 있습니다.

### <a name="access-the-job-view"></a>작업 보기 액세스
1. Azure 탐색기에서 **HDInsight**, Spark 클러스터 이름을 차례로 확장한 다음 **작업**을 선택합니다. 

   ![작업 보기 노드](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. **작업** 노드를 선택합니다. Java 버전이 **1.8**보다 낮으면 HDInsight Tools가 자동으로 **E(fx)clipse** 플러그인 설치를 미리 알립니다. **확인**을 선택하여 계속 진행한 다음 마법사에 따라 Eclipse Marketplace에서  Eclipse를 설치하고 다시 시작합니다. 

   ![E(fx)clipse 설치](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. **작업** 노드에서 작업 보기를 엽니다. 오른쪽 창의 **Spark 작업 보기** 탭에는 클러스터에서 실행된 모든 애플리케이션이 표시됩니다. 자세한 내용을 보려면 원하는 애플리케이션 이름을 선택합니다.

   ![애플리케이션 세부 정보](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   다음 작업 중 하나를 수행할 수 있습니다.

   * 작업 그래프로 마우스를 가져갑니다. 실행 중인 작업에 대한 기본 정보가 표시됩니다. 작업 그래프를 선택하면 모든 작업이 생성하는 단계 및 정보가 표시됩니다.

     ![작업 단계 세부 정보](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * **드라이버 Stderr**, **드라이버 Stdout** 및 **디렉터리 정보**와 같은 자주 사용되는 로그를 보려면 **로그** 탭을 선택합니다.

     ![로그 세부 정보](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * 창 맨 위에 있는 하이퍼링크를 선택하여 Spark 기록 UI 및 Apache Hadoop YARN UI(애플리케이션 수준)를 엽니다.

### <a name="access-the-storage-container-for-the-cluster"></a>클러스터에 대한 저장소 컨테이너 액세스
1. Azure 탐색기에서 **HDInsight** 루트 노드를 확장하여 사용할 수 있는 HDInsight Spark 클러스터의 목록을 표시합니다.
1. 클러스터 이름을 확장하여 클러스터에 대한 저장소 계정 및 기본 저장소 컨테이너를 표시합니다.
   
   ![Storage 계정 및 기본 스토리지 컨테이너](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. 클러스터와 연결된 저장소 컨테이너 이름을 선택합니다. 오른쪽 창에서 **HVACOut** 폴더를 두 번 클릭합니다. **파트-** 파일 중 하나를 열어 애플리케이션의 출력을 표시합니다.

### <a name="access-the-spark-history-server"></a>Spark 기록 서버 액세스
1. Azure 탐색기에서 Spark 클러스터 이름을 마우스 오른쪽 단추로 클릭한 다음 **Spark 기록 UI 열기**를 선택합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정한 것입니다.
1. Spark 기록 서버 대시보드에서 애플리케이션 이름을 사용하여 방금 실행을 마친 애플리케이션을 찾을 수 있습니다. 위의 코드에서 `val conf = new SparkConf().setAppName("MyClusterApp")`을 사용하여 애플리케이션 이름을 설정합니다. 따라서 Spark 애플리케이션의 이름은 **MyClusterApp**입니다.

### <a name="start-the-apache-ambari-portal"></a>Apache Ambari 포털 시작
1. Azure 탐색기에서 Spark 클러스터 이름을 마우스 오른쪽 단추로 클릭한 다음 **클러스터 관리 포털(Ambari) 열기**를 선택합니다. 
1. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다. 이러한 항목은 클러스터를 프로비전하는 동안 지정한 것입니다.

### <a name="manage-azure-subscriptions"></a>Azure 구독 관리
기본적으로 Eclipse용 Azure 도구 키트의 HDInsight Tool에는 모든 Azure 구독의 Spark 클러스터가 나열됩니다. 필요한 경우 클러스터에 액세스하려는 구독을 지정할 수 있습니다. 

1. Azure 탐색기에서 **Azure** 루트 노드를 마우스 오른쪽 단추로 클릭한 다음 **구독 관리**를 선택합니다. 
1. 대화 상자에서 액세스하지 않으려는 구독의 확인란 선택을 취소하고 **닫기**를 선택합니다. Azure 구독에서 로그아웃하려는 경우 **로그아웃**을 선택할 수도 있습니다.

## <a name="run-a-spark-scala-application-locally"></a>로컬로 Spark Scala 애플리케이션 실행
Eclipse용 Azure 도구 키트의 HDInsight Tools를 사용하여 워크스테이션에서 Spark Scala 애플리케이션을 로컬로 실행할 수 있습니다. 일반적으로 이러한 애플리케이션은 저장소 컨테이너와 같은 클러스터 리소스에 액세스할 필요가 없으므로 로컬로 실행하고 테스트할 수 있습니다.

### <a name="prerequisite"></a>필수 요소
Windows 컴퓨터에서 로컬 Spark Scala 애플리케이션을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에서 설명한 예외가 발생할 수 있습니다. 이 예외는 Windows에 **WinUtils.exe**가 없기 때문에 발생합니다. 

이 오류를 해결하려면 [실행 파일](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)을 **C:\WinUtils\bin** 등의 위치에 다운로드한 다음 환경 변수 **HADOOP_HOME**을 추가하고 변수 값을 **C\WinUtils**로 설정합니다.

### <a name="run-a-local-spark-scala-application"></a>로컬 Spark Scala 애플리케이션 실행
1. Eclipse를 시작하고 프로젝트를 만듭니다. **새 프로젝트** 대화 상자에서 다음과 같이 선택하고 **다음**을 선택합니다.
   
   * 왼쪽 창에서 **HDInsight**를 선택합니다.
   * 오른쪽 창에서 **HDInsight의 Spark 로컬 실행 샘플(Scala)** 을 선택합니다.

   ![새 프로젝트 대화 상자](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. 프로젝트 세부 정보를 제공하려면 이전의 [HDInsight Spark 클러스터용 Spark Scala 프로젝트 설정](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster) 섹션의 3-6단계를 따릅니다.

1. 템플릿은 컴퓨터에서 로컬로 실행할 수 있는 샘플 코드(**LogQuery**)를 **src** 폴더 아래에 추가합니다.
   
   ![LogQuery 위치](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. **LogQuery** 애플리케이션을 마우스 오른쪽 단추로 클릭하고, **다음으로 실행**을 가리킨 다음, **1 Scala 애플리케이션**을 선택합니다. **콘솔** 탭에 다음과 같은 출력이 표시됩니다.
   
   ![Spark 애플리케이션 로컬 실행 결과](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>알려진 문제
클러스터를 연결하는 경우 저장소 자격 증명을 제공하는 것이 좋습니다.

![대화형 로그인](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

작업을 제출하는 두 가지 모드가 있습니다. 저장소 자격 증명을 제공하는 경우 작업을 제출하는 데 일괄 처리 모드가 사용됩니다. 그렇지 않으면 대화형 모드가 사용됩니다. 클러스터가 사용 중인 경우 아래 오류가 발생할 수 있습니다.

![클러스터가 사용 중인 경우 Eclipse에 오류가 발생합니다.](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![클러스터가 사용 중인 경우 Eclipse에 오류가 발생합니다.](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="feedback"></a>사용자 의견
의견이 있거나 이 도구를 사용할 때 다른 문제가 발생하는 경우 hdivstool@microsoft.com으로 메일을 보내 주시기 바랍니다.

## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>애플리케이션 만들기 및 실행
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ용 Azure 도구 키트를 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 원격으로 Apache Spark 애플리케이션 디버그](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 원격으로 Apache Spark 애플리케이션 디버그](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)

