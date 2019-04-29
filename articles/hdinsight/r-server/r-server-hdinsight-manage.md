---
title: HDInsight에서 ML 서비스 클러스터 관리 - Azure
description: Azure HDInsight에서 ML 서비스 클러스터를 관리하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 607f85c10183366e88d597d84090f49fc30aff48
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124597"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>Azure HDInsight에서 ML 서비스 클러스터 관리

이 문서에서는 여러 동시 사용자를 추가 하는 ML Services 클러스터에 원격으로 연결 등, 계산 컨텍스트를 변경 하는 등의 작업을 수행 하려면 Azure HDInsight에서 기존 ML 서비스 클러스터를 관리 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* **HDInsight의 ML 서비스 클러스터**: 자세한 내용은 [HDInsight에서 ML 서비스 시작](r-server-get-started.md)을 참조하세요.

* **SSH(Secure Shell) 클라이언트**: SSH 클라이언트는 HDInsight 클러스터에 원격으로 연결하여 클러스터에서 직접 명령을 실행하는 데 사용됩니다. 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.


## <a name="enable-multiple-concurrent-users"></a>여러 동시 사용자 사용

RStudio Community 버전이 실행되는 에지 노드에 대해 더 많은 사용자를 추가하여 HDInsight의 ML 서비스 클러스터에 대해 여러 동시 사용자를 사용하도록 설정할 수 있습니다. HDInsight 클러스터를 만들 때 다음과 같이 두 사용자, 즉 HTTP 사용자와 SSH 사용자를 제공해야 합니다.

