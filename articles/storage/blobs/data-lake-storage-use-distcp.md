---
title: DistCp를 사용하여 Azure Data Lake Storage Gen2에 데이터 복사 | Microsoft Docs
description: DistCp 도구를 사용하여 Data Lake Storage Gen2에서 데이터 복사
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 3b58dc8dabc55ba428ce6e35091a6947e5f4a824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481805"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>DistCp를 사용하여 Azure Storage Blob과 Azure Data Lake Storage Gen2 간에 데이터 복사

[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)를 사용하여 범용 V2 스토리지 계정과 계층 구조 네임스페이스를 사용하도록 설정된 범용 V2 스토리지 계정 간에 데이터를 복사할 수 있습니다. 이 문서에서는 DistCp 도구를 사용하는 방법에 대한 지침을 제공합니다.

DistCp는 다양한 명령줄 매개 변수를 제공하며, 이 도구의 사용을 최적화하기 위해 이 문서를 참조하는 것이 좋습니다. 이 문서에서는 데이터를 계층 구조 네임스페이스 사용 계정에 복사하는 데 집중하면서 기본적인 기능을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Data Lake Storage Gen2 기능(계층 구조 네임스페이스)을 사용하도록 설정되지 않은 기존 Azure Storage 계정**.
* **Data Lake Storage Gen2 기능을 사용하는 Azure Storage 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen2 스토리지 계정 만들기](data-lake-storage-quickstart-create-account.md)를 참조하세요.
* 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정에 만든 **파일 시스템**
* Data Lake Storage Gen2를 사용하는 스토리지 계정에 대한 액세스 권한이 있는 **Azure HDInsight 클러스터**. [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux 클러스터에서 DistCp 사용

HDInsight 클러스터는 서로 다른 원본에서 HDInsight 클러스터로 데이터를 복사하는 데 사용할 수 있는 DistCp 유틸리티와 함께 제공됩니다. Azure Blob Storage와 Azure Data Lake Storage를 함께 사용하도록 HDInsight 클러스터를 구성한 경우 기본 제공되는 DistCp 유틸리티를 사용하여 두 스토리지 간에 데이터를 복사할 수 있습니다. 이 섹션에서는 DistCp 유틸리티를 사용하는 방법을 살펴봅니다.

1. HDI 클러스터에 대한 SSH 세션을 만듭니다. [Linux 기반 HDInsight 클러스터에 연결](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 계층 구조 네임스페이스를 사용하지 않고 기존 범용 V2 계정에 액세스할 수 있는지 확인합니다.

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    출력에서 컨테이너의 콘텐츠 목록을 제공해야 합니다.

3. 마찬가지로 클러스터에서 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정에 액세스할 수 있는지 확인합니다. 다음 명령 실행:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    출력에 Data Lake Storage 계정의 파일/폴더 목록이 제공되어야 합니다.

4. DistCp를 사용하여 데이터를 WASB에서 Data Lake Storage 계정으로 복사합니다.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    이 명령은 Blob 스토리지에 있는 **/example/data/gutenberg/** 폴더의 콘텐츠를 Data Lake Storage 계정의 **/myfolder** 폴더에 복사합니다.

5. 마찬가지로 DistCp를 사용하여 데이터를 Data Lake Storage 계정에서 Blob Storage(WASB)로 복사합니다.

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    이 명령은 Data Lake Store 계정에 있는 **/myfolder**의 콘텐츠를 WASB의 **/example/data/gutenberg/** 폴더에 복사합니다.

## <a name="performance-considerations-while-using-distcp"></a>DistCp 사용에 대한 성능 고려 사항

DistCp의 가장 낮은 세분성은 단일 파일이므로 최대 동시 복사본 수를 설정하는 것이 Data Lake Storage에서 최적화할 가장 중요한 매개 변수입니다. 동시 복사본의 수는 명령줄의 매퍼 수(**m**) 매개 변수와 같습니다. 이 매개 변수는 데이터를 복사하는 데 사용되는 최대 매퍼 수를 지정합니다. 기본값은 20입니다.

**예제**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>사용할 매퍼 수는 어떻게 확인하나요?

다음은 사용할 수 있는 몇 가지 지침입니다.

* **1단계: '기본' YARN 앱 큐에서 사용할 수 있는 총 메모리 양 결정** - 첫 번째 단계는 '기본' YARN 앱 큐에서 사용할 수 있는 메모리 양을 결정하는 것입니다. 이 정보는 클러스터와 연결된 Ambari 포털에서 사용할 수 있습니다. YARN으로 이동하여 Configs(구성) 탭을 보고 '기본' 앱 큐에서 사용할 수 있는 YARN 메모리 양을 확인합니다. 이는 DistCp 작업(실제로 MapReduce 작업임)에 사용할 수 있는 총 메모리 양입니다.

* **2단계: 매퍼 수 계산** - **m** 값은 총 YARN 메모리 양을 YARN 컨테이너 크기로 나눈 몫과 같습니다. YARN 컨테이너 크기 정보도 Ambari 포털에서 사용할 수 있습니다. YARN으로 이동한 후 Configs 탭을 확인합니다. 이 창에 YARN 컨테이너 크기가 표시됩니다. 매퍼 수(**m**)를 구하는 수식은 다음과 같습니다.

        m = (number of nodes * YARN memory for each node) / YARN container size

**예제**

4개의 D14v2 클러스터가 있고 10개의 다른 폴더에서 10TB의 데이터를 전송하려고 한다고 가정해보겠습니다. 각 폴더에는 다양한 크기의 데이터가 포함되어 있고 각 폴더 내의 파일 크기가 서로 다릅니다.

* **총 YARN 메모리 양**: Ambari 포털에서 하나의 D14 노드에 대한 YARN 메모리 양이 96GB임을 확인할 수 있습니다. 따라서 4노드 클러스터의 전체 YARN 메모리는 다음과 같습니다. 

        YARN memory = 4 * 96GB = 384GB

* **매퍼 수**: Ambari 포털에서 하나의 D14 클러스터 노드에 대한 YARN 컨테이너 크기가 3,072MB임을 확인할 수 있습니다. 따라서 매퍼 수는 다음과 같습니다.

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

다른 애플리케이션에서 메모리를 사용하고 있으면 DistCp에 대한 클러스터 YARN 메모리 중에서 일부만 사용하도록 선택할 수 있습니다.

### <a name="copying-large-datasets"></a>큰 데이터 세트 복사

이동할 데이터 세트의 크기가 매우 크거나(예: 1TB 초과) 다른 폴더가 많이 있는 경우 여러 DistCp 작업을 사용하는 것을 고려해야 합니다. 성능이 좋아지지는 않을 수 있지만 작업이 분산되므로 한 작업이 실패해도 전체 작업이 아닌 해당 작업만 다시 시작하면 됩니다.

### <a name="limitations"></a>제한 사항

* DistCp는 성능을 최적화하기 위해 크기가 서로 비슷한 매퍼를 만들려고 합니다. 매퍼 수를 늘린다고 항상 성능이 증가하는 것은 아닐 수 있습니다.

* DistCp는 파일당 하나의 매퍼로 제한됩니다. 따라서 파일 개수보다 매퍼가 더 많지 않아야 합니다. DistCp는 파일에 하나의 매퍼를 할당하므로 큰 파일을 복사하는 데 사용할 수 있는 동시성 크기가 제한됩니다.

* 적은 수의 큰 파일이 있는 경우 이러한 파일을 256MB 파일 청크로 나누면 동시성이 높아질 수 있습니다.
