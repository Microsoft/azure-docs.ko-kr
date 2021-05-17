---
title: Apache Pig 사용
titleSuffix: Azure HDInsight
description: HDInsight에서 Apache Hadoop과 함께 Pig를 사용하는 방법을 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 4cbb7b96610a56f3b6049038bb5c9c6bc0870b57
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871366"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용

HDInsight에서 [Apache Pig](https://pig.apache.org/)를 사용하는 방법을 알아봅니다.

Apache Pig는 *Pig Latin* 이라는 절차형 언어를 사용하여 Apache Hadoop용 프로그램을 만드는 플랫폼입니다. Pig는 *MapReduce* 을 만드는 Java를 대체하는 솔루션이며 Azure HDInsight와 함께 포함됩니다. 다음 표를 사용하여 HDInsight에서 Pig를 사용하는 다양한 방법을 검색하세요.

## <a name="why-use-apache-pig"></a><a id="why"></a>Apache Pig를 사용하는 이유

Hadoop의 MapReduce를 사용하여 데이터를 처리할 때 문제는 지도 및 reduce 함수만을 사용하여 처리 논리를 구현하는 것입니다. 복잡한 처리를 위해 종종 처리를 같이 연결된 여러 MapReduce 작업으로 나누어야 원하는 결과를 얻을 수 있습니다.

Pig를 사용하면 원하는 출력을 생산하기 위해 데이터가 통과하는 일련의 변환으로 처리를 규정할 수 있게 됩니다.

원하는 출력을 생산하기 위해 Pig Litin 언어를 사용하여 하나 이상의 변환을 거쳐 원시 입력을 데이터 플로로 설명할 수 있습니다. Pig Latin 프로그램이 일반적인 패턴을 따릅니다:

* **로드**: 파일 시스템에서 조작할 데이터 읽습니다.

* **변환**: 데이터를 조작합니다.

* **덤프 또는 저장**: 데이터를 화면에 출력하거나 처리를 위해 저장합니다.

### <a name="user-defined-functions"></a>사용자 정의 함수

또한 Pig Latin는 사용자가 Pig Latin의 모델에 어려운 논리를 구현하는 외부 구성 요소를 적용할 수 있도록 사용자 정의 함수(UDF)를 지원합니다.

Pig Latin에 대한 자세한 내용은 [Pig Latin 참조 설명서 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) 및 [Pig Latin 참조 설명서 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html)를 참조하십시오.

## <a name="example-data"></a><a id="data"></a>예제 데이터

HDInsight에서는 `/example/data` 및 `/HdiSamples` 디렉터리에 저장되는 다양한 예제 데이터 집합을 제공합니다. 이러한 디렉터리는 클러스터의 기본 스토리지에 있습니다. 이 문서의 Pig 예제는 `/example/data/sample.log`의 *log4j* 파일을 사용합니다.

파일 내부의 각 로그는 유형과 심각도를 표시하는 `[LOG LEVEL]`  필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들어:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

이전 예에서 로그 수준은 ERROR입니다.

> [!NOTE]  
> [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) 도구 로깅하여 log4j 파일을 생성하고 해당 파일을 사용자의 blob에 업로드할 수도 있습니다. 해당 지침은 [HDInsight에 데이터 업로드](hdinsight-upload-data.md) 를 참조하세요. HDInsight와 함께 Azure Blob Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.

## <a name="example-job"></a><a id="job"></a>예제 작업

다음 Pig Latin 작업은 HDInsight 클러스터의 기본 스토리지에서 `sample.log` 파일을 로드합니다. 그런 다음 입력된 데이터에서 각 로그 수준이 발생한 횟수로 나타나는 일련의 변환을 수행합니다. 결과는 STDOUT에 기록됩니다.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

다음 그림에서는 각 변환으로 인해 데이터에 수행되는 작업을 요약해서 보여 줍니다.

:::image type="content" source="./media/use-pig/hdi-data-transformation.gif" alt-text="변환의 그래픽 표현" border="false":::

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Pig Latin 작업 실행

HDInsight는 다양한 메서드를 사용하여 Pig Latin 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르세요.

## <a name="pig-and-sql-server-integration-services"></a>Pig 및 SQL Server Integration Services

SSIS(SQL Server Integration Services)를 사용하여 Pig 작업을 실행할 수 있습니다. Azure Feature Pack for SSIS는 HDInsight에서 Pig 작업을 하는 다음 구성 요소를 제공합니다.

* [Azure HDInsight Pig 태스크][pigtask]

* [Azure 구독 연결 관리자][connectionmanager]

[여기][ssispack]에서 Azure Feature Pack for SSIS에 대해 자세히 알아보세요.

## <a name="next-steps"></a><a id="nextsteps"></a>다음 단계

Scalding을 사용하여 HDInsight와 함께 Pig를 사용하는 방법을 살펴보았으므로 이제 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에 데이터 업로드](hdinsight-upload-data.md)
* [HDInsight에서 Apache Hive 사용](./hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Sqoop 사용](./hadoop/hdinsight-use-sqoop.md)
* [HDInsight에서 MapReduce 작업 사용](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


