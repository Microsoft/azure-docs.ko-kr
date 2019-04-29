---
title: HDInsight에서 Apache Hadoop MapReduce 예제 실행 - Azure
description: HDInsight에 포함된 jar 파일의 MapReduce 샘플을 사용하여 시작하세요. SSH를 통해 클러스터에 연결한 다음 Hadoop 명령을 사용하여 샘플 작업을 실행합니다.
keywords: hadoop 예제 jar,hadoop 예제 jar,hadoop mapreduce 예제,mapreduce 예제
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5fd2d27533d725102a4c334f1e8a1abed6cd78cc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121897"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>HDInsight에 포함된 MapReduce 예제 실행

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

HDInsight의 Apache Hadoop에 포함된 MapReduce 예제를 실행하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* **HDInsight 클러스터**: [Linux HDInsight에서 Apache Hive와 Apache Hadoop 사용 시작](apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

    > [!IMPORTANT]  
    > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* **SSH 클라이언트**: 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="the-mapreduce-examples"></a>MapReduce 예제

**위치**: 샘플은 HDInsight 클러스터의 `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`에 있습니다.

**내용**: 이 보관 파일에는 다음 샘플이 들어 있습니다.

* `aggregatewordcount`: 입력 파일의 단어 수를 계산하는 집계 기반 mapreduce 프로그램입니다.
* `aggregatewordhist`: 입력 파일의 단어 히스토그램을 계산하는 집계 기반 mapreduce 프로그램입니다.
* `bbp`: Bailey-Borwein-Plouffe를 사용하여 Pi의 정확한 숫자를 계산하는 mapreduce 프로그램입니다.
* `dbcount`: 데이터베이스에 저장된 페이지 보기 로그를 계산하는 예제 작업입니다.
* `distbbp`: BBP 형식의 수식을 사용하여 Pi의 정확한 비트를 계산하는 mapreduce 프로그램입니다.
* `grep`: 입력에서 정규식과 일치하는 항목 수를 계산하는 mapreduce 프로그램입니다.
* `join`: 정렬되고 동일하게 분할된 데이터 세트를 통해 조인을 수행하는 작업입니다.
* `multifilewc`: 여러 파일의 단어 수를 계산하는 작업입니다.
* `pentomino`: pentomino 문제에 대한 해결 방법을 찾는 mapreduce 타일 배치 프로그램입니다.
* `pi`: 준난수 몬테카를로 방법을 사용하여 Pi를 추정하는 mapreduce 프로그램입니다.
* `randomtextwriter`: 노드당 10GB의 임의 텍스트 데이터를 기록하는 mapreduce 프로그램입니다.
* `randomwriter`: 노드당 10GB의 임의 데이터를 기록하는 mapreduce 프로그램입니다.
* `secondarysort`: reduce 단계에 대한 보조 정렬을 정의하는 예제입니다.
* `sort`: 임의 기록기에서 기록한 데이터를 정렬하는 mapreduce 프로그램입니다.
* `sudoku`: sudoku 해 찾기입니다.
* `teragen`: terasort에 대한 데이터를 생성합니다.
* `terasort`: terasort를 실행합니다.
* `teravalidate`: terasort 결과를 확인합니다.
* `wordcount`: 입력 파일의 단어 수를 계산하는 mapreduce 프로그램입니다.
* `wordmean`: 입력 파일의 단어 길이에 대한 평균값을 계산하는 mapreduce 프로그램입니다.
* `wordmedian`: 입력 파일의 단어 길이에 대한 중앙값을 계산하는 mapreduce 프로그램입니다.
* `wordstandarddeviation`: 입력 파일의 단어 길이에 대한 표준 편차를 계산하는 mapreduce 프로그램입니다.

**소스 코드**: 이러한 샘플의 소스 코드는 HDInsight 클러스터의 `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`에 있습니다.

## <a name="run-the-wordcount-example"></a>wordcount 예제 실행

1. SSH를 사용하여 HDInsight에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. `username@#######:~$` 프롬프트에서 다음 명령을 사용하여 샘플을 나열합니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    이 명령은 이 문서의 이전 섹션에 나와 있는 샘플 목록을 생성합니다.

3. 특정 샘플에 대한 도움말을 보려면 다음 명령을 사용합니다. 이 명령은 **wordcount** 샘플에 적용됩니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    다음과 같은 메시지가 표시됩니다.

        Usage: wordcount <in> [<in>...] <out>

    이 메시지는 원본 문서에 대해 여러 입력 경로를 제공할 수 있음을 나타냅니다. 최종 경로는 출력(원본 문서의 단어 수)이 저장되는 곳입니다.

4. 다음을 사용하여 클러스터와 함께 샘플 데이터로 제공되는 Notebooks of Leonardo Da Vinci의 모든 단어 수를 계산할 수 있습니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    이 작업에 대한 입력은 `/example/data/gutenberg/davinci.txt`에서 읽습니다. 이 예제의 출력은 `/example/data/davinciwordcount`에 저장됩니다. 두 경로는 모두 로컬 파일 시스템이 아니라 클러스터의 기본 저장소에 있습니다.

   > [!NOTE]  
   > Wordcount 샘플에 대한 도움말에서 설명했듯이 여러 입력 파일을 지정할 수도 있습니다. 예를 들어 `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` 는 davinci.txt와 ulysses.txt 모두에서 단어 수를 계산합니다.

5. 작업이 완료되면 다음 명령을 사용하여 결과를 확인합니다.

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    이 명령은 작업에서 생성된 모든 출력 파일을 연결합니다. 콘솔에 출력이 표시됩니다. 다음 텍스트와 유사하게 출력됩니다.

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    각 줄은 단어와 해당 단어가 입력 데이터에서 발생한 횟수를 나타냅니다.

## <a name="the-sudoku-example"></a>Sudoku 예제

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) 는 9개의 3x3 표로 구성된 논리 퍼즐입니다. 표의 일부 셀에는 숫자가 있고 다른 셀은 비어 있으며, 빈 셀을 해결하는 것이 목표입니다. 이전 링크에는 퍼즐에 대한 자세한 정보가 있지만, 이 샘플의 목적은 빈 셀을 해결하는 것입니다. 따라서 입력은 다음과 같은 형식의 파일이어야 합니다.

