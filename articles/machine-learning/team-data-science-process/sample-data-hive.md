---
title: Azure HDInsight Hive 테이블에서 데이터 샘플링 - Team Data Science Process
description: Hive 쿼리를 사용하여 Azure HDInsight Hive 테이블에 저장된 데이터를 다운 샘플링하여 분석을 위해 보다 관리하기 쉬운 크기로 데이터를 줄입니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c417950e07ae3c6922aa260a3ef40d862870aa1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042886"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Azure HDInsight Hive 테이블에서 데이터 샘플링
이 문서에서는 Hive 쿼리를 사용하여 Azure HDInsight Hive 테이블에 저장된 데이터를 다운 샘플링함으로써 분석을 위해 보다 관리하기 쉬운 크기로 줄이는 방법에 대해 설명합니다. 일반적으로 다음 세 가지 샘플링 방법이 사용됩니다.

* 균일한 무작위 샘플링
* 그룹별 무작위 샘플링
* 계층화된 샘플링

**데이터를 샘플링하는 이유**
분석할 데이터 세트가 큰 경우 일반적으로 데이터를 다운 샘플링하여 작지만 전형적이고 관리하기 쉬운 크기로 줄이는 것이 좋습니다. 다운 샘플링을 수행하면 데이터 이해, 탐색 및 기능 엔지니어링이 용이해집니다. 팀 데이터 과학 프로세스에서는 데이터 처리 기능 및 기계 학습 모델의 빠른 프로토타입 제작을 지원하는 역할을 합니다.

이 샘플 작업은 [TDSP(팀 데이터 과학 프로세스)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)의 단계입니다.

## <a name="how-to-submit-hive-queries"></a>Hive 쿼리를 제출하는 방법
Hadoop 클러스터 헤드 노드의 Hadoop 명령줄 콘솔에서 Hive 쿼리를 제출할 수 있습니다. 이렇게 하려면 Hadoop 클러스터의 헤드 노드에 로그인하여 Hadoop 명령줄 콘솔을 열고 여기에서 Hive 쿼리를 제출합니다. Hadoop 명령줄 콘솔에서 Hive 쿼리를 제출하는 방법에 대한 지침은 [Hive 쿼리를 제출하는 방법](move-hive-tables.md#submit)을 참조하세요.

## <a name="uniform"></a> 균일한 무작위 샘플링
균일한 무작위 샘플링은 데이터 집합의 각 행에 동일한 샘플링 기회가 주어짐을 의미합니다. 이 샘플링은 해당 무작위 필드에 대한 조건을 부여하는 내부 "select" 쿼리 및 외부 "select" 쿼리에서 데이터 집합에 추가 필드 rand()를 추가하여 구현할 수 있습니다.

다음은 예제 쿼리입니다.

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

여기서 `<sample rate, 0-1>` 은 사용자가 샘플링할 레코드의 비율을 지정합니다.

## <a name="group"></a> 그룹별 무작위 샘플링
범주 데이터를 샘플링할 때 범주 변수의 일부 값 인스턴스를 모두 포함하거나 제외할 수 있습니다. 이러한 종류의 샘플링을 "그룹별 샘플링"이라고 합니다. 예를 들어 값이 NY, MA, CA, NJ, PA 등인 범주 변수 "*State*"가 있는 경우 샘플링 여부에 상관없이 각 state의 레코드를 항상 함께 유지할 수 있습니다.

다음은 그룹별로 샘플링하는 예제 쿼리입니다.

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>계층화된 샘플링
무작위 샘플링은 가져온 샘플에 상위 모집단과 동일한 비율로 존재하는 범주 값이 있는 경우 범주 변수에 대해 계층화됩니다. 위와 동일한 예제를 사용하여 데이터에 상태별 다음 관찰이 있다고 가정합니다. NJ에 100개의 관찰, NY에 60개의 관찰, WA에 300개의 관찰이 있습니다. 계층화된 샘플링 비율을 0.5로 지정하면 가져온 샘플에 대략적으로 각각 50개, 30개 및 150개의 NJ, NY 및 WA 관찰이 있게 됩니다.

다음은 예제 쿼리입니다.

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Hive에서 사용할 수 있는 고급 샘플링 방법에 대한 자세한 내용은 [LanguageManual 샘플링](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)을 참조하세요.

