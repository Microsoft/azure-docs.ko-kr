---
title: Apache Ambari를 사용하여 클러스터 구성 최적화 - Azure HDInsight
description: Apache Ambari 웹 UI를 사용하여 HDInsight 클러스터를 구성하고 최적화합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: f0db36fa380d0d1bb7f2b581c4bf8fa1abfaadaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60698823"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Apache Ambari를 사용하여 HDInsight 클러스터 구성 최적화

HDInsight는 대규모 데이터 처리 애플리케이션을 위한 [Apache Hadoop](https://hadoop.apache.org/) 클러스터를 제공합니다. 이렇게 복잡한 다중 노드 클러스터를 관리, 모니터링 및 최적화하는 작업은 어려울 수 있습니다. [Apache Ambari](https://ambari.apache.org/)는 HDInsight Linux 클러스터를 관리하고 모니터링하는 웹 인터페이스입니다.  Windows 클러스터의 경우 [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)를 사용합니다.

Ambari Web UI 사용에 대한 소개는 [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

`https://CLUSTERNAME.azurehdidnsight.net`에서 클러스터 자격 증명을 사용하여 Ambari에 로그인합니다. 초기 화면에 개요 대시보드가 표시됩니다.

![Ambari 대시보드](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

Ambari 웹 UI는 호스트, 서비스, 경고, 구성 및 보기를 관리하는 데 사용됩니다. HDInsight 클러스터 생성, 서비스 업그레이드, 스택 및 버전 관리, 호스트 서비스 해제 또는 재승인, 클러스터에 서비스 추가 등의 작업에는 Ambari를 사용할 수 없습니다.

## <a name="manage-your-clusters-configuration"></a>클러스터 구성 관리

구성 설정은 특정 서비스를 조정하는 데 도움이 됩니다. 서비스의 구성 설정을 수정하려면 **서비스** 사이드바(왼쪽)에서 서비스를 선택한 다음 서비스 세부 정보 페이지에서 **Configs**(구성) 탭으로 이동합니다.

![서비스 사이드바](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>NameNode Java 힙 크기 수정

NameNode Java 힙 크기는 클러스터의 로드, 파일 수 및 블록 수와 같은 많은 요소에 따라 달라집니다. 기본 크기인 1GB는 대부분의 클러스터에서 잘 작동하지만, 일부 워크로드에는 메모리가 더 많이 또는 적게 필요할 수 있습니다. 

NameNode Java 힙 크기를 수정하려면:

1. 서비스 사이드바에서 **HDFS**를 선택하고 **Configs**(구성) 탭으로 이동합니다.

    ![HDFS 구성](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. **NameNode Java heap size**(NameNode Java 힙 크기) 설정을 찾습니다. **필터** 텍스트 상자에 특정 설정을 입력하여 찾을 수도 있습니다. 설정 이름 옆에 있는 **펜** 아이콘을 선택합니다.

    ![NameNode Java 힙 크기](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. 텍스트 상자에 새 값을 입력한 다음 **Enter** 키를 눌러 변경 내용을 저장합니다.

    ![NameNode Java 힙 크기 편집](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. NameNode Java 힙 크기는 2GB에서 1GB로 변경 됩니다.

    ![편집된 NameNode Java 힙 크기](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. 구성 화면 위쪽에서 **저장** 단추를 클릭하여 변경 내용을 저장합니다.

    ![변경 내용 저장](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="apache-hive-optimization"></a>Apache Hive 최적화

다음 섹션에서는 전반적인 Apache Hive 성능을 최적화하기 위한 구성 옵션을 설명합니다.

1. Hive 구성 매개 변수를 수정하려면 서비스 사이드바에서 **Hive**를 선택합니다.
1. **Configs**(구성) 탭으로 이동합니다.

### <a name="set-the-hive-execution-engine"></a>Hive 실행 엔진 설정

Hive는 [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 및 [Apache TEZ](https://tez.apache.org/)라는 두 가지 실행 엔진을 제공합니다. Tez는 MapReduce보다 빠릅니다. HDInsight Linux 클러스터에는 Tez가 기본 실행 엔진으로 있습니다. 실행 엔진을 변경하려면:

1. Hive **Configs**(구성) 탭의 필터 상자에 **실행 엔진**을 입력합니다.

    ![검색 실행 엔진](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. **Optimization**(최적화) 속성의 기본값은 **Tez**입니다.

    ![최적화 - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>매퍼 조정

Hadoop은 단일 파일을 여러 파일로 분할(*매핑*)하여 생성되는 파일을 병렬로 처리하려고 합니다. 매퍼의 수는 분할 수에 따라 달라집니다. 다음 두 가지 구성 매개 변수는 Tez 실행 엔진의 분할 수를 결정합니다.

* `tez.grouping.min-size`: 그룹화된 분할 크기의 하한이며 기본값은 16MB(16,777,216바이트)입니다.
* `tez.grouping.max-size`: 그룹화된 분할 크기의 상한이며 기본값은 1GB(1,073,741,824바이트)입니다.

성능을 위해, 대기 시간 개선하고 처리량을 늘리려면 두 매개 변수를 모두 줄입니다.

예를 들어 크기가 128MB인 데이터에 매퍼 작업을 4개 설정하려면 두 매개 변수를 각각 32MB(33,554,432바이트)로 설정합니다.

1. 제한 매개 변수를 수정하려면 Tez 서비스의 **Configs**(구성) 탭으로 이동합니다. **일반** 패널을 확장하고 `tez.grouping.max-size` 및 `tez.grouping.min-size` 매개 변수를 찾습니다.

1. 두 매개 변수를 모두 **33,554,432** 바이트(32MB)로 설정합니다.

    ![Tez 그룹화 크기](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
이러한 변화는 서버의 모든 Tez 작업에 영향을 미칩니다. 최적의 결과를 내려면 적절한 매개 변수 값을 선택합니다.

### <a name="tune-reducers"></a>리듀서 조정

[Apache ORC](https://orc.apache.org/) 및 [Snappy](https://google.github.io/snappy/)는 모두 고성능을 제공합니다. 하지만 Hive는 기본적으로 리듀서가 너무 적어서 이로 인해 병목 상태가 발생합니다.

예를 들어 입력 데이터 크기가 50GB라고 가정하겠습니다. Snappy 압축을 통한 ORC 형식의 해당 데이터는 1GB입니다. Hive는 필요한 리듀서 수를 매퍼에 입력되는 바이트 수/`hive.exec.reducers.bytes.per.reducer`로 추정합니다.

기본 설정의 경우 이 예는 리듀서가 4개입니다.

`hive.exec.reducers.bytes.per.reducer` 매개 변수는 리듀서당 처리되는 바이트 수를 지정합니다. 기본값은 64MB입니다. 이 값을 낮게 조정하면 병렬 처리가 증가하고 성능이 향상될 수 있습니다. 너무 낮게 조정하면 너무 리듀서가 너무 많이 생성되어 잠재적으로 성능이 저하될 수 있습니다. 이 매개 변수는 특정 데이터 요구 사항, 압축 설정 및 기타 환경 요인을 기반으로 합니다.

1. 매개 변수를 수정하려면 Hive **Configs**(구성) 탭으로 이동하고 설정 페이지에서 **Data per Reducer**(리듀서당 데이터) 매개 변수를 찾습니다.

    ![리듀서당 데이터](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. **편집**을 선택하여 값을 128MB(134,217,728바이트)로 수정한 다음 **Enter**를 눌러 저장합니다.

    ![리듀서당 데이터 - 편집됨](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    리듀서당 데이터가 128MB이며 입력 크기가 1,024MB이면 리듀서는 8개(1024/128)입니다.

1. **Data per Reducer**(리듀서당 데이터) 매개 변수의 값이 올바르지 않으면 많은 수의 리듀서가 생성되어 쿼리 성능에 악영향을 미칠 수 있습니다. 최대 리듀서 수를 제한하려면 `hive.exec.reducers.max`를 적절한 값으로 설정합니다. 기본값은 1009입니다.

### <a name="enable-parallel-execution"></a>병렬 실행 사용

Hive 쿼리는 하나 이상의 단계에서 실행됩니다. 독립적인 단계를 병렬로 실행할 수 있으면 쿼리 성능이 향상됩니다.

1.  병렬 쿼리 실행을 사용하려면 Hive **Config**(구성) 탭으로 이동하여 `hive.exec.parallel` 속성을 검색합니다. 기본값은 False입니다. 값을 true로 변경한 다음 **Enter**를 눌러서 값을 저장합니다.
 
1.  병렬로 실행 하는 작업의 수를 제한 하려면 수정 된 `hive.exec.parallel.thread.number` 속성입니다. 기본값은 8입니다.

    ![Hive 병렬 실행](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>벡터화 사용

Hive는 데이터를 한 행씩 처리합니다. 벡터화는 Hive가 데이터를 한 번에 1개 행이 아니라 1,024개의 행 블록으로 처리하도록 지시합니다. 벡터화는 ORC 파일 형식에만 적용됩니다.

1. 벡터화된 쿼리 실행을 사용하도록 설정하려면 Hive **Configs**(구성) 탭으로 이동하여 `hive.vectorized.execution.enabled` 매개 변수를 검색합니다. Hive 0.13.0 이상에서는 기본값이 true입니다.
 
1. 쿼리의 리듀스 측에 대해 벡터화된 실행을 사용하도록 설정하려면 `hive.vectorized.execution.reduce.enabled` 매개 변수를 true로 설정합니다. 기본값은 False입니다.

    ![Hive 벡터화된 실행](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>CBO(비용 기반 최적화) 사용

기본적으로 Hive는 일련의 규칙에 따라 하나의 최적 쿼리 실행 계획을 찾습니다. CBO(비용 기반 최적화)는 쿼리를 실행할 여러 계획을 평가하고 각 계획에 비용을 할당한 다음 쿼리를 실행하기에 가장 저렴한 계획을 결정합니다.

CBO를 사용하도록 설정하려면 Hive **Configs**(구성) 탭으로 이동하여 `parameter hive.cbo.enable`을 검색한 다음 토글 단추를 **On**으로 전환합니다.

![CBO 구성](./media/hdinsight-changing-configs-via-ambari/cbo.png)

다음과 같은 추가적인 구성 매개 변수는 CBO를 사용할 때 Hive 쿼리 성능을 높입니다.

* `hive.compute.query.using.stats`

    true로 설정하면 Hive는 metastore에 저장된 통계를 사용하여 `count(*)`와 같은 간단한 쿼리에 응답합니다.

    ![CBO 통계](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    열 통계는 CBO를 사용하도록 설정한 경우 생성됩니다. Hive는 metastore에 저장된 열 통계를 사용하여 쿼리를 최적화합니다. 열의 수가 많을수록 각 열의 열 통계를 가져오는 시간이 길어집니다. false로 설정하면 metastore에서 열 통계 가져오기를 사용하지 않도록 설정됩니다.

    ![Hive 통계 설정 열 통계](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    행 수, 데이터 크기 및 파일 크기와 같은 기본 파티션 통계는 metastore에 저장됩니다. true로 설정하면 metastore에서 파티션 통계를 페치합니다. false로 설정하면 파일 시스템에서 파일 크기를 페치하고 행 스키마에서 행 수를 페치합니다.

    ![Hive 통계 설정 파티션 통계](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>중간 압축 사용

맵 작업은 리듀서 작업에 사용되는 중간 파일을 만듭니다. 중간 압축은 중간 파일 크기를 축소합니다.

Hadoop 작업은 일반적으로 I/O 병목 상태가 됩니다. 데이터를 압축하면 I/O 및 전반적인 네트워크 전송 속도를 높일 수 있습니다.

사용 가능한 압축 유형은 다음과 같습니다.

| 형식 | 도구 | 알고리즘 | 파일 확장명 | 분할 가능? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | 아닙니다. |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | 예 |
| LZO | Lzop | LZO | .lzo | 예(인덱싱된 경우) |
| Snappy | N/A | Snappy | Snappy | 아닙니다. |

일반적으로 분할 가능한 압축 방법이 있는 것이 중요합니다. 그렇지 않으면 매우 적은 수의 매퍼가 생성됩니다. 입력 데이터가 텍스트인 경우 `bzip2`가 최고 옵션입니다. ORC 형식의 경우 Snappy가 가장 빠른 압축 옵션입니다.

1. 중간 압축을 사용하려면 Hive **Configs**(구성) 탭으로 이동한 다음 `hive.exec.compress.intermediate` 매개 변수를 true로 설정합니다. 기본값은 False입니다.

    ![Hive 실행 중간 압축](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > 중간 파일을 압축하려면 코덱의 압축 출력이 높지 않더라도 CPU 비용이 낮은 압축 코덱을 선택합니다.

1. 중간 압축 코덱을 설정하려면 사용자 지정 속성 `mapred.map.output.compression.codec`을 `hive-site.xml` 또는 `mapred-site.xml` 파일에 추가합니다.

1. 사용자 지정 설정을 추가하려면:

    a. Hive **Configs**(구성) 탭으로 이동하여 **고급** 탭을 선택합니다.

    b. **고급** 탭에서 **사용자 지정 hive-site** 창을 찾아서 확장합니다.

    다. 사용자 지정 hive-site 창 아래에서 **속성 추가** 링크를 클릭합니다.

    d. 속성 추가 창에서 키에 `mapred.map.output.compression.codec`을 입력하고 값에 `org.apache.hadoop.io.compress.SnappyCodec`을 입력합니다.

    e. **추가**를 클릭합니다.

    ![Hive 사용자 지정 속성](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    이렇게 하면 Snappy 압축을 사용하여 중간 파일이 압축됩니다. 속성이 추가되면 사용자 지정 hive-site 창에 나타납니다.

    > [!NOTE]  
    > 이 프로시저는 `$HADOOP_HOME/conf/hive-site.xml` 파일을 수정합니다.

### <a name="compress-final-output"></a>압축 최종 출력

최종 Hive 출력도 압축될 수 있습니다.

1. 최종 Hive 출력을 압축하려면 Hive **Configs**(구성) 탭으로 이동한 다음 `hive.exec.compress.output` 매개 변수를 true로 설정합니다. 기본값은 False입니다.

1. 출력 압축 코덱을 선택하려면 이전 섹션 3단계의 설명에 따라 사용자 지정 hive-site 창에 `mapred.output.compression.codec` 사용자 지정 속성을 추가합니다.

    ![Hive 사용자 지정 속성](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>투기적 실행 사용

투기적 실행은 개별 작업 결과를 최적화하여 전반적인 작업 실행을 향상시키는 한편 느리게 실행되는 task tracker를 검색하고 블랙리스트에 올리기 위해 일정한 수의 중복 작업을 시작합니다.

입력 양이 많고 오래 실행되는 MapReduce 작업에는 투기적 실행을 사용하지 말아야 합니다.

* 투기적 실행을 사용하려면 Hive **Configs**(구성) 탭으로 이동한 다음 `hive.mapred.reduce.tasks.speculative.execution` 매개 변수를 true로 설정합니다. 기본값은 False입니다.

    ![Hive mapred 리듀스 태스크 투기적 실행](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>동적 파티션은 조정

Hive에서는 각 파티션을 미리 정의하지 않고 테이블에 레코드를 삽입할 때 동적 파티션을 만들 수 있습니다. 이로 인해 많은 수의 파티션이 만들어지고 각 파티션에 많은 수의 파일이 만들어질 수 있지만 강력한 기능입니다.

1. Hive에서 동적 파티션을 수행하려면 `hive.exec.dynamic.partition` 매개 변수 값이 true(기본값)여야 합니다.

1. 동적 파티션 모드를 *strict*로 변경합니다. strict 모드에서는 적어도 하나의 파티션이 static이어야 합니다. 이렇게 하면 WHERE 절에서 파티션 필터가 없는 쿼리를 방지합니다. 즉 *strict*는 모든 파티션을 검색하는 쿼리를 방지합니다. Hive **Configs**(구성) 탭으로 이동한 다음 `hive.exec.dynamic.partition.mode`를 **strict**로 설정합니다. 기본값은 **nonstrict**입니다.
 
1. 생성되는 동적 파티션 수를 제한하려면 `hive.exec.max.dynamic.partitions` 매개 변수를 수정합니다. 기본값은 5000입니다.
 
1. 노드당 동적 파티션의 총 수를 제한하려면 `hive.exec.max.dynamic.partitions.pernode`를 수정합니다. 기본값은 2000입니다.

### <a name="enable-local-mode"></a>로컬 모드 사용

로컬 모드에서는 Hive가 단일 컴퓨터에서 때로는 단일 프로세스에서 작업의 모든 작업을 수행할 수 있습니다. 이렇게 하면 입력 데이터가 작고 쿼리 실행 작업의 오버 헤드가 전체 쿼리 실행에서 상당한 부분을 차지하는 경우 쿼리 성능이 향상됩니다.

로컬 모드를 사용하려면 [중간 압축 사용](#enable-intermediate-compression) 섹션의 3단계 설명에 따라 `hive.exec.mode.local.auto` 매개 변수를 사용자 지정 hive-site 패널에 추가합니다.

![Hive 실행 모드 로컬 자동](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>단일 MapReduce MultiGROUP BY 설정

이 속성을 true로 설정하면 공통 group-by 키가 있는 MultiGROUP BY 쿼리가 단일 MapReduce 작업을 생성합니다.  

이 동작을 사용하려면 [중간 압축 사용](#enable-intermediate-compression) 섹션의 3단계 설명에 따라 `hive.multigroupby.singlereducer` 매개 변수를 사용자 지정 hive-site 창에 추가합니다.

![Hive에서 단일 MapReduce MultiGROUP BY 설정](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>추가적인 Hive 최적화

다음 섹션에서는 추가로 설정할 수 있는 Hive 관련 최적화를 설명합니다.

#### <a name="join-optimizations"></a>조인 최적화

Hive의 기본 조인 유형은 *순서 섞기 조인*입니다. Hive에서는 특수 매퍼가 입력을 읽어서 조인 키/값 쌍을 중간 파일로 내보냅니다. Hadoop은 이러한 쌍을 순서 섞기 단계에서 정렬하고 병합합니다. 순서 섞기 단계는 비용이 높습니다. 데이터를 기반으로 올바른 조인을 선택하면 성능이 상당히 향상될 수 있습니다.

| 조인 유형 | 디렉터리 계층 구조에 | 방법 | Hive 설정 | 설명 |
| -- | -- | -- | -- | -- |
| 순서 섞기 조인 | <ul><li>기본적 선택</li><li>항상 작동</li></ul> | <ul><li>테이블 중 하나의 부분 읽기</li><li>조인 키 버킷 및 정렬</li><li>각 리듀스에 하나의 버킷 보내기</li><li>리듀스 측에서 조인 수행</li></ul> | 특정 Hive 설정 필요 없음 | 매번 작동 |
| 맵 조인 | <ul><li>하나의 테이블이 메모리에 들어가는 경우</li></ul> | <ul><li>작은 테이블을 메모리 해시 테이블로 읽기</li><li>큰 파일의 일부를 통해 스트리밍</li><li>해시 테이블에서 각 레코드 조인</li><li>매퍼 단독으로 조인</li></ul> | `hive.auto.confvert.join=true` | 매우 빠르지만 제한적임 |
| 병합 버킷 정렬 | 두 테이블이 모두: <ul><li>동일하게 정렬된 경우</li><li>동일하게 버킷 처리된 경우</li><li>정렬된/버킷 처리된 열에 조인하는 경우</li></ul> | 각 프로세스가: <ul><li>각 테이블에서 버킷 읽기</li><li>값이 가장 낮은 행 처리하기</li></ul> | `hive.auto.convert.sortmerge.join=true` | 매우 효율적임 |

#### <a name="execution-engine-optimizations"></a>실행 엔진 최적화

Hive 실행 엔진 최적화를 위한 추가 권장 사항:

| 설정 | 권장 | HDInsight 기본값 |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = 더 안전하고 느림; false = 빠름 | false |
| `tez.am.resource.memory.mb` | 대부분의 상한 4GB | 자동 조정 |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Apache Pig 최적화

[Apache Pig](https://pig.apache.org/) 속성을 Ambari Web UI에서 수정하여 Pig 쿼리를 조정할 수 있습니다. Pig 속성을 Ambari에서 직접 수정하면 `/etc/pig/2.4.2.0-258.0/pig.properties` 파일의 Pig 속성이 수정됩니다.

1. Pig 속성을 수정하려면 Pig **Configs**(구성) 탭으로 이동한 다음 **Advanced pig-properties**(고급 Pig 속성) 창을 확장합니다.

1. 수정할 속성의 값을 찾아서 주석 처리를 제거하고 변경합니다.

1. 선택 **저장할** 새 값을 저장 하려면 창의 오른쪽 상단에 있습니다. 일부 속성은 서비스를 다시 시작해야 할 수도 있습니다.

    ![고급 Pig 속성](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]  
> 모든 세션 수준 설정은 `pig.properties` 파일의 속성 값을 재정의합니다.

### <a name="tune-execution-engine"></a>실행 엔진 조정

Pig 스크립트를 실행하기 위해 두 개의 실행 엔진 즉, MapReduce 및 Tez를 사용할 수 있습니다. Tez는 최적화된 엔진이며 MapReduce보다 훨씬 빠릅니다.

1. 실행 엔진을 수정하려면 **Advanced pig-properties**(고급 Pig 속성) 창에서 `exectype` 속성을 찾습니다.

1. 기본값은 **MapReduce**입니다. 이것을 **Tez**로 변경합니다.


### <a name="enable-local-mode"></a>로컬 모드 사용

Hive와 마찬가지로 로컬 모드는 비교적 양이 적은 데이터의 작업 속도를 높이는 데 사용됩니다.

1. 로컬 모드를 사용하도록 설정하려면 `pig.auto.local.enabled`를 **true**로 설정합니다. 기본값은 False입니다.

1. 입력 데이터 크기가 `pig.auto.local.input.maxbytes` 속성 값보다 작은 작업은 작은 작업으로 간주됩니다. 기본값은 1GB입니다.


### <a name="copy-user-jar-cache"></a>사용자 jar 캐시 복사

Pig는 UDF에 필요한 JAR 파일을 태스크 노드에서 사용할 수 있도록 분산 캐시에 복사합니다. 이러한 jar 파일은 자주 변경되지 않습니다. `pig.user.cache.enabled` 설정을 사용하도록 설정하면 jar를 캐시에 배치하여 동일한 사용자가 실행하는 작업에 다시 사용할 수 있습니다. 이로 인해 작업 성능이 약간 향상됩니다.

1. 사용하도록 설정하려면 `pig.user.cache.enabled`를 true로 설정합니다. 기본값은 false입니다.

1. 캐시된 jar의 기본 경로를 설정하려면 `pig.user.cache.location`을 기본 경로로 설정합니다. 기본값은 `/tmp`입니다.


### <a name="optimize-performance-with-memory-settings"></a>메모리 설정으로 성능 최적화

다음 메모리 설정은 Pig 스크립트 성능을 최적화하는 데 도움이 됩니다.

* `pig.cachedbag.memusage`: bag에 할당된 메모리 양입니다. bag은 튜플의 컬렉션입니다. 튜플은 정렬된 필드 집합이며 필드는 데이터의 일부입니다. bag의 데이터가 할당된 메모리를 초과하면 데이터가 디스크로 유출됩니다. 기본값은 0.2이며, 이것은 사용 가능한 메모리의 20%를 나타냅니다. 이 메모리는 애플리케이션의 모든 bag에서 공유됩니다.

* `pig.spill.size.threshold`: 이 유출 크기 임계값(바이트 단위)보다 큰 bag은 디스크로 유출됩니다. 기본값은 5MB입니다.


### <a name="compress-temporary-files"></a>임시 파일 압축

Pig는 작업 실행 중에 임시 파일을 생성합니다. 임시 파일을 압축하면 디스크에 파일을 쓰거나 읽을 때 성능이 향상됩니다. 다음 설정을 사용하여 임시 파일을 압축할 수 있습니다.

* `pig.tmpfilecompression`: true이면 임시 파일 압축이 사용됩니다. 기본값은 False입니다.

* `pig.tmpfilecompression.codec`: 임시 파일을 압축하는 데 사용하는 압축 코덱입니다. CPU 사용률을 낮추기 위해 권장되는 압축 코덱은 [LZO](https://www.oberhumer.com/opensource/lzo/) 및 Snappy입니다.

### <a name="enable-split-combining"></a>분할 결합 사용

사용하도록 설정하면 맵 작업 수가 줄어들도록 작은 파일이 결합됩니다. 이렇게 하면 작은 파일이 많이 있는 작업의 효율이 향상됩니다. 사용하도록 설정하려면 `pig.noSplitCombination`을 true로 설정합니다. 기본값은 False입니다.


### <a name="tune-mappers"></a>매퍼 조정

매퍼 수는 `pig.maxCombinedSplitSize` 속성을 수정하여 제어됩니다. 이것은 단일 맵 작업으로 처리되는 데이터 크기를 지정합니다. 기본값은 파일 시스템의 기본 블록 크기입니다. 이 값을 높이면 매퍼 작업 수가 감소됩니다.


### <a name="tune-reducers"></a>리듀서 조정

리듀서 수는 `pig.exec.reducers.bytes.per.reducer` 매개 변수를 기반으로 계산됩니다. 이 매개 변수는 리듀서당 처리되는 바이트 수를 지정하며 기본값은 1GB입니다. 최대 리듀서 수를 제한하려면 `pig.exec.reducers.max` 속성을 설정합니다. 기본값은 999입니다.


## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Ambari Web UI로 Apache HBase 최적화

[Apache HBase](https://hbase.apache.org/) 구성은 **HBase Configs** 탭에서 수정됩니다. 다음 섹션에서는 HBase 성능에 영향을 주는 중요한 구성 설정 중 일부를 설명합니다.

### <a name="set-hbaseheapsize"></a>HBASE_HEAPSIZE 설정

HBase 힙 크기는 *영역* 및 *마스터* 서버에서 사용할 최대 힙 크기를 메가 바이트 단위로 지정합니다. 기본값은 1,000MB입니다. 클러스터 워크로드에 맞게 조정해야 합니다.

1. 수정하려면 HBase **Configs**(구성) 탭의 **Advanced HBase-env**(고급 HBase-env) 창으로 이동한 다음 `HBASE_HEAPSIZE` 설정을 찾습니다.

1. 기본값을 5,000MB로 변경합니다.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>읽기 작업이 많은 워크로드 최적화

다음 구성은 읽기 작업이 많은 워크로드의 성능을 향상시키는 데 중요합니다.

#### <a name="block-cache-size"></a>블록 캐시 크기

블록 캐시는 읽기 캐시입니다. 이 크기는 `hfile.block.cache.size` 매개 변수로 제어됩니다. 기본값은 0.4이며 이것은 전체 영역 서버 메모리의 40퍼센트입니다. 블록 캐시 크기가 클수록 임의 읽기가 빨라집니다.

1. 이 매개 변수를 수정하려면 HBase **Configs**(구성) 탭의 **Settings**(설정) 탭으로 이동한 다음 **% of RegionServer Allocated to Read Buffers**(버퍼를 읽기 위해 할당된 RegionServer의 %)를 찾습니다.

    ![HBase 블록 캐시 크기](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. 값을 변경하려면 **편집** 아이콘을 선택합니다.


#### <a name="memstore-size"></a>Memstore 크기

모든 편집은 *Memstore*라는 메모리 버퍼에 저장됩니다. 이렇게 하면 한 번의 작업으로 디스크에 쓸 수 있는 총 데이터 양이 늘어나고 최근 편집에 대한 후속 액세스 속도가 빨라집니다. Memstore 크기는 다음 두 가지 매개 변수로 정의됩니다.

* `hbase.regionserver.global.memstore.UpperLimit`: Memstore가 함께 사용할 수 있는 영역 서버의 최대 백분율을 정의합니다.

* `hbase.regionserver.global.memstore.LowerLimit`: Memstore가 함께 사용할 수 있는 영역 서버의 최소 백분율을 정의합니다.

임의 읽기를 최적화하려면 Memstore 상한 및 하한을 줄입니다.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>디스크에서 스캔할 때 페치하는 행 수

`hbase.client.scanner.caching` 설정은 스캐너에서 `next` 메서드가 호출될 때 디스크에서 읽어오는 행 수를 정의합니다.  기본값은 100입니다. 숫자가 높을수록 클라이언트에서 영역 서버로의 원격 호출이 줄어들어 스캔 속도가 빨라집니다. 단, 이렇게 하면 클라이언트의 메모리 부족도 증가합니다.

![HBase 페치한 행 수](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> 스캐너에서 다음 메서드 호출 사이의 간격이 스캐너 시간 제한보다 크도록 값을 설정하지 않습니다. 스캐너 시간 제한 기간은 `hbase.regionserver.lease.period` 속성에 의해 정의됩니다.


### <a name="optimize-write-heavy-workloads"></a>쓰기 작업이 많은 워크로드 최적화

다음 구성은 쓰기 작업이 많은 워크로드의 성능을 향상시키는 데 중요합니다.


#### <a name="maximum-region-file-size"></a>최대 영역 파일 크기

HBase는 *HFile*이라는 내부 파일 형식으로 데이터를 저장합니다. `hbase.hregion.max.filesize` 속성은 영역에 대한 단일 HFile의 크기를 정의합니다.  영역 내 모든 HFile의 합계가 이 설정보다 큰 경우 하나의 영역이 두 영역으로 분할됩니다.
 
![HBase HRegion 최대 파일 크기](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

영역 파일 크기가 클수록 분할 수가 줄어듭니다. 파일 크기를 늘려서 쓰기 성능이 최대화되는 값을 결정할 수 있습니다.


#### <a name="avoid-update-blocking"></a>업데이트 차단 방지

* `hbase.hregion.memstore.flush.size` 속성은 Memstore가 디스크로 플러시되는 크기를 정의합니다. 기본 크기는128MB입니다.

* Hbase 영역 블록 승수는 `hbase.hregion.memstore.block.multiplier`에 의해 정의됩니다. 기본값은 4입니다. 허용되는 최대값은 8입니다.

* Memstore가 (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`)바이트이면 HBase가 업데이트를 차단합니다.

    플러시 크기 및 블록 승수의 기본값을 사용하면 Memstore 크기가 128 * 4 = 512MB인 경우 업데이트가 차단됩니다. 업데이트 차단 횟수를 줄이려면 `hbase.hregion.memstore.block.multiplier` 값을 늘립니다.

![HBase 영역 블록 승수](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Memstore 크기 정의

Memstore 크기는 `hbase.regionserver.global.memstore.UpperLimit` 및 `hbase.regionserver.global.memstore.LowerLimit` 매개 변수로 정의됩니다. 이 값을 서로 같게 설정하면 쓰기 중에 일시 중지가 줄어들어서(또한 플러시 빈도가 높아져서) 쓰기 성능이 향상됩니다.


### <a name="set-memstore-local-allocation-buffer"></a>Memstore 로컬 할당 버퍼 설정

Memstore 로컬 할당 버퍼 사용은 `hbase.hregion.memstore.mslab.enabled` 속성으로 결정됩니다. 사용하도록(true) 설정되면 쓰기 작업이 과도할 때 힙 조각화를 막습니다. 기본값은 true입니다.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI로 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
