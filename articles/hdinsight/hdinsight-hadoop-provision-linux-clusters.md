---
title: Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정
description: 브라우저, Azure 클래식 CLI, Azure PowerShell, REST 또는 SDK에서 Hadoop, Kafka, Spark, HBase, R Server 또는 HDInsight의 Storm 클러스터를 설정합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 08/06/2020
ms.openlocfilehash: f9c5b8ae16cb43576d788f72478e2cfba521a736
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749877"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

HDInsight에서 Apache Hadoop, Apache Spark, Apache Kafka, 대화형 쿼리, Apache HBase, ML 서비스 또는 Apache Storm를 설정 하 고 구성 하는 방법에 대해 알아봅니다. 또한, 도메인에 조인하여 클러스터 및 보안을 사용자 지정하는 방법을 알아봅니다.

Hadoop 클러스터는 작업의 분산 처리에 사용되는 여러 가상 머신(노드)로 구성됩니다. Azure HDInsight는 개별 노드의 설치 및 구현에 대한 세부 구현을 처리하므로 일반적인 구성 정보만 제공해야 합니다.

> [!IMPORTANT]  
> 클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. [클러스터 삭제](hdinsight-delete-cluster.md) 방법에 대해 알아보세요.

여러 클러스터를 함께 사용하는 경우 가상 네트워크를 만들고, Spark 클러스터를 사용하는 경우 Hive Warehouse Connector도 사용하는 것이 좋습니다. 자세한 내용은 [Azure HDInsight에 대한 가상 네트워크 계획](./hdinsight-plan-virtual-network-deployment.md) 및 [Hive Warehouse Connector를 사용하여 Apache Spark 및 Apache Hive 통합](interactive-query/apache-hive-warehouse-connector.md)을 참조하세요.

## <a name="cluster-setup-methods"></a>클러스터 설정 방법

다음 표는 HDInsight 클러스터를 설정하기 위해 사용할 수 있는 다양한 방법을 보여줍니다.

