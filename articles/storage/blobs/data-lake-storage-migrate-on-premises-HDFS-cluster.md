---
title: 데이터를 마이그레이션할 Azure Data Box를 사용 하 여 온-프레미스 HDFS를 Azure Storage에 저장
description: 온-프레미스 HDFS 저장소에서 데이터를 Azure Storage로 마이그레이션
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: d0908e9edce8efb7a378ee04b6076b61cae2d2bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708682"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Azure Data Box를 사용 하 여 Azure Storage에 온-프레미스 HDFS 저장소에서 데이터를 마이그레이션하려면

Data Box 장치를 사용 하 여 Hadoop 클러스터를 Azure Storage (blob storage 또는 Data Lake 저장소 Gen2)로 온-프레미스 HDFS 저장소에서 데이터를 마이그레이션할 수 있습니다.

이 문서에서는 이러한 작업을 완료할 수 있습니다.

:heavy_check_mark: Data Box 장치에 데이터를 복사 합니다.

:heavy_check_mark: Microsoft Data Box 장치 배송 합니다.

:heavy_check_mark: Data Lake 저장소 Gen2 저장소 계정의 데이터를 이동 합니다.

## <a name="prerequisites"></a>필수 조건

마이그레이션을 완료 하려면 이러한 항목이 필요 합니다.

* Azure Storage 계정을 **하지** 계층적 네임 스페이스를 사용 하도록 설정 했습니다.

* Azure Data Lake 저장소 Gen2 계정을 사용 하려는 경우 (저장소 계정을 **않습니다** 계층적 네임 스페이스를 사용 하도록 설정),이 시점에서 만들어야 할 수 있습니다.

* 원본 데이터를 포함 하는 온-프레미스 Hadoop 클러스터입니다.

* [Azure Data Box 장치](https://azure.microsoft.com/services/storage/databox/)합니다. 

    - [Data Box를 주문](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)합니다. Box에 정렬 하는 동안 선택 해야 하는 저장소 계정 **하지** 계층적 네임 스페이스를 사용 하도록 설정 했습니다. Data Box를 Azure Data Lake 저장소 Gen2 직접 수집을 아직 지원 하지 않기 때문입니다. Storage 계정으로 복사 하 고 다음 Gen2 ADLS 계정에 두 번째 복사본을 수행 해야 합니다. 이 대 한 지침은 아래 단계에 제공 됩니다.
    - [케이블 연결 하 고 Data Box에 연결](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 온-프레미스 네트워크에 있습니다.

준비 인 경우 시작 해 보겠습니다.

## <a name="copy-your-data-to-a-data-box-device"></a>Data Box 장치에 데이터 복사

Data Box 장치에 온-프레미스 HDFS 저장소에서 데이터를 복사, 몇 가지 항목을 설정 및 사용 하 여 수를 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 도구입니다.

복사할 데이터 양이 단일 데이터 상자의 용량 보다를 사용 하는 경우에 사용자가 데이터 상자에 맞지는 크기로 배치 된 데이터 집합을 나눌 수 있습니다.

데이터 상자에는 REST Api의 Blob/개체 저장소를 통해 데이터를 복사 하려면 다음이 단계를 수행 합니다. REST API 인터페이스를 표시 하 여 클러스터에 HDFS 저장소로 Data Box를 확인 합니다. 


1. REST 통해 데이터를 복사 하기 전에 Data Box의 REST 인터페이스에 연결할 기본 보안 및 연결 형식을 식별 합니다. 로컬 웹 UI의 Data Box에 로그인 하 고 이동할 **연결 및 복사** 페이지입니다. Azure storage에 대 한 계정에 데이터 상자에 대 한 아래 **설정에 액세스**을 찾아 선택 **REST(Preview)** 합니다.

    !["연결 및 복사" 페이지](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 액세스 저장소 계정에 업로드 데이터 대화 상자에서 복사 하 고는 **Blob service 끝점** 하며 **저장소 계정 키**합니다. Blob service 끝점에서 생략 된 `https://` 및 후행 슬래시입니다.

    이 경우에 끝점은: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`합니다. 사용 하는 URI의 호스트 부분은: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`합니다. 예를 들어 참조 하는 방법 [http 통한 REST에 연결](/azure/databox/data-box-deploy-copy-data-via-rest)합니다. 

     !["저장소 계정에 액세스 및 데이터 업로드" 대화 상자](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 끝점 및 데이터 상자 IP 주소를 추가 `/etc/hosts` 각 노드에서 합니다.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    DNS에 대 한 일부 다른 메커니즘을 사용 해야 하는 Data Box 끝점을 해결할 수 있습니다.
    
3. 셸 변수를 설정 `azjars` 를 가리키도록 합니다 `hadoop-azure` 고 `microsoft-windowsazure-storage-sdk` jar 파일입니다. 이러한 파일은 Hadoop 설치 디렉터리 (이 명령을 사용 하 여 이러한 파일이 존재 하는 경우를 확인할 수 있습니다 `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` 여기서 `<hadoop_install_dir>` Hadoop를 설치한 디렉터리) 전체 경로 사용 합니다. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

4. Hadoop HDFS에서 데이터 상자 Blob storage로 데이터를 복사 합니다.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   `-libjars` 되도록 옵션을 사용 합니다 `hadoop-azure*.jar` 및 종속 `azure-storage*.jar` 파일을 사용할 수 `distcp`입니다. 이 일부 클러스터에 대 한 이미 발생할 수 있습니다.
   
   다음 예제와 방법을 `distcp` 명령은 데이터를 복사 하는 데 사용 됩니다.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
속도를 향상 복사 합니다.
- 매퍼 수를 변경해 보세요. (위의 예제에서는 `m` 4 매퍼 =.)
- 여러 실행 `distcp` 병렬로 합니다.
- 큰 파일을 작은 파일 보다 더 잘 수행 해야 합니다.       
    
## <a name="ship-the-data-box-to-microsoft"></a>Microsoft Data Box를 배송 합니다.

Microsoft Data Box 장치 배송 준비 하려면 다음이 단계를 따릅니다.

1. 데이터 복사가 완료 되 면 실행 [배송 준비](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest) Data Box에서. 장치 준비를 완료 한 후 BOM 파일을 다운로드 합니다. 이러한 BOM을 사용 하거나 나중에 Azure에 데이터 업로드를 확인 하려면 파일 매니페스트. 장치를 종료 하 고 케이블을 제거 합니다. 
2.  하기를 사용 하 여는 픽업을 예약할 [를 Azure Data Box에 배송](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)합니다. 
3.  Microsoft가 장치를 수신 하 고 데이터 센터 네트워크에 연결 된 (계층적 네임 스페이스를 사용 하지 않도록 설정)으로 지정한 저장소 계정에 데이터를 업로드 한 후 Data Box 주문 경우. 모든 데이터를 Azure에 업로드 되는 BOM 파일에 대해 확인 합니다. 이제 Data Lake 저장소 Gen2 저장소 계정에이 데이터를 이동할 수 있습니다.

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Data Lake 저장소 Gen2 저장소 계정의 데이터를 이동 합니다.

이 단계는 데이터 저장소로 Azure Data Lake 저장소 Gen2를 사용 하는 경우에 필요 합니다. 방금 계층적 네임 스페이스가 없는 blob 저장소 계정을 데이터 저장소로을 사용 하는 경우이 단계를 수행할 필요가 없습니다.

두 가지 방법으로이 수행할 수 있습니다. 

- 사용 하 여 [ADLS Gen2로 데이터를 이동 하려면 Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)합니다. 지정 해야 합니다 **Azure Blob Storage** 원본으로 합니다.

- Azure 기반 Hadoop 클러스터를 사용 합니다. DistCp 명령을 실행할 수 있습니다.

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

이 명령은 Data Lake 저장소 Gen2 저장소 계정의 저장소 계정에서 데이터와 메타 데이터를 복사합니다.

## <a name="next-steps"></a>다음 단계

HDInsight 클러스터를 사용 하 여 Data Lake 저장소 Gen2 작동 하는 방법을 알아봅니다. [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.
