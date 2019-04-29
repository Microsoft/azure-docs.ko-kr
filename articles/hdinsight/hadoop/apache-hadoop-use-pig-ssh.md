---
title: HDInsight 클러스터에서 SSH로 Apache Pig 사용 - Azure
description: SSH를 사용하여 Linux 기반 Apache Hadoop 클러스터에 연결한 다음, Pig 명령을 사용하여 대화형으로 또는 일괄 처리 작업으로 Pig Latin 문을 실행하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 503449e36b2b11e5663449dd732fdaf785417570
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110753"
---
# <a name="run-apache-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Pig 명령(SSH)를 사용하여 Linux 기반 클러스터에서 Apache Pig 작업 실행

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

SSH 연결에서 HDInsight 클러스터로 Apache Pig 작업을 대화형으로 실행하는 방법에 대해 알아봅니다. Pig Latin 프로그래밍 언어를 사용하면 원하는 출력을 생성하는 입력 데이터에 적용되는 변환을 설명할 수 있습니다.

> [!IMPORTANT]  
> 이 문서의 단계에는 Linux 기반 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a id="ssh"></a>SSH를 사용하여 연결

SSH를 사용하여 HDInsight 클러스터에 연결합니다. 다음은 **sshuser** 계정으로 **myhdinsight** 클러스터에 연결하는 예입니다.

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a id="pig"></a> Apache Pig 명령 사용

1. 연결되면 다음 명령을 사용하여 Pig CLI(명령줄 인터페이스)를 시작합니다.

    ```bash
    pig
    ```

    잠시 후 프롬프트가 `grunt>`로 바뀝니다.

2. 다음 문을 입력합니다.

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    이 명령은 sample.log 파일의 내용을 로그에 로드합니다. 다음 문을 사용하여 파일의 내용을 볼 수 있습니다.

    ```piglatin
    DUMP LOGS;
    ```

3. 이어서 다음 문을 사용하여 각 레코드에서 로깅 수준만 추출하는 정규식을 적용하여 데이터를 변환합니다.

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    **DUMP** 를 사용하여 변환 후 데이터를 볼 수 있습니다. 이 예제의 경우 `DUMP LEVELS;`입니다.

4. 다음 표에서 문을 사용하여 계속해서 변환을 적용합니다.

    | Pig Latin 문 | 문의 기능 |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | 로그 수준의 null 값이 포함된 행을 제거하고 결과를 `FILTEREDLEVELS`에 저장합니다. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | 행을 로그 수준으로 그룹화하고 결과를 `GROUPEDLEVELS`에 저장합니다. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | 고유한 각 로그 수준 값 및 발생 횟수를 포함하는 데이터 집합을 만듭니다. 데이터 집합은 `FREQUENCIES`에 저장됩니다. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | 로그 수준을 개수(내림차순)를 기준으로 정렬하고 `RESULT`에 저장합니다. |

    > [!TIP]  
    > `DUMP`를 사용하여 각 단계 후의 변환 결과를 확인합니다.

5. `STORE` 문을 사용하여 변환 결과를 저장할 수도 있습니다. 예를 들어 다음 문은 `RESULT`를 클러스터 기본 저장소의 `/example/data/pigout` 디렉터리에 저장합니다.

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]  
   > 데이터는 `part-nnnnn` 파일의 지정된 디렉터리에 저장됩니다. 해당 디렉터리가 이미 존재하는 경우 오류가 발생합니다.

6. 프롬프트를 종료하려면 다음 문을 입력합니다.

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Pig Latin 배치 파일

Pig 명령을 사용하여 파일에 포함된 Pig Latin을 실행할 수도 있습니다.

1. grunt 프롬프트가 종료되면 다음 명령을 사용하여 `pigbatch.pig`라는 파일을 만듭니다.

    ```bash
    nano ~/pigbatch.pig
    ```

2. 다음 줄을 입력하거나 붙여넣습니다.

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    완료되면 __Ctrl__ + __X__, __Y__ 및 __Enter__ 키를 차례로 사용하여 파일을 저장합니다.

3. 다음의 Pig 명령을 사용하여 `pigbatch.pig` 파일을 실행합니다.

    ```bash
    pig ~/pigbatch.pig
    ```

    배치 작업이 완료되면 다음과 같은 출력이 표시됩니다.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>다음 단계

HDInsight의 Pig에 대한 일반적인 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)

HDInsight에서 Hadoop을 사용하여 작업하는 다른 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)
