---
title: Apache Hadoop, Apache Spark, Apache Kafka, Apache HBase 또는 R Server에 대한 클러스터 설정 - Azure HDInsight
description: 브라우저, Azure 클래식 CLI, Azure PowerShell, REST 또는 SDK에서 Hadoop, Kafka, Spark, HBase, R Server 또는 HDInsight의 Storm 클러스터를 설정합니다.
keywords: hadoop 클러스터 설정, kafka 클러스터 설정, spark 클러스터 설정, hadoop에서 클러스터란
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 01/28/2019
ms.openlocfilehash: 3ccff4409dde92a23ccdbb1c31af687449bf9d48
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766343"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, Apache HBase, ML Services 또는 Apache Storm을 사용하여 HDInsight에서 클러스터를 설정 및 구성하는 방법을 알아봅니다. 또한, 도메인에 조인하여 클러스터 및 보안을 사용자 지정하는 방법을 알아봅니다.

Hadoop 클러스터는 작업의 분산 처리에 사용되는 여러 가상 머신(노드)로 구성됩니다. Azure HDInsight는 개별 노드의 설치 및 구현에 대한 세부 구현을 처리하므로 일반적인 구성 정보만 제공해야 합니다. 

> [!IMPORTANT]  
> 클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. [클러스터 삭제](hdinsight-delete-cluster.md) 방법에 대해 알아보세요.

## <a name="cluster-setup-methods"></a>클러스터 설정 방법
다음 표는 HDInsight 클러스터를 설정하기 위해 사용할 수 있는 다양한 방법을 보여줍니다.

