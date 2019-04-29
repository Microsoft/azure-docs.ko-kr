---
title: HDInsight에서 ML Services 시작 - Azure
description: ML 서비스를 포함하는 HDInsight 클러스터에서 Apache Spark를 만들고 클러스터에 R 스크립트를 제출하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 465b53e1c5f56c5c05c860ebd69a825141f7e703
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124614"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Azure HDInsight에서 ML 서비스 시작

Azure HDInsight를 사용하면 ML 서비스 클러스터를 만들 수 있습니다. 이 옵션을 사용하면 R 스크립트에서 [Apache Spark](https://spark.apache.org/) 및 [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)를 사용하여 분산 컴퓨팅을 실행할 수 있습니다. 이 아티클에서는 HDInsight에서 ML 서비스 클러스터를 만드는 방법 및 R 스크립트를 실행하여 분산된 R 계산에 Spark를 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**: 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. 자세한 내용은 [Microsoft Azure 평가판 가져오기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.
* **SSH(보안 셸) 클라이언트**: SSH 클라이언트는 HDInsight 클러스터에 원격으로 연결하여 클러스터에서 직접 명령을 실행하는 데 사용됩니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Azure Portal을 사용하여 클러스터 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **+ 리소스 만들기** > **Analytics** > **HDInsight**로 이동합니다.

3. **기본**에서 다음 정보를 입력합니다.

    * **클러스터 이름**: HDInsight 클러스터의 이름입니다.
    * **구독**: 사용할 구독을 선택합니다.
    * **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**: HTTPS를 통해 클러스터에 액세스할 때의 로그인입니다. 이러한 자격 증명을 사용하여 Apache Ambari Web UI 또는 REST API와 같은 서비스에 액세스합니다.
    * **SSH(Secure Shell) 사용자 이름**: SSH를 통해 클러스터에 액세스할 때 사용되는 로그인입니다. 기본적으로 암호는 클러스터 로그인 암호와 동일합니다.
    * **리소스 그룹**: 클러스터를 만들어 놓은 리소스 그룹입니다.
    * **위치**: 클러스터를 만들어 놓은 Azure 지역입니다.

        ![클러스터 기본 세부 정보](./media/r-server-get-started/clustername.png)

4. **클러스터 유형**을 선택한 다음 **클러스터 구성** 섹션에서 다음 값을 설정합니다.

    * **클러스터 유형**: ML 서비스

    * **운영 체제**: Linux

    * **버전**: ML Server 9.3(HDI 3.6) ML Server 9.3에 대한 릴리스 정보는 [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server)에서 지원됩니다.

    * **ML Server용 R Studio 커뮤니티 버전**: 기본적으로 에지 노드에 설치되는 브라우저 기반 IDE입니다. 설치하지 않으려면 확인란의 선택을 취소합니다. 설치를 선택하면 만들어진 클러스터의 포털 애플리케이션 블레이드에서 RStudio Server 로그인에 액세스하기 위한 URL을 사용할 수 있습니다.

        ![클러스터 기본 세부 정보](./media/r-server-get-started/clustertypeconfig.png)

4. 클러스터 유형을 선택한 후 __선택__ 단추를 사용하여 클러스터 유형을 설정합니다. 그 다음, __다음__ 단추를 사용하여 기본 구성을 완료합니다.

5. **저장소** 섹션에서 저장소 계정을 선택하거나 만듭니다. 이 문서의 단계에서는 이 섹션의 다른 필드를 기본값으로 둡니다. __다음__ 단추를 사용하여 저장소 구성을 저장합니다.

    ![HDInsight에 대한 저장소 계정 설정 지정](./media/r-server-get-started/cluster-storage.png)

6. **요약** 섹션에서 클러스터에 대한 구성을 검토합니다. __편집__ 링크를 사용하여 올바르지 않은 설정을 변경합니다. 마지막으로 __만들기__ 단추를 사용하여 클러스터를 만듭니다.

    ![HDInsight에 대한 저장소 계정 설정 지정](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]  
    > 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>RStudio Server에 연결

HDInsight 클러스터의 일부로 R Studio Server Community Edition을 설치하기로 선택한 경우 다음 두 가지 방법 중 하나를 사용하여 RStudio 로그인에 액세스할 수 있습니다.

* **옵션 1** - 다음 URL로 이동합니다(여기서 **CLUSTERNAME**은 만든 ML 서비스 클러스터의 이름임).

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **옵션 2** - Azure Portal을 사용합니다.
  포털에서 다음을 수행합니다.
  1. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.
  2. **ANALYTICS** 아래에서 **HDInsight 클러스터**를 선택합니다.
  3. **HDInsight 클러스터** 페이지에서 클러스터 이름을 선택합니다.
  4. **ML Services 대시보드**에서 **R Studio 서버**를 선택합니다. 

     ![HDInsight에 대한 저장소 계정 설정 지정](./media/r-server-get-started/r-studio-server-dashboard.png)

     > [!IMPORTANT]  
     > 사용한 방법과 관계 없이 처음 로그인할 때는 두 번 인증해야 합니다.  첫 번째 인증 메시지의 경우 *클러스터 관리자 사용자 ID*와 *암호*를 입력합니다. 두 번째 인증 메시지의 경우 *SSH 사용자 ID*와 *암호*를 입력합니다. 후속 로그인 SSH 자격 증명이 필요합니다.

연결되면 다음 스크린샷과 유사한 화면이 표시됩니다.

![RStudio에 연결](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>샘플 작업 실행

ScaleR 함수를 사용하여 작업을 제출할 수 있습니다. 다음은 작업을 실행하는 데 사용되는 명령의 예제입니다.

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>클러스터 에지 노드에 연결

이 섹션에서는 SSH를 사용하여 ML 서비스 HDInsight 클러스터의 에지 노드에 연결하는 방법을 설명합니다. SSH 사용에 익숙해지려면 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

ML 서비스 클러스터 에지 노드에 연결하기 위한 SSH 명령은 다음과 같습니다.

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

클러스터에 대한 SSH 명령을 찾으려면 Azure Portal에서 클러스터 이름을 클릭하고, **SSH + 클러스터 로그인**을 클릭한 다음, **Hostname**에 에지 노드를 선택합니다. 그러면 에지 노드에 대한 SSH 엔드포인트 정보가 표시됩니다.

![에지 노드에 대한 SSH 엔드포인트의 이미지](./media/r-server-get-started/sshendpoint.png)

SSH 사용자 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우, `-i` 매개 변수를 사용하고 일치하는 개인 키를 지정합니다. 예를 들면 다음과 같습니다.

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

연결되면 다음과 유사한 메시지가 표시됩니다.

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>R 콘솔 사용

1. SSH 세션에서 다음 명령을 사용하여 R 콘솔을 시작합니다.  

        R

2. 다른 정보 외에 ML Server의 버전이 포함된 출력이 표시되어야 합니다.
    
3. `>` 프롬프트에서 R 코드를 입력할 수 있습니다. HDInsight의 ML 서비스에는 Hadoop과 쉽게 상호 작용하고 분산된 계산을 실행할 수 있는 패키지가 포함되어 있습니다. 예를 들어, 다음 명령을 사용하여 HDInsight 클러스터에 대한 기본 파일 시스템의 루트를 볼 수입니다.

        rxHadoopListFiles("/")

4. WASB 스타일 주소 지정을 사용할 수도 있습니다.

        rxHadoopListFiles("wasb:///")

5. R 콘솔을 종료하려면 다음 명령을 사용합니다.

        quit()

## <a name="automated-cluster-creation"></a>자동화된 클러스터 생성

SDK 및 PowerShell을 사용하여 HDInsight에서 ML 서비스 클러스터의 생성을 자동화할 수 있습니다.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* .NET SDK를 사용하여 ML 서비스 클러스터를 만들려면 [.NET SDK를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)를 참조하세요.
* Powershell을 사용하여 ML 서비스 클러스터를 만들려면 [Azure PowerShell을 사용하여 HDInsight 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)의 문서를 참조하세요.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](../hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure HDInsight에서 새로운 ML 서비스 클러스터를 만드는 방법 및 SSH 세션에서 R 콘솔을 사용하는 기본 사항에 대해 알아보았습니다. 다음 아티클에서는 HDInsight에서 ML 서비스를 관리하고 작업하는 다른 방법에 대해 설명합니다.

* [Visual Studio용 R 도구에서 작업 제출](r-server-submit-jobs-r-tools-vs.md)
* [HDInsight에서 ML 서비스 클러스터 관리](r-server-hdinsight-manage.md)
* [HDInsight에서 ML 서비스 클러스터 운영](r-server-operationalize.md)
* [HDInsight에서 ML 서비스 클러스터에 대한 계산 컨텍스트 옵션](r-server-compute-contexts.md)
* [HDInsight에서 ML 서비스 클러스터에 대한 Azure Storage 옵션](r-server-storage.md)
