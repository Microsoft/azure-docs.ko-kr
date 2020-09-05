---
title: Apache HBase에 대한 Azure HDInsight 가속 쓰기
description: Premium managed disks를 사용 하 여 Apache HBase Write 미리 로그의 성능을 개선 하는 Azure HDInsight 가속 쓰기 기능의 개요를 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: 99253aa2e7e2e1f3f58f2ab7d5c40a695c2b9690
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654857"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Apache HBase에 대한 Azure HDInsight 가속 쓰기

이 문서에서는 Azure HDInsight의 Apache HBase에 대 한 **가속화 된 쓰기** 기능 및 쓰기 성능을 향상 시키기 위해 효과적으로 사용할 수 있는 방법에 대 한 배경 지식을 제공 합니다. **가속화 된 쓰기** 는 [Azure premium SSD 관리 디스크](../../virtual-machines/disks-types.md#premium-ssd) 를 사용 하 여 Apache HBase WRITE 미리 로그 (WAL)의 성능을 향상 시킵니다. Apache HBase에 대해 자세히 알아보려면 [HDInsight의 Apache hbase 란?](apache-hbase-overview.md)을 참조 하세요.

## <a name="overview-of-hbase-architecture"></a>HBase 아키텍처 개요

HBase에서 **행** 은 하나 이상의 **열** 로 구성 되며 **행 키**로 식별 됩니다. 여러 행이 **테이블**을 구성 합니다. 열에는 해당 열에 있는 값의 타임 스탬프 버전인 **셀**이 포함 됩니다. 열은 **열**패밀리로 그룹화 되 고, 열 패밀리의 모든 열은 **hfiles**라는 저장소 파일에 함께 저장 됩니다.

HBase의 **지역은** 데이터 처리 부하의 균형을 유지 하는 데 사용 됩니다. HBase는 먼저 단일 지역에 테이블의 행을 저장 합니다. 행은 테이블의 데이터 양이 증가할수록 여러 지역에 걸쳐 분산 됩니다. **지역 서버** 는 여러 지역에 대 한 요청을 처리할 수 있습니다.

## <a name="write-ahead-log-for-apache-hbase"></a>Apache HBase의 미리 쓰기 로그

HBase는 먼저 데이터 업데이트를 WAL (Write 앞선 로그) 이라는 커밋 로그 형식에 기록 합니다. 업데이트는 WAL에 저장 된 후 메모리 내 **Memstore**에 기록 됩니다. 메모리의 데이터가 최대 용량에 도달 하면 디스크에 **Hfile**로 기록 됩니다.

MemStore를 플러시하는 동안에는 지역 **서버가** 충돌 하거나 사용할 수 없게 되 면 미리 쓰기 로그를 사용 하 여 업데이트를 재생할 수 있습니다. WAL을 사용 하지 않을 경우 **Hfile**에 업데이트를 플러시하는 동안 지역 **서버가** 충돌 하면 해당 업데이트가 모두 손실 됩니다.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Apache HBase 용 Azure HDInsight의 가속화 되는 쓰기 기능

가속화 된 쓰기 기능은 클라우드 저장소에 있는 미리 쓰기 로그를 사용 하 여 발생 하는 더 높은 쓰기 대기 시간 문제를 해결 합니다.  HDInsight Apache HBase 클러스터의 가속화 된 쓰기 기능을 통해 프리미엄 SSD 관리 디스크를 모든 영역 서버 (작업자 노드)에 연결 합니다. 그런 다음 쓰기 미리 로그가 클라우드 저장소 대신 이러한 프리미엄 관리 디스크에 탑재 된 HDFS (Hadoop 파일 시스템)에 기록 됩니다.  프리미엄 managed disks는 Ssd (반도체 디스크)를 사용 하 고 내결함성을 갖춘 뛰어난 i/o 성능을 제공 합니다.  관리 되지 않는 디스크와 달리 한 저장소 단위가 중단 되 면 동일한 가용성 집합의 다른 저장 단위에 영향을 주지 않습니다.  결과적으로 managed disks는 응용 프로그램에 대 한 낮은 쓰기 대기 시간과 더 나은 복원 력을 제공 합니다. Azure managed disks에 대해 자세히 알아보려면 [azure managed Disks 소개](../../virtual-machines/managed-disks-overview.md)를 참조 하세요.

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>HDInsight에서 HBase에 대해 가속화 되는 쓰기를 사용 하도록 설정 하는 방법

가속화 된 쓰기 기능을 사용 하 여 새 HBase 클러스터를 만들려면 **3 단계 저장소**에 도달할 때까지 [HDInsight에서 클러스터 설정](../hdinsight-hadoop-provision-linux-clusters.md) 의 단계를 따릅니다. **Metastore 설정**에서 **HBase 가속 쓰기 사용**옆의 확인란을 선택 합니다. 그런 다음 클러스터를 만들기 위한 나머지 단계를 계속 진행 합니다.

![HDInsight Apache HBase에 대 한 가속 쓰기 옵션 사용](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>기타 고려 사항

데이터 내 구성을 유지 하려면 최소 3 개의 작업자 노드를 사용 하 여 클러스터를 만듭니다. 만든 후에는 클러스터를 3 개 미만의 작업자 노드로 확장할 수 없습니다.

클러스터를 삭제 하기 전에 HBase 테이블을 플러시 또는 사용 하지 않도록 설정 하 여 쓰기 미리 로그 데이터를 잃지 않도록 합니다.

```
flush 'mytable'
```

```
disable 'mytable'
```

클러스터를 축소 하는 경우 비슷한 단계를 수행 합니다. 테이블을 플러시하고 들어오는 데이터를 중지 하기 위해 테이블을 사용 하지 않도록 설정 합니다. 클러스터를 3 개 미만의 노드로 확장할 수 없습니다.

이러한 단계를 수행 하면 성공적으로 축소 되 고, 복제 된 파일 또는 임시 파일 때문에 namenode 안전 모드로 전환 되지 않을 수 있습니다.

규모 축소 후에 namenode가 안전 하지 않은 경우 hdfs 명령을 사용 하 여 복제 된 블록을 다시 복제 하 고 안전 모드에서 hdfs를 가져옵니다. 이 다시 복제를 통해 HBase를 성공적으로 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [미리 쓰기 로그 기능의](https://hbase.apache.org/book.html#wal) 공식 Apache HBase 설명서
* 가속화 된 쓰기를 사용 하도록 HDInsight Apache HBase 클러스터를 업그레이드 하려면 [Apache hbase 클러스터를 새 버전으로 마이그레이션](apache-hbase-migrate-new-version.md)을 참조 하세요.
