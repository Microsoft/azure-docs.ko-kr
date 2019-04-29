---
title: Azure Data Lake Storage Gen1 Hive 성능 조정 지침 | Microsoft Docs
description: Azure Data Lake Storage Gen1 Hive 성능 조정 지침
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437279"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight의 Hive 및 Azure Data Lake Storage Gen1에 대한 성능 조정 지침

서로 다른 여러 사용 사례 간에 적절한 성능을 제공하도록 기본 설정이 지정되었습니다.  I/O 집약적인 쿼리의 경우 Azure Data Lake Storage Gen1로 더 나은 성능을 얻도록 Hive를 조정할 수 있습니다.  

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.
* Data Lake Storage Gen1 계정에 대한 액세스 권한이 있는 **Azure HDInsight 클러스터**. [Data Lake Storage Gen1을 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.
* **HDInsight에서 Hive 실행**.  HDInsight에서 Hive 작업 실행에 대한 자세한 내용은 [HDInsight의 Hive 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)을 참조하세요.
* **Data Lake Storage Gen1 성능 조정 지침**.  일반적인 성능 개념은 [Data Lake Storage Gen1 성능 조정 지침](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)을 참조하세요.

## <a name="parameters"></a>매개 변수

Data Lake Storage Gen1 성능 향상을 위해 조정할 가장 중요한 설정은 다음과 같습니다.

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

**Set hive.tez.container.size** – 각 노드에서 메모리는 yarn.nodemanager.resource.memory-mb에 의해 지정되고 기본적으로 HDI 클러스터에서 제대로 설정해야 합니다.  YARN에서 적절한 메모리 설정에 대한 추가 정보는 이 [게시물](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom)을 참조하세요.

I/O 집약적인 워크로드의 경우 Tez 컨테이너 크기를 줄여 더 많은 병렬 처리의 이점을 얻을 수 있습니다. 이렇게 하면 사용자에게 더 많은 컨테이너가 제공되어 동시성이 증가합니다.  하지만 일부 Hive 쿼리에는 상당한 양의 메모리가 필요합니다(예: MapJoin).  태스크에 충분한 메모리가 없는 경우 런타임 중에 메모리 부족 예외가 발생합니다.  메모리 부족 예외가 발생하면 메모리를 늘려야 합니다.   

병렬 처리에서 실행 중인 동시 태스크 수는 총 YARN 메모리의 제약을 받습니다.  YARN 컨테이너 수에 따라 실행할 수 있는 동시 태스크 수가 결정됩니다.  노드당 YARN 메모리를 찾으려면 Ambari로 이동할 수 있습니다.  YARN으로 이동한 후 Configs 탭을 확인합니다.  이 창에 YARN 메모리가 표시됩니다.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Data Lake Storage Gen1을 사용하여 성능을 개선하기 위한 핵심은 가능한 동시성을 늘리는 것입니다.  Tez가 생성할 태스크 수를 자동으로 계산하므로 설정할 필요가 없습니다.   

## <a name="example-calculation"></a>계산 예

8 노드 D14 클러스터가 있다고 가정해 보겠습니다.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>제한 사항

**Data Lake Storage Gen1 제한** 

Data Lake Storage Gen1에서 제공하는 대역폭 한도에 도달한 경우 작업 오류가 표시되기 시작합니다. 작업 로그에서 제한 오류를 확인하여 파악할 수 있습니다.  Tez 컨테이너 크기를 늘려 병렬 처리를 줄일 수 있습니다.  작업에 대한 동시성이 더 필요한 경우 문의하세요.

제한 여부를 확인하려면 클라이언트 쪽에서 디버그 로깅을 사용하도록 설정해야 합니다. 그 방법은 다음과 같습니다.

1. Hive 구성의 log4j 속성에 다음 속성을 배치합니다. Ambari 보기의 log4j.logger.com.microsoft.azure.datalake.store=DEBUG에서 이 작업을 수행할 수 있습니다. 구성을 적용하려면 노드/서비스를 다시 시작합니다.

2. 제한이 적용되면 hive 로그 파일에 HTTP 429 오류 코드가 표시됩니다. hive 로그 파일은 /tmp/&lt;user&gt;/hive.log에 있습니다.

## <a name="further-information-on-hive-tuning"></a>Hive 조정에 대한 추가 정보

Hive 쿼리를 조정하는 데 도움이 되는 몇 가지 블로그는 다음과 같습니다.
* [HDInsight에서 Hadoop에 대한 Hive 쿼리 최적화](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Hive 쿼리 성능 문제 해결](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [HDInsight에서 Hive 최적화에 대한 논의](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
