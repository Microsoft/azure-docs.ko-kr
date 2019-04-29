---
title: HDInsight에서 Apache Hadoop과 MapReduce 및 SSH 연결 - Azure
description: SSH를 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 작업을 실행하는 방법을 알아봅니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlunb
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 04/10/2018
ms.date: 01/14/2019
ms.author: v-yiso
ms.openlocfilehash: 3448a5e89f6930a5bdcb7d0d77b92576e58fc90b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129385"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>SSH를 사용하여 HDInsight에서 Apache Hadoop으로 MapReduce 사용

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

SSH(Secure Shell) 연결에서 HDInsight로 MapReduce 작업을 제출하는 방법을 알아봅니다.

> [!NOTE]
> Linux 기반 Apache Hadoop 서버 사용에 익숙하지만 HDInsight는 생소하다면 [Linux 기반 HDInsight 팁](../hdinsight-hadoop-linux-information.md)을 참조하세요.

## <a id="prereq"></a>필수 조건

* Linux 기반 HDInsight(HDInsight의 Hadoop) 클러스터

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* SSH 클라이언트. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a id="ssh"></a>SSH를 사용하여 연결

SSH를 사용하여 클러스터에 연결합니다. 예를 들어, 다음 명령은 **myhdinsight**라는 클러스터에 **sshuser** 계정으로 연결합니다.

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.cn
```

**SSH 인증을 위해 인증서 키를 사용하는 경우** 클라이언트 시스템에서 개인 키의 위치를 지정해야 할 수도 있습니다. 예를 들면 다음과 같습니다.

```bash
ssh -i ~/mykey.key sshuser@myhdinsight-ssh.azurehdinsight.cn
```

**SSH 인증을 위해 암호를 사용하는 경우** 메시지가 표시되면 암호를 제공해야 합니다.

HDInsight에서의 SSH 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a id="hadoop"></a>Hadoop 명령 사용

1. HDInsight 클러스터에 연결되면 다음 명령을 사용하여 MapReduce 작업을 시작합니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    이 명령은 `hadoop-mapreduce-examples.jar` 파일에 포함되어 있는 `wordcount` 클래스를 시작합니다. 입력으로 `/example/data/gutenberg/davinci.txt` 문서를 사용하고 출력은 `/example/data/WordCountOutput`에 저장됩니다.

    > [!NOTE]
    > 이 MapReduce 작업 및 예 데이터에 대한 자세한 내용은 [HDInsight의 Apache Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.

2. 작업이 처리되는 동안 세부 정보를 내보내며 작업이 완료될 때 다음 텍스트와 유사한 정보를 반환합니다.

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. 작업이 완료되면 다음 명령을 사용하여 출력 파일을 나열합니다.

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    이 명령은 `_SUCCESS` 및 `part-r-00000`의 두 개의 파일을 표시합니다. `part-r-00000` 파일은 이 작업에 대한 출력을 포함합니다.

    > [!NOTE]
    > 일부 MapReduce 작업은 여러 **part-r-#####** 파일로 결과를 분할할 수 있습니다. 그럴 경우 ##### 접미사가 파일의 순서를 나타냅니다.

4. 출력을 보려면 다음 명령을 사용합니다.

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    이 명령은 **wasb://example/data/gutenberg/davinci.txt** 파일에 포함된 단어의 목록과 각 단어가 나타나는 횟수를 표시합니다. 다음 텍스트는 파일에 포함된 데이터의 예입니다.

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>요약

여기에서 볼 수 있듯이 Hadoop 명령은 HDInsight 클러스터에서 MapReduce 작업을 실행하고 작업 출력을 볼 수 있는 쉬운 방법을 제공합니다.

## <a id="nextsteps"></a>다음 단계

HDInsight의 MapReduce 작업에 대한 일반적인 정보:

* [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)