| 다음을 사용하여 만든 클러스터 | 웹 브라우저 사용 | 명령 줄 | REST API | SDK) | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure 데이터 팩터리](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure 클래식 CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Azure 리소스 관리자 템플릿](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>빠른 만들기: 기본 클러스터 설정
이 문서는 *빨리 만들기* 또는 *사용자 지정*을 사용하여 HDInsight 클러스터를 만들 수 있는 [Azure Portal](https://portal.azure.com)에서 설정을 안내합니다. 

![HDInsight 생성 옵션 사용자 지정 빨리 만들기](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

기본 클러스터 설정을 수행하려면 화면의 지침을 따릅니다. 다음 사항에 대한 세부 정보가 아래에 제공됩니다.

* [리소스 그룹 이름](#resource-group-name)
* [클러스터 유형 및 구성](#cluster-types) 
* 클러스터 로그인 및 SSH 사용자 이름
* [위치](#location):

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 3.3 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.
>

## <a name="resource-group-name"></a>리소스 그룹 이름 

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)를 사용하면 Azure 리소스 그룹이라는 그룹으로서 애플리케이션에서 리소스로 작업할 수 있습니다. 애플리케이션에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다.

## <a name="cluster-types"></a> 클러스터 유형 및 구성
Azure HDInsight는 현재 각각이 특정 기능을 제공하는 구성 요소 모음을 포함하는 다음과 같은 클러스터 유형을 제공합니다.

> [!IMPORTANT]  
> HDInsight 클러스터는 각 단일 워크로드 또는 기술에 다양한 유형으로 사용 가능합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다. 사용자 솔루션에 여러 유형의 HDInsight 클러스터에 분산되어 있는 기술이 필요한 경우, [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network)는 필요한 클러스터 유형을 연결할 수 있습니다. 

| 클러스터 유형 | 기능 |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |저장된 데이터의 일괄 처리 쿼리 및 분석 |
| [HBase](hbase/apache-hbase-overview.md) |많은 양의 스키마 없는 NoSQL 데이터에 대한 처리 |
| [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) |대화형 및 더 빠른 Hive 쿼리에 대한 메모리 내 캐싱 |
| [Kafka](kafka/apache-kafka-introduction.md) | 실시간 스트리밍 데이터 파이프라인과 애플리케이션을 만드는 데 사용할 수 있는 분산형 스트리밍 플랫폼 |
| [ML Services](r-server/r-server-overview.md) |다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 기능 |
| [Spark](spark/apache-spark-overview.md) |메모리 내 처리, 대화형 쿼리, 마이크로 배치 스트림 처리 |
| [Storm](storm/apache-storm-overview.md) |실시간 이벤트 처리 |


### <a name="hdinsight-version"></a>HDInsight 버전
이 클러스터에 대한 HDInsight 버전을 선택합니다. 자세한 내용은 [지원되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.


## <a name="cluster-login-and-ssh-username"></a>클러스터 로그인 및 SSH 사용자 이름
HDInsight 클러스터를 사용하면 클러스터 생성 중에 다음과 같은 두 개의 사용자 계정을 구성할 수 있습니다.

* HTTP 사용자: 기본 사용자 이름은 *admin*입니다. Azure Portal에서 기본 구성을 사용합니다. 경우에 따라 "클러스터 사용자"라고도 합니다.
* SSH 사용자: SSH를 통해 클러스터에 연결하는 데 사용됩니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

엔터프라이즈 보안 패키지를 사용하면 Active Directory 및 Apache Ranger와 HDInsight를 통합할 수 있습니다. Enterprise Security Package를 사용하여 여러 사용자를 만들 수 있습니다.

## <a name="location"></a>클러스터 및 저장소 위치(영역)

클러스터 위치를 명시적으로 지정할 필요는 없습니다. 클러스터는 기본 스토리지와 동일한 위치에 있습니다. 지원되는 지역 목록은 **HDInsight 가격** 에서 [지역](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)드롭다운 목록을 클릭하세요.

## <a name="storage-endpoints-for-clusters"></a>클러스터에 대한 저장소 엔드포인트

Hadoop의 온-프레미스 설치가 클러스터의 저장소에 HDFS(Hadoop 분산 파일 시스템)를 사용하고 있더라도 클라우드에서는 클러스터에 연결된 저장소 엔드포인트를 사용합니다. HDInsight 클러스터는 [Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md) 또는 [Azure Storage의 Blob](hdinsight-hadoop-use-blob-storage.md)을 사용합니다. Azure Storage 또는 Data Lake Storage를 사용하면 데이터를 보존하면서 계산에 사용한 HDInsight 클러스터를 안전하게 삭제할 수 있습니다. 

> [!WARNING]  
> HDInsight 클러스터와 다른 위치에서는 추가 저장소 계정을 사용할 수 없습니다.

구성하는 동안 기본 스토리지 엔드포인트에 대해 Azure Storage 계정 또는 Data Lake Storage의 Blob 컨테이너를 지정합니다. 기본 스토리지에는 애플리케이션 및 시스템 로그가 포함되어 있습니다. 필요에 따라 클러스터에서 액세스할 수 있는 추가 연결된 Azure Storage 계정 및 Data Lake Storage 계정을 지정할 수 있습니다. HDInsight 클러스터와 종속된 저장소 계정은 같은 Azure 위치에 있어야 합니다.

![클러스터 스토리지 설정: HDFS 호환 스토리지 엔드포인트](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>선택적 Metastore
선택적 Hive 또는 Apache Oozie metastore를 만들 수 있습니다. 그러나 일부 클러스터 형식은 Metastore를 지원하지 않으며, Azure SQL Data Warehouse는 Metastore와 호환되지 않습니다. 

자세한 내용은 [Azure HDInsight에서 외부 메타데이터 저장소 사용](./hdinsight-use-external-metadata-stores.md)을 참조하세요.

> [!IMPORTANT]  
> 사용자 지정 Metastore를 만들 때 데이터베이스 이름에 대시, 하이픈 또는 공백을 포함하지 마세요. 이렇게 하면 클러스터 만들기 프로세스가 실패할 수 있습니다.

### <a name="use-hiveoozie-metastore"></a>Hive metastore

HDInsight 클러스터를 삭제한 후 Hive 테이블을 유지하려는 경우 사용자 지정 metastore를 사용하세요. 그런 다음 해당 metastore를 다른 HDInsight 클러스터에 연결할 수 있습니다.

특정 HDInsight 클러스터 버전에 대해 만든 HDInsight metastore는 여러 다른 HDInsight 클러스터 버전 간에 공유할 수 없습니다. HDInsight 버전 목록은 [지원되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.

### <a name="oozie-metastore"></a>Oozie Metastore

Oozie 사용 시 성능을 높이려면 사용자 지정 Metastore를 사용합니다. 또한 Metastore는 클러스터를 삭제 한 후 Oozie 작업 데이터에 액세스를 제공할 수도 있습니다. 

> [!IMPORTANT]  
> 사용자 지정 Oozie Metastore는 다시 사용할 수 없습니다. 사용자 지정 Oozie Metastore를 사용하려면 HDInsight 클러스터를 만들 때 빈 Azure SQL Database를 제공해야 합니다.


## <a name="custom-cluster-setup"></a>사용자 지정 클러스터 설정
사용자 지정 클러스터 설정은 빨리 만들기 설정에 빌드되며 다음 옵션을 추가합니다.
- [Enterprise security Package](#enterprise-security-package)
- [HDInsight 애플리케이션](#install-hdinsight-applications-on-clusters)
- [클러스터 크기](#configure-cluster-size):
- [스크립트 동작](#advanced-settings-script-actions)
- [가상 네트워크](#advanced-settings-extend-clusters-with-a-virtual-network)
 
## <a name="enterprise-security-package"></a>엔터프라이즈 보안 패키지

Hadoop, Spark, HBase, Kafaka 및 대화형 쿼리 클러스터 형식의 경우 **Enterprise Security Package**를 사용하도록 선택할 수 있습니다. 이 패키지는 Apache Ranger를 사용하고 Azure Active Directory와 통합하여 보다 안전한 클러스터를 설정하는 옵션을 제공합니다. 자세한 내용은 [Azure HDInsight의 엔터프라이즈 보안 패키지](./domain-joined/apache-domain-joined-introduction.md)를 참조하세요.

![HDInsight 생성 옵션 선택 엔터프라이즈 보안 패키지](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

도메인에 가입된 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [도메인에 가입된 HDInsight 샌드박스 환경 만들기](./domain-joined/apache-domain-joined-configure.md)를 참조하세요. 

## <a name="install-hdinsight-applications-on-clusters"></a>클러스터에 HDInsight 애플리케이션 설치

HDInsight 애플리케이션은 Linux 기반 HDInsight 클러스터에 사용자가 설치할 수 있는 애플리케이션입니다. Microsoft, 타사에서 제공하거나 또는 직접 개발한 애플리케이션을 사용할 수 있습니다. 자세한 내용은 [Azure HDInsight에 타사 Apache Hadoop 애플리케이션 설치](hdinsight-apps-install-applications.md)를 참조하세요.

HDInsight 애플리케이션 대부분은 빈 Edge 노드에 설치됩니다.  빈 에지 노드는 헤드 노드에 설치되고 구성된 것과 동일한 클라이언트 도구를 사용하는 Linux 가상 머신입니다. 클러스터에 액세스하고, 클라이언트 애플리케이션을 테스트하며 클라이언트 애플리케이션을 호스트하는 데 에지 노드를 사용할 수 있습니다. 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)을 참조하세요.

## <a name="configure-cluster-size"></a>클러스터 크기 구성

클러스터가 있는 동안 노드 사용량에 대해 청구됩니다. 클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 클러스터의 경우 할당을 취소하거나 보류할 수 없습니다.

### <a name="number-of-nodes-for-each-cluster-type"></a>각 클러스터 유형에 대한 노드 수
각 클러스터 유형에는 자체 노드 수, 노드에 대한 용어 및 기본 VM 크기가 있습니다. 다음 표에서는 각 노드 유형에 대한 노드 수는 괄호로 묶어서 표시됩니다.

| Type | 노드 | 다이어그램 |
| --- | --- | --- |
| Hadoop은 |헤드 노드(2), 데이터 노드(1+) |![HDInsight Hadoop 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |헤드 서버(2), 지역 서버(1+), 마스터/ZooKeeper 노드(3) |![HDInsight HBase 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus 노드(2), 감독자 서버(1+), ZooKeeper 노드(3) |![HDInsight Storm 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |헤드 노드(2), 작업자 노드(1+), ZooKeeper 노드(3)(A1 ZooKeeper VM 크기의 경우 무료) |![HDInsight Spark 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

자세한 내용은 "HDInsight에서 사용하는 Hadoop 구성 요소 및 버전"에서 [클러스터의 기본 노드 구성 및 가상 머신 크기](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)를 참조하세요.

HDInsight 클러스터의 비용은 노드 수와 노드에 대한 가상 머신 크기에 따라 결정됩니다. 

클러스터 유형마다 서로 다른 노드 유형, 노드 수 및 노드 크기를 포함합니다.
* Hadoop 클러스터 유형 기본값: 
    * *헤드 노드* 2개  
    * *데이터 노드* 4개
* Storm 클러스터 유형 기본값: 
    * *Nimbus 노드* 2개
    * *ZooKeeper 노드* 3개
    * *감독자 노드* 4개 

HDInsight만 사용하려는 경우, 하나의 데이터 노드를 사용하는 것이 좋습니다. HDInsight 가격에 대한 자세한 내용은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)을 참조하세요.

> [!NOTE]  
> 클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 [Azure Billing Support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)에 문의하세요.
>

클러스터를 구성하기 위해 Azure Portal을 사용하는 경우 노드 크기는 **노드 가격 책정 계층** 블레이드를 통해 사용할 수 있습니다. 포털에서는 또한 다양한 노드 크기와 관련된 비용도 볼 수 있습니다. 

![HDInsight VM 노드 크기](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>가상 머신 크기 
클러스터를 배포하는 경우 배포하려는 솔루션에 기반하여 계산 리소스를 선택하세요. HDInsight 클러스터에 사용하는 VM은 다음과 같습니다.
* A 및 D1-4 시리즈 VM: [범용 Linux VM 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 시리즈 VM: [메모리 최적화 Linux VM 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

다른 SDK를 사용하여 클러스터를 만들거나 Azure PowerShell을 사용하는 동안 VM을 지정하기 위해 사용해야 하는 값을 확인하려면 [HDInsight 클러스터에 사용할 VM 크기](../cloud-services/cloud-services-sizes-specs.md#size-tables)를 참조하세요. 이 연결된 문서에서 테이블의 **크기** 열의 값을 사용합니다.

> [!IMPORTANT]  
> 클러스터에 필요한 작업자 노드 수가 32개를 초가하는 경우 최소한 코어 8개와 14GB RAM을 가진 헤드 노드 크기를 선택해야 합니다.
>
>

자세한 내용은 [가상 머신의 크기](../virtual-machines/windows/sizes.md)를 참조하세요. 다양한 크기의 가격 책정에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight)을 참조하세요.   

## <a name="advanced-settings-script-actions"></a>고급 설정: 스크립트 작업

만드는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 작업**을 통해 호출됩니다. 스크립트 작업은 Azure 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)(영문)을 참조하세요.

Apache Mahout, Cascading 등의 일부 네이티브 Java 구성 요소는 클러스터에서 JAR(Java Archive) 파일로 실행할 수 있습니다. 이러한 JAR 파일은 Azure Storage에 배포되고 Hadoop 작업 제출 메커니즘을 통해 HDInsight 클러스터에 제출될 수 있습니다. 자세한 내용은 [프로그래밍 방식으로 Apache Hadoop 작업 제출](hadoop/submit-apache-hadoop-jobs-programmatically.md)을 참조하세요.

> [!NOTE]  
> HDInsight 클러스터에 JAR 파일을 배포하거나 HDInsight 클러스터에서 JAR 파일을 호출하는 데 문제가 있는 경우 [Microsoft 지원](https://azure.microsoft.com/support/options/)으로 문의하세요.
>
> Cascading은 HDInsight에서 지원되지 않으며 Microsoft 지원 대상이 아닙니다. 지원되는 구성 요소 목록은 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)을 참조하세요.
>
>

경우에 따라 생성 프로세스 동안 다음과 같은 구성 파일을 구성해야 할 수 있습니다.

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

자세한 내용은 [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-bootstrap.md)을 참조하세요.

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>고급 설정: 가상 네트워크를 사용하여 클러스터 확장
사용자 솔루션에 여러 유형의 HDInsight 클러스터에 분산되어 있는 기술이 필요한 경우, [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network)는 필요한 클러스터 유형을 연결할 수 있습니다. 이 구성은 클러스터를 허용하며, 배포하는 임의의 코드가 서로 직접 통신하도록 허용합니다.

HDInsight와 함께 Azure Virtual Network를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

Azure Virtual Network 내에서 두 개의 클러스터 유형을 사용하는 예제는 [Apache Kafka에 Apache Spark 구조적 스트림 사용](hdinsight-apache-kafka-spark-structured-streaming.md)을 참조하세요. 가상 네트워크에 대한 특정 구성 요구 사항을 비롯한 가상 네트워크로 HDInsight를 사용하는 것에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [HDInsight, Apache Hadoop 에코시스템 및 Hadoop 클러스터란?](hadoop/apache-hadoop-introduction.md)
- [HDInsight에서 Apache Hadoop 사용 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
