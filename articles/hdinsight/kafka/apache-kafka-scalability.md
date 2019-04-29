---
title: Apache Kafka 규모 늘리기 - Azure HDInsight
description: 확장성을 높이기 위해 Azure HDInsight에서 Apache Kafka 클러스터에 대해 관리 디스크를 구성하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: a3c4f78e9d3b46b66cb2a893c4eed39f865172dc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114977"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>HDInsight에서 Apache Kafka에 대한 확장성 및 저장소 구성

HDInsight에서 [Apache Kafka](https://kafka.apache.org/)에 사용된 관리 디스크 수를 구성하는 방법을 알아봅니다.

HDInsight의 Kafka는 HDInsight 클러스터에서 가상 머신의 로컬 디스크를 사용합니다. Kafka는 입출력이 매우 많으며 높은 처리량을 제공하고 노드당 더 많은 저장소를 제공하기 위해 [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)를 사용합니다. Kafka에는 기존 VHD(가상 하드 드라이브)가 사용된 경우 각 노드는 1TB로 제한됩니다. 관리 디스크를 통해 여러 디스크를 사용하여 클러스터에서 노드당 16TB를 달성할 수 있습니다.

다음 다이어그램은 관리 디스크가 있기 전, HDInsight의 Kafka와 관리 디스크가 있는 HDInsight의 Kafka를 비교하여 보여줍니다.

![vm당 하나의 vhd를 사용하는 HDInsight의 Kafka와 vm당 여러 관리 디스크가 있는 Kafka를 보여주는 다이어그램](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>관리 디스크 구성: Azure portal

1. [HDInsight 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)의 단계에 따라 포털을 사용하여 클러스터를 만드는 일반적인 단계를 이해합니다. 포털 생성 프로세스를 완료하지 마세요.

2. __클러스터 크기__ 섹션에서 __작업자 노드당 디스크__ 필드를 사용하여 디스크 수를 구성합니다.

    > [!NOTE]  
    > 관리 디스크 유형은 __표준__(HDD) 또는 __프리미엄__(SSD)일 수 있습니다. 프리미엄 디스크는 DS 및 GS 시리즈 VM에 사용됩니다. 다른 모든 VM 유형은 표준을 사용합니다.

    ![작업자 노드당 디스크가 강조 표시된 클러스터 크기 섹션의 이미지](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>관리 디스크 구성: Resource Manager 템플릿

Kafka 클러스터에서 작업자 노드에 사용된 디스크 수를 제어하려면 템플릿의 다음 섹션을 사용합니다.

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

[https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)에서 관리되는 디스크를 구성하는 방법을 보여주는 전체 템플릿을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 Apache Kafka를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [MirrorMaker를 사용하여 HDInsight에 Apache Kafka 복제본 만들기](apache-kafka-mirroring.md)
* [HDInsight에서 Apache Storm 및 Apache Kafka 사용](../hdinsight-apache-storm-with-kafka.md)
* [HDInsight에서 Apache Spark 및 Apache Kafka 사용](../hdinsight-apache-spark-with-kafka.md)
* [Azure Virtual Network를 통해 Apache Kafka에 연결](apache-kafka-connect-vpn-gateway.md)

* [Apache Kafka와 관리 디스크에 대한 HDInsight 블로그](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
