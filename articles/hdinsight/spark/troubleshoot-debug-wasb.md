---
title: Azure HDInsight의 WASB 파일 작업 디버그
description: Azure HDInsight 클러스터와 상호 작용 하는 경우 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.openlocfilehash: 1256575eea7ee80b41a875c6bcd9b281b98aa360
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163851"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Azure HDInsight의 WASB 파일 작업 디버그

WASB 드라이버가 Azure Storage 시작 하는 작업을 이해 하려는 경우가 있습니다. 클라이언트 쪽의 경우 WASB 드라이버는 **디버그** 수준에서 각 파일 시스템 작업에 대 한 로그를 생성 합니다. WASB 드라이버는 log4j를 사용 하 여 로깅 수준을 제어 하 고 기본값은 **정보** 수준입니다. 서버 쪽 분석 로그 Azure Storage [Azure Storage 분석 로깅](../../storage/common/storage-analytics-logging.md)을 참조 하세요.

생성 된 로그는 다음과 유사 하 게 표시 됩니다.

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>파일 작업에 대 한 WASB 디버그 로그 설정

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`로 이동 합니다. 여기서 `CLUSTERNAME`은 Spark 클러스터의 이름입니다.

1. **Spark2** > **Configs** > **advanced Spark2-log4j**로 이동 합니다.

1. `log4j.appender.console.Threshold=DEBUG``log4j.appender.console.Threshold=INFO`을 수정 합니다.

1. **Advanced livy2-log4j**로 이동 합니다.

1. 다음 속성을 추가 합니다.

    ```
    log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG
    ```

1. 변경 내용을 저장합니다.

## <a name="additional-logging"></a>추가 로깅

위의 로그는 파일 시스템 작업에 대 한 높은 수준의 이해를 제공 해야 합니다. 위의 로그가 여전히 유용한 정보를 제공 하지 않거나 blob storage api 호출을 조사 하려는 경우에는 `core-site`에 `fs.azure.storage.client.logging=true`를 추가 합니다. 이 설정은 wasb 저장소 드라이버에 대 한 java sdk 로그를 사용 하도록 설정 하 고 blob storage server에 대 한 각 호출을 인쇄 합니다. 디스크를 빠르게 채우고 프로세스를 늦출 수 있으므로 조사 후 설정을 제거 합니다.

백 엔드가 Azure Data Lake 기반으로 하는 경우 구성 요소 (예: spark/tez/hdfs)에 대해 다음 log4j 설정을 사용 합니다.

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

로그에 대 한 `/var/log/adl/adl.log`에서 로그를 찾습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