* 9개 열의 9개 행
* 각 열은 숫자 또는 `?` (빈 셀을 나타냄)를 포함할 수 있음
* 셀은 공백으로 구분됨

열이나 행에서 숫자를 반복할 수 없다는 Sudoku 퍼즐을 작성하는 특정 방법이 있습니다. 적절히 구성된 HDInsight 클러스터에 대한 예제가 있습니다. 이 예제는 `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta`에 있으며 다음 텍스트를 포함하고 있습니다.

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

이 예제 문제를 Sudoku 예제를 통해 실행하려면 다음 명령을 사용합니다.

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

결과는 다음 텍스트와 유사하게 표시됩니다.

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Pi(π) 예제

Pi 샘플에서는 통계(준난수 몬테카를로) 방법을 사용하여 Pi 값을 추정합니다. 점은 단위 정사각형 내에 임의로 배치됩니다. 정사각형에는 원도 포함되어 있습니다. 점이 원 안에 들어올 확률은 원의 영역인 pi/4와 같습니다. Pi의 값은 4R의 값에서 추정할 수 있습니다. R은 정사각형 내에 있는 점의 총수에 대한 원 내부에 있는 점 개수의 비율입니다. 사용한 점 샘플이 크면 클수록 추정이 향상됩니다.

다음 명령을 사용하여 이 샘플을 실행합니다. 이 명령은 각각 10,000,000개의 샘플이 있는 16개의 맵을 사용하여 pi 값을 추정합니다.

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

이 명령에서 반환되는 값은 **3.14159155000000000000**과 유사합니다. 참고로, Pi의 소수점 이하 10자리는 3.1415926535입니다.

## <a name="10-gb-greysort-example"></a>10GB Greysort 예제

GraySort는 벤치마크 정렬입니다. 이 메트릭은 엄청난 양, 일반적으로 최소 100TB의 데이터를 정렬하는 동안 도달하는 정렬 속도(TB/분)입니다.

이 샘플에서는 비교적 빠르게 실행할 수 있도록 적절한 10GB의 데이터를 사용합니다. Owen O'Malley와 Arun Murthy가 개발한 MapReduce 애플리케이션을 사용합니다. 이 애플리케이션은 0.578TB/분(100TB 정렬에 173분 소요)의 속도로, 2009년 연간 범용("daytona") 테라바이트 정렬 벤치마크로 선정되었습니다. 이 정렬 벤치마크 및 다른 정렬 벤치마크에 대한 자세한 내용은 [정렬 벤치마크](https://sortbenchmark.org/) (영문) 사이트를 참조하십시오.

이 샘플에서는 세 가지 집합의 MapReduce 프로그램을 사용합니다.

* **TeraGen**: 정렬할 데이터의 행을 생성하는 MapReduce 프로그램입니다.

* **TeraSort**: 입력 데이터를 샘플링하고 MapReduce를 사용하여 데이터를 전체 순서로 정렬합니다.

    TeraSort는 사용자 지정 파티셔너를 제외하고 표준 MapReduce 정렬입니다. 파티셔너는 각 reduce의 키 범위를 정의하는 N-1 샘플 키의 정렬된 목록을 사용합니다. 특히, sample[i-1] <= key < sample[i]와 같은 모든 키는 reduce i로 전송됩니다. 이 파티셔너는 reduce i의 출력이 모두 reduce i+1의 출력보다 작도록 보증합니다.

* **TeraValidate**: 출력이 전역으로 정렬되는지 확인하는 MapReduce 프로그램입니다.

    이 프로그램은 출력 디렉터리에 파일당 하나의 맵을 만들며 각 맵에서 각 키가 이전 키보다 작거나 같은지 확인합니다. map 함수는 각 파일의 첫 번째 키와 마지막 키의 레코드를 생성하며, reduce 함수는 i 파일의 첫 번째 키가 i-1 파일의 마지막 키보다 큰지 확인합니다. 모든 문제가 뒤바뀐 순서의 키와 함께 reduce 단계의 출력으로 보고됩니다.

데이터를 생성하고 정렬한 다음 출력의 유효성을 검사하려면 다음 단계를 사용합니다.

1. `/example/data/10GB-sort-input`에 있는 HDInsight 클러스터의 기본 저장소에 저장되는 10GB의 데이터를 생성합니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` 는 이 작업에 사용할 map 작업 수를 Hadoop에 알려 줍니다. 마지막 두 매개 변수는 10GB의 데이터를 만들어 `/example/data/10GB-sort-input`에 저장하도록 작업에 지시합니다.

2. 다음 명령을 사용하여 데이터를 정렬합니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` 는 작업에 사용할 reduce 작업 수를 Hadoop에 알려 줍니다. 마지막 두 매개 변수는 데이터의 입력 및 출력 위치입니다.

3. 다음을 사용하여 정렬에 의해 생성된 데이터의 유효성을 검사합니다.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Linux 기반 HDInsight 클러스터에 포함된 샘플을 실행하는 방법을 알아보았습니다. HDInsight에서 Pig, Hive 및 MapReduce를 사용하는 방법에 대한 자습서는 다음 항목을 참조하세요.

* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-introduction]:apache-hadoop-introduction.md

