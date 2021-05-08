---
title: Azure HDInsight에서 서비스를 시작할 때의 포트 충돌
description: Azure HDInsight 클러스터와 상호 작용할 때 포트 충돌 문제에 대한 문제 해결 단계 및 가능한 해결 방법입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f42e84d5d9c1dd49d9bf5604fe2f967eae0b6276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943101"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>시나리오: Azure HDInsight에서 서비스를 시작할 때 포트 충돌

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

서비스가 시작되지 않습니다.

## <a name="cause"></a>원인

포트 충돌이 있습니다.

## <a name="resolution"></a>해결 방법

### <a name="method-1"></a>방법 1

아래 명령을 사용하여 포트 문제의 영향을 받는 실행 중 프로세스를 모두 가져오거나 중지합니다.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

그런 다음 서비스를 시작합니다.

### <a name="method-2"></a>방법 2

노드를 다시 부팅합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.