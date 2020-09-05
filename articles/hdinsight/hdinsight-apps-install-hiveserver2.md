---
title: Azure HDInsight에서 HiveServer2 크기 조정
description: Edge 노드를 사용 하 여 Azure HDInsight 클러스터에서 HiveServer2을 수평으로 확장 하 여 내결함성 및 가용성을 높입니다.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227398"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>고가용성을 위해 Azure HDInsight 클러스터에서 HiveServer2 크기 조정

클러스터에 추가 HiveServer2를 배포 하 여 가용성 및 부하 분산을 높이는 방법에 대해 알아봅니다. 헤드 노드 크기를 늘릴 필요가 없는 경우에 지 노드를 사용 하 여 HiveServer2를 배포할 수도 있습니다. 

> [!NOTE]
> 사용량에 따라 HiveServer2 수를 늘리면 Hive metastore 연결 수가 증가할 수 있습니다. 또한 Azure SQL database의 크기가 적절 하 게 조정 되었는지 확인 합니다.

## <a name="prerequisites"></a>전제 조건

이 가이드를 사용 하려면 다음 문서를 이해 해야 합니다.
- [HDInsight의 Apache Hadoop 클러스터에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>HiveServer2 설치

이 섹션에서는 대상 호스트에 추가 HiveServer2를 설치 합니다.

1. 브라우저에서 Ambari을 열고 대상 호스트를 클릭 합니다.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Ambari의 호스트 메뉴입니다.":::

2. 추가 단추를 클릭 하 고 HiveServer2를 클릭 합니다.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="호스트의 HiveServer2 panel을 추가 합니다.":::

3. 를 확인 하 고 프로세스를 실행 합니다. 모든 원하는 호스트에 대해 1-3을 반복 합니다.

4. 설치가 완료 되 면 오래 된 configs를 사용 하 여 모든 서비스를 다시 시작 하 고 HiveServer2를 시작 합니다.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="HiveServer2 패널을 시작 합니다.":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 HiveServer2를 클러스터에 설치 하는 방법을 배웠습니다. 에 지 노드 및 응용 프로그램에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Edge 노드 설치](hdinsight-apps-use-edge-node.md): HDInsight 클러스터에에 지 노드를 설치 하는 방법을 알아봅니다.
* [HDInsight 애플리케이션 설치](hdinsight-apps-install-applications.md): 클러스터에 HDInsight 애플리케이션을 설치하는 방법을 알아봅니다.
* [AZURE SQL Dtu 연결 제한](../azure-sql/database/resource-limits-dtu-single-databases.md): DTU를 사용 하 여 azure sql database 제한에 대해 알아봅니다.
* [AZURE Sql vCore 연결 제한: vcore](../azure-sql/database/resource-limits-vcore-elastic-pools.md)를 사용 하 여 azure sql database 제한에 대해 알아봅니다.
