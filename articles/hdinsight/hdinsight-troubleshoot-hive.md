---
title: Azure HDInsight를 사용한 Hive 문제 해결
description: Apache Hive 및 Azure HDInsight 작업에 대한 일반적인 질문에 답합니다.
keywords: Azure HDInsight, Hive, FAQ, 문제 해결 가이드, 일반적인 질문
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dharmeshkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 43886a132f2f3cf75f0ec7a0b2dc0680a0f69589
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765814"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Azure HDInsight를 사용하여 Apache Hive 문제 해결

Apache Ambari에서 Apache Hive 페이로드를 사용할 때의 주요 질문 사항 및 해결 방법을 알아봅니다.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hive metastore를 내보내고 다른 클러스터로 가져오려면 어떻게 하나요?


### <a name="resolution-steps"></a>해결 단계:

1. SSH(Secure Shell) 클라이언트를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [더 보기](#additional-reading-end)를 참조하세요.

2. metastore를 내보내려는 HDInsight 클러스터에서 다음 명령을 실행합니다.

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   이 명령은 allatables.sql이라는 파일을 생성합니다.

3. alltables.sql 파일을 새 HDInsight 클러스터에 복사하고 다음 명령을 실행합니다.

   ```apache
   hive -f alltables.sql
   ```

해결 단계의 코드는 새 클러스터의 데이터 경로가 이전 클러스터의 데이터 경로와 동일하다고 가정합니다. 데이터 경로가 다른 경우 변경 내용을 반영하도록 생성된 alltables.sql 파일을 수동으로 편집할 수 있습니다.

### <a name="additional-reading"></a>추가 참조 자료

- [SSH를 사용하여 HDInsight 클러스터 연결](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>클러스터에서 Hive 로그를 찾으려면 어떻게 하나요?

### <a name="resolution-steps"></a>해결 단계:

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 **더 보기**를 참조하세요.

2. Hive 클라이언트 로그를 보려면 다음 명령을 사용합니다.

   ```apache
   /tmp/<username>/hive.log 
   ```

3. Hive metastore 로그를 보려면 다음 명령을 사용합니다.

   ```apache
   /var/log/hive/hivemetastore.log 
   ```

4. Hiveserver 로그를 보려면 다음 명령을 사용합니다.

   ```apache
   /var/log/hive/hiveserver2.log 
   ```

### <a name="additional-reading"></a>추가 참조 자료

- [SSH를 사용하여 HDInsight 클러스터 연결](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>클러스터에서 특정 구성으로 Hive 셸을 시작하려면 어떻게 하나요?

### <a name="resolution-steps"></a>해결 단계:

1. Hive 셸을 시작할 때 구성 키-값 쌍을 지정합니다. 자세한 내용은 [더 보기](#additional-reading-end)를 참조하세요.

   ```apache
   hive -hiveconf a=b 
   ```

2. Hive 셸에서 모든 유효 구성을 나열하려면 다음 명령을 사용합니다.

   ```apache
   hive> set;
   ```

   예를 들어 다음 명령을 사용하여 콘솔에서 디버그 로깅이 사용하도록 설정된 상태로 Hive 셸을 시작합니다.

   ```apache
   hive -hiveconf hive.root.logger=ALL,console 
   ```

### <a name="additional-reading"></a>추가 참조 자료

- [Hive configuration properties](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)(Hive 구성 속성)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>클러스터 중요 경로에서 Apache Tez DAG 데이터를 분석하려면 어떻게 하나요?


### <a name="resolution-steps"></a>해결 단계:
 
1. 클러스터에 중요한 그래프에서 Apache Tez DAG(방향성 비순환 그래프)를 분석하려면 SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [더 보기](#additional-reading-end)를 참조하세요.

2. 명령 프롬프트에서 다음 명령을 실행합니다.
   
   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Tez DAG를 분석하는 데 사용할 수 있는 다른 분석기를 나열하려면 다음 명령을 사용합니다.

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   예제 프로그램을 첫 번째 인수로 제공해야 합니다.

   올바른 프로그램의 이름은 다음과 같습니다.
    - **ContainerReuseAnalyzer**: DAG에 컨테이너 다시 사용 세부 정보를 출력합니다.
    - **CriticalPath**: DAG의 중요한 경로를 찾습니다.
    - **LocalityAnalyzer**: DAG에 위치 정보를 인쇄합니다.
    - **ShuffleTimeAnalyzer**: DAG에서 순서 섞기 시간 정보를 분석합니다.
    - **SkewAnalyzer**: DAG에서 기울이기 정보를 분석합니다.
    - **SlowNodeAnalyzer**: DAG에 노드 정보를 출력합니다.
    - **SlowTaskIdentifier**: DAG에 느린 작업 정보를 출력합니다.
    - **SlowestVertexAnalyzer**: DAG에 가장 느린 꼭지점 정보를 출력합니다.
    - **SpillAnalyzer**: DAG에 분산 정보를 출력합니다.
    - **TaskConcurrencyAnalyzer**: DAG에 작업 동시성 정보를 출력합니다.
    - **VertexLevelCriticalPathAnalyzer**: DAG의 꼭짓점 수준에서 중요 경로를 찾습니다.


### <a name="additional-reading"></a>추가 참조 자료

- [SSH를 사용하여 HDInsight 클러스터 연결](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>클러스터에서 Tez DAG 데이터를 다운로드하려면 어떻게 하나요?


#### <a name="resolution-steps"></a>해결 단계:

Tez DAG 데이터를 수집하는 방법에는 다음 두 가지가 있습니다.

- 명령줄에서:
 
    SSH를 사용하여 HDInsight 클러스터에 연결합니다. 명령 프롬프트에서 다음 명령을 실행합니다.

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Ambari Tez 보기 사용:
   
  1. Ambari로 이동합니다. 
  2. 오른쪽 위 구석의 타일 아이콘 아래에 있는 Tez 보기로 이동합니다. 
  3. 보려는 DAG를 선택합니다.
  4. **데이터 다운로드**를 선택합니다.

### <a name="additional-reading-end"></a>추가 참조 자료

[SSH를 사용하여 HDInsight 클러스터 연결](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>관련 항목
[Azure HDInsight를 사용하여 문제 해결](hdinsight-troubleshoot-guide.md)




