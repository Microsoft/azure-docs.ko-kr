---
title: 'Azure Toolkit for IntelliJ: SSH를 통해 원격으로 Spark 애플리케이션 디버그 '
description: Azure Toolkit for IntelliJ의 HDInsight 도구를 사용하여 SSH를 통해 HDInsight 클러스터에서 애플리케이션을 원격으로 디버그하는 방법에 대한 단계별 지침
keywords: IntelliJ 원격으로 디버그, IntelliJ 원격 디버깅, SSH, IntelliJ, HDInsight, IntelliJ 디버그, 디버깅
ms.service: hdinsight
author: hrasheed
ms.author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: ef2507a15579ea3d145bfe37df281e2c044d181c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124308"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 HDInsight 클러스터에서 로컬 또는 원격으로 Apache Spark 애플리케이션 디버그

이 문서에서는 [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable)의 HDInsight 도구를 사용하여 HDInsight 클러스터에서 애플리케이션을 원격으로 디버그하는 방법에 대한 단계별 지침을 제공합니다. 프로젝트를 디버그하려는 경우 [Azure Toolkit for IntelliJ로 HDInsight Spark 애플리케이션 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) 비디오를 시청할 수도 있습니다.

**필수 구성 요소**
* **IntelliJ용 Azure 도구 키트의 HDInsight 도구** 이 도구는 IntelliJ용 Azure 도구 키트의 일부입니다. 자세한 내용은 [IntelliJ용 Azure 도구 키트 설치](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)를 참조하세요. **IntelliJ용 Azure 도구 키트**. 이 도구 키트를 사용하여 HDInsight 클러스터용 Apache Spark 애플리케이션을 만듭니다. 자세한 내용은 [Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터용 Apache Spark 애플리케이션 만들기](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)의 지침을 따르세요.

* **사용자 이름 및 암호 관리를 포함한 HDInsight SSH 서비스**. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 및 [SSH 터널링을 사용하여 Ambari 웹 UI, JobHistory, NameNode, Apache Oozie 및 기타 웹 UI에 액세스](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)를 참조하세요. 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>로컬 실행 및 디버깅을 수행하는 방법에 대해 알아봅니다
### <a name="scenario-1-create-a-spark-scala-application"></a>시나리오 1: Spark Scala 애플리케이션 만들기 

1. IntelliJ IDEA를 시작하고 프로젝트를 만듭니다. **새 프로젝트** 대화 상자에서 다음을 수행합니다.

   a. 선택 **Azure Spark/HDInsight**합니다. 

   b. 기본 설정에 따라 Java 또는 Scala 템플릿을 선택합니다. 다음 옵션 중에서 선택합니다.

   - **Spark 프로젝트 (Java)**

   - **Spark (Scala) 프로젝트**

   - **샘플 (Scala)를 사용 하 여 Spark 프로젝트**

   - **실패 한 작업이 디버깅 샘플 (미리 보기) (Scala)를 사용 하 여 Spark 프로젝트**

     사용 하 여이 예제는 **샘플 (Scala)를 사용 하 여 Spark 프로젝트** 템플릿.

   다. **빌드 도구** 목록에서 요구 사항에 따라 다음 중 하나를 선택합니다.

   - Scala 프로젝트 만들기 마법사 지원에 대해 **Maven**

   - 종속성 관리 및 Scala 프로젝트에 대한 빌드에 대해 **SBT** 

     ![디버그 프로젝트 만들기](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. **다음**을 선택합니다.     
 
1. 다음 **새 프로젝트** 창에서 다음을 수행합니다.

   ![Spark SDK 선택](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. 프로젝트 이름과 프로젝트 위치를 입력합니다.

   b. **프로젝트 SDK** 드롭다운 목록에서 **Spark 2.x** 클러스터에 대해 **Java 1.8**을 선택하거나 **Spark 1.x** 클러스터에 대해 **Java 1.7**을 선택합니다.

   다. **Spark 버전** 드롭다운 목록에서 Scala 프로젝트 생성 마법사는 Spark SDK 및 Scala SDK에 대한 올바른 버전을 통합합니다. Spark 클러스터 2.0 이하 버전을 사용하는 경우 **Spark 1.x**를 선택합니다. 그렇지 않은 경우 **Spark 2.x**을 선택합니다. 이 예제에서는 **Spark 2.0.2(Scala 2.11.8)** 를 사용합니다.

   d. **마침**을 선택합니다.

1. **src** > **main** > **scala**를 선택하여 프로젝트에서 코드를 엽니다. 이 예제에서는 **SparkCore_wasbloTest** 스크립트를 사용합니다.

### <a name="prerequisite-for-windows"></a>Windows에 대 한 필수 구성 요소
Windows 컴퓨터에서 로컬 Spark Scala 애플리케이션을 실행하는 동안 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356)에서 설명한 예외가 발생할 수 있습니다. 이 예외는 Windows에 WinUtils.exe가 없기 때문에 발생합니다. 

이 오류를 해결하려면 [실행 파일을 다운로드](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)하여 **C:\WinUtils\bin** 등의 위치에 저장합니다. 그런 다음 **HADOOP_HOME** 환경 변수를 추가하고 이 변수 값을 **C:\WinUtils**로 설정합니다.

### <a name="scenario-2-perform-local-run"></a>시나리오 2: 로컬 실행 수행
1. **SparkCore_wasbloTest** 스크립트를 열고 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음 **'[Spark 작업] XXX' 실행** 옵션을 선택하여 로컬 실행을 수행합니다.
1. 로컬 실행이 완료되면 현재 프로젝트 탐색기 **data** > **__default__** 에 저장된 출력 파일을 볼 수 있습니다.

    ![로컬 실행 결과](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. 해당 도구는 로컬 실행 및 로컬 디버그를 수행하면 기본 로컬 실행 구성을 자동으로 설정합니다. 구성을 열고 **[HDInsight에서 Spark] XXX** 오른쪽 위 모서리에서 볼 수 있습니다 합니다 **[HDInsight에서 Spark] XXX** 아래에 이미 만들어진 **HDInsight에서 Apache Spark**합니다. **로컬로 실행** 탭으로 전환합니다.

    ![로컬 실행 구성](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [환경 변수](#prerequisite-for-windows): 시스템 환경 변수 **HADOOP_HOME**을 **C:\WinUtils**로 이미 설정한 경우 자동으로 검색되므로 수동으로 추가할 필요가 없습니다.
    - [WinUtils.exe 위치](#prerequisite-for-windows): 시스템 환경 변수를 설정하지 않은 경우 해당 단추를 클릭하여 위치를 찾을 수 있습니다.
    - 두 옵션 중 하나를 선택하면 됩니다. 단, MacOS 및 Linux에서는 필요하지 않습니다.
1. 또한 로컬 실행 및 로컬 디버그를 수행하기 전에 수동으로 구성을 설정할 수 있습니다. 이전 스크린샷에서 더하기 기호(**+**)를 선택합니다. 다음을 선택 합니다 **HDInsight에서 Apache Spark** 옵션입니다. 저장할 **이름**, **주 클래스 이름**에 대한 정보를 입력하고 로컬 실행 단추를 클릭합니다.

### <a name="scenario-3-perform-local-debugging"></a>시나리오 3: 로컬 디버깅 수행
1. **SparkCore_wasbloTest** 스크립트를 열고 중단점을 설정합니다.
1. 스크립트 편집기를 마우스 오른쪽 단추로 클릭 하 고 다음 옵션을 선택 **디버그 ' [HDInsight에서 Spark] XXX'** 로컬 디버깅을 수행 하 합니다.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>원격 실행 및 디버깅 수행 방법 알아보기
### <a name="scenario-1-perform-remote-run"></a>시나리오 1: 원격 실행 수행

1. **구성 편집** 메뉴에 액세스하려면 오른쪽 위 구석에 있는 아이콘을 선택합니다. 이 메뉴에서 원격 디버깅에 대한 구성을 만들거나 편집할 수 있습니다.

   ![구성 편집](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. **실행/디버깅 구성** 대화 상자에서 더하기 기호(**+**)를 선택합니다. 다음을 선택 합니다 **HDInsight에서 Apache Spark** 옵션입니다.

   ![새 구성 추가](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. **클러스터에서 원격으로 실행** 탭으로 전환합니다. **이름**, **Spark 클러스터** 및 **주 클래스 이름**에 정보를 입력합니다. 누른 **고급 구성 (원격 디버깅)** 합니다. 이 도구는 **실행기**를 사용하여 디버그를 지원합니다. **numExectors**의 기본값은 5입니다. 3보다 큰 값을 설정하지 않는 것이 좋습니다.

   ![디버그 구성 실행](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. 에 **고급 구성 (원격 디버깅)** 파트 선택 **Spark 원격 디버그 설정**합니다. SSH 사용자 이름을 입력하고 암호를 입력하거나 개인 키 파일을 사용합니다. 원격 디버그를 수행하려는 경우 설정해야 합니다. 원격 실행만 사용하려는 경우는 설정할 필요가 없습니다.

   ![Spark 원격 디버그 설정](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. 이제 사용자가 입력한 이름으로 구성이 저장됩니다. 구성 세부 정보를 보려면 구성 이름을 선택합니다. 변경하려면 **구성 편집**을 선택합니다. 

1. 구성 설정을 완료한 후에 원격 클러스터에 대해 프로젝트를 실행하거나 원격 디버깅을 수행할 수 있습니다.
   
   ![원격 실행 단추](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. **연결 해제** 단추를 클릭하면 제출 로그가 왼쪽 패널에 나타나지 않습니다. 그러나 백 엔드에서는 계속 실행되고 있습니다.

   ![원격 실행 단추](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>시나리오 2: 원격 디버깅 수행
1. 중단점을 설정한 다음 **원격 디버그** 아이콘을 클릭합니다. 원격 제출과의 차이점은 SSH 사용자 이름/암호를 구성해야 한다는 것입니다.

   ![디버그 아이콘 선택](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. 프로그램 실행이 중단점에 도달하면 **디버거** 창에 1개의 **드라이버** 탭과 2개의 **실행기** 탭이 표시됩니다. **프로그램 다시 시작** 아이콘을 선택하여 코드를 계속 실행합니다. 그러면 다음 중단점에 도달합니다. 디버깅할 대상 실행기를 찾으려면 올바른 **실행기** 탭으로 전환해야 합니다. 해당 **콘솔** 탭에서 실행 로그를 검토할 수 있습니다.

   ![디버깅 탭](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>시나리오 3: 원격 디버깅 및 버그 수정 수행
1. 두 개의 중단점을 설정하고 **디버그** 아이콘을 선택하여 원격 디버깅 프로세스를 시작합니다.

1. 첫 번째 중단점에서 코드가 중지되고 **변수** 창에 매개 변수 및 변수 정보가 표시됩니다. 

1. **프로그램 다시 시작** 아이콘을 선택하여 계속합니다. 두 번째 지점에서 코드가 중지됩니다. 예상대로 예외가 catch됩니다.

   ![오류 throw](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. **프로그램 다시 시작** 아이콘을 다시 선택합니다. **HDInsight Spark 제출** 창에 “작업 실행 실패” 오류가 표시됩니다.

   ![오류 전송](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. IntelliJ 디버깅 기능을 사용하여 변수 값을 동적으로 업데이트하려면 **디버그**를 다시 선택합니다. **변수** 창이 다시 나타납니다. 

1. **디버그** 탭에서 대상을 마우스 오른쪽 단추로 클릭한 다음 **값 설정**을 선택합니다. 다음으로 변수의 새 값을 입력합니다. 그런 다음 **Enter** 키를 선택하여 값을 저장합니다. 

   ![값 설정](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. **프로그램 다시 시작** 아이콘을 선택하여 프로그램을 계속 실행합니다. 이번에는 예외가 catch되지 않습니다. 예외 없이 프로젝트가 성공적으로 실행되는지 확인할 수 있습니다.

   ![예외 없이 디버그](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>다음 단계
* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>데모
* Scala 프로젝트(비디오) 작성: [Apache Spark Scala 애플리케이션 만들기](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* 원격 디버그(비디오): [Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터에서 원격으로 Apache Spark 애플리케이션 디버그](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Apache Spark 및 Machine Learning: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 및 Machine Learning: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](../hdinsight-apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [Azure Toolkit for IntelliJ를 사용하여 HDInsight 클러스터용 Apache Spark 애플리케이션 만들기](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 원격으로 Apache Spark 애플리케이션 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션 만들기](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
