---
title: HDInsight에서 Apache Hadoop 작업용 데이터 업로드
description: Azure 클래식 CLI, Azure Storage 탐색기, Azure PowerShell, Hadoop 명령줄 또는 Sqoop을 사용하여 HDInsight에서 Apache Hadoop 작업 데이터를 업로드 및 액세스하는 방법에 대해 알아봅니다.
keywords: etl hadoop, hadoop으로 데이터 가져오기, hadoop 데이터 로드
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: 3283c885956c5b43171c6287dc00efa9a82db28e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763916"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>HDInsight에서 Apache Hadoop 작업용 데이터 업로드

Azure HDInsight는 Azure Storage 및 Azure Data Lake Store(Gen1 및 Gen2)를 통해 모든 기능을 갖춘 HDFS(Hadoop Distributed File System)를 제공합니다. Azure Storage, Data Lake Store Gen1 및 Gen2는 고객에게 원활한 환경을 제공하기 위해 HDFS 확장으로 설계되었습니다. 이를 통해 Hadoop 에코시스템에서 구성 요소의 전체 집합이 관리하는 데이터에서 직접 작동하도록 합니다. Azure Storage, Data Lake Store Gen1 및 Gen2는 데이터 스토리지 및 해당 데이터의 계산에 최적화된 별개의 파일 시스템입니다. Azure Storage 사용 시 얻을 수 있는 혜택에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용][hdinsight-storage], [HDInsight에서 Data Lake Store Gen1 사용](hdinsight-hadoop-use-data-lake-store.md) 및 [HDInsight에서 Data Lake Store Gen2 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 요구 사항을 확인하세요.

* Azure HDInsight 클러스터를 만듭니다. 자세한 내용은 [Azure HDInsight 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
* 다음 문서의 정보:

    - [HDInsight에서 Azure Storage 사용][hdinsight-storage]
    - [HDInsight에서 Data Lake Store Gen1 사용](hdinsight-hadoop-use-data-lake-store.md)
    - [HDInsight에서 Data Lake Store Gen2 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Azure Storage에 데이터 업로드

## <a name="utilities"></a>공공 시설
Microsoft는 Azure Storage에서 작업할 다음 유틸리티를 제공합니다.

| 도구 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AZCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop 명령](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Hadoop 명령은 HDInsight 클러스터에서만 사용할 수 있습니다. 이 명령을 통해 로컬 파일 시스템의 데이터를 Azure Storage로 로드할 수만 있습니다.  


## <a id="commandline"></a>Hadoop 명령줄
데이터가 클러스터 헤드 노드에 존재하는 경우 Hadoop 명령줄은 Azure Storage Blob에 데이터를 저장하는데만 유용합니다.

Hadoop 명령을 사용 하려면 먼저 연결 해야 사용 하 여 헤드 노드 [SSH 또는 PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)합니다.

연결된 후에 저장소에 파일을 업로드하려면 다음 구문을 사용할 수 있습니다.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

예를 들어 `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

HDInsight의 기본 파일 시스템이 Azure Storage에 있으므로 /example/datadavinci.txt는 실제로 Azure Storage에 있습니다. 파일이 다음과 같을 수도 있습니다.

    wasb:///example/data/data.txt

또는

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

파일로 작업하는 다른 Hadoop 명령의 목록은 [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)을 참조하세요.

> [!WARNING]  
> Apache HBase 클러스터에서 데이터 쓰기 시 사용되는 기본 블록 크기는 256KB입니다. HBase API 또는 REST API를 사용할 때는 잘 작동하는 반면 `hadoop` 또는 `hdfs dfs` 명령을 사용하여 12GB를 초과하는 데이터를 기록하면 오류가 발생합니다. 자세한 내용은 이 문서의 [Blob에서 쓰기를 위한 저장소 예외](#storageexception) 섹션을 참조하세요.

## <a name="graphical-clients"></a>그래픽 클라이언트
Azure Storage를 사용하기 위한 그래픽 인터페이스를 제공하는 몇몇 애플리케이션이 있습니다. 다음 테이블은 이러한 애플리케이션 중 일부의 목록입니다.

| 클라이언트 | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [HDInsight 용 Microsoft Visual Studio Tools](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage 탐색기](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Microsoft Azure용 CloudBerry Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage를 로컬 드라이브로 탑재
[Azure Storage를 로컬 드라이브로 탑재](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)를 참조하세요.

## <a name="upload-using-services"></a>서비스를 사용하여 업로드
### <a name="azure-data-factory"></a>Azure Data Factory
Azure 데이터 팩터리 서비스는 데이터 저장소, 처리 및 이동 서비스를 효율적이고 확장 가능하며 안정적인 데이터 프로덕션 파이프라인으로 구성하기 위한 완전히 관리되는 서비스입니다.

|저장소 유형|문서화|
|----|----|
|Linux에서 File Storage 사용에 대한 자세한 내용은 Linux에서 Azure 파일 스토리지 사용 방법을 참조하세요.|[Azure Data Factory를 사용하여 Azure Blob Storage에(서) 데이터 복사](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에(서) 데이터 복사](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2에 데이터 로드](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop은 Hadoop과 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL Server, MySQL, Oracle 등의 관계형 데이터베이스 관리 시스템(RDBMS)에서 Hadoop 분산형 파일 시스템(HDFS)으로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다.

자세한 내용은 [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]을 참조하세요.

### <a name="development-sdks"></a>개발 SDK
Azure Storage는 다음 프로그래밍 언어에서 Azure SDK를 사용하여 액세스할 수 있습니다.

* .NET
* 자바
* Node.js
* PHP
* Python
* Ruby

Azure SDK 설치에 대한 자세한 내용은 [Azure 다운로드](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>문제 해결
### <a id="storageexception"></a>Blob에서 쓰기를 위한 저장소 예외
**증상**: `hadoop` 또는 `hdfs dfs` 명령을 사용하여 HBase 클러스터에12GB를 초과하는 데이터를 기록하면 다음 오류가 발생할 수 있습니다.

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**원인**: HDInsight의 HBase를 Azure Storage에 쓸 때 블록 크기는 기본적으로 256KB로 설정됩니다. HBase API 또는 REST API를 사용할 때는 잘 작동하는 반면 `hadoop` 또는 `hdfs dfs` 명령줄 유틸리티를 사용할 때 오류가 발생합니다.

**해결 방법**: `fs.azure.write.request.size`를 사용하여 블록 크기를 더 크게 지정합니다. `-D` 매개 변수를 사용하여 사용량에 따라 이 작업을 수행할 수 있습니다. `hadoop` 명령에서 이 매개 변수를 사용하는 예는 다음 명령과 같습니다.

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Apache Ambari를 사용하여 `fs.azure.write.request.size` 값을 전역적으로 늘릴 수도 있습니다. 다음 단계에 따라 Ambari 웹 UI 값을 변경합니다.

1. 브라우저에서 클러스터에 대한 Ambari 웹 UI로 이동합니다. https://CLUSTERNAME.azurehdinsight.net에서 **CLUSTERNAME**은 클러스터의 이름입니다.

    메시지가 표시되면 클러스터의 관리자 이름 및 암호를 입력합니다.
2. 화면 왼쪽에서 **HDFS**를 선택한 다음 **구성** 탭을 선택합니다.
3. **필터...** 필드에 `fs.azure.write.request.size`를 입력합니다. 그러면 페이지 가운데에 필드 및 현재 값이 표시됩니다.
4. 값을 262144(256KB)에서 새 값으로 변경합니다. 예를 들어 4194304(4MB)로 변경합니다.

![Ambari 웹 UI를 통해 값을 변경하는 이미지](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Ambari 사용에 대한 자세한 내용은 [Apache Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 HDInsight로 데이터를 가져오는 방법을 익혔으니 다음 문서를 읽고 분석을 수행하는 방법을 알아보세요:

* [Azure HDInsight 시작][hdinsight-get-started]
* [프로그래밍 방식으로 Apache Hadoop 작업 제출][hdinsight-submit-jobs]
* [HDInsight에서 Apache Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Apache Pig 사용][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
