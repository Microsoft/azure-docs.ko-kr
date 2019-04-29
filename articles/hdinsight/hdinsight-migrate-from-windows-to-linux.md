---
title: Windows 기반 HDInsight에서 Linux 기반 HDInsight로 마이그레이션 - Azure
description: Windows 기반 HDInsight 클러스터에서 Linux 기반 HDInsight 클러스터로 마이그레이션하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: hrasheed
ms.openlocfilehash: 49f55416cb9224736acd7b8ac5eac5b6c5ba5979
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766711"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Windows 기반 HDInsight 클러스터에서 Linux 기반 클러스터로 마이그레이션

이 문서에서는 Windows와 Linux의 HDInsight가 어떻게 다른지 자세히 설명합니다. 기존 워크로드를 Linux 기반 클러스터로 마이그레이션하는 방법에 대해서도 설명합니다.

Windows 기반 HDInsight가 클라우드에서 Apache Hadoop을 사용하는 쉬운 방법을 제공하지만 Linux 기반 클러스터로 마이그레이션해야 합니다. 예를 들어, 솔루션에 필요한 Linux 기반 도구 및 기술을 활요하는 것입니다. Hadoop 에코시스템 내의 많은 개체가 Linux 기반 시스템에서 개발되며 Windows 기반 HDInsight에서 사용하지 못할 수도 있습니다. 많은 책, 동영상 및 기타 교육 자료에서는 사용자가 Hadoop 작업 시 Linux 시스템을 사용한다고 가정합니다.