| 다음을 사용하여 만든 클러스터 | 웹 브라우저 | 명령줄 | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure 리소스 관리자 템플릿](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

이 문서에서는 HDInsight 클러스터를 만들 수 있는 [Azure Portal](https://portal.azure.com)의 설치 과정을 안내 합니다.

## <a name="basics"></a>기본 사항

![hdinsight 만들기 옵션 사용자 지정 빠른](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>프로젝트 세부 정보

[Azure Resource Manager](../azure-resource-manager/management/overview.md) 를 사용 하면 Azure [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups)이라고 하는 그룹으로 응용 프로그램의 리소스를 사용할 수 있습니다. 애플리케이션에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다.

### <a name="cluster-details"></a>클러스터 세부 정보

#### <a name="cluster-name"></a>클러스터 이름

HDInsight 클러스터 이름에는 다음 제한 사항이 있습니다.

* 허용되는 문자: a-z, 0-9, A-Z
* 최대 길이: 59
* 예약된 이름: apps
* 클러스터 명명 범위는 모든 구독에서 모든 Azure에 해당합니다. 따라서 클러스터 이름은 전 세계에서 고유 해야 합니다.
* 처음 6 자는 가상 네트워크 내에서 고유 해야 합니다.

#### <a name="region"></a>지역

클러스터 위치를 명시적으로 지정할 필요가 없습니다. 클러스터는 기본 스토리지와 같은 위치에 있습니다. 지원 되는 지역 목록은 [HDInsight 가격 책정](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)에서 **지역** 드롭다운 목록을 선택 합니다.

#### <a name="cluster-type"></a>클러스터 유형

Azure HDInsight는 현재 각각이 특정 기능을 제공하는 구성 요소 모음을 포함하는 다음과 같은 클러스터 유형을 제공합니다.

> [!IMPORTANT]  
> HDInsight 클러스터는 각 단일 워크로드 또는 기술에 다양한 유형으로 사용 가능합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다. 솔루션에 여러 HDInsight 클러스터 유형에 걸쳐 분산 된 기술이 필요한 경우 [Azure 가상 네트워크](https://docs.microsoft.com/azure/virtual-network) 에서 필요한 클러스터 유형을 연결할 수 있습니다.

| 클러스터 유형 | 기능 |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |저장된 데이터의 일괄 처리 쿼리 및 분석 |
| [HBase](hbase/apache-hbase-overview.md) |많은 양의 스키마 없는 NoSQL 데이터에 대한 처리 |
| [대화형 쿼리](./interactive-query/apache-interactive-query-get-started.md) |대화형 및 더 빠른 Hive 쿼리에 대한 메모리 내 캐싱 |
| [Kafka](kafka/apache-kafka-introduction.md) | 실시간 스트리밍 데이터 파이프라인과 애플리케이션을 만드는 데 사용할 수 있는 분산형 스트리밍 플랫폼 |
| [ML 서비스](r-server/r-server-overview.md) |다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 기능 |
| [Spark](spark/apache-spark-overview.md) |메모리 내 처리, 대화형 쿼리, 마이크로 배치 스트림 처리 |
| [Storm](storm/apache-storm-overview.md) |실시간 이벤트 처리 |

#### <a name="version"></a>버전

이 클러스터에 대한 HDInsight 버전을 선택합니다. 자세한 내용은 [지원되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.

### <a name="cluster-credentials"></a>클러스터 자격 증명

HDInsight 클러스터를 사용하면 클러스터 생성 중에 다음과 같은 두 개의 사용자 계정을 구성할 수 있습니다.

* 클러스터 로그인 사용자 이름: 기본 사용자 이름은 *admin*입니다. Azure Portal에서 기본 구성을 사용 합니다. "클러스터 사용자" 또는 "HTTP 사용자" 라고도 합니다.
* SSH (Secure Shell) 사용자 이름: SSH를 통해 클러스터에 연결 하는 데 사용 됩니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

HTTP 사용자 이름에는 다음과 같은 제한 사항이 있습니다.

* 허용 되는 특수 문자: `_` 및 `@`
* 허용 되지 않는 문자: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ space
* 최대 길이: 20

SSH 사용자 이름에는 다음과 같은 제한 사항이 있습니다.

* 허용 되는 특수 문자: `_` 및 `@`
* 허용 되지 않는 문자: #;. "', \/ : '! *? $ () {} [] <>|&--= +% ~ ^ space
* 최대 길이: 64
* 예약 된 이름: hadoop, 사용자, oozie, hive, mapred, ambari-qa, 사육 사, tez, hdfs, sqoop, yarn, hcat, ams, hbase, 스톰, 관리자, 관리자, 사용자, user1, 테스트, 사용자 2, test1, user3, 1, 1, 123, a, actuser, adm, 2, aspnet, backup, console, david, guest, john, owner, root, server, sql, support, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>스토리지

![클러스터 저장소 설정: HDFS 호환 끝점](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Hadoop의 온-프레미스 설치가 클러스터의 스토리지에 HDFS(Hadoop 분산 파일 시스템)를 사용하고 있더라도 클라우드에서는 클러스터에 연결된 스토리지 엔드포인트를 사용합니다. 클라우드 저장소를 사용 하면 데이터를 그대로 유지 하면서 계산에 사용 되는 HDInsight 클러스터를 안전 하 게 삭제할 수 있습니다.

HDInsight 클러스터는 다음과 같은 저장소 옵션을 사용할 수 있습니다.

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage 범용 v2
* Azure Storage 범용 v1
* Azure Storage 블록 blob (**보조 저장소로만 지원 됨**)

HDInsight의 저장소 옵션에 대 한 자세한 내용은 [Azure hdinsight 클러스터와 함께 사용 하기 위한 저장소 옵션 비교](hdinsight-hadoop-compare-storage-options.md)를 참조 하세요.

> [!WARNING]  
> HDInsight 클러스터와 다른 위치에서는 추가 스토리지 계정을 사용할 수 없습니다.

구성하는 동안 기본 스토리지 엔드포인트에 대해 Azure Storage 계정 또는 Data Lake Storage의 Blob 컨테이너를 지정합니다. 기본 스토리지에는 애플리케이션 및 시스템 로그가 포함되어 있습니다. 필요에 따라 클러스터에서 액세스할 수 있는 추가 연결된 Azure Storage 계정 및 Data Lake Storage 계정을 지정할 수 있습니다. HDInsight 클러스터와 종속된 스토리지 계정은 같은 Azure 위치에 있어야 합니다.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

> [!IMPORTANT]
> 클러스터를 만든 후 보안 저장소 전송을 사용 하도록 설정 하면 저장소 계정을 사용 하 여 오류가 발생할 수 있으므로 권장 되지 않습니다. 보안 전송이 이미 활성화 된 저장소 계정을 사용 하 여 새 클러스터를 만드는 것이 좋습니다.

> [!Note]  
> Azure HDInsight는 Azure Storage에 저장 된 데이터를 한 지역에서 다른 지역으로 자동으로 전송 하거나 이동 하거나 복사 하지 않습니다.

### <a name="metastore-settings"></a>Metastore 설정

선택적 Hive 또는 Apache Oozie metastore를 만들 수 있습니다. 그러나 일부 클러스터 형식은 Metastore를 지원하지 않으며, Azure SQL Data Warehouse는 Metastore와 호환되지 않습니다.

자세한 내용은 [Azure HDInsight에서 외부 메타데이터 저장소 사용](./hdinsight-use-external-metadata-stores.md)을 참조하세요.

> [!IMPORTANT]  
> 사용자 지정 Metastore를 만들 때 데이터베이스 이름에 대시, 하이픈 또는 공백을 포함하지 마세요. 이렇게 하면 클러스터 만들기 프로세스가 실패할 수 있습니다.

#### <a name="sql-database-for-hive"></a>Hive 용 SQL 데이터베이스

HDInsight 클러스터를 삭제한 후 Hive 테이블을 유지하려는 경우 사용자 지정 metastore를 사용하세요. 그런 다음 해당 metastore를 다른 HDInsight 클러스터에 연결할 수 있습니다.

하나의 HDInsight 클러스터 버전에 대해 생성 된 An HDInsight metastore는 다른 HDInsight 클러스터 버전 간에 공유할 수 없습니다. HDInsight 버전 목록은 [지원되는 HDInsight 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.

> [!IMPORTANT]
> 기본 metastore **기본 계층 5 DTU 제한 (업그레이드할 수 없음)** 을 사용 하는 Azure SQL Database를 제공 합니다. 기본 테스트 목적으로 적합 합니다. 대량 또는 프로덕션 워크 로드의 경우 외부 metastore 마이그레이션하는 것이 좋습니다.

#### <a name="sql-database-for-oozie"></a>Oozie 용 SQL database

Oozie 사용 시 성능을 높이려면 사용자 지정 Metastore를 사용합니다. 또한 Metastore는 클러스터를 삭제 한 후 Oozie 작업 데이터에 액세스를 제공할 수도 있습니다.

#### <a name="sql-database-for-ambari"></a>Ambari 용 SQL database

Ambari는 HDInsight 클러스터를 모니터링 하 고, 구성을 변경 하 고, 클러스터 관리 정보 및 작업 기록을 저장 하는 데 사용 됩니다. 사용자 지정 Ambari DB 기능을 사용 하면 관리 하는 외부 데이터베이스에 새 클러스터를 배포 하 고 Ambari를 설정할 수 있습니다. 자세한 내용은 [Custom AMBARI DB](./hdinsight-custom-ambari-db.md)를 참조 하세요.

> [!IMPORTANT]  
> 사용자 지정 Oozie Metastore는 다시 사용할 수 없습니다. 사용자 지정 Oozie Metastore를 사용하려면 HDInsight 클러스터를 만들 때 빈 Azure SQL Database를 제공해야 합니다.

## <a name="security--networking"></a>보안 + 네트워킹

![HDInsight 생성 옵션 선택 엔터프라이즈 보안 패키지](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>엔터프라이즈 보안 패키지

Hadoop, Spark, HBase, Kafaka 및 Interactive Query 클러스터 유형의 경우 **Enterprise Security Package**를 사용하도록 선택할 수 있습니다. 이 패키지는 Apache Ranger를 사용하고 Azure Active Directory와 통합하여 보다 안전한 클러스터를 설정하는 옵션을 제공합니다. 자세한 내용은 [Azure HDInsight의 엔터프라이즈 보안 개요](./domain-joined/hdinsight-security-overview.md)를 참조 하세요.

엔터프라이즈 보안 패키지를 사용하면 Active Directory 및 Apache Ranger와 HDInsight를 통합할 수 있습니다. Enterprise Security Package를 사용하여 여러 사용자를 만들 수 있습니다.

도메인에 가입된 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [도메인에 가입된 HDInsight 샌드박스 환경 만들기](./domain-joined/apache-domain-joined-configure.md)를 참조하세요.

### <a name="tls"></a>TLS

자세한 내용은 [전송 계층 보안](./transport-layer-security.md) 을 참조 하세요.

### <a name="virtual-network"></a>가상 네트워크

솔루션에 여러 HDInsight 클러스터 유형에 걸쳐 분산 된 기술이 필요한 경우 [Azure 가상 네트워크](https://docs.microsoft.com/azure/virtual-network) 에서 필요한 클러스터 유형을 연결할 수 있습니다. 이 구성은 클러스터를 허용하며, 배포하는 임의의 코드가 서로 직접 통신하도록 허용합니다.

HDInsight에서 Azure virtual network를 사용 하는 방법에 대 한 자세한 내용은 [hdinsight에 대 한 가상 네트워크 계획](hdinsight-plan-virtual-network-deployment.md)을 참조 하세요.

Azure Virtual Network 내에서 두 개의 클러스터 유형을 사용하는 예제는 [Apache Kafka에 Apache Spark 구조적 스트림 사용](hdinsight-apache-kafka-spark-structured-streaming.md)을 참조하세요. 가상 네트워크에 대 한 특정 구성 요구 사항을 포함 하 여 가상 네트워크에서 HDInsight를 사용 하는 방법에 대 한 자세한 내용은 [hdinsight에 대 한 가상 네트워크 계획](hdinsight-plan-virtual-network-deployment.md)을 참조 하세요.

### <a name="disk-encryption-setting"></a>디스크 암호화 설정

자세한 내용은 [고객 관리형 키 디스크 암호화](./disk-encryption.md)를 참조하세요.

### <a name="kafka-rest-proxy"></a>Kafka REST 프록시

이 설정은 클러스터 유형 Kafka에 대해서만 사용할 수 있습니다. 자세한 내용은 [REST 프록시 사용](./kafka/rest-proxy.md)을 참조 하세요.

### <a name="identity"></a>ID

자세한 내용은 [Azure HDInsight에서 관리 되는 id](./hdinsight-managed-identities.md)를 참조 하세요.

## <a name="configuration--pricing"></a>구성 + 가격 책정

![HDInsight에서 노드 크기를 선택 합니다.](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

클러스터가 존재 하는 한 노드 사용에 대 한 요금이 청구 됩니다. 클러스터가 만들어지면 청구가 시작되고 클러스터가 삭제되면 청구가 중지됩니다. 클러스터를 할당 취소 하거나 보류 중으로 설정할 수 없습니다.

### <a name="node-configuration"></a>노드 구성

각 클러스터 유형에는 자체 노드 수, 노드에 대한 용어 및 기본 VM 크기가 있습니다. 다음 표에서는 각 노드 유형에 대한 노드 수는 괄호로 묶어서 표시됩니다.

| Type | 노드 | 다이어그램 |
| --- | --- | --- |
| Hadoop은 |헤드 노드 (2), 작업자 노드 (1 +) |![HDInsight Hadoop 클러스터 노드](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |헤드 서버(2), 지역 서버(1+), 마스터/ZooKeeper 노드(3) |![HDInsight HBase 클러스터 유형 설정](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus 노드(2), 감독자 서버(1+), ZooKeeper 노드(3) |![HDInsight 스톰 클러스터 유형 설정](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |헤드 노드 (2), 작업자 노드 (1 +), 사육 사 노드 (3) (A1 사육 전 VM 크기의 경우 무료) |![HDInsight spark 클러스터 유형 설정](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

자세한 내용은 "HDInsight에서 사용하는 Hadoop 구성 요소 및 버전"에서 [클러스터의 기본 노드 구성 및 가상 머신 크기](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)를 참조하세요.

HDInsight 클러스터의 비용은 노드 수와 노드에 대한 가상 머신 크기에 따라 결정됩니다.

클러스터 유형마다 서로 다른 노드 유형, 노드 수 및 노드 크기를 포함합니다.
* Hadoop 클러스터 유형 기본값:
    * *헤드 노드* 2개  
    * *작업자 노드* 4 개
* Storm 클러스터 유형 기본값:
    * *Nimbus 노드* 2개
    * *ZooKeeper 노드* 3개
    * *감독자 노드* 4개

HDInsight를 사용 하는 경우 하나의 작업자 노드를 사용 하는 것이 좋습니다. HDInsight 가격에 대한 자세한 내용은 [HDInsight 가격](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409)을 참조하세요.

> [!NOTE]  
> 클러스터 크기 제한은 Azure 구독에 따라 다릅니다. 제한을 늘리려면 [Azure Billing Support](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)에 문의하세요.

Azure Portal를 사용 하 여 클러스터를 구성 하는 경우에는 **구성 + 가격 책정** 탭을 통해 노드 크기를 사용할 수 있습니다. 포털에서 다양 한 노드 크기와 관련 된 비용도 볼 수 있습니다.

### <a name="virtual-machine-sizes"></a>가상 머신 크기

클러스터를 배포하는 경우 배포하려는 솔루션에 기반하여 컴퓨팅 리소스를 선택하세요. HDInsight 클러스터에 사용하는 VM은 다음과 같습니다.

* A 및 D1-4 시리즈 VM: [범용 Linux VM 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 시리즈 VM: [메모리 최적화 Linux VM 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

다른 SDK를 사용하여 클러스터를 만들거나 Azure PowerShell을 사용하는 동안 VM을 지정하기 위해 사용해야 하는 값을 확인하려면 [HDInsight 클러스터에 사용할 VM 크기](../cloud-services/cloud-services-sizes-specs.md#size-tables)를 참조하세요. 이 연결된 문서에서 테이블의 **크기** 열의 값을 사용합니다.

> [!IMPORTANT]  
> 클러스터에서 32 개 이상의 작업자 노드를 필요로 하는 경우 최소 8 개의 코어와 14GB RAM을 가진 헤드 노드 크기를 선택 해야 합니다.

자세한 내용은 [가상 컴퓨터의 크기](../virtual-machines/windows/sizes.md)를 참조 하세요. 다양한 크기의 가격 책정에 대한 자세한 내용은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight)을 참조하세요.

### <a name="add-application"></a>애플리케이션 추가

HDInsight 애플리케이션은 Linux 기반 HDInsight 클러스터에 사용자가 설치할 수 있는 애플리케이션입니다. Microsoft, 타사에서 제공하거나 또는 직접 개발한 애플리케이션을 사용할 수 있습니다. 자세한 내용은 [Azure HDInsight에 타사 Apache Hadoop 애플리케이션 설치](hdinsight-apps-install-applications.md)를 참조하세요.

HDInsight 애플리케이션 대부분은 빈 Edge 노드에 설치됩니다.  빈 에지 노드는 헤드 노드에 설치되고 구성된 것과 동일한 클라이언트 도구를 사용하는 Linux 가상 머신입니다. 클러스터에 액세스하고, 클라이언트 애플리케이션을 테스트하며 클라이언트 애플리케이션을 호스트하는 데 에지 노드를 사용할 수 있습니다. 자세한 내용은 [HDInsight에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)을 참조하세요.

### <a name="script-actions"></a>스크립트 작업

만드는 동안 스크립트를 사용하여 추가 구성 요소를 설치하거나 클러스터 구성을 사용자 지정할 수 있습니다. 해당 스크립트는 **스크립트 작업**을 통해 호출됩니다. 스크립트 작업은 Azure 포털, HDInsight Windows PowerShell cmdlet 또는 HDInsight .NET SDK에서 사용할 수 있는 구성 옵션입니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)(영문)을 참조하세요.

Apache Mahout, Cascading 등의 일부 네이티브 Java 구성 요소는 클러스터에서 JAR(Java Archive) 파일로 실행할 수 있습니다. 이러한 JAR 파일은 Azure Storage에 배포되고 Hadoop 작업 제출 메커니즘을 통해 HDInsight 클러스터에 제출될 수 있습니다. 자세한 내용은 [프로그래밍 방식으로 Apache Hadoop 작업 제출](hadoop/submit-apache-hadoop-jobs-programmatically.md)을 참조하세요.

> [!NOTE]  
> HDInsight 클러스터에 JAR 파일을 배포하거나 HDInsight 클러스터에서 JAR 파일을 호출하는 데 문제가 있는 경우 [Microsoft 지원](https://azure.microsoft.com/support/options/)으로 문의하세요.
>
> Cascading은 HDInsight에서 지원되지 않으며 Microsoft 지원 대상이 아닙니다. 지원 되는 구성 요소 목록은 [HDInsight에서 제공 하는 클러스터 버전의 새로운 기능](hdinsight-component-versioning.md)을 참조 하세요.

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

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight를 사용 하 여 클러스터 만들기 오류 문제 해결](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [HDInsight, Apache Hadoop 에코시스템 및 Hadoop 클러스터란?](hadoop/apache-hadoop-introduction.md)
* [HDInsight에서 Apache Hadoop 사용 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
