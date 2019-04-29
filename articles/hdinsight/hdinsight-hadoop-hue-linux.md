---
title: HDInsight Linux 기반 클러스터에서 Hadoop으로 Hue - Azure
description: HDInsight 클러스터에서 Hue를 설치하고 터널링을 사용하여 Hue로 요청을 라우팅하는 방법을 알아봅니다. Hue를 사용하여 저장소를 찾은 후 Hive 또는 Pig를 실행합니다.
keywords: hue hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: b0354803a117e8e2c2382ae888bde94a502f24c6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760624"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>HDInsight Hadoop 클러스터에 Hue 설치 및 사용

HDInsight 클러스터에서 Hue를 설치하고 터널링을 사용하여 Hue로 요청을 라우팅하는 방법을 알아봅니다.

> [!IMPORTANT]  
> 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="what-is-hue"></a>Hue 정의
Hue는 Apache Hadoop 클러스터와 상호 작용하는 데 사용되는 웹 애플리케이션 세트입니다. Hue를 사용하여 Hadoop 클러스터(HDInsight 클러스터의 경우, WASB)와 연결된 저장소를 찾아보고 Hive 작업 및 Pig 스크립트 등을 실행할 수 있습니다. HDInsight Hadoop 클러스터에서 Hue 설치는 다음 구성 요소를 지원합니다.

* Beeswax Hive 편집기
* Apache Pig
* 메타스토어 관리자
* Apache Oozie
* FileBrowser (WASB 기본 컨테이너로 전달)
* 작업 브라우저

> [!WARNING]  
> HDInsight 클러스터와 함께 제공된 구성 요소는 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [https://apache.org](https://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](https://hadoop.apache.org/)).
>
>

## <a name="install-hue-using-script-actions"></a>스크립트 동작을 사용하여 Hue 설치

Linux 기반 HDInsight 클러스터에서 Hue를 설치하는 스크립트는 https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh에서 사용할 수 있습니다. 이 스크립트를 사용하여 Azure Storage Blobs(WASB) 또는 Azure Data Lake Storage를 기본 스토리지로 클러스터에 Hue를 설치할 수 있습니다.

이 섹션에서는 Azure Portal을 사용하여 클러스터를 프로비전할 때 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [!NOTE]  
> Azure PowerShell, Azure 클래식 CLI, HDInsight .NET SDK 또는 Azure Resource Manager 템플릿을 사용하여 스크립트 동작을 적용할 수도 있습니다. 이미 실행 중인 클러스터에도 스크립트 동작을 적용할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.
>
>

1. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)의 단계를 사용하여 클러스터 프로비전을 시작하는 한편 프로비전을 완료하지는 마세요.

   > [!NOTE]  
   > HDInsight 클러스터에 Hue를 설치하려면 권장 헤드 노드 크기는 A4(8개 코어, 14GB 메모리) 이상입니다.
   >
   >
2. **선택적 구성** 블레이드에서 **스크립트 동작**을 선택하고 아래와 같은 정보를 제공합니다.

    ![색상에 대한 스크립트 작업 매개 변수 제공](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "색상에 대한 스크립트 작업 매개 변수 제공")

   * **이름**: 스크립트 동작의 이름을 입력합니다.
   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: 이 옵션을 선택합니다.
   * **WORKER**: 이 항목을 비워둡니다.
   * **ZOOKEEPER**: 이 항목을 비워둡니다.
   * **PARAMETERS**: 이 항목을 비워둡니다.
3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다. 마지막으로 **선택적 구성** 블레이드의 아래 쪽에서 **선택** 단추를 사용하여 선택적 구성 정보를 저장합니다.
4. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)에서 설명한 대로 클러스터를 계속 프로비전합니다.

## <a name="use-hue-with-hdinsight-clusters"></a>HDInsight 클러스터로 Hue 사용

SSH 터널링이 실행되면 클러스터에서 Hue를 액세스하는 유일한 방법입니다. SSH를 통한 터널링을 사용하면 트래픽은 Hue가 실행되는 클러스터의 헤드 노드로 직접 이동할 수 있습니다. 클러스터가 프로비전을 완료하면 다음 단계를 수행하여 HDInsight Linux 클러스터에서 Hue를 사용합니다.

> [!NOTE]  
> Firefox 웹 브라우저를 사용하여 아래 지침을 따르는 것이 좋습니다.
>
>

1. [SSH 터널링을 사용하여 Apache Ambari Web UI, ResourceManager, JobHistory, NameNode, Oozie 및 다른 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)에 있는 정보를 사용하여 클라이언트 시스템에서 HDInsight 클러스터로 SSH 터널을 만들고 SSH 터널을 프록시로 사용하도록 웹 브라우저를 구성합니다.

2. SSH 터널을 생성하고 이를 통해 프록시 트래픽에 대한 브라우저를 구성했으면 기본 헤드 노드의 호스트 이름을 찾아야 합니다. 포트 22에 SSH를 사용하는 클러스터에 연결하여 수행할 수 있습니다. 예를 들어 `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`과 같으며, 여기서 **USERNAME**은 SSH 사용자 이름이고 **CLUSTERNAME**은 클러스터의 이름입니다.

    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