> [!NOTE]  
> HDInsight 클러스터는 클러스터의 노드에 대한 운영 체제로 Ubuntu LTS(장기 지원)를 사용합니다. HDInsight와 함께 사용할 수 있는 Ubuntu의 버전 및 기타 구성 요소 버전 정보는 [HDInsight 구성 요소 버전](hdinsight-component-versioning.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migration-tasks"></a>마이그레이션 작업

마이그레이션에 대한 일반적인 워크플로는 다음과 같습니다.

![마이그레이션 워크플로 다이어그램](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. 마이그레이션할 때 필요할 수 있는 변경 내용을 이해하려면 이 문서의 각 섹션을 읽어보세요.

2. 테스트/품질 보증 환경으로 Linux 기반 클러스터를 만듭니다. Linux 기반 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

3. 기존 작업, 데이터 원본 및 싱크를 새 환경으로 복사합니다.

4. 작업이 새 클러스터에서 예상대로 작동하는지 확인하려면 유효성 검사 테스트를 수행합니다.

예상대로 작동하는 것이 확인되면 마이그레이션을 위해 가동 중지 시간을 예약합니다. 가동 중지 시간 중 다음 작업을 수행합니다.

1. 클러스터 노드에 로컬로 저장된 모든 임시 데이터를 백업합니다. 예를 들어 헤드 노드에 직접 저장된 데이터가 있는 경우입니다.

2. Windows 기반 클러스터를 삭제합니다.

3. Windows 기반 클러스터가 사용한 기본 데이터 저장소를 동일하게 사용하여 Linux 기반 클러스터를 만듭니다. Linux 기반 클러스터는 기존 프로덕션 데이터에 대한 작업을 계속할 수 있습니다.

4. 백업한 모든 임시 데이터를 가져옵니다.

5. 새 클러스터를 사용하여 작업을 시작하거나 계속 처리합니다.

### <a name="copy-data-to-the-test-environment"></a>테스트 환경으로 데이터 복사

데이터 및 작업을 복사하는 방법은 여러 가지가 있지만, 이 섹션에서 설명하는 두 가지 방법이 파일을 테스트 클러스터로 직접 이동시키는 가장 간단한 방법입니다.

#### <a name="hdfs-copy"></a>HDFS 복사

다음 단계를 사용하여 프로덕션 클러스터에서 테스트 클러스터로 데이터를 복사합니다. 이 단계에서는 HDInsight에 포함된 `hdfs dfs` 유틸리티를 사용합니다.

1. 기존 클러스터에 대한 저장소 계정 및 기본 컨테이너 정보를 찾습니다. 다음 예제에서는 PowerShell을 사용하여 이 정보를 검색합니다.

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. 새 테스트 환경을 만들려면 HDInsight에서 Linux 기반 클러스터 만들기 문서에 나와 있는 단계를 따릅니다. 클러스터를 만들기 전에 **옵션 구성**을 선택합니다.

3. 선택적 구성 섹션에서 **연결된 저장소 계정**을 선택합니다.

4. **저장소 키 추가**를 선택하고 메시지가 표시되면, 1단계의 PowerShell 스크립트에서 반환된 저장소 계정을 선택합니다. 각 섹션에서 **선택**을 클릭합니다. 마지막으로 클러스터를 만듭니다.

5. 클러스터가 만들어지면 **SSH**를 사용하여 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

6. SSH 세션에서 다음 명령을 사용하여 연결된 저장소 계정에서 새 기본 저장소 계정으로 파일을 복사합니다. CONTAINER를 PowerShell에서 반환하는 컨테이너 정보로 바꿉니다. __ACCOUNT__를 계정 이름으로 바꿉니다. 데이터 경로를 데이터 파일 경로로 바꿉니다.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]  
    > 데이터를 포함하는 디렉터리 구조가 테스트 환경에 없는 경우 다음 명령을 사용하면 만들 수 있습니다.

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    `-p` 스위치를 사용하면 경로의 모든 디렉터리를 만들 수 있습니다.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Azure Storage Blob 간 직접 복사

또는 `Start-AzStorageBlobCopy` Azure PowerShell cmdlet을 사용하여 HDInsight 외부의 저장소 계정 간에 Blob을 복사할 수 있습니다. 자세한 내용은 Azure Storage에서 Azure PowerShell 사용에 대한 Azure Blob 섹션 관리 방법을 참조하세요.

## <a name="client-side-technologies"></a>클라이언트 쪽 기술

[Azure PowerShell cmdlet](/powershell/azureps-cmdlets-docs), [Azure 클래식 CLI](../cli-install-nodejs.md), [.NET SDK for Apache Hadoop](https://hadoopsdk.codeplex.com/) 등의 클라이언트 쪽 기술은 계속해서 Linux 기반 클러스터에서 작동합니다. 이러한 기술은 두 클러스터 OS 유형에서 동일한 REST API에 의존합니다.

## <a name="server-side-technologies"></a>서버 쪽 기술

다음 표는 Windows와 관련된 서버 쪽 구성 요소 마이그레이션에 대한 참고 자료를 제공합니다.

| 사용 기술 | 수행 작업 |
| --- | --- |
| **PowerShell** (서버 쪽 스크립트, 클러스터 생성 중 사용한 스크립트 동작 포함) |Bash 스크립트로 다시 작성합니다. 스크립트 동작의 경우 [스크립트 동작에서 Linux 기반 HDInsight 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md) 및 [Linux 기반 HDInsight에 대한 스크립트 동작 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요. |
| **Azure 클래식 CLI**(서버 쪽 스크립트) |Linux에서 Azure 클래식 CLI를 사용할 수 있지만, HDInsight 클러스터 헤드 노드에 사전에 설치되어 있지는 않습니다. Azure 클래식 설치에 대한 자세한 내용은 [Azure 클래식 CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조하세요. |
| **.NET 구성 요소** |.NET은 Linux 기반 HDInsight 클러스터에서 [Mono](https://mono-project.com)를 통해 완전히 지원되지 않습니다. 자세한 내용은 [.NET 솔루션을 Linux 기반 HDInsight로 마이그레이션](hdinsight-hadoop-migrate-dotnet-to-linux.md)을 참조하세요. |
| **Win32 구성 요소 또는 기타 Windows 전용 기술** |참고 자료는 구성 요소 또는 기술에 따라 다릅니다. Linux와 호환되는 버전을 찾을 수도 있습니다. 찾지 못한 경우 대체 솔루션을 찾거나 이 구성 요소를 다시 작성해야 합니다. |

> [!IMPORTANT]  
> HDInsight 관리 SDK는 Mono와 완전히 호환되지 않습니다. HDInsight 클러스터에 배포되는 솔루션의 일부로 사용하지 마세요.

## <a name="cluster-creation"></a>클러스터 만들기

이 섹션에서는 클러스터 만들기의 차이에 대한 정보를 제공합니다.

### <a name="ssh-user"></a>SSH 사용자

Linux 기반 HDInsight 클러스터는 클러스터 노드에 원격 액세스를 제공하기 위해 **SSH(Secure Shell)** 프로토콜을 사용합니다. Window 기반 클러스터용 원격 데스크톱과 달리, 대부분의 SSH 클라이언트는 그래픽 사용자 경험을 제공하지 않습니다. 대신, SSH 클라이언트가 클러스터에서 명령을 실행할 수 있는 명령줄을 제공합니다. 일부 클라이언트(예: [MobaXterm](https://mobaxterm.mobatek.net/))는 원격 명령줄 외에 그래픽 파일 시스템 브라우저를 제공합니다.

클러스터를 생성하는 동안 인증을 위해 SSH 사용자와 더불어 **암호** 또는 **공개 키 인증서** 중 하나를 제공해야 합니다.

암호보다는 공개 키 인증서가 더 안전하므로 공개 키 인증서를 사용하는 것이 좋습니다. 서명된 공개/개인 키 쌍을 생성한 후 클러스터를 생성할 때 공개 키를 제공하면 인증서 인증이 이루어집니다. SSH를 사용하여 서버에 연결할 때 클라이언트의 개인 키는 연결을 위한 인증을 제공합니다.

자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

### <a name="cluster-customization"></a>클러스터 사용자 지정

**스크립트 동작** 은 Bash 스크립트에서 작성해야 합니다. Linux 기반 클러스터는 클러스터를 만드는 동안 또는 후에 스크립트 작업을 사용할 수 있습니다. 자세한 내용은 [스크립트 동작에서 Linux 기반 HDInsight 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md) 및 [Linux 기반 HDInsight에 대한 스크립트 동작 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.

다른 사용자 지정 기능은 **부트스트랩**입니다. Windows 클러스터의 경우 이 기능을 사용하면 Hive와 함께 사용하기 위한 추가 라이브러리 위치를 지정할 수 있습니다. 클러스터를 생성한 다음 이러한 라이브러리는 `ADD JAR`를 사용할 필요 없이 자동으로 Hive 쿼리와 함께 사용할 수 있습니다.

Linux 기반 클러스터용 부트스트랩은 이 기능을 제공하지 않습니다. 대신 [클러스터 생성 중 Apache Hive 라이브러리 추가](hdinsight-hadoop-add-hive-libraries.md)에 설명된 스크립트 동작을 사용합니다.

### <a name="virtual-networks"></a>Virtual Network

Windows 기반 HDInsight 클러스터는 클래식 Virtual Network에서만 작동하는 반면 Linux 기반 HDInsight 클러스터는 리소스 관리자 Virtual Network가 필요합니다. Linux-HDInsight 클러스터에서 연결해야 하는 Classic Virtual Network에 리소스가 있는 경우 [Resource Manager Virtual Network에 Classic Virtual Network 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)을 참조하세요.

구성 요구 사항에 대한 자세한 내용은 [가상 네트워크를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md) 문서를 참조하세요.

## <a name="management-and-monitoring"></a>관리 및 모니터링

작업 기록 또는 Yarn UI와 같이 Windows 기반 HDInsight와 함께 사용했을 수도 있는 여러 웹 UI는 Apache Ambari를 통해 사용할 수 있습니다. 또한 Ambari Hive 보기는 사용자의 웹 브라우저를 사용하여 Hive 쿼리를 실행하는 방법을 제공합니다. Ambari 웹 UI는 https://CLUSTERNAME.azurehdinsight.net의 Linux 기반 클러스터에서 사용할 수 있습니다.

Ambari 작업에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Apache Ambari 웹](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari 경고

Ambari에는 클러스터의 잠재적인 문제를 알려주는 경고 시스템이 있습니다. 경고는 Ambari 웹 UI에 빨간색 또는 노란색 항목으로 나타나며 REST API를 통해서도 검색할 수 있습니다.

> [!IMPORTANT]  
> Ambari 경고는 문제가 *있을 수 있다*라는 의미이며, 문제가 *있다*는 것은 아닙니다. 예를 들어 평소처럼 액세스할 수 있는데도 HiveServer2에 액세스할 수 없다는 경고가 나타날 수 있습니다.
>
> 많은 경고는 서비스에서 간격 기반 쿼리로 구현되므로 특정 시간 프레임 내에서 응답을 기대합니다. 그러므로 경고가 발생했다고 해서 반드시 해당 서비스가 다운되었다는 것이 아니라 예상된 시간 프레임 내에 결과를 반환하지 않았다는 것을 의미합니다.

## <a name="file-system-locations"></a>파일 시스템 위치

Linux 클러스터 파일 시스템은 Windows 기반 HDInsight 클러스터와는 다르게 배치됩니다. 다음 표를 사용하여 일반적으로 사용되는 파일을 찾습니다.

| 찾는 대상 | 위치 |
| --- | --- |
| 구성 |`/etc`. 예: `/etc/hadoop/conf/core-site.xml` |
| 로그 파일 |`/var/logs` |
| HDP(Hortonworks Data Platform) |`/usr/hdp`.두 개의 디렉터리가 여기에 있으며 현재 HDP 버전 및 `current`입니다. `current` 디렉터리에는 버전 번호 디렉터리에 있는 파일 및 디렉터리에 대한 바로 가기 링크가 포함되어 있습니다. `current` 디렉터리는 HDP 버전의 업데이트에 따라 버전 번호가 변경되므로 HDP 파일에 액세스하는 편리한 방법으로 제공됩니다. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

일반적으로 파일 이름을 알면 파일 경로를 찾을 때 SSH 세션에서 다음 명령을 사용할 수 있습니다.

    find / -name FILENAME 2>/dev/null

파일 이름과 함께 와일드카드를 사용할 수도 있습니다. 예를 들어 `find / -name *streaming*.jar 2>/dev/null`는 파일 이름 중 'streaming'이라는 단어를 포함하는 모든 jar 파일의 경로를 반환합니다.

## <a name="apache-hive-apache-pig-and-mapreduce"></a>Apache Hive, Apache Pig 및 MapReduce

Pig 및 MapReduce 워크로드는 Linux 기반 클러스터에서 매우 유사합니다. 하지만 Linux 기반 HDInsight 클러스터는 Hadoop, Hive, Pig의 최신 버전을 사용하여 만들 수 있습니다. 이러한 버전 차이로 인해 기존 솔루션이 작동하는 방식이 달라질 수 있습니다. HDInsight와 함께 제공되는 구성 요소 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md)를 참조하세요.

Linux 기반 HDInsight는 원격 데스크톱 기능을 제공하지 않습니다. 대신, SSH를 사용하여 클러스터 헤드 노드로 원격으로 연결할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* [SSH에서 Apache Hive 사용](hdinsight-hadoop-use-hive-ssh.md)
* [SSH에서 Apache Pig 사용](hadoop/apache-hadoop-use-pig-ssh.md)
* [SSH와 함께 MapReduce 사용](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]  
> 외부 Hive 메타스토어를 사용하는 경우 Linux 기반 HDInsight와 함께 사용하기 전에 메타스토어를 백업해야 합니다. Linux 기반 HDInsight는 최신 버전의 Hive에서 사용할 수 있으며, 이 경우 이전 버전에서 만든 메타스토어와 호환되지 않을 수 있습니다.

다음 차트는 Hive 작업 마이그레이션에 대한 참고 자료를 제공합니다.

| Windows 기반 | Linux 기반에서... |
| --- | --- |
| **Hive 편집기** |[Ambari에서 Apache Hive 보기](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` - Tez 사용 설정 |Apache Tez는 Linux 기반 클러스터에 대한 기본 실행 엔진이므로 SET 문이 더 이상 필요하지 않습니다. |
| C# 사용자 정의 함수 | Linux 기반 HDInsight로 C# 구성 요소를 검증하는 방법에 대한 자세한 내용은 [.NET 솔루션을 Linux 기반 HDInsight로 마이그레이션](hdinsight-hadoop-migrate-dotnet-to-linux.md)을 참조하세요. |
| Hive 작업의 일부로 호출된 서버의 CMD 파일 또는 스크립트 |Bash 스크립트 사용 |
| `hive` 명령 |SSH 세션에서 [Apache Hive Beeline](hadoop/apache-hadoop-use-hive-beeline.md) 또는 [Apache Hive](hdinsight-hadoop-use-hive-ssh.md) 사용 |

### <a name="pig"></a>Pig

| Windows 기반 | Linux 기반에서... |
| --- | --- |
| C# 사용자 정의 함수 | Linux 기반 HDInsight로 C# 구성 요소를 검증하는 방법에 대한 자세한 내용은 [.NET 솔루션을 Linux 기반 HDInsight로 마이그레이션](hdinsight-hadoop-migrate-dotnet-to-linux.md)을 참조하세요. |
| 서버에서 Pig 작업의 일부로 호출된 CMD 파일 또는 스크립트 |Bash 스크립트 사용 |

### <a name="mapreduce"></a>MapReduce

| Windows 기반 | Linux 기반에서... |
| --- | --- |
| C# 매퍼 및 리듀서 구성 요소 | Linux 기반 HDInsight로 C# 구성 요소를 검증하는 방법에 대한 자세한 내용은 [.NET 솔루션을 Linux 기반 HDInsight로 마이그레이션](hdinsight-hadoop-migrate-dotnet-to-linux.md)을 참조하세요. |
| Hive 작업의 일부로 호출된 서버의 CMD 파일 또는 스크립트 |Bash 스크립트 사용 |

## <a name="apache-oozie"></a>Apache Oozie

> [!IMPORTANT]  
> 외부 Oozie 메타스토어를 사용하는 경우 Linux 기반 HDInsight와 함께 사용하기 전에 메타스토어를 백업해야 합니다. Linux 기반 HDInsight는 최신 버전의 Oozie에서 사용할 수 있으며, 이 경우 이전 버전에서 만든 메타스토어와 호환되지 않을 수 있습니다.

Oozie 워크플로에서는 셸 작업이 가능합니다. 셸 작업은 운영 체제의 기본 셸을 사용하여 명령줄 명령을 실행합니다. Windows 셸에 의존하는 Oozie 워크플로가 있는 경우 Linux 셸 환경(Bash)에 의존하도록 워크플로를 다시 작성해야 합니다. Oozie와 함께 셸 작업을 사용하는 방법에 대한 자세한 내용은 [Oozie 셸 작업 확장](https://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html)을 참조하세요.

C# 애플리케이션을 사용하는 워크플로가 있는 경우 Linux 환경에서 해당 애플리케이션의 유효성을 검사하세요. 자세한 내용은 [.NET 솔루션을 Linux 기반 HDInsight로 마이그레이션](hdinsight-hadoop-migrate-dotnet-to-linux.md)을 참조하세요.

## <a name="storm"></a>Storm

| Windows 기반 | Linux 기반에서... |
| --- | --- |
| Storm 대시보드 |Storm 대시보드를 사용할 수 없습니다. 토폴로지를 제출하는 방법은 [Linux 기반 HDInsight에서 Apache Storm 토폴로지 배포 및 관리](storm/apache-storm-deploy-monitor-topology-linux.md)를 참조하세요. |
| Storm UI |Storm UI는 https://CLUSTERNAME.azurehdinsight.net/stormui에서 사용할 수 있습니다. |
| C# 또는 하이브리드 토폴로지를 생성, 배포 및 관리하기 위한 Visual Studio |Visual Studio를 사용하여 HDInsight 클러스터의 Linux 기반 Storm에서 C#(SCP.NET) 또는 하이브리드 토폴로지를 생성, 배포 및 관리할 수 있습니다. 2016년 10월 28일 이후에 생성된 클러스터에만 사용할 수 있습니다. |

## <a name="apache-hbase"></a>Apache HBase

Linux 기반 클러스터에서 HBase에 대한 znode 상위는 `/hbase-unsecure`입니다. 기본 HBase Java API를 사용하는 모든 Java 클라이언트 애플리케이션 구성에서 이 값을 설정합니다.

이 값을 설정하는 예제 클라이언트에 대한 내용은 [Java 기반 Apache HBase 애플리케이션 빌드](hbase/apache-hbase-build-java-maven-linux.md)를 참조하세요.

## <a name="spark"></a>Spark

Spark 클러스터는 미리 보기 중 Windows 클러스터에서 사용 가능했지만 Spark GA은 Linux 기반 클러스터에서만 사용 가능합니다. Windows 기반 Spark 미리 보기 클러스터에서 릴리스 Linux 기반 Spark 클러스터까지의 마이그레이션 경로는 없습니다.

## <a name="known-issues"></a>알려진 문제

### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory 사용자 지정 .NET 작업

Azure Data Factory 사용자 지정 .NET 작업은 현재 Linux 기반 HDInsight 클러스터에서 지원되지 않습니다. 대신 ADF 파이프라인의 일부로 사용자 지정 작업을 구현하기 위해서는 다음 방법 중 하나를 사용해야 합니다.

* Azure Batch 풀에서 .NET 작업을 실행합니다.  [Azure Data Factory 파이프라인에서 사용자 지정 작업 사용](../data-factory/transform-data-using-dotnet-custom-activity.md)
* MapReduce 작업으로 작업을 구현합니다. 자세한 내용은 [데이터 팩터리에서 MapReduce 프로그램 호출](../data-factory/transform-data-using-hadoop-map-reduce.md)을 참조하세요.

### <a name="line-endings"></a>줄 끝

일반적으로 Windows 기반 시스템에서는 줄 끝으로 CRLF를 사용하며, Linux 기반 시스템에서는 LF를 사용합니다. LF와 함께 작동하도록 기존 데이터 생산자와 소비자를 수정해야 할 수도 있습니다.

예를 들어 Windows 기반 클러스터에서 Azure PowerShell을 사용하여 HDInsight를 쿼리하면 CRLF를 사용하여 데이터를 반환합니다. Linux 기반 클러스터의 동일한 쿼리는 LF를 반환합니다. Linux 기반 클러스터로 마이그레이션하기 전에 줄 끝이 솔루션에 문제를 일으키는지 테스트합니다.

항상 LF를 클러스터 노드에서 실행되는 스크립트의 끝 줄로 사용해야 합니다. CRLF를 사용하는 경우 Linux 기반 클러스터에서 스크립트를 실행할 때 오류가 표시될 수 있습니다.

포함된 CR 문자가 있는 문자열이 스크립트에 없는 경우 다음 방법 중 하나를 사용하여 줄 끝을 대량 변경할 수 있습니다.

* **클러스터에 업로드하기 전**: 스크립트를 클러스터에 업로드하기 전 다음 PowerShell 문을 사용하여 줄 끝을 CRLF에서 LF로 변경합니다.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **클러스터로 업로드한 후**: Linux 기반 클러스터에 대한 SSH 세션에서 다음 명령을 사용하여 스크립트를 수정합니다.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>다음 단계

* [Linux 기반 HDInsight 클러스터를 만드는 방법 알아보기](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH를 사용하여 HDInsight에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache Ambari를 사용하여 Linux 기반 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
