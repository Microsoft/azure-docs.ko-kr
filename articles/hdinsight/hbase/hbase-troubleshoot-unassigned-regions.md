---
title: Azure HDInsight의 지역 서버 문제
description: Azure HDInsight의 지역 서버 문제
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936622"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight의 지역 서버 문제

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="scenario-unassigned-regions"></a>시나리오: 할당되지 않은 지역

### <a name="issue"></a>문제

`hbase hbck` 명령을 실행하면 다음과 비슷한 오류 메시지가 표시됩니다.

```
multiple regions being unassigned or holes in the chain of regions
```

Apache HBase Master UI에서 모든 영역 서버 중 부하가 분산되지 않은 영역 수를 확인할 수 있습니다. 그런 후 `hbase hbck` 명령을 실행하여 영역 체인의 빈 영역을 확인할 수 있습니다.

### <a name="cause"></a>원인

빈 영역은 오프라인 영역으로 인해 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

할당을 수정합니다. 할당되지 않은 지역을 정상 상태로 전환하려면 다음 단계를 수행합니다.

1. SSH를 사용하여 HDInsight HBase 클러스터에 로그인합니다.

1. `hbase zkcli` 명령을 실행하여 ZooKeeper 셸에 연결합니다.

1. `rmr /hbase/regions-in-transition` 또는 `rmr /hbase-unsecure/regions-in-transition` 명령을 실행합니다.

1. `exit` 명령을 사용하여 ZooKeeper 셸을 끝냅니다.

1. Apache Ambari UI를 열고 활성 HBase Master 서비스를 다시 시작합니다.

1. 다른 옵션 없이 `hbase hbck` 명령을 다시 실행합니다. 출력을 확인하고 모든 영역이 할당되었는지 확인합니다.

---

## <a name="scenario-dead-region-servers"></a>시나리오: 중지된 지역 서버

### <a name="issue"></a>문제

지역 서버를 시작할 수 없습니다.

### <a name="cause"></a>원인

여러 WAL 디렉터리 분할.

1. 현재 WAL의 목록 `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`을 가져옵니다.

1. `wals.out` 파일을 검사합니다. 분할 디렉터리가 너무 많은 경우(*-splitting으로 시작) 이러한 디렉터리로 인해 영역 서버가 실패할 수 있습니다.

### <a name="resolution"></a>해결 방법

1. Ambari 포털에서 HBase를 중지합니다.

1. 새 WAL 목록을 가져오려면 `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`를 실행합니다.

1. *-splitting 디렉터리를 임시 폴더 `splitWAL`로 이동하고 *-splitting 디렉터리를 삭제합니다.

1. `hbase zkcli` 명령을 실행하여 ZooKeeper 셸에 연결합니다.

1. `rmr /hbase-unsecure/splitWAL`을 실행합니다.

1. HBase 서비스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.