---
title: Azure HDInsight의 클러스터 용량 계획
description: 용량 및 성능을 위해 HDInsight 클러스터를 지정하는 방법입니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: hrasheed
ms.openlocfilehash: 81be4d5809fe8a3f36f7364b15e19d0046a8b5cd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125156"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 클러스터의 용량 계획

HDInsight 클러스터를 배포하기 전에 필요한 성능 및 크기를 확인하여 원하는 클러스터 용량을 계획합니다. 이러한 계획은 유용성 및 비용을 둘 다 최적화합니다. 일부 클러스터 용량 결정은 배포 후에 변경할 수 없습니다. 성능 매개 변수가 변경되면 저장된 데이터를 손실하지 않으면서 클러스터를 분해한 후 다시 만들 수 있습니다.

용량 계획을 위해 확인할 핵심 질문은 다음과 같습니다.

* 클러스터를 어떤 지리적 지역에 배포하려고 하나요?
* 저장 공간은 얼마나 필요한가요?
* 어떤 클러스터 유형을 배포해야 하나요?
* 클러스터 노드는 어떤 크기 및 유형의 VM(가상 머신)을 사용해야 하나요?
* 클러스터는 몇 개의 작업자 노드가 있어야 하나요?

## <a name="choose-an-azure-region"></a>Azure 지역 선택

Azure 지역은 클러스터가 물리적으로 프로비전되는 위치를 결정합니다. 읽기 및 쓰기 대기 시간을 최소화하려면 클러스터가 데이터 근처에 있어야 합니다.

HDInsight는 여러 Azure 지역에서 사용할 수 있습니다. 가장 가까운 지역을 찾으려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)의 *분석*에서 *HDInsight* 항목을 참조하세요.

## <a name="choose-storage-location-and-size"></a>저장소 위치 및 크기 선택

### <a name="location-of-default-storage"></a>기본 저장소의 위치

Azure Storage 계정 또는 Azure Data Lake Storage에 해당하는 기본 스토리지는 클러스터와 동일한 위치에 있어야 합니다. Azure Storage는 모든 위치에서 사용할 수 있습니다. Data Lake Storage Gen1은 일부 지역에서 사용할 수 있습니다. [지역별로 사용 가능한 Azure 제품](https://azure.microsoft.com/regions/services/)의 *Storage*에서 현재 Data Lake Storage 가용성을 참조하세요.

### <a name="location-of-existing-data"></a>기존 데이터의 위치

데이터를 포함하는 스토리지 계정 또는 Data Lake Storage가 이미 있으며 이 스토리지를 클러스터의 기본 스토리지로 사용하려면 같은 위치에 클러스터를 배포해야 합니다.

### <a name="storage-size"></a>스토리지 크기

HDInsight 클러스터를 배포한 후에 추가 Azure Storage 계정을 연결하거나 다른 Data Lake Storage에 액세스할 수 있습니다. 모든 저장소 계정은 클러스터와 동일한 위치에 있어야 합니다. Data Lake Storage는 다른 위치에 있을 수 있으며 이 경우 데이터 읽기/쓰기 대기 시간이 발생할 수 있습니다.

Azure Storage에는 일부 [용량 제한](../azure-subscription-service-limits.md#storage-limits)이 있지만 Data Lake Storage Gen1은 사실상 제한이 없습니다.

클러스터는 다른 저장소 계정 조합에 액세스할 수 있습니다. 일반적인 예제:

* 데이터 양이 단일 Blob Storage 컨테이너의 스토리지 용량을 초과하기 쉬운 경우
* Blob 컨테이너에 대한 액세스 속도가 임계값을 초과하여 제한이 발생하는 경우
* 데이터를 만들려고 하는 데 이미 클러스터에서 사용할 수 있는 Blob 컨테이너를 업로드한 경우
* 보안상의 이유나 관리 간소화를 위해 저장소의 다른 부분을 격리하려고 하는 경우

48 노드 클러스터의 경우 4~8개 스토리지 계정이 권장됩니다. 총 스토리지 이미 충분할 수 있지만 각 스토리지 계정이 계산 노드에 대해 추가 네트워킹 대역폭을 제공합니다. 저장소 계정이 여러 개 있는 경우 각 저장소 계정에 대해 접두사 없는 임의 이름을 사용합니다. 임의 이름을 지정하는 목적은 모든 계정에서 저장소 병목 상태(제한) 또는 일반 모드 오류가 발생할 가능성을 줄이려는 것입니다. 성능 향상을 위해서는 저장소 계정당 하나의 컨테이너만 사용합니다.

## <a name="choose-a-cluster-type"></a>클러스터 유형 선택

클러스터 유형은 HDInsight 클러스터(예: [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/) 또는 [Apache Spark](https://spark.apache.org/))가 실행하도록 구성되는 워크로드를 결정합니다. 사용 가능한 클러스터 형식의 대한 자세한 설명은 [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight)를 참조하세요. 각 클러스터 유형은 필요한 노드 크기 및 개수를 포함하는 배포 토폴로지를 갖습니다.

## <a name="choose-the-vm-size-and-type"></a>VM 크기 및 유형 선택

클러스터 유형마다 노드 유형 집합이 있으며 각 노드 유형은 VM 크기 및 유형에 대한 특정 옵션을 제공합니다.

사용하는 애플리케이션에 대해 최적의 클러스터 크기를 결정하려면 클러스터 용량을 벤치마킹하고 지정된 대로 크기를 늘릴 수 있습니다. 예를 들어 시뮬레이트한 워크로드 또는 *카나리아 쿼리*를 사용할 수 있습니다. 시뮬레이트한 워크로드를 사용할 경우 크기가 다른 클러스터에서 예상되는 워크로드를 실행하면서 원하는 성능이 도달할 때까지 크기를 서서히 늘려봅니다. 카나리아 쿼리는 다른 프로덕션 쿼리 간에 주기적으로 삽입하여 클러스터에 충분한 리소스가 있는지 여부를 표시할 수 있습니다.

VM 크기와 유형은 CPU 처리 성능, RAM 크기 및 네트워크 대기 시간에 따라 결정됩니다.

* CPU: VM 크기는 코어 수를 나타냅니다. 코어 수가 많을수록 각 노드가 달성할 수 있는 병렬 계산 수준이 높아집니다. 또한 일부 VM 유형은 더 빠른 코어를 포함합니다.

* RAM: VM 크기는 VM에서 사용할 수 있는 RAM의 양을 나타내기도 합니다. 데이터를 디스크에서 읽지 않고, 처리를 위해 데이터를 메모리에 저장하는 워크로드의 경우, 작업자 노드에 데이터에 맞는 충분한 메모리가 있는지 확인합니다.

* 네트워크: 대부분의 클러스터 유형에서 클러스터에 의해 처리된 데이터는 로컬 디스크에 저장되지 않고 Data Lake Storage 또는 Azure Storage와 같은 외부 스토리지 서비스에 저장됩니다. 노드 VM 및 저장소 서비스 간의 네트워크 대역폭과 처리량을 고려합니다. VM에 사용할 수 있는 네트워크 대역폭은 일반적으로 더 크게 늘어납니다. 자세한 내용은 [VM 크기 개요](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)를 참조하세요.

## <a name="choose-the-cluster-scale"></a>클러스터 확장 선택

클러스터 확장은 VM 노드의 수량에 따라 결정됩니다. 모든 클러스터 유형에는 특정 크기를 갖는 노드 유형과 확장을 지원하는 노드 유형이 있습니다. 예를 들어 클러스터에 정확히 3개의 [Apache ZooKeeper](https://zookeeper.apache.org/) 노드 또는 2개의 Head 노드가 필요할 수 있습니다. 분산 방식으로 데이터 처리를 수행하는 작업자 노드는 추가 작업자 노드를 추가하는 확장을 통해 이점을 얻을 수 있습니다.

클러스터 유형에 따라 작업자 노드 수를 늘리면 계산 용량(예: 추가 코어)이 더 추가되지만, 전체 클러스터가 처리되는 데이터의 메모리 내 저장소를 지원하는 데 필요한 총 메모리 양도 추가될 수 있습니다. VM 크기 및 유형을 선택하는 경우처럼 올바른 클러스터 크기 선택도 시뮬레이트된 워크로드 또는 카나리아 쿼리를 사용하여 실험적으로 진행됩니다.

최대 부하 요구를 충족하도록 클러스터를 확장한 후 해당 추가 노드가 더 이상 필요하지 않을 때 축소할 수 있습니다. 자세한 내용은 [HDInsight 클러스터 크기 조정](hdinsight-scaling-best-practices.md)을 참조하세요.

### <a name="cluster-lifecycle"></a>클러스터 수명 주기

클러스터의 수명은 사용자의 책임입니다. 특정 시간에만 클러스터를 가동하고 실행해야 하는 경우 [Azure Data Factory를 사용하여 요청 시 클러스터를 만들 수 있습니다](hdinsight-hadoop-create-linux-clusters-adf.md). 클러스터를 프로비전하고 삭제하는 PowerShell 스크립트를 만든 다음 [Azure Automation](https://azure.microsoft.com/services/automation/)을 사용하여 해당 스크립트를 예약할 수도 있습니다.

> [!NOTE]  
> 클러스터를 삭제하면 해당 기본 Hive metastore도 삭제됩니다. 다음에 클러스터를 다시 만들 때를 대비해서 metastore를 보존하려면 Azure Database 또는 [Apache Oozie](https://oozie.apache.org/)와 같은 외부 메타데이터 저장소를 사용합니다.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>클러스터 작업 오류 격리

경우에 따라 오류가 여러 맵의 병렬 실행으로 인해 발생하고, 다중 노드 클러스터의 구성 요소를 줄일 수 있습니다. 문제를 격리하는 데 도움이 되도록 단일 노드 클러스터에서 여러 동시 작업을 실행하여 분산 테스트를 시도한 다음, 이 방식을 확장하여 둘 이상의 노드를 포함하는 클러스터에서 여러 작업을 동시에 실행해봅니다. Azure에서 단일 노드 HDInsight 클러스터를 만들려면 *고급* 옵션을 사용합니다.

로컬 컴퓨터에서 단일 노드 개발 환경을 설치하고 해당 환경에서 솔루션을 테스트할 수도 있습니다. Hortonworks는 초기 개발, 개념 증거 및 테스트에 유용한 Hadoop 기반 솔루션을 위한 단일 노드 로컬 개발 환경을 제공합니다. 자세한 내용은 [Hortonworks 샌드박스](https://hortonworks.com/products/hortonworks-sandbox/)를 참조하세요.

로컬 단일 노드 클러스터의 문제를 식별하려면 실패한 작업을 다시 실행하고 입력 데이터를 조정하거나 더 작은 데이터 세트를 사용할 수 있습니다. 이러한 작업을 실행하는 방법은 플랫폼 및 애플리케이션 유형에 따라 달라집니다.

## <a name="quotas"></a>할당량

대상 클러스터 VM 크기, 확장 및 유형을 결정했으면 구독의 현재 할당량 용량 제한을 확인합니다. 할당량 제한에 도달하면 새 클러스터를 배포하거나, 작업자 노드를 더 추가하여 기존 클러스터를 확장하지 못할 수 있습니다. 유일한 할당량 제한은 각 구독에 대한 지역 수준에서 존재하는 CPU 코어 할당량입니다. 예를 들어 구독에는 미국 동부 지역에서 30 코어 제한이 있을 수 있습니다. 할당량 증가를 요청해야 하는 경우 다음 단계를 수행합니다.

1. Azure Portal로 이동
1. 페이지 맨 아래 왼쪽에서 **도움말 및 지원**을 클릭합니다.
1. **새 지원 요청**을 클릭합니다.
1. **새 지원 요청** 페이지의 **기본 사항** 탭에서 다음 옵션을 선택합니다.
   - **문제 유형**: **서비스 및 구독 제한(할당량)**
   - **구독**: 수정하려는 구독
   - **할당량 유형**: **HDInsight**
    
     ![HDInsight 코어 할당량을 늘리는 지원 요청 만들기](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. **다음**을 클릭합니다.
1. **세부 정보** 페이지에서 문제에 대한 설명을 입력하고, 해당 문제의 심각도를 선택하고, 기본 연락 방법을 선택합니다.
1. **다음: 리뷰 + 만들기**를 클릭합니다.
1. **리뷰 + 만들기** 탭에서 **만들기**를 클릭합니다.

> [!NOTE]  
> 개인 영역에서 HDInsight 코어 할당량을 늘려야 하는 경우 [허용 목록에 추가 요청을 제출합니다](https://aka.ms/canaryintwhitelist).

[지원 서비스에 문의하여 할당량 증가를 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)할 수 있습니다.

그러나 몇 가지 고정된 할당량 제한이 있습니다. 예를 들어 단일 Azure 구독에는 코어가 10,000개까지 있을 수 있습니다. 이러한 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Apache Hadoop, Spark, Kafka 등을 사용하여 HDInsight에서 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md): Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services 또는 Storm을 사용하여 HDInsight에서 클러스터를 설정 및 구성하는 방법을 알아봅니다.
* [클러스터 성능 모니터링](hdinsight-key-scenarios-to-monitor.md): 클러스터의 용량에 영향을 줄 수 있는 HDInsight 클러스터를 모니터링하는 주요 시나리오에 대해 알아봅니다.
