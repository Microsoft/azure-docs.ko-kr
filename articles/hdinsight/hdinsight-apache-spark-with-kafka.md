---
title: Apache Kafka에서 Apache Spark 스트리밍 - Azure HDInsight
description: DStreams를 사용하여 Apache Kafka 간에 데이터를 스트리밍하기 위해 Apache Spark를 사용하는 방법을 알아봅니다. 이 예제에서는 HDInsight의 Spark에서 Jupyter Notebook을 사용하여 데이터를 스트리밍합니다.
keywords: kafka 예제,kafka zookeeper,spark 스트리밍 kafka,spark 스트리밍 kafka 예제
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e0c39ae5f5c23ae0715ef1eee38b6dd34704538a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097152"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>HDInsight에서 Apache Kafka를 사용한 Apache Spark 스트리밍(DStream) 예제

[DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html)를 사용하여 HDInsight에서 [Apache Kafka](https://kafka.apache.org/) 간에 데이터를 스트리밍하기 위해 [Apache Spark](https://spark.apache.org/)를 사용하는 방법을 알아봅니다. 이 예제에서는 Spark 클러스터에서 실행되는 [Jupyter Notebook](https://jupyter.org/)을 사용합니다.

> [!NOTE]  
> 이 문서의 단계는 HDInsight의 Spark와 HDInsight의 Kafka 클러스터를 모두 포함하는 Azure 리소스 그룹을 만듭니다. 이러한 클러스터는 모두 Azure Virtual Network에 있으며, 여기서는 Spark 클러스터와 Kafka 클러스터 간에 직접 통신할 수 있습니다.
>
> 이 문서의 단계를 완료하는 경우 과도한 요금이 청구되지 않도록 클러스터를 삭제해야 합니다.

> [!IMPORTANT]  
> 이 예에서는 이전 Spark 스트리밍 기술인 DStreams을 사용합니다. 최신 Spark 스트리밍 기능을 사용하는 예제는 [Apache Kafka를 사용하는 Spark 구조적 스트리밍](hdinsight-apache-kafka-spark-structured-streaming.md) 문서를 참조하세요.

## <a name="create-the-clusters"></a>클러스터 만들기

HDInsight의 Apache Kafka는 공용 인터넷을 통한 액세스를 Kafka broker에 제공하지 않습니다. Kafka와 통신하는 대상은 Kafka 클러스터의 노드와 동일한 Azure 가상 네트워크에 있어야 합니다. 여기서는 Kafka 클러스터와 Spark 클러스터가 모두 Azure 가상 네트워크에 있습니다. 클러스터 간의 통신 흐름을 보여 주는 다이어그램은 다음과 같습니다.

![Azure 가상 네트워크에 있는 Spark 및 Kafka 클러스터 다이어그램](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Kafka 자체는 가상 네트워크 내의 통신으로 제한되지만, 클러스터의 다른 서비스(예: SSH, Ambari)는 인터넷을 통해 액세스할 수 있습니다. HDInsight에서 사용할 수 있는 공용 포트에 대한 자세한 내용은 [HDInsight에서 사용하는 포트 및 URI](hdinsight-hadoop-port-settings-for-services.md)를 참조하세요.

Azure 가상 네트워크, Kafka 클러스터 및 Spark 클러스터를 수동으로 만들 수 있지만 Azure Resource Manager 템플릿을 사용하는 것이 더 쉽습니다. 다음 단계에 따라 Azure 가상 네트워크, Kafka 클러스터 및 Spark 클러스터를 Azure 구독에 배포합니다.

1. Azure에 로그인하고 Azure Portal에서 템플릿을 열려면 다음 단추를 사용합니다.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Azure Resource Manager 템플릿의 위치는 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**입니다.

    > [!WARNING]  
    > HDInsight에서 Kafka의 사용 가능성을 보장하려면 클러스터에 작업자 노드가 3개 이상 포함되어야 합니다. 이 템플릿은 세 개의 작업자 노드를 포함하는 Kafka 클러스터를 만듭니다.

    이 템플릿은 Kafka와 Spark 둘 다에 대해 HDInsight 3.6 클러스터를 만듭니다.

2. 다음 정보를 사용하여 **사용자 지정 배포** 섹션의 항목을 채웁니다.
   
    ![HDInsight 사용자 지정 배포](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **리소스 그룹**: 그룹을 만들거나 기존 그룹을 선택합니다. 이 그룹에는 HDInsight 클러스터가 포함됩니다.

    * **위치**: 지리적으로 가까운 위치를 선택합니다.

    * **기본 클러스터 이름**: 이 값은 Spark 및 Kafka 클러스터의 기본 이름으로 사용됩니다. 예를 들어, **hdistreaming**를 입력하면 __spark-hdistreaming__라는 Spark 클러스터와 **kafka-hdistreaming**라는 Kafka 클러스터가 생성됩니다.

    * **클러스터 로그인 사용자 이름**: Spark 및 Kafka 클러스터의 관리 사용자 이름입니다.

    * **클러스터 로그인 암호**: Spark 및 Kafka 클러스터의 관리자 사용자 암호입니다.

    * **SSH 사용자 이름**: Spark 및 Kafka 클러스터에 만들 SSH 사용자입니다.

    * **SSH 암호**: Spark 및 Kafka 클러스터에 대한 SSH 사용자의 암호입니다.

3. **사용 약관**을 읽은 다음 **위에 명시된 사용 약관에 동의함**을 선택합니다.

4. 마지막으로, **구매**를 선택합니다. 클러스터를 만드는 데 약 20분이 걸립니다.

리소스를 만든 후에 요약 페이지가 나타납니다.

![VNet 및 클러스터에 대한 리소스 그룹 요약](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]  
> HDInsight 클러스터의 이름은 **spark-BASENAME** 및 **kafka-BASENAME**이며, 여기서 BASENAME은 템플릿에 제공된 이름입니다. 이후 단계에서 클러스터에 연결할 때 이러한 이름을 사용합니다.

## <a name="use-the-notebooks"></a>노트북 사용

이 문서에 설명된 예제에 대한 코드는 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)에서 지원됩니다.

이 예제를 완료하려면 `README.md`의 단계를 따르세요.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

이 문서의 단계를 수행하면 동일한 Azure 리소스 그룹에 두 클러스터가 모두 만들어지므로 Azure Portal에서 해당 리소스 그룹을 삭제할 수 있습니다. 그룹을 삭제하면 이 문서의 단계를 수행하여 만들어진 모든 리소스, Azure Virtual Network 및 클러스터에서 사용하는 저장소 계정이 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 예제에서는 Spark를 사용하여 Kafka에(서) 쓰고 읽는 방법을 배웠습니다. Kafka를 사용하는 다른 방법을 찾으려면 다음 링크를 사용하세요.

* [HDInsight에서 Apache Kafka 시작](kafka/apache-kafka-get-started.md)
* [MirrorMaker를 사용하여 HDInsight에 Apache Kafka 복제본 만들기](kafka/apache-kafka-mirroring.md)
* [HDInsight에서 Apache Storm 및 Apache Kafka 사용](hdinsight-apache-storm-with-kafka.md)

