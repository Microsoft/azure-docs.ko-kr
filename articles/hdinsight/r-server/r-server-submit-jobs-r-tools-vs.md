---
title: Visual Studio용 R 도구에서 작업 제출 - Azure HDInsight
description: 로컬 Visual Studio 컴퓨터에서 HDInsight 클러스터로 R 작업을 제출합니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: d977d5a25db0cbe641179bce860e9f67c60f29ab
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340811"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Visual Studio용 R 도구에서 작업 제출

RTVS([Visual Studio용 R 도구](https://www.visualstudio.com/vs/rtvs/))는 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 및 [Visual Studio 2015 업데이트 3](https://go.microsoft.com/fwlink/?LinkId=691129) 이상의 Community(무료), Professional 및 Enterprise 버전에 대한 무료, 오픈 소스 확장입니다. RTVS는 사용할 수 없습니다 [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019)합니다.

RTVS는 ggplot2 및 ggviz 등의 R 라이브러리, [R 코드 디버깅](https://docs.microsoft.com/visualstudio/rtvs/debugging) 등을 통해 [R 대화형 창](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl)(REPL), intellisense(코드 완성), [플롯 시각화](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data)와 같은 도구를 제공하여 R 워크플로를 개선합니다.

## <a name="set-up-your-environment"></a>환경 설정

1. [Visual Studio용 R 도구](https://docs.microsoft.com/visualstudio/rtvs/installation)를 설치합니다.

    ![Visual Studio 2017에서 RTVS 설치](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. *데이터 과학 및 분석 애플리케이션* 워크로드를 선택한 후 **R 언어 지원**, **R 개발에 대한 런타임 지원** 및  **Microsoft R Client** 옵션을 선택합니다.

3. SSH 인증에 대한 공용 및 프라이빗 키가 필요합니다.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. 컴퓨터에 [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows)를 설치합니다. ML Server는 [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) 및 `RxSpark` 함수를 제공합니다.

5. [PuTTY](https://www.putty.org/)를 설치하여 로컬 클라이언트에서 HDInsight 클러스터로 `RevoScaleR` 함수를 실행하기 위한 계산 컨텍스트를 제공합니다.

6. Visual Studio 환경에 데이터 과학 설정을 적용하여 작업 영역에 R 도구를 위한 새 레이아웃을 제공하는 옵션이 제공됩니다.
   1. 현재 Visual Studio 설정을 저장하려면 **도구 > 설정 가져오기 및 내보내기** 명령을 사용한 다음, **선택한 환경 설정 내보내기**를 선택하고 파일 이름을 지정합니다. 이러한 설정을 복원하려면 동일한 명령을 사용하고 **선택한 환경 설정 가져오기**를 선택합니다.

   2. **R 도구** 메뉴 항목으로 이동한 후 **데이터 과학 설정...** 을 선택합니다.

       ![데이터 과학 설정...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > 1단계의 방법을 사용하여, **데이터 과학 설정** 명령을 반복할 필요 없이 개인 설정된 데이터 과학 레이아웃을 저장 및 복원할 수도 있습니다.

## <a name="execute-local-r-methods"></a>로컬 R 메서드 실행

1. ML Services HDInsight 클러스터를 만듭니다.
2. [RTVS 확장](https://docs.microsoft.com/visualstudio/rtvs/installation)을 설치합니다.
3. [샘플 zip 파일](https://github.com/Microsoft/RTVS-docs/archive/master.zip)을 다운로드합니다.
4. Visual Studio에서 `examples/Examples.sln`을 열어 솔루션을 시작합니다.
5. `A first look at R` 솔루션 폴더에서 `1-Getting Started with R.R` 파일을 엽니다.
6. 파일 맨 위부터, Ctrl + Enter를 눌러 각 줄을 한 번에 하나씩 지나가면서 R 대화형 창까지 이동합니다. 일부 줄은 패키지를 설치하므로 다소 시간이 걸릴 수 있습니다.
    * 또는 R 파일의 모든 줄을 선택한 다음(Ctrl+A), 모든 줄을 실행하거나(Ctrl+Enter) 도구 모음에서 대화형으로 실행 아이콘을 선택합니다.
        ![대화형으로 실행](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. 스크립트에 모든 줄을 실행하면 다음과 비슷한 출력이 표시됩니다.

    ![데이터 과학 설정...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>HDInsight ML 서비스 클러스터에 작업 제출

PuTTY가 장착된 Windows 컴퓨터에서 Microsoft ML Server/Microsoft R Client를 사용하여, 로컬 클라이언트에서 HDInsight 클러스터로 분산 `RevoScaleR` 함수를 실행하는 계산 컨텍스트를 만들 수 있습니다. `RxSpark`를 통해 사용자 이름, Apache Hadoop 클러스터의 Edge 노드, SSH 스위치 등을 지정하여 컴퓨팅 컨텍스트를 만듭니다.

1. HDInsight에서 기계 학습 서비스에 지 노드 주소는 `CLUSTERNAME-ed-ssh.azurehdinsight.net` 여기서 `CLUSTERNAME` ML 서비스 클러스터의 이름입니다.

1. 사용자 환경에 맞게 설정 변수 값을 변경하여 Visual Studio의 R 대화형 창에 다음 코드를 붙여 넣습니다.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
      sshHostname = mySshHostname,
      sshSwitches = mySshSwitches,
      sshProfileScript = mySshProfileScript,
      consoleOutput = TRUE,
      hdfsShareDir = myHdfsShareDir,
      shareDir = myShareDir,
      sshClientDir = mySshClientDir
    )
    
    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```
    
    ![Spark 컨텍스트 설정](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

1. R 대화형 창에서 다음 명령을 실행합니다.

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    다음과 유사한 결과가 표시됩니다.

    ![성공적인 rx 명령 실행](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

1. `rxHadoopCopy`가 예제 데이터 폴더의 `people.json` 파일을 새로 만든 `/user/RevoShare/newUser` 폴더로 복사했는지 확인합니다.

    1. Azure의 HDInsight ML 서비스 클러스터 창의 왼쪽 메뉴에서 **저장소 계정**을 선택합니다.

        ![Storage 계정](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. 클러스터의 기본 저장소 계정을 선택하고 컨테이너/디렉터리 이름을 적어둡니다.

    3. 저장소 계정 창의 왼쪽 메뉴에서 **컨테이너**를 선택합니다.

        ![컨테이너](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. 클러스터의 컨테이너 이름을 선택하고, **사용자** 폴더(목록 맨 아래의 *추가 로드*를 클릭해야 할 수 있음)로 이동한 후 *RevoShare*, **newUser**를 차례로 선택합니다. `people.json` 파일이 `newUser` 폴더에 표시되어야 합니다.

        ![복사된 파일](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

1. 현재 Apache Spark 컨텍스트의 사용이 끝나면 중지해야 합니다. 여러 컨텍스트를 한 번에 실행할 수 없습니다.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>다음 단계

* [HDInsight의 ML Services에 대한 계산 컨텍스트 옵션](r-server-compute-contexts.md)
* [ScaleR 및 SparkR 결합](../hdinsight-hadoop-r-scaler-sparkr.md)에서는 항공편 지연 예측의 예를 제공합니다.

