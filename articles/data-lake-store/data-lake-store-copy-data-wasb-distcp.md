---
title: DistCp를 사용 하 여 WASB에서 Azure Data Lake Storage Gen1로 데이터 복사
description: DistCp 도구를 사용 하 여 Azure Storage blob 간에 데이터를 복사 Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 455e73ece2d46a508b3077c13c8106fe53beb4de
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638836"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>DistCp를 사용 하 여 Azure Storage blob와 Azure Data Lake Storage Gen1 간에 데이터 복사

> [!div class="op_single_selector"]
> * [DistCp 사용](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy 사용](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1에 대한 액세스 권한이 있는 HDInsight 클러스터가 있는 경우 DistCp 같은 Hadoop 에코 시스템 도구를 사용 하 여 HDInsight 클러스터 저장소 (WASB) 간에 데이터를 Data Lake Storage Gen1 계정으로 복사할 수 있습니다. 이 문서에서는 DistCp 도구를 사용 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.
* Data Lake Storage Gen1 계정에 대한 액세스 권한이 있는 **Azure HDInsight 클러스터**. [Data Lake Storage Gen1을 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux 클러스터에서 DistCp 사용

An HDInsight 클러스터는 서로 다른 원본에서 HDInsight 클러스터로 데이터를 복사 하는 데 사용할 수 있는 DistCp 도구와 함께 제공 됩니다. Data Lake Storage Gen1 추가 저장소로 사용 하도록 HDInsight 클러스터를 구성한 경우 DistCp를 사용 하 여 Data Lake Storage Gen1 계정 간에 데이터를 복사할 수 있습니다. 이 섹션에서는 DistCp 도구를 사용 하는 방법을 살펴봅니다.

1. 데스크탑에서 SSH를 사용하여 클러스터에 연결합니다. [Linux 기반 HDInsight 클러스터에 연결](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. SSH 프롬프트에서 명령을 실행합니다.

1. Azure Storage blob (WASB)에 액세스할 수 있는지 여부를 확인 합니다. 다음 명령 실행:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   출력은 저장소 blob의 내용 목록을 제공 합니다.

1. 마찬가지로, 클러스터에서 Data Lake Storage Gen1 계정에 액세스할 수 있는지 확인합니다. 다음 명령 실행:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    출력은 Data Lake Storage Gen1 계정에서 파일 및 폴더의 목록을 제공 합니다.

1. DistCp를 사용 하 여 WASB에서 Data Lake Storage Gen1 계정으로 데이터를 복사 합니다.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    이 명령은 WASB의 **/example/data/gutenberg/** 폴더 내용을 Data Lake Storage Gen1 계정의 **/myfolder** 폴더에 복사합니다.

1. 마찬가지로, DistCp를 사용 하 여 Data Lake Storage Gen1 계정에서 WASB로 데이터를 복사 합니다.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    명령은 Data Lake Storage Gen1 계정의 **있는/myfolder** 내용을 WASB의 **/example/data/gutenberg/** 폴더에 복사 합니다.

## <a name="performance-considerations-while-using-distcp"></a>DistCp 사용에 대한 성능 고려 사항

DistCp 도구의 가장 낮은 세분성은 단일 파일이 기 때문에 Data Lake Storage Gen1에 대해 최적화 하기 위해 최대 동시 복사본 수를 설정 하는 것이 가장 중요 한 매개 변수입니다. 명령줄에서 매퍼 수 (' m ') 매개 변수를 설정 하 여 동시 복사의 수를 제어할 수 있습니다. 이 매개 변수는 데이터를 복사하는 데 사용되는 최대 매퍼 수를 지정합니다. 기본값은 20입니다.

예:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>사용할 매퍼 수를 결정 하는 방법

다음은 사용할 수 있는 몇 가지 지침입니다.

* **1단계: 전체 YARN 메모리 확인** - 첫 번째 단계는 DistCp 작업을 실행하는 클러스터에서 사용할 수 있는 YARN 메모리를 확인하는 것입니다. 이 정보는 클러스터와 연결된 Ambari 포털에서 사용할 수 있습니다. YARN으로 이동 하 고 **Configs** 탭을 확인 하 여 YARN 메모리를 확인 합니다. 전체 YARN 메모리를 알려면 노드당 YARN 메모리와 클러스터에 있는 노드 수를 곱합니다.

* **2단계: 매퍼 수 계산** - **m** 값은 전체 YARN 메모리를 YARN 컨테이너 크기로 나눈 몫과 같습니다. YARN 컨테이너 크기 정보는 Ambari 포털 에서도 사용할 수 있습니다. YARN으로 이동 하 여 **Configs** 탭을 확인 합니다. YARN 컨테이너 크기가이 창에 표시 됩니다. 매퍼 (**m**)의 수에 도달 하는 수식은 다음과 같습니다.

   `m = (number of nodes * YARN memory for each node) / YARN container size`

예:

클러스터에 4 개의 D14v2s 노드가 있고 10 개의 서로 다른 폴더에서 10TB의 데이터를 전송 하려는 경우를 가정해 보겠습니다. 각 폴더에는 다양한 크기의 데이터가 포함되어 있고 각 폴더 내의 파일 크기가 서로 다릅니다.

* 전체 YARN 메모리 - Ambari 포털에서 D14 노드 1개의 YARN 메모리가 96GB임을 확인할 수 있습니다. 따라서 4노드 클러스터의 전체 YARN 메모리는 다음과 같습니다.

   `YARN memory = 4 * 96GB = 384GB`

* 매퍼 수 - Ambari 포털에서 D14 클러스터 노드 1개에 대한 YARN 컨테이너 크기는 3072임을 확인할 수 있습니다. 따라서 매퍼 수는 다음과 같습니다.

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

다른 응용 프로그램에서 메모리를 사용 하는 경우 DistCp에 클러스터의 YARN 메모리 일부만 사용 하도록 선택할 수 있습니다.

### <a name="copying-large-datasets"></a>큰 데이터 세트 복사

이동할 데이터 집합의 크기가 큰 경우 (예: 1TB >) 또는 여러 폴더를 사용 하는 경우 여러 DistCp 작업을 사용 하는 것이 좋습니다. 성능이 향상 되지 않을 수 있지만 작업이 실패 하는 경우 전체 작업 대신 특정 작업을 다시 시작 해야 하도록 작업을 분산 합니다.

### <a name="limitations"></a>제한 사항

* DistCp는 성능을 최적화하기 위해 크기가 서로 비슷한 매퍼를 만들려고 합니다. 매퍼 수를 늘린다고 항상 성능이 증가하는 것은 아닐 수 있습니다.

* DistCp는 파일당 하나의 매퍼로 제한됩니다. 따라서 파일이 있는 것 보다 더 많은 매퍼를 사용할 수 없습니다. DistCp는 파일에 하나의 매퍼만 할당할 수 있으므로 대용량 파일을 복사 하는 데 사용할 수 있는 동시성의 양이 제한 됩니다.

* 적은 수의 용량이 있는 경우 더 많은 잠재적 동시성을 제공 하기 위해 256 MB 파일 청크로 분할 합니다.

* Azure Blob storage 계정에서 복사 하는 경우 복사 작업은 Blob storage 쪽에서 제한 될 수 있습니다. 따라서 복사 작업의 성능이 저하됩니다. Azure Blob storage의 제한에 대한 자세한 내용은 [azure 구독 및 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md)에서 Azure Storage 제한을 참조 하세요.

## <a name="see-also"></a>참고 항목

* [Azure Storage blob에서 Data Lake Storage Gen1로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1에서 Azure Data Lake Analytics 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1에서 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
