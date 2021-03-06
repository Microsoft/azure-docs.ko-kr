---
title: 액세스 되는 계정이 Azure HDInsight의 http 오류를 지원 하지 않습니다.
description: 이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943081"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>액세스 되는 계정이 Azure HDInsight의 http 오류를 지원 하지 않습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

다음 오류 메시지가 수신 됩니다.

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>원인

오류 메시지를 수신 하는 이유는 여러 가지가 있습니다.

* 저장소 계정에 [보안 전송이](../../storage/common/storage-require-secure-transfer.md) 활성화 되어 있으며 잘못 된 [URI 체계가](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 사용 되 고 있습니다.

* 보안 전송이 *사용 하지 않도록 설정* 된 저장소 계정을 사용 하 여 클러스터를 만들었습니다. 그런 다음 저장소 계정에서 보안 전송이 사용 되도록 설정 되었습니다.

## <a name="resolution"></a>해결 방법

Azure Storage 또는 Data Lake Storage Gen2에 대해 보안 전송이 사용 되는 경우 URI는 `wasbs://` 각각 또는 `abfss://` 입니다.  [보안 전송](../../storage/common/storage-require-secure-transfer.md)도 참조하세요.

새 클러스터의 경우 원하는 보안 전송 설정이 이미 있는 저장소 계정을 사용 합니다. 기존 클러스터에서 사용 중인 저장소 계정에 대 한 보안 전송 설정을 변경 하지 마십시오.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.