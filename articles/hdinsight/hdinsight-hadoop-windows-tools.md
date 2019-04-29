---
title: HDInsight에서 Hadoop을 통해 Windows PC 사용 - Azure
description: HDInsight에서 Hadoop의 Windows PC에서 작업. PowerShell, Visual Studio 및 Linux 도구를 사용하는 클러스터를 관리 및 쿼리합니다. .NET을 사용하는 빅 데이터 솔루션을 개발합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/17/2017
ms.openlocfilehash: 4d9d1ef6b7906ecebc399948a1ca0dcd590d5910
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765824"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Windows PC에서 HDInsight의 Apache Hadoop 에코시스템 작업

HDInsight의 Apache Hadoop 에코시스템 작업을 위한 Windows PC의 개발 및 관리 옵션에 대해 알아봅니다. 

HDInsight는 Apache Hadoop 및 Hadoop 구성 요소, Linux에서 개발된 오픈 소스 기술을 기반으로 합니다. HDInsight 버전 3.4 이상에서는 클러스터에 대한 기본 OS로 Ubuntu Linux 배포를 사용합니다. 그러나 Windows 클라이언트 또는 Windows 개발 환경에서 HDInsight로 작업할 수 있습니다.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>PowerShell을 사용하여 배포 및 관리 작업
Azure PowerShell은 Windows에서 HDInsight의 배포 및 관리 작업을 제어하고 자동화하는 데 사용할 수 있는 스크립팅 환경입니다.

PowerShell로 수행할 수 있는 작업의 예:

* [PowerShell을 사용하여 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [PowerShell을 사용하여 Apache Hive 쿼리 실행](hadoop/apache-hadoop-use-hive-powershell.md)
* [PowerShell을 사용하여 클러스터 관리](hdinsight-administer-use-powershell.md)

[Azure Powershell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-az-ps) 단계에 따라 최신 버전을 가져옵니다. Azure Resource Manager용 새로운 cmdlet을 사용하도록 수정해야 하는 스크립트가 있는 경우 [HDInsight 클러스터에 대한 Azure Resource Manager 기반 개발 도구에 마이그레이션](hdinsight-hadoop-development-using-azure-resource-manager.md)을 참조하세요.

## <a name="utilities-you-can-run-in-a-browser"></a>브라우저에서 실행할 수 있는 유틸리티
다음 유틸리티는 브라우저에서 실행되는 웹 UI가 있습니다.
* **[Azure Cloud Shell(미리 보기)](https://docs.microsoft.com/azure/cloud-shell/quickstart)** 은 브라우저 및 Azure Portal 내에서 실행되는 대화형 명령줄 셸입니다.
* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** 는 다음과 같은 다양한 종류의 작업을 관리하는 데 사용할 수 있으며 Azure Portal에서 사용 가능한 관리 및 모니터링 유틸리티입니다.
    * [REST API로 Apache Ambari 사용](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Ambari에서 Apache Hive 보기](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Ambari에서 Apache Tez 보기](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Visual Studio용 Data Lake(Hadoop) 도구
Visual Studio용 Data Lake 도구를 사용하여 Storm 토폴로지를 배포 및 관리합니다. Data Lake 도구는 또한 Visual Studio와 함께 C# Storm 토폴로지를 개발할 수 있는 SCP.NET SDK를 설치합니다.

다음 예제를 진행하기 전에 [Visual Studio용 Data Lake 도구를 설치 및 시도](hadoop/apache-hadoop-visual-studio-tools-get-started.md)합니다. 

다음은 Visual Studio용 Data Lake 도구와 Visual Studio를 사용할 수 있는 작업 예제입니다.
* [Visual Studio에서 Storm 토폴로지 배포 및 관리](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Visual Studio를 사용하여 Storm용 C# 토폴로지를 개발합니다](storm/apache-storm-develop-csharp-visual-studio-topology.md). 비트에는 Azure Cosmos DB 및 SQL Database와 같은 데이터베이스에 연결할 수 있는 Storm 토폴로지를 위한 예제 템플릿이 포함됩니다.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio 및 .NET SDK 

.NET SDK와 함께 Visual Studio를 사용하여 클러스터를 관리하고 빅 데이터 애플리케이션을 개발할 수 있습니다. 다음 작업에 대해 다른 IDE를 사용할 수 있으나 예제는 Visual Studio에 표시됩니다.

Visual Studio에서 .NET SDK와 함께 수행할 수 있는 작업의 예:
* [.NET Framework 애플리케이션의 HDInsight에서 클러스터 만들기 및 작업](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [.NET SDK를 사용하여 Apache Hive 쿼리 실행](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [Apache Hadoop에서 Apache Hive 및 Apache Pig 스트림과 함께 C# 사용자 정의 함수 사용](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

> [!TIP]
> .NET 솔루션을 Windows 기반 HDInsight 클러스터와 함께 실행하는 경우 Linux 기반 클러스터로의 마이그레이션을 계획하는 것이 좋습니다. 자세한 내용은 [Windows 기반 HDInsight용 .NET 솔루션을 Linux 기반 HDInsight로 마이그레이션](hdinsight-hadoop-migrate-dotnet-to-linux.md)을 참조하세요.

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Spark 클러스터에 대한 Intellij IDEA 및 Eclipse IDE
[Intellij IDEA](https://www.jetbrains.com/idea/download) 및 [Eclipse IDE](https://www.eclipse.org/downloads/)는 다음에 사용할 수 있습니다.
* HDInsight Spark 클러스터에서 Scala Spark 애플리케이션을 개발 및 제출합니다.
* Spark 클러스터 리소스에 액세스합니다.
* Scala Spark 애플리케이션을 로컬로 개발 및 실행합니다.

다음 문서에 방법이 나와 있습니다. 
* Intellij IDEA: [Azure Toolkit for Intellij 플러그 인 및 Scala SDK를 사용하여 Apache Spark 애플리케이션 만들기](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE 또는 Eclipse용 Scala IDE: [Apache Spark 애플리케이션 및 Azure Toolkit for Eclipse 만들기](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>데이터 과학자들을 위한 Spark의 Notebook 
HDInsight의 Apache Spark 클러스터는 Jupyter Notebook과 함께 사용할 수 있는 Apache Zeppelin Notebook 및 커널을 포함합니다. 

* [Apache Spark 클러스터에서 Jupyter Notebook과 함께 커널을 사용하여 Spark 애플리케이션을 테스트하는 방법 알아보기](spark/apache-spark-zeppelin-notebook.md)
* [Apache Spark 클러스터에서 Apache Zeppelin Notebook을 사용하여 Spark 작업을 실행하는 방법 알아보기](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Windows에서 Linux 기반 도구 및 기술 실행

Linux에서만 사용 가능한 도구 또는 기술을 사용해야 하는 상황이 발생한 경우 다음 옵션을 고려합니다.

* **Windows 10의 Bash(베타)** 는 Windows에서 Linux 하위 시스템을 제공합니다. Bash를 사용하면 전용 Linux 설치를 유지하지 않고도 Linux 유틸리티를 직접 실행할 수 있습니다. [Bash 베타를 Windows 10에서 설치 및 실행](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Windows용 Docker**는 대부분의 Linux 기반 도구에 대한 액세스를 제공하며 Windows에서 직접 실행할 수 있습니다. 예를 들어, Docker를 사용하여 Hive에 대한 Beeline 클라이언트를 Windows에서 직접 실행할 수 있습니다. 또한 Docker를 사용하여 로컬 Jupyter Notebook을 실행하고 HDInsight의 Spark에 원격으로 연결할 수 있습니다. [Windows용 Docker 시작](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** 을 사용하면 그래픽 방식으로 SSH 연결을 통해 클러스터 파일 시스템을 찾아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
Linux 기반 클러스터에서 작업하는 데 익숙하지 않은 경우 다음 문서를 참조하세요.
* [Apache Hadoop, Apache Kafka, Apache Spark 또는 기타 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
* [Linux의 HDInsight 클러스터에 대한 팁](hdinsight-hadoop-linux-information.md)