3. 일단 연결되면 다음 명령을 사용하여 기본 헤드 노드의 정규화된 도메인 이름을 확인합니다.

        hostname -f

    다음과 유사한 이름을 반환합니다.

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Hue 웹 사이트가 위치한 기본 헤드의 호스트 이름입니다.
4. 브라우저를 사용 하 여 http에서 Hue 포털을 엽니다.\//HOSTNAME:8888 합니다. HOSTNAME을 이전 단계에서 얻은 이름으로 바꿉니다.

   > [!NOTE]  
   > 처음으로 로그인할 때 Hue 포털에 로그인할 계정을 만들라는 메시지가 표시됩니다. 여기에서 지정한 자격 증명은 포털로 제한되며 클러스터를 프로비전하는 동안 지정한 관리자 또는 SSH 사용자 자격 증명과 관련이 없습니다.
   >
   >

    ![Hue 포털에 로그인](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Hue 포털에 자격 증명 지정")

### <a name="run-a-hive-query"></a>HIVE 쿼리 실행
1. Hue 포털에서 **쿼리 편집기**를 클릭하고 **Hive**를 클릭하여 Hive 편집기를 엽니다.

    ![Hive 사용](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Hive 사용")
2. **지원** 탭의 **데이터베이스**에서 **hivesampletable**이 표시되어야 합니다. HDInsight에서 모든 Hadoop 클러스터로 제공되는 예제 테이블입니다. 스크린 캡처에 표시된 것처럼 오른쪽 창에서 예제 쿼리를 입력하면 **결과** 탭에서 출력이 표시됩니다.

    ![Hive 쿼리 실행](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Hive 쿼리 실행")

    **차트** 탭을 사용하여 결과를 시각적으로 표시할 수 있습니다.

### <a name="browse-the-cluster-storage"></a>클러스터 저장소 찾아보기
1. Hue 포털에서 메뉴 모음의 오른쪽 위에 있는 **파일 브라우저** 를 클릭합니다.
2. 기본적으로 **/user/myuser** 디렉터리에서 파일 브라우저를 엽니다. 경로에서 사용자 디렉터리 바로 앞 슬래시를 클릭하여 클러스터와 연결된 Azure 저장소 컨테이너의 루트로 이동합니다.

    ![파일 브라우저 사용](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "파일 브라우저 사용")
3. 파일 또는 폴더를 마우스 오른쪽 단추로 클릭하여 사용 가능한 작업을 참조하세요. 오른쪽 모서리에서 **업로드** 단추를 사용하여 현재 디렉터리에 파일을 업로드합니다. **새로 만들기** 단추를 사용하여 새 파일 또는 디렉터리를 만듭니다.

> [!NOTE]  
> Hue 파일 브라우저는 HDInsight 클러스터와 연결된 기본 컨테이너의 콘텐츠만을 표시할 수 있습니다. 클러스터와 연결된 모든 추가 저장소 계정/컨테이너는 파일 브라우저를 사용하여 액세스할 수 없습니다. 그러나 클러스터와 관련된 추가 컨테이너는 항상 Hive 작업에 액세스할 수 있습니다. 예를 들어 하이브 편집기에 명령 `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` 을 입력하는 경우 추가 컨테이너의 내용도 볼 수 있습니다. 이 명령에서 **newcontainer** 는 클러스터와 연결된 기본 컨테이너가 아닙니다.
>
>

## <a name="important-considerations"></a>중요 고려 사항
1. Hue를 설치하는 데 사용한 스크립트는 클러스터의 기본 헤드 노드에만 설치합니다.

2. 설치하는 동안 구성을 업데이트하기 위해 여러 Hadoop 서비스(HDFS, YARN, MR2, Oozie)를 다시 시작합니다. 스크립트가 Hue의 설치를 완료한 후에 다른 Hadoop 서비스를 시작하려면 시간이 걸릴 수 있습니다. 처음에 Hue의 성능이 달라질 수 있습니다. 모든 서비스를 시작하면 Hue는 완벽하게 작동합니다.
3. Hue는 Hive의 현재 기본값인 Apache Tez 작업을 인식하지 못합니다. MapReduce를 Hive 실행 엔진으로 사용하려는 경우 스크립트를 업데이트하여 스크립트에서 다음 명령을 사용합니다.

        set hive.execution.engine=mr;

4. Linux 클러스터의 경우 보조 헤드 노드에서 Resource Manager를 실행하는 반면 기본 헤드 노드에서 서비스를 실행하는 시나리오가 있을 수 있습니다. Hue를 사용하여 클러스터에서 실행 중인 작업의 세부 정보를 보려면  이러한 시나리오에 오류가 발생할 수 있습니다. 그러나 작업이 완료되었을 때 작업 세부 정보를 볼 수 있습니다.

   ![Hue 포털 오류](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue 포털 오류")

   이는 알려진 문제 때문입니다. 해결 방법으로 Ambari를 수정하여 활성 Resource Manager가 기본 헤드 노드에서 실행되도록 합니다.
5. `wasb://`을 사용하여 HDInsight 클러스터가 Azure Storage를 사용하는 동안 Hue는 WebHDFS를 이해합니다. 따라서 스크립트 동작에 사용할 사용자 지정 스크립트는 WASB와 통신을 위한 WebHDFS와 호환 가능한 서비스인 WebWasb를 설치합니다. 따라서 Hue 포털이 HDFS가 제대로 있다고 하더라도( **파일 브라우저**로 마우스를 이동할 때처럼) WASB로 해석되어야 합니다.

## <a name="next-steps"></a>다음 단계
* [HDInsight 클러스터에서 Apache Giraph 설치](hdinsight-hadoop-giraph-install-linux.md) 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에 Giraph를 설치합니다. Giraph를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다.
* [HDInsight 클러스터에 R 설치](hdinsight-hadoop-r-scripts-linux.md). 클러스터 사용자 지정을 사용하여 HDInsight Hadoop 클러스터에서 R을 설치합니다. R은 통계 계산을 위한 오픈 소스 언어 및 환경입니다. 수백 개의 기본 제공 통계 함수와 기능 및 개체 지향 프로그래밍의 측면을 결합하는 고유한 프로그래밍 언어를 제공합니다. 또한 광범위한 그래픽 기능도 제공합니다.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
