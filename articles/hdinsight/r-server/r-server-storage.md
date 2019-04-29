---
title: HDInsight에서 ML Services에 대한 Azure Storage 솔루션 - Azure
description: HDInsight의 ML Services에서 사용할 수 있는 다양한 저장소 옵션에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: cb0c350df3056636701b5ff5d3962e2a0e96f40d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124529"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure HDInsight의 ML Services에 대한 Azure Storage 솔루션

HDInsight의 ML Services에는 데이터, 코드, 또는 분석 결과가 포함된 개체를 유지할 수 있는 다양한 저장 솔루션이 있습니다. 여기에는 다음 옵션이 포함됩니다.

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

필요한 경우, HDInsight 클러스터가 있는 여러 Azure Storage 계정 또는 컨테이너에 액세스할 수도 있습니다. Azure File Storage는 Azure Storage 파일 공유를 Linux 파일 시스템 등에 마운팅할 수 있도록 에지 노드에서 사용할 수 있는 편리한 데이터 저장 옵션입니다. 하지만, Azure File 공유는 마운팅이 가능하고 Windows 또는 Linux 등 지원되는 운영 체제가 있는 모든 시스템에서 사용할 수 있습니다. 

HDInsight에서 Apache Hadoop 클러스터를 만들 때 **Azure Storage** 계정 또는 **Data Lake Storage**를 지정합니다. 해당 계정의 특정 저장소 컨테이너에는 사용자가 만든 클러스터의 파일 시스템(예: Hadoop 분산 파일 시스템)이 있습니다. 자세한 내용 및 지침은 다음을 참조하세요.

- [HDInsight에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md)
- [Azure HDInsight 클러스터에 Data Lake Storage 사용](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>ML Services 클러스터에서 Azure Blob Storage 계정 사용

ML Services 클러스터를 만들 때 둘 이상의 저장소 계정을 지정한 경우, 다음 지침에서는 ML Services 클러스터에서 데이터 액세스 및 작업에 보조 계정을 사용하는 방법을 설명합니다. **storage1** 및 **container1**(기본 컨테이너), **storage2** 및 **container2** 등의 스토리지 계정과 컨테이너를 가정합니다.

> [!WARNING]  
> 성능을 위해 HDInsight 클러스터는 사용자가 지정한 기본 저장소 계정과 동일한 데이터 센터에 만들어집니다. HDInsight 클러스터와 다른 위치에서는 저장소 계정을 사용할 수 없습니다.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>HDInsight의 ML Services에서 기본 저장소 사용

1. SSH 클라이언트를 사용하여 클러스터의 에지 노드에 연결합니다. HDInsight 클러스터에서 SSH를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
  
2. 샘플 파일인 mysamplefile.csv를 /share 디렉터리에 복사합니다. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. R Studio 또는 다른 R 콘솔로 전환하고 R 코드를 작성하여 이름 노드를 **기본값** 및 액세스하려는 파일의 위치로 설정합니다.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

모든 디렉터리와 파일 참조는 저장소 계정 `wasb://container1@storage1.blob.core.windows.net`을 지정합니다. 이는 HDInsight 클러스터와 연결된 **기본 저장소 계정**입니다.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>HDInsight의 ML Services에서 추가 저장소 사용

이제 **storage2**에서 **container2**의 /private 디렉터리에 있는 mysamplefile1.csv 파일을 처리한다고 가정합니다.

R 코드에서 이름 노드 참조를 **storage2** 스토리지 계정으로 지정합니다.

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

모든 디렉터리와 파일 참조는 이제 저장소 계정 `wasb://container2@storage2.blob.core.windows.net`을 지정합니다. 지정한 **이름 노드**입니다.

구성 해야 합니다 `/user/RevoShare/<SSH username>` 디렉터리에 **storage2** 다음과 같습니다.


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>ML Services 클러스터에서 Azure Data Lake Storage 사용 

HDInsight 클러스터에서 Data Lake Storage를 사용하려면 사용할 각 Azure Data Lake Storage에 대한 액세스 권한을 클러스터에 부여해야 합니다. Azure Portal에서 기본 스토리지 또는 추가 스토리지로 Azure Data Lake Storage 계정을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure Portal을 사용하여 Data Lake Storage를 포함한 HDInsight 클러스터 만들기](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요.

그런 다음, 이전 절차에서 설명한 대로 보조 Azure Storage 계정을 사용할 때처럼 R 스크립트에서 스토리지를 사용합니다.

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>Azure Data Lake Storage에 클러스터 액세스 추가
HDInsight 클러스터와 연결된 Azure AD(Azure Active Directory) 서비스 주체를 사용하여 Data Lake Storage에 액세스합니다.

1. HDInsight 클러스터를 만들 때 **데이터 원본** 탭에서 **클러스터 AAD ID**를 선택합니다.

2. **클러스터 AAD ID** 대화 상자의**AD 서비스 주체 선택**에서 **새로 만들기**를 선택합니다.

서비스 주체에 이름을 지정하고 암호를 만든 후에 **ADLS 액세스 관리**를 클릭하여 Data Lake Storage와 서비스 주체를 연결합니다.

클러스터를 만든 후 하나 이상의 Data Lake Storage 계정에 클러스터 액세스를 추가할 수도 있습니다. Data Lake Storage에 대한 Azure Portal 항목을 열고 **데이터 탐색기 > 액세스 > 추가**로 이동합니다. 

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>HDInsight의 ML Services에서 Data Lake Storage Gen1에 액세스하는 방법

Data Lake Storage Gen1에 대한 액세스 권한을 부여했으면 보조 Azure Storage 계정과 동일한 방식으로 HDInsight의 ML Services 클러스터에서 스토리지를 사용할 수 있습니다. 유일한 차이점은 다음과 같이 **wasb://** 접두사가 **adl://** 로 변경된다는 것입니다.


    # Point to the ADL Storage (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

다음 명령은 RevoShare 디렉터리로 Data Lake Storage Gen1 계정을 구성하고 이전 예제의 샘플 .csv 파일을 추가하는 데 사용합니다.


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>HDInsight의 ML Services에서 Azure File Storage 사용

에지 노드에 사용할 수 있는 [Azure Files](https://azure.microsoft.com/services/storage/files/)라는 편리한 데이터 저장소 옵션도 있습니다. Azure 파일을 사용하면 Linux 파일 시스템에서 Azure Storage 파일 공유를 탑재할 수 있습니다. 이 옵션은 특히 나중에 HDFS 대신 에지 노드의 원시 파일 시스템을 사용하는 것이 유용할 때 필요할 수 있는 데이터 파일, R 스크립트 및 결과 개체를 저장하는 데 편리합니다. 

Azure 파일의 장점은 파일 공유가 탑재되고 Windows 또는 Linux 등 지원되는 OS가 있는 모든 시스템에서 사용할 수 있다는 점입니다. 예를 들어, 사용자 또는 팀의 다른 사용자가 보유한 HDInsight 클러스터, Azure VM 또는 온-프레미스 시스템에서도 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Linux에서 Azure File Storage 사용 방법](../../storage/files/storage-how-to-use-files-linux.md)
- [Windows에서 Azure File Storage 사용 방법](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>다음 단계

* [HDInsight의 ML Services 클러스터 개요](r-server-overview.md)
* [Apache Hadoop에서 ML Services 클러스터 시작](r-server-get-started.md)
* [HDInsight에서 ML 서비스 클러스터에 대한 계산 컨텍스트 옵션](r-server-compute-contexts.md)
* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
