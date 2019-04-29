---
title: Azure Portal을 사용하여 Azure Data Lake Storage Gen1로 Azure HDInsight 클러스터 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Data Lake Storage Gen1로 HDInsight Hadoop 클러스터 만들기 및 사용
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6f9064c6027499fff3a8551ee60722cd66c54dc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877698"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Data Lake Storage Gen1로 HDInsight 클러스터 만들기
> [!div class="op_single_selector"]
> * [Azure Portal 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 사용(기본 저장소의 경우)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 사용(추가 저장소의 경우)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure Portal을 사용해 기본 스토리지나 추가 스토리지로 Azure Data Lake Storage Gen1 계정을 사용하는 HDInsight 클러스터를 만드는 방법에 대해 알아봅니다. 추가 저장소는 HDInsight 클러스터에 대해 선택적 사항이지만 추가 저장소 계정에 비즈니스 데이터를 저장하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음 요구 사항을 충족하는지 확인합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 이동합니다.
* **Data Lake Storage Gen1 계정**. [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다. 또한 계정에서 루트 폴더를 만들어야 합니다.  이 자습서에서는 __/clusters__라는 루트 폴더가 사용됩니다.
* **Azure Active Directory 서비스 주체** 이 자습서에서는 Azure AD(Azure Active Directory)에서 서비스 주체를 만드는 방법에 대한 지침을 제공합니다. 그러나 서비스 주체를 만들려면 Azure AD 관리자여야 합니다. 관리자인 경우 이 필수 요소를 건너뛰고 자습서를 진행할 수 있습니다.

    >[!NOTE]
    >Azure AD 관리자인 경우에만 서비스 주체를 만들 수 있습니다. Azure AD 관리자가 서비스 주체를 만들어야 Data Lake Storage Gen1을 사용하는 HDInsight 클러스터를 만들 수 있습니다. 또한 [인증서를 사용하여 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)에 설명된 대로 인증서를 사용하여 서비스 주체를 만들어야 합니다.
    >

## <a name="create-an-hdinsight-cluster"></a>HDInsight 클러스터 만들기

이 섹션에서는 기본 또는 추가 스토리지로 Data Lake Storage Gen1 계정을 사용하는 HDInsight 클러스터를 만듭니다. 이 문서는 Data Lake Storage Gen1 계정을 구성하는 과정만 주로 다룹니다.  클러스터 만들기에 대한 일반적인 정보 및 절차는 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1을 기본 스토리지로 사용하는 클러스터 만들기

**Data Lake Storage Gen1 계정을 기본 스토리지 계정으로 사용하는 HDInsight 클러스터를 만들려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. HDInsight 클러스터를 만드는 방법에 대한 일반 정보는 [클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)를 따릅니다.
3. **스토리지** 블레이드의 **기본 스토리지 유형** 아래에서 **Azure Data Lake Storage Gen1**을 선택한 후 다음 정보를 입력합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "HDInsight 클러스터에 서비스 주체 추가")

    - **Data Lake Store 계정 선택**: 기존 Data Lake Storage Gen1 계정을 선택합니다. 기존 Data Lake Storage Gen1 계정은 필수 항목입니다.  [필수 조건](#prerequisites)을 참조하세요.
    - **루트 경로**: 클러스터 관련 파일이 저장되는 경로를 입력합니다. 스크린샷에서 __/clusters__ 폴더가 존재해야 하는 __/clusters/myhdiadlcluster/__ 이며 포털은 *myhdicluster* 폴더를 만듭니다.  *myhdicluster*는 클러스터 이름입니다.
    - **Data Lake Store 액세스**: Data Lake Storage Gen1 계정과 HDInsight 클러스터 간의 액세스를 구성합니다. 지침은 Data Lake Storage Gen1 액세스 구성을 참조하세요.
    - **추가 Storage 계정**: 클러스터에 대한 추가 스토리지 계정으로 Azure Storage 계정을 추가합니다. Data Lake Storage Gen1 계정을 기본 스토리지 유형으로 구성하고 다른 Data Lake Storage Gen1 계정에 대해 클러스터 권한을 제공하는 방식으로 Data Lake Storage Gen1 계정을 더 추가합니다. Data Lake Storage Gen1 액세스 구성을 참조하세요.

4. **Data Lake Store 액세스**에서 **선택**을 클릭한 다음 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터 만들기를 계속 진행합니다.


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1을 추가 스토리지로 사용하는 클러스터 만들기

다음 지침에 따라 Azure 스토리지 계정을 기본 스토리지로, Data Lake Storage Gen1 계정을 추가 스토리지로 사용하는 HDInsight 클러스터를 만듭니다.

**Data Lake Storage Gen1 계정을 추가 스토리지 계정으로 사용하는 HDInsight 클러스터를 만들려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. HDInsight 클러스터를 만드는 방법에 대한 일반 정보는 [클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)를 따릅니다.
3. **스토리지** 블레이드의 **기본 스토리지 형식** 아래에서 **Azure Storage**를 선택한 후 다음 정보를 입력합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight 클러스터에 서비스 주체 추가")

    - **선택 메서드**: 다음 옵션 중 하나를 사용합니다.

        * Azure 구독의 일부인 저장소 계정을 지정하려면 **내 구독**을 선택한 다음 저장소 계정을 선택합니다.
        * Azure 구독 외부에 있는 저장소 계정을 지정하려면 **선택키**를 선택한 다음 외부 저장소 계정에 대한 정보를 제공합니다.

    - **기본 컨테이너**: 기본값을 사용하거나 고유한 이름을 지정합니다.

    - 추가 저장소 계정: 추가 저장소로 Azure 저장소 계정을 더 추가합니다.
    - Data Lake Store 액세스: Data Lake Storage Gen1 계정과 HDInsight 클러스터 간의 액세스를 구성합니다. 지침은 [Data Lake Storage Gen1 액세스 구성](#configure-data-lake-storage-gen1-access)을 참조하세요.

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 액세스 구성 

이 섹션에서는 Azure Active Directory 서비스 주체를 사용하여 HDInsight 클러스터에서 Data Lake Storage Gen1 액세스를 구성합니다. 

### <a name="specify-a-service-principal"></a>서비스 주체 지정

Azure Portal에서 기존 서비스 주체를 사용하거나 새로 만들 수 있습니다.

**Azure Portal에서 서비스 주체를 만들려면**

1. 저장소 블레이드에서 **Data Lake Store 액세스**를 클릭합니다.
2. **Data Lake Storage Gen1 액세스** 블레이드에서 **새로 만들기**를 클릭합니다.
3. **서비스 주체**를 클릭한 다음 지침에 따라 서비스 주체를 만듭니다.
4. 나중에 다시 사용하려는 경우 인증서를 다운로드합니다. 추가 HDInsight 클러스터를 만들 때 동일한 서비스 주체를 사용하려면 인증서를 다운로드하는 것이 유용합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "HDInsight 클러스터에 서비스 주체 추가")

4. **액세스**를 클릭하여 폴더 액세스를 구성합니다.  [파일 권한 구성](#configure-file-permissions)을 참조하세요.


**Azure Portal에서 기존 서비스 주체를 사용하려면**

1. **Data Lake Store 액세스**를 클릭합니다.
1. **Data Lake Storage Gen1 액세스** 블레이드에서 **기존 항목 사용**을 클릭합니다.
2. **서비스 주체**를 클릭한 다음 서비스 주체를 선택합니다. 
3. 선택한 서비스 주체와 연결된 인증서(.pfx 파일)를 업로드한 다음 인증서 암호를 입력합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight 클러스터에 서비스 주체 추가")

4. **액세스**를 클릭하여 폴더 액세스를 구성합니다.  [파일 권한 구성](#configure-file-permissions)을 참조하세요.


### <a name="configure-file-permissions"></a>파일 권한 구성

구성은 계정이 기본 저장소 또는 추가 저장소 계정으로 사용되는지 여부에 따라 달라집니다.

- 기본 저장소로 사용됨

    - Data Lake Storage Gen1 계정의 루트 수준 권한
    - HDInsight 클러스터 저장소의 루트 수준에서 권한 예를 들어 자습서의 앞부분에서 사용된 __/clusters__ 폴더입니다.
- 추가 저장소로 사용

    - 파일 액세스가 필요한 폴더에서 권한

**Data Lake Storage Gen1 계정 루트 수준에서 권한을 할당하려면**

1. **Data Lake Storage Gen1 액세스** 블레이드에서 **액세스**를 클릭합니다. **파일 권한 선택** 블레이드가 열립니다. 그리고 구독의 모든 Data Lake Storage Gen1 계정이 나열됩니다.
2. 확인란이 표시되도록 Data Lake Storage Gen1 계정의 이름을 마우스로 가리킨 다음(이름을 클릭하면 안 됨) 확인란을 선택합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight 클러스터에 서비스 주체 추가")

   기본적으로 __읽기__, __쓰기__ 및 __실행__이 모두 선택되어 있습니다.

3. 페이지 아래쪽에서 **선택**을 클릭합니다.
4. **실행**을 클릭하여 권한을 할당합니다.
5. **Done**을 클릭합니다.

**HDInsight 클러스터 루트 수준에서 권한을 할당하려면**

1. **Data Lake Storage Gen1 액세스** 블레이드에서 **액세스**를 클릭합니다. **파일 권한 선택** 블레이드가 열립니다. 그리고 구독의 모든 Data Lake Storage Gen1 계정이 나열됩니다.
1. **파일 권한 선택** 블레이드에서 Data Lake Storage Gen1 계정 이름을 클릭하여 해당 내용을 표시합니다.
2. 폴더 왼쪽의 확인란을 선택하여 HDInsight 클러스터 저장소 루트를 선택합니다. 위의 스크린샷에 나와 있는 것처럼 클러스터 스토리지 루트는 Data Lake Storage Gen1을 기본 스토리지로 선택할 때 지정한 __/clusters__ 폴더입니다.
3. 폴더에 권한을 설정합니다.  기본적으로 읽기, 쓰기 및 실행이 모두 선택되어 있습니다.
4. 페이지 아래쪽에서 **선택**을 클릭합니다.
5. **실행**을 클릭합니다.
6. **Done**을 클릭합니다.

Data Lake Storage Gen1을 추가 스토리지로 사용하는 경우 HDInsight 클러스터에서 액세스하려는 폴더에만 권한을 할당해야 합니다. 예를 들어 아래 스크린샷에서는 Data Lake Storage Gen1 계정의 **mynewfolder** 폴더에만 액세스 권한을 제공합니다.

![HDInsight 클러스터에 서비스 주체 사용 권한 할당](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "HDInsight 클러스터에 서비스 주체 사용 권한 할당")


## <a name="verify-cluster-set-up"></a>클러스터 설정 확인

클러스터 설정을 완료한 후에 클러스터 블레이드에서 다음 단계 중 하나 또는 모두를 수행하여 결과를 확인합니다.

* 클러스터의 연결된 스토리지가 지정한 Data Lake Storage Gen1 계정인지 확인하려면 왼쪽 창에서 **Storage 계정**을 클릭합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "HDInsight 클러스터에 서비스 주체 추가")

* 서비스 주체가 HDInsight 클러스터와 올바르게 연결되었는지 확인하려면 왼쪽 창에서 **Data Lake Storage Gen1 액세스**를 클릭합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight 클러스터에 서비스 주체 추가")


## <a name="examples"></a>예

Data Lake Storage Gen1을 스토리지로 사용하여 클러스터를 설정한 후에 HDInsight 클러스터를 사용하여 Data Lake Storage Gen1에 저장된 데이터를 분석하는 방법에 대한 다음과 같은 예제를 참조합니다.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>기본 스토리지인 Data Lake Storage Gen1에서 데이터에 대한 Hive 쿼리 실행

Hive 쿼리를 실행하려면 Ambari 포털에서 [Hive 보기] 인터페이스를 사용합니다. [Ambari Hive 보기]를 사용하는 방법에 대한 지침은 [ HDInsight에서 Hadoop을 사용하여 Hive 보기 사용](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)을 참조하세요.

Data Lake Storage Gen1 계정에서 데이터를 사용하는 경우 다음과 같은 몇 가지 문자열을 변경합니다.

예를 들어 Data Lake Storage Gen1을 기본 스토리지로 사용하여 만든 클러스터에서 데이터 경로는 *adl://&lt;data_lake_storage_gen1_account_name&gt;/azuredatalakestore.net/path/to/file*입니다. Data Lake Storage Gen1 계정에 저장된 샘플 데이터에서 테이블을 만드는 Hive 쿼리는 다음 문과 같습니다.

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

설명:
* `adl://hdiadlsg1storage.azuredatalakestore.net/`은 Data Lake Storage Gen1 계정의 루트입니다.
* `/clusters/myhdiadlcluster`는 클러스터를 만드는 동안에 지정한 클러스터 데이터의 루트입니다.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`는 쿼리에서 사용하는 샘플 파일의 위치입니다.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>추가 스토리지인 Data Lake Storage Gen1에서 데이터에 대한 Hive 쿼리 실행

직접 만든 클러스터에서 Blob Storage를 기본 스토리지로 사용하는 경우 샘플 데이터는 추가 스토리지로 사용되는 Data Lake Storage Gen1 계정에 포함되지 않습니다. 이 경우에는 먼저 Blob Storage에서 Data Lake Storage Gen1 계정으로 데이터를 전송하고 앞의 예제와 같이 쿼리를 실행합니다.

Blob Storage에서 Data Lake Storage Gen1로 데이터를 복사하는 방법에 대한 내용은 다음 문서를 참조하세요.

* [Distcp를 사용하여 Azure Storage Blob와 Data Lake Storage Gen1 간에 데이터 복사](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy를 사용하여 Azure Storage Blob에서Data Lake Storage Gen1로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Spark 클러스터에서 Data Lake Storage Gen1 사용
Spark 클러스터를 사용하여 Data Lake Storage Gen1 계정에 저장된 데이터에서 Spark 작업을 실행할 수 있습니다. 자세한 내용은 [HDInsight Spark 클러스터를 사용하여 Data Lake Storage Gen1의 데이터 분석](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)을 참조하세요.


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Storm 토폴로지에서 Data Lake Storage Gen1 사용
Data Lake Storage Gen1 계정을 사용하면 Storm 토폴로지에서 데이터를 쓸 수 있습니다. 이 시나리오를 수행하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm에 Azure Data Lake Storage Gen1 사용](../hdinsight/storm/apache-storm-write-data-lake-store.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [Azure HDInsight 클러스터에 Data Lake Storage Gen1 사용](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: HDInsight 클러스터를 만들어 Data Lake Storage Gen1 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
