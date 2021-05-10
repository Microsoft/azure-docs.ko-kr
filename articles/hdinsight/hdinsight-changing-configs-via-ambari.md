---
title: Azure HDInsight에서 Apache Ambari를 사용하여 클러스터 최적화
description: Apache Ambari 웹 UI를 사용하여 Azure HDInsight 클러스터를 구성하고 최적화합니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 2146ccb0c4d7f263c3e1a69db9b172649fcd25ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863499"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight에서 Apache Ambari를 사용하여 클러스터 최적화

HDInsight는 대규모 데이터 처리 애플리케이션을 위한 Apache Hadoop 클러스터를 제공합니다. 이렇게 복잡한 다중 노드 클러스터를 관리, 모니터링 및 최적화하는 작업은 어려울 수 있습니다. Apache Ambari는 HDInsight Linux 클러스터를 관리하고 모니터링하는 웹 인터페이스입니다.

Ambari Web UI 사용에 대한 소개는 [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

`https://CLUSTERNAME.azurehdidnsight.net`에서 클러스터 자격 증명을 사용하여 Ambari에 로그인합니다. 초기 화면에 개요 대시보드가 표시됩니다.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png" alt-text="표시되는 Apache Ambari 사용자 대시보드":::

Ambari 웹 UI는 호스트, 서비스, 경고, 구성 및 보기를 관리하는 데 사용됩니다. Ambari는 HDInsight 클러스터를 만들거나 서비스를 업그레이드하는 데는 사용할 수 없습니다. 또한 스택 및 버전 관리, 호스트 서비스 해제 또는 재승인, 클러스터에 서비스 추가 등의 작업에도 Ambari를 사용할 수 없습니다.

## <a name="manage-your-clusters-configuration"></a>클러스터 구성 관리

구성 설정은 특정 서비스를 조정하는 데 도움이 됩니다. 서비스의 구성 설정을 수정하려면 **서비스** 사이드바(왼쪽)에서 서비스를 선택합니다. 그런 다음, 서비스 세부 정보 페이지에서 **구성** 탭으로 이동합니다.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png" alt-text="Apache Ambari Services 사이드바":::

## <a name="modify-namenode-java-heap-size"></a>NameNode Java 힙 크기 수정

NameNode Java 힙 크기는 클러스터의 로드와 같은 많은 요소에 따라 달라집니다. 이러한 요소에는 파일 수 및 블록 수도 포함됩니다. 기본 크기인 1GB는 대부분의 클러스터에서 잘 작동하지만, 일부 워크로드에는 메모리가 더 많이 또는 적게 필요할 수 있습니다.

NameNode Java 힙 크기를 수정하려면:

1. 서비스 사이드바에서 **HDFS** 를 선택하고 **Configs**(구성) 탭으로 이동합니다.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png" alt-text="Apache Ambari HDFS 구성":::

1. **NameNode Java heap size**(NameNode Java 힙 크기) 설정을 찾습니다. **필터** 텍스트 상자에 특정 설정을 입력하여 찾을 수도 있습니다. 설정 이름 옆에 있는 **펜** 아이콘을 선택합니다.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png" alt-text="Apache Ambari NameNode Java 힙 크기":::

1. 텍스트 상자에 새 값을 입력한 다음 **Enter** 키를 눌러 변경 내용을 저장합니다.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png" alt-text="Ambari NameNode Java 힙 크기1 편집":::

1. NameNode Java 힙 크기가 1GB에서 2GB로 변경되었습니다.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png" alt-text="편집한 NameNode Java 힙 크기2":::

1. 구성 화면 위쪽에서 **저장** 단추를 클릭하여 변경 내용을 저장합니다.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png" alt-text="'Apache Ambari 구성 저장'":::

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI로 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache HBase 최적화](./optimize-hbase-ambari.md)
* [Apache Hive 최적화](./optimize-hive-ambari.md)
* [Apache Pig 최적화](./optimize-pig-ambari.md)
