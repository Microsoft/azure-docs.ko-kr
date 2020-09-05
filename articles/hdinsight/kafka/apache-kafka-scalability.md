---
title: Apache Kafka 규모 늘리기 - Azure HDInsight
description: 확장성을 높이기 위해 Azure HDInsight에서 Apache Kafka 클러스터에 대해 관리 디스크를 구성하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 586094f9e6ee696487064dcfea68af0bfc26c1ae
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649111"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>HDInsight에서 Apache Kafka에 대한 확장성 및 스토리지 구성

HDInsight에서 [Apache Kafka](https://kafka.apache.org/)에 사용된 관리 디스크 수를 구성하는 방법을 알아봅니다.

HDInsight의 Kafka는 HDInsight 클러스터에서 가상 머신의 로컬 디스크를 사용합니다. Kafka는 입출력이 매우 많으며 높은 처리량을 제공하고 노드당 더 많은 스토리지를 제공하기 위해 [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md)를 사용합니다. Kafka에는 기존 VHD(가상 하드 드라이브)가 사용된 경우 각 노드는 1TB로 제한됩니다. 관리 디스크를 통해 여러 디스크를 사용하여 클러스터에서 노드당 16TB를 달성할 수 있습니다.

다음 다이어그램은 관리 디스크가 있기 전, HDInsight의 Kafka와 관리 디스크가 있는 HDInsight의 Kafka를 비교하여 보여줍니다.

![관리 디스크 아키텍처를 사용 하는 kafka](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>관리 디스크 구성: Azure Portal

1. [HDInsight 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)의 단계에 따라 포털을 사용하여 클러스터를 만드는 일반적인 단계를 이해합니다. 포털 만들기 프로세스를 완료 하지 마세요.

2. **Configuration & 가격 책정** 섹션에서 __노드 수__ 필드를 사용 하 여 디스크 수를 구성 합니다.

    > [!NOTE]  
    > 관리 디스크 유형은 __표준__ (HDD) 또는 __프리미엄__ (SSD)일 수 있습니다. 프리미엄 디스크는 DS 및 GS 시리즈 VM에 사용됩니다. 다른 모든 VM 유형은 표준을 사용합니다.

    ![작업자 노드당 디스크가 강조 표시 된 클러스터 크기 섹션](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>관리 디스크 구성: Resource Manager 템플릿

Kafka 클러스터에서 작업자 노드에 사용된 디스크 수를 제어하려면 템플릿의 다음 섹션을 사용합니다.

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

에서 관리 디스크를 구성 하는 방법을 보여 주는 전체 템플릿을 찾을 수 있습니다 [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) .

## <a name="next-steps"></a>다음 단계

HDInsight에서 Apache Kafka를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [MirrorMaker를 사용하여 HDInsight에 Apache Kafka 복제본 만들기](apache-kafka-mirroring.md)
* [HDInsight에서 Apache Storm 및 Apache Kafka 사용](../hdinsight-apache-storm-with-kafka.md)
* [HDInsight에서 Apache Spark 및 Apache Kafka 사용](../hdinsight-apache-spark-with-kafka.md)
* [Azure Virtual Network를 통해 Apache Kafka에 연결](apache-kafka-connect-vpn-gateway.md)

* [Apache Kafka와 관리 디스크에 대한 HDInsight 블로그](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
