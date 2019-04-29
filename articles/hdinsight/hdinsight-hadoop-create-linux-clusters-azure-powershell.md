---
title: PowerShell를 사용하여 Apache Hadoop 클러스터 만들기 - Azure HDInsight
description: Azure PowerShell을 사용하여 HDInsight용 Linux에서 Apache Hadoop, Apache HBase, Apache Storm 또는 Apache Spark 클러스터를 만드는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 757c3ef55e93397030b0fe847dc19d81a7bbe568
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124907"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Azure PowerShell을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell은 Microsoft Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 문서에서는 Azure PowerShell을 사용하여 Linux 기반 HDInsight 클러스터를 만드는 방법에 대한 정보를 제공합니다. 또한 예제 스크립트도 포함됩니다.

> [!NOTE]  
> Azure PowerShell은 Windows 클라이언트에서만 사용할 수 있습니다. Linux, Unix 또는 Mac OS X 클라이언트를 사용하는 경우 클래식 CLI를 사용하는 방법에 대한 자세한 내용은 [Azure 클래식 CLI를 사용하여 Linux 기반 HDInsight 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-azure-cli.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 절차를 시작하기 전에 다음이 있어야 합니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > Azure Service Manager를 사용하여 HDInsight 리소스를 관리하는 Azure PowerShell 지원은 더 이상 **지원되지 않고** 2017년 1월 1일에 제거되었습니다. 이 문서의 단계에서는 Azure Resource Manager로 작동하는 새 HDInsight cmdlet을 사용합니다.
    >
    > [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-Az-ps) 단계를 수행하여 최신 버전의 Azure PowerShell을 설치합니다. Azure Resource Manager로 작동하는 새로운 cmdlet을 사용하도록 수정해야 하는 스크립트가 있는 경우 자세한 내용은 [HDInsight 클러스터에 대한 Azure Resource Manager 기반 개발 도구에 마이그레이션](hdinsight-hadoop-development-using-azure-resource-manager.md) 을 참조하세요.

## <a name="create-cluster"></a>클러스터 만들기

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure PowerShell을 사용하여 HDInsight 클러스터를 만들려면 다음 절차를 완료해야 합니다.

* Azure 리소스 그룹 만들기
* Azure Storage 계정 만들기
* Azure Blob 컨테이너 만들기
* HDInsight 클러스터 만들기

다음 스크립트는 새 클러스터를 만드는 방법을 보여 줍니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

클러스터 로그인에 대해 지정한 값은 클러스터의 Hadoop 사용자 계정을 만드는 데 사용됩니다. 이 계정을 사용하여 웹 UI 또는 REST API 등의 클러스터에서 호스팅된 서비스에 연결합니다.

SSH 사용자에 대해 지정한 값은 클러스터의 SSH 사용자를 만드는 데 사용됩니다. 이 계정을 사용하여 클러스터에서 원격 SSH 세션을 시작하고 작업을 실행합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

> [!IMPORTANT]  
> 클러스터를 생성할 때 또는 생성 후 클러스터를 확장할 때 32개 이상의 작업자 노드를 사용하려는 경우 최소한 8개의 노드와 14GB RAM으로 헤드 노드의 크기를 지정해야 합니다.
>
> 노드 크기 및 관련된 비용에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요.

클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

## <a name="create-cluster-configuration-object"></a>클러스터 만들기: 구성 개체

또한 `New-AzHDInsightClusterConfig` cmdlet을 사용하여 HDInsight 구성 개체를 만들 수 있습니다. 그런 다음 이 구성 개체를 수정하여 클러스터에 대한 추가 구성 옵션을 사용하도록 설정합니다. 마지막으로 `New-AzHDInsightCluster` cmdlet의 `-Config` 매개 변수를 사용하여 구성을 사용합니다.

다음 스크립트는 HDInsight 클러스터 유형에 R Server를 구성하기 위해 구성 개체를 만듭니다. 이 구성은 에지 노드, RStudio, 추가 저장소 계정을 활성화합니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> HDInsight 클러스터와 다른 위치에서는 저장소 계정을 사용할 수 없습니다. 이 예제를 사용할 경우 서버와 동일한 위치에 추가 저장소 계정을 만듭니다.

## <a name="customize-clusters"></a>클러스터 사용자 지정

* [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)을 참조하세요.
* [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 성공적으로 만들었으므로 다음 리소스를 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 클러스터

* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache HBase용 Java 애플리케이션 개발](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 클러스터

* [HDInsight에서 Storm용 Java 토폴로지 개발](storm/apache-storm-develop-java-topology.md)
* [HDInsight의 Storm에서 Python 구성 요소 사용](storm/apache-storm-develop-python-topology.md)
* [HDInsight에서 Storm을 사용하는 토폴로지 배포 및 모니터링](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 클러스터

* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](spark/apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](spark/apache-spark-livy-rest-interface.md)
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](spark/apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](spark/apache-spark-machine-learning-mllib-ipython.md)

