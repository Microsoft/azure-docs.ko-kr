---
title: '성능 튜닝: Hive, HDInsight 및 Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Hive, HDInsight 및 Azure Data Lake Storage Gen2를 사용하여 I/O 집약적 쿼리를 위한 튜닝 지침을 파악합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4b1e5dd3c72122ade2fd4d4092bb18a7acf215f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912946"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>성능 튜닝: Hive, HDInsight 및 Azure Data Lake Storage Gen2

서로 다른 여러 사용 사례 간에 적절한 성능을 제공하도록 기본 설정이 지정되었습니다.  I/O 집약적 쿼리의 경우 Azure Data Lake Storage Gen2를 사용하여 Hive를 튜닝함으로써 더 나은 성능을 얻을 수 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Data Lake Storage Gen2 계정**. 계정을 만드는 방법에 대한 지침은 [빠른 시작: Azure Data Lake Storage Gen2 스토리지 계정 만들기](../common/storage-account-create.md)를 참조하세요.
* Data Lake Storage Gen2 계정에 대한 액세스 권한이 있는 **Azure HDInsight 클러스터**. [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) 참조
* **HDInsight에서 Hive 실행**.  HDInsight에서 Hive 작업 실행에 대한 자세한 내용은 [HDInsight의 Hive 사용](../../hdinsight/hadoop/hdinsight-use-hive.md)을 참조하세요.
* **Data Lake Storage Gen2에 대한 성능 튜닝 지침**.  일반적인 성능 개념은 [Data Lake Storage Gen2 성능 튜닝 지침](data-lake-storage-performance-tuning-guidance.md)을 참조하세요.

## <a name="parameters"></a>매개 변수

향상된 Data Lake Storage Gen2 성능을 튜닝하는 가장 중요한 설정은 다음과 같습니다.

* **hive.tez.container.size** – 각 태스크에 사용된 메모리 양

* **tez.grouping.min-size** – 각 매퍼의 최소 크기

* **tez.grouping.max-size** – 각 매퍼의 최대 크기

* **hive.exec.reducer.bytes.per.reducer** – 각 리듀서의 크기

**hive.tez.container.size** - 컨테이너 크기에 따라 각 태스크에 사용 가능한 메모리 양이 결정됩니다.  Hive에서 동시성을 제어하기 위한 기본 입력입니다.  

**tez.grouping.min-size** – 이 매개 변수를 통해 각 매퍼의 최소 크기를 설정할 수 있습니다.  Tez에서 선택한 매퍼 수가 이 매개 변수 값보다 작은 경우 Tez에서 여기서 설정된 값을 사용합니다.

**tez.grouping.max-size** – 매개 변수를 통해 각 매퍼의 최대 크기를 설정할 수 있습니다.  Tez에서 선택한 매퍼 수가 이 매개 변수 값보다 큰 경우 Tez에서 여기에 설정된 값을 사용합니다.

**hive.exec.reducer.bytes.per.reducer** – 이 매개 변수는 각 리듀서의 크기를 설정합니다.  기본적으로 각 리듀서는 256MB입니다.  

## <a name="guidance"></a>지침

**hive.exec.reducer.bytes.per.reducer 설정** – 데이터가 압축되지 않은 경우 기본 값이 제대로 작동합니다.  압축된 데이터의 경우 리듀서의 크기를 줄여야 합니다.  

**Set hive.tez.container.size** – 각 노드에서 메모리는 yarn.nodemanager.resource.memory-mb에 의해 지정되고 기본적으로 HDI 클러스터에서 제대로 설정해야 합니다.  YARN에서 적절한 메모리 설정에 대한 추가 정보는 이 [게시물](../../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md)을 참조하세요.

I/O 집약적인 워크로드의 경우 Tez 컨테이너 크기를 줄여 더 많은 병렬 처리의 이점을 얻을 수 있습니다. 이렇게 하면 사용자에게 더 많은 컨테이너가 제공되어 동시성이 증가합니다.  하지만 일부 Hive 쿼리에는 상당한 양의 메모리가 필요합니다(예: MapJoin).  태스크에 충분한 메모리가 없는 경우 런타임 중에 메모리 부족 예외가 발생합니다.  메모리 부족 예외가 발생하면 메모리를 늘려야 합니다.   

병렬 처리에서 실행 중인 동시 태스크 수는 총 YARN 메모리의 제약을 받습니다.  YARN 컨테이너 수에 따라 실행할 수 있는 동시 태스크 수가 결정됩니다.  노드당 YARN 메모리를 찾으려면 Ambari로 이동할 수 있습니다.  YARN으로 이동한 후 Configs 탭을 확인합니다. 이 창에 YARN 메모리가 표시됩니다.  

- 총 YARN 메모리 = 노드 수 * 노드당 YARN 메모리
- YARN 컨테이너 수 = 총 YARN 메모리 / Tez 컨테이너 크기

Data Lake Storage Gen2를 사용하여 성능을 향상시키는 핵심 요소는 동시성을 최대한 높이는 것입니다.  Tez가 생성할 태스크 수를 자동으로 계산하므로 설정할 필요가 없습니다.   

## <a name="example-calculation"></a>계산 예제

8 노드 D14 클러스터가 있다고 가정해 보겠습니다.  

- 총 YARN 메모리 = 노드 수 * 노드당 YARN 메모리
- 총 YARN 메모리 = 8 노드 * 96GB = 768GB
- YARN 컨테이너 수 = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Hive 조정에 대한 추가 정보

Hive 쿼리를 조정하는 데 도움이 되는 몇 가지 블로그는 다음과 같습니다.
* [HDInsight에서 Hadoop에 대한 Hive 쿼리 최적화](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Azure HDInsight에서 Apache Hive 쿼리를 최적화](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [HDInsight에서 Hive 최적화에 대한 논의](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)