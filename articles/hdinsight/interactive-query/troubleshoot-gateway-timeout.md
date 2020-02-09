---
title: Azure HDInsight의 Apache Ambari Hive 뷰에서 쿼리를 실행할 때 예외
description: Azure HDInsight의 Apache Ambari Hive 뷰를 통해 Apache Hive 쿼리를 실행할 때의 문제 해결 단계입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895044"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Azure HDInsight의 Apache Ambari Hive 뷰에서 쿼리를 실행할 때 예외

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Apache Ambari Hive 보기에서 Apache Hive 쿼리를 실행 하는 경우 간헐적으로 다음 오류 메시지가 표시 됩니다.

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>원인

게이트웨이 시간 제한입니다.

게이트웨이 시간 제한 값은 2 분입니다. Ambari Hive 보기의 쿼리는 게이트웨이를 통해 `/hive2` 엔드포인트으로 전송 됩니다. 쿼리가 성공적으로 컴파일되고 수락 되 면 HiveServer는 `queryid`을 반환 합니다. 클라이언트는 쿼리 상태에 대 한 폴링을 유지 합니다. 이 프로세스 중에 HiveServer가 2 분 내에 HTTP 응답을 반환 하지 않으면 HDI 게이트웨이가 호출자에 게 502.3 게이트웨이 시간 초과 오류를 throw 합니다. 처리를 위해 쿼리를 제출 하는 경우 (더 가능성이 높음) 및 get status 호출에도 오류가 발생할 수 있습니다 (가능성 낮음). 사용자는 둘 중 하나를 볼 수 있습니다.

Http 처리기 스레드는 신속 하 게 수행 해야 합니다. 작업을 준비 하 고 `queryid`을 반환 합니다. 그러나 몇 가지 이유로 인해 모든 처리기 스레드를 사용 하 여 새 쿼리에 대 한 시간 제한이 발생 하 고 get 상태 호출이 발생할 수 있습니다.

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 처리기 스레드의 책임

클라이언트는 HiveServer에 쿼리를 제출할 때 포그라운드 스레드에서 다음 작업을 수행 합니다.

* 요청 구문 분석, 의미 체계 확인
* 잠금 가져오기
* 필요한 경우 Metastore lookup
* 쿼리 (DDL 또는 DML) 컴파일
* 쿼리 계획 준비
* 권한 부여 수행 (보안 클러스터에서 적용 가능한 모든 레인저 정책 실행)

## <a name="resolution"></a>해상도

상황을 개선할 수 있는 몇 가지 일반적인 권장 사항은 다음과 같습니다.

* 외부 hive metastore를 사용 하는 경우 DB 메트릭을 확인 하 고 데이터베이스가 오버 로드 되지 않았는지 확인 합니다. Metastore 데이터베이스 계층의 크기를 조정 하는 것이 좋습니다.

* 병렬 ops가 설정 되어 있는지 확인 합니다. 이렇게 하면 HTTP 처리기 스레드를 병렬로 실행할 수 있습니다. 값을 확인 하려면 [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) 를 시작 하 고 **hive** > **Configs** > **고급** > **사용자 지정 hive 사이트로**이동 합니다. `hive.server2.parallel.ops.in.session` 값은 `true`해야 합니다.

* 클러스터의 VM SKU가 부하에 비해 너무 크지 않은지 확인 하십시오. 여러 클러스터 간에 작업을 분할 하는 것이 좋습니다. 자세한 내용은 [클러스터 유형 선택](../hdinsight-capacity-planning.md#choose-a-cluster-type)을 참조 하세요.

* 클러스터에 레인저가 설치 된 경우 각 쿼리에 대해 평가 해야 하는 너무 많은 레인저 정책이 있는지 확인 하세요. 중복 되거나 불필요 한 정책을 찾습니다.

* Ambari에서 **HiveServer2 힙 크기** 값을 확인 합니다. **Hive** > **Configs** > **설정** > **최적화**로 이동 합니다. 값이 10gb 보다 큰지 확인 합니다. 성능을 최적화 하기 위해 필요에 따라 조정 합니다.

* Hive 쿼리가 잘 조정 되었는지 확인 합니다. 자세한 내용은 [Azure HDInsight에서 Apache Hive 쿼리 최적화](../hdinsight-hadoop-optimize-hive-query.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
