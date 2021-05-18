---
title: 클러스터를 최신 버전으로 마이그레이션
titleSuffix: Azure HDInsight
description: Azure HDInsight 클러스터를 최신 버전으로 마이그레이션하기 위한 지침을 알아봅니다.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 4aa25368e156ce793e969f866490352e253559fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871727"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight 클러스터를 최신 버전으로 마이그레이션

최신 HDInsight 기능을 활용하려면 HDInsight 클러스터를 정기적으로 최신 버전으로 마이그레이션하는 것이 좋습니다. HDInsight는 기존 클러스터가 최신 구성 요소 버전으로 업그레이드되는 현재 위치 업그레이드를 지원하지 않습니다. 원하는 구성 요소 및 플랫폼 버전으로 새 클러스터를 만든 다음 새 클러스터를 사용하도록 애플리케이션을 마이그레이션해야 합니다. 아래 지침에 따라 HDInsight 클러스터 버전을 마이그레이션합니다.

> [!NOTE]  
> 지원되는 HDInsight 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.

## <a name="migration-tasks"></a>마이그레이션 작업

HDInsight 클러스터를 업그레이드하는 워크플로는 다음과 같습니다.
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="HDInsight 업그레이드 워크플로 다이어그램" border="false":::

1. HDInsight 클러스터를 업그레이드할 때 필요할 수 있는 변경 내용을 이해하려면 이 문서의 각 섹션을 읽어보세요.
2. 클러스터를 테스트/품질 보증 환경으로 만듭니다. 클러스터를 만드는 방법에 대한 자세한 내용은 [Linux 기반 HDInsight 클러스터를 만드는 방법 알아보기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
3. 기존 작업, 데이터 원본 및 싱크를 새 환경으로 복사합니다.
4. 작업이 새 클러스터에서 예상대로 작동하는지 확인하려면 유효성 검사 테스트를 수행합니다.

예상대로 작동하는 것이 확인되면 마이그레이션을 위해 가동 중지 시간을 예약합니다. 이 가동 중지 시간 동안 다음 작업을 수행합니다.

1. 클러스터 노드에 로컬로 저장된 모든 임시 데이터를 백업합니다. 예를 들어 헤드 노드에 직접 저장된 데이터가 있는 경우입니다.
1. [기존 클러스터를 삭제합니다](./hdinsight-delete-cluster.md).
1. 이전 클러스터에서 사용된 것과 동일한 기본 데이터 저장소를 사용하여 최신(또는 지원되는) HDI 버전과 동일한 VNET 서브넷에서 클러스터를 만듭니다. 이렇게 하면 새 클러스터에서 기존의 프로덕션 데이터에 대해 작업을 계속할 수 있습니다.
1. 백업한 모든 임시 데이터를 가져옵니다.
1. 새 클러스터를 사용하여 작업을 시작하거나 계속 처리합니다.

## <a name="workload-specific-guidance"></a>워크로드 관련 지침

다음 문서에서는 특정 워크로드를 마이그레이션하는 방법에 대한 지침을 제공합니다.

* [HBase 마이그레이션](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka 마이그레이션](./kafka/migrate-versions.md)
* [Hive/Interactive Query 마이그레이션](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>백업 및 복원

데이터베이스 백업 및 복원에 대한 자세한 내용은 [자동화된 데이터베이스 백업을 사용하여 Azure SQL Database에서 데이터베이스 복구](../azure-sql/database/recovery-using-backups.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Linux 기반 HDInsight 클러스터를 만드는 방법 알아보기](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH를 사용하여 HDInsight에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache Ambari를 사용하여 Linux 기반 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [HDInsight 릴리스 정보](./hdinsight-version-release.md)