![동시 사용자 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **클러스터 로그인 사용자 이름**: 사용자가 만든 HDInsight 클러스터를 보호하는 데 사용되는 HDInsight 게이트웨이를 통해 인증하기 위한 HTTP 사용자입니다. 이 HTTP 사용자는 Apache Ambari UI, Apache Hadoop YARN UI 및 다른 UI 구성 요소에 액세스하는 데 사용됩니다.
- **SSH(보안 셸) 사용자 이름**: 보안 셸을 통해 클러스터에 액세스하는 SSH 사용자입니다. 이 사용자는 모든 헤드 노드, 작업자 노드 및 에지 노드에 대한 Linux 시스템의 사용자입니다. 따라서 보안 셸을 사용하여 원격 클러스터의 노드 중 하나에 액세스할 수 있습니다.

HDInsight의 ML 서비스 클러스터에 사용되는 R Studio Server Community 버전은 로그인 메커니즘으로 Linux 사용자 이름과 암호만 허용하며, 토큰 전달은 지원하지 않습니다. 따라서 ML 서비스 클러스터에서 R Studio에 처음 액세스하려는 경우 두 번 로그인해야 합니다.

- 먼저 HDInsight 게이트웨이를 통해 HTTP 사용자 자격 증명을 사용하여 로그인합니다. 

- 그런 다음, SSH 사용자 자격 증명을 사용하여 RStudio에 로그인합니다.
  
현재 HDInsight 클러스터를 프로비전할 때 SSH 사용자 계정 하나만 만들 수 있습니다. 따라서 여러 사용자가 HDInsight의 ML 서비스 클러스터에 액세스할 수 있게 하려면 Linux 시스템에서 사용자를 추가로 만들어야 합니다.

RStudio가 클러스터의 에지 노드에서 실행되므로 여기서는 다음과 같은 몇 가지 단계가 있습니다.

1. 기존 SSH 사용자를 사용하여 에지 노드에 로그인
2. 에지 노드에 더 많은 Linux 사용자 추가
3. 만든 사용자를 통해 RStudio Community 버전 사용

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>1단계: 만든 SSH 사용자를 사용하여 에지 노드에 로그인

[SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)의 지침에 따라 에지 노드에 액세스합니다. HDInsight에서 ML 서비스 클러스터의 에지 노드 주소는 `CLUSTERNAME-ed-ssh.azurehdinsight.net`입니다.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>2단계: 에지 노드에 더 많은 Linux 사용자 추가

에지 노드에 사용자를 추가하려면 다음 명령을 실행합니다.

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

다음 스크린샷은 출력을 보여 줍니다.

![동시 사용자 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

“현재 Kerberos 암호:”를 묻는 메시지가 표시될 때 **Enter** 키를 누르기만 하면 무시됩니다. `useradd` 명령의 `-m` 옵션은 시스템에서 사용자의 홈 폴더를 만듦을 나타내며, 이 폴더는 RStudio Community 버전에 필요합니다.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>3단계: 만든 사용자를 통해 RStudio Community 버전 사용

https://CLUSTERNAME.azurehdinsight.net/rstudio/에서 RStudio에 액세스합니다. 클러스터를 만든 후 처음 로그인하는 경우, 클러스터 관리자 자격 증명을 입력한 후 만든 SSH 사용자 자격 증명을 입력합니다. 첫 번째 로그인이 아닌 경우 사용자가 만든 SSH 사용자의 자격 증명만 입력하면 됩니다.

또한 다른 브라우저 창에서 원래 자격 증명(기본적으로 *sshuser*)을 사용하여 동시에 로그인할 수도 있습니다.

새로 추가된 사용자는 Linux 시스템에서 루트 권한을 가지고 있지 않지만 원격 HDFS 및 WASB 저장소의 모든 파일에 동일한 액세스 권한을 가지고 있음에 유의하세요.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Microsoft ML 서비스에 원격으로 연결

데스크톱에서 실행되는 ML 클라이언트의 원격 인스턴스에서 HDInsight Spark 계산 컨텍스트에 대한 액세스를 설정할 수 있습니다. 이렇게 하려면 데스크톱 컴퓨터에서 RxSpark 컴퓨팅 컨텍스트를 정의할 때 옵션(hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches 및 sshProfileScript)을 지정해야 합니다. 예를 들면 다음과 같습니다.

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

자세한 내용은 [Apache Spark 컴퓨팅 컨텍스트에서 RevoScaleR을 사용하는 방법](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)의 "Apache Hadoop 클라이언트로 Microsoft Machine Learning Server 사용" 섹션을 참조하세요.

## <a name="use-a-compute-context"></a>계산 컨텍스트 사용

계산 컨텍스트를 사용하여 계산을 이제 노드에서 로컬로 수행할지 여부 또는 HDInsight 클러스터의 노드 간에 분산할지 여부를 제어할 수 있습니다.

1. RStudio Server 또는 R 콘솔(SSH 세션)에서 다음 코드를 사용하여 HDInsight의 기본 저장소에 예제 데이터를 로드합니다.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
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

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 다음으로 몇 가지 데이터 정보를 만들고 두 개의 데이터 원본을 정의합니다.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. 로컬 계산 컨텍스트를 사용하여 데이터에 대해 로지스틱 회귀를 실행합니다.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    다음 코드 조각과 유사한 줄로 끝나는 출력이 나타나야 합니다.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Spark 컨텍스트를 사용하여 동일한 로지스틱 회귀를 실행합니다. Spark 컨텍스트는 HDInsight 클러스터의 모든 작업자 노드에서 처리를 분산시킵니다.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )

        # Display a summary
        summary(modelSpark)


## <a name="distribute-r-code-to-multiple-nodes"></a>여러 노드에 R 코드 분산

HDInsight에서 ML 서비스를 사용하면 기존 R 코드를 가져와 `rxExec`을 통해 클러스터의 여러 노드에서 실행할 수 있습니다. 이 함수는 매개 변수 비우기 또는 시뮬레이션을 수행할 때 유용합니다. 다음 코드는 `rxExec`를 사용하는 방법에 대한 예제입니다.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Spark 컨텍스트를 계속 사용하는 경우 이 명령에서는 `(Sys.info()["nodename"])` 코드가 실행되는 작업자 노드의 nodename(노드 이름) 값을 반환합니다. 예를 들어 4개 노드 클러스터에는 다음 코드 조각과 비슷한 출력이 표시됩니다.

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Apache Hive 및 Parquet에서 데이터에 액세스

HDInsight ML 서비스를 사용하면 Spark 계산 컨텍스트의 ScaleR 함수에서 Hive 및 Parquet의 데이터에 직접 액세스하여 사용할 수 있습니다. 이러한 기능은 RxHiveData 및 RxParquetData라는 새로운 ScaleR 데이터 소스 함수를 통해 사용할 수 있습니다. 이 함수는 Spark SQL을 사용하여 Spark DataFrame에 데이터를 직접 로드하여 ScaleR을 통해 분석합니다.

다음 코드는 새 함수를 사용하는 샘플 코드 일부를 제공합니다.

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


이러한 새 기능의 사용에 대한 자세한 내용은 `?RxHivedata` 및 `?RxParquetData` 명령을 사용하여 ML 서비스의 온라인 도움말을 참조하세요.  

## <a name="install-additional-r-packages-on-the-cluster"></a>클러스터에 추가 R 패키지 설치

### <a name="to-install-r-packages-on-the-edge-node"></a>에지 노드에 R 패키지를 설치하려면

에지 노드에 추가 R 패키지를 설치하려면 SSH를 통해 에지 노드에 연결된 후 R 콘솔 내에서 직접 `install.packages()`를 사용할 수 있습니다. 

### <a name="to-install-r-packages-on-the-worker-node"></a>작업자 노드에 R 패키지를 설치하려면

클러스터의 작업자 노드에 R 패키지를 설치하려면 스크립트 작업을 사용해야 합니다. 스크립트 동작은 HDInsight 클러스터의 구성을 변경하거나 추가 소프트웨어(예: 추가 R 패키지)를 설치하는 데 사용되는 Bash 스크립트입니다. 

> [!IMPORTANT]  
> 추가 R 패키지를 설치하는 데 스크립트 작업을 사용하려는 경우 클러스터가 만들어진 후에만 사용할 수 있습니다. 클러스터를 만드는 동안 이 프로시저를 사용하지 마세요. 스크립트는 완전히 구성된 ML 서비스를 사용하기 때문입니다.

1. [스크립트 작업을 사용하여 클러스터 사용자 지정](../hdinsight-hadoop-customize-cluster-linux.md)의 단계를 따르세요.

3. **스크립트 작업 제출**에서 다음 정보를 제공합니다.

   * **스크립트 유형**에서 **사용자 지정**을 선택합니다.

   * **이름**에서 스크립트 작업의 이름을 지정합니다.

     * **Bash 스크립트 URI**에서 `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`를 입력합니다. 이 스크립트는 작업자 노드에 추가 R 패키지를 설치하는 스크립트입니다.

   * **작업자** 확인란만 선택합니다.

   * **매개 변수**: 설치할 R 패키지. 예를 들어 `bitops stringr arules`

   * **이 스크립트 작업 유지** 확인란을 선택합니다.  

   > [!NOTE]
   > 1. 기본적으로 모든 R 패키지는 설치된 ML Server의 버전과 일치하는 Microsoft MRAN 리포지토리의 스냅숏에서 설치됩니다. 최신 버전의 패키지를 설치하려는 경우 호환성 문제가 발생할 수 있습니다. 그러나 이 설치 유형은 패키지 목록의 첫 번째 요소로 `useCRAN`을 지정함으로써 가능합니다(예: `useCRAN bitops, stringr, arules`).  
   > 2. 일부 R 패키지에는 추가 Linux 시스템 라이브러리가 필요합니다. 편의상 HDInsight ML 서비스는 가장 인기 있는 상위 100개의 R 패키지에서 필요한 종속성을 사용하여 미리 설치되었습니다. 그러나 설치한 R 패키지에 더 많은 라이브러리가 필요한 경우 여기에 사용되는 기본 스크립트를 다운로드하고 시스템 라이브러리를 설치하는 단계를 추가해야 합니다. 그런 다음 수정된 스크립트를 Azure 저장소의 공용 blob 컨테이너에 업로드하고 수정된 스크립트를 사용하여 패키지를 설치해야 합니다.
   >    스크립트 작업 개발에 대한 자세한 내용은 [스크립트 작업 개발](../hdinsight-hadoop-script-actions-linux.md)을 참조하세요.  
   >
   >

   ![스크립트 작업 추가](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. **만들기**를 선택하여 스크립트를 실행합니다. 스크립트가 완료되면 모든 작업자 노드에서 R 패키지를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 ML 서비스 클러스터 운영](r-server-operationalize.md)
* [HDInsight에서 ML 서비스 클러스터에 대한 계산 컨텍스트 옵션](r-server-compute-contexts.md)
* [HDInsight에서 ML 서비스 클러스터에 대한 Azure Storage 옵션](r-server-storage.md)
