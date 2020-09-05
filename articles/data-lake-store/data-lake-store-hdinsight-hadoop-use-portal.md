---
title: Data Lake Storage Gen1-포털을 사용 하 여 Azure HDInsight 클러스터 만들기
description: Azure Portal을 사용하여 Azure Data Lake Storage Gen1로 HDInsight Hadoop 클러스터 만들기 및 사용
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd49ddcb59e0d0f3a706f566cf0c011116b1501a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229228"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Data Lake Storage Gen1로 HDInsight 클러스터 만들기

> [!div class="op_single_selector"]
> * [Azure Portal 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 사용(기본 스토리지의 경우)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 사용(추가 스토리지의 경우)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Resource Manager 사용](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure Portal을 사용해 기본 스토리지나 추가 스토리지로 Azure Data Lake Storage Gen1 계정을 사용하는 HDInsight 클러스터를 만드는 방법에 대해 알아봅니다. 추가 저장소는 HDInsight 클러스터에 대 한 선택 사항 이지만 추가 저장소 계정에 비즈니스 데이터를 저장 하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 다음 요구 사항을 충족 하는지 확인 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 이동합니다.
* **Data Lake Storage Gen1 계정**. [Azure Portal을 사용하여 Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다. 또한 계정에서 루트 폴더를 만들어야 합니다.  이 문서에서는 __/ks__ 라는 루트 폴더가 사용 됩니다.
* **Azure Active Directory 서비스 주체**입니다. 이 방법 가이드에서는 Azure Active Directory (Azure AD)에서 서비스 주체를 만드는 방법에 대 한 지침을 제공 합니다. 그러나 서비스 주체를 만들려면 Azure AD 관리자여야 합니다. 관리자 인 경우이 필수 구성 요소를 건너뛰고 계속 진행할 수 있습니다.

>[!NOTE]
>Azure AD 관리자 인 경우에만 서비스 주체를 만들 수 있습니다. Azure AD 관리자가 서비스 주체를 만들어야 Data Lake Storage Gen1을 사용하는 HDInsight 클러스터를 만들 수 있습니다. 또한 [인증서를 사용하여 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)에 설명된 대로 인증서를 사용하여 서비스 주체를 만들어야 합니다.
>

## <a name="create-an-hdinsight-cluster"></a>HDInsight 클러스터 만들기

이 섹션에서는 기본 또는 추가 스토리지로 Data Lake Storage Gen1 계정을 사용하는 HDInsight 클러스터를 만듭니다. 이 문서에서는 Data Lake Storage Gen1 계정을 구성 하는 부분에 대해서만 중점적으로 설명 합니다. 클러스터 만들기에 대한 일반적인 정보 및 절차는 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Data Lake Storage Gen1을 기본 스토리지로 사용하는 클러스터 만들기

Data Lake Storage Gen1 계정을 기본 저장소 계정으로 사용 하 여 HDInsight 클러스터를 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. HDInsight 클러스터를 만드는 방법에 대한 일반 정보는 [클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)를 따릅니다.
3. **스토리지** 블레이드의 **기본 스토리지 유형** 아래에서 **Azure Data Lake Storage Gen1**을 선택한 후 다음 정보를 입력합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "HDInsight 클러스터에 서비스 주체 추가")

    * **Data Lake Store 계정 선택**: 기존 Data Lake Storage Gen1 계정을 선택합니다. 기존 Data Lake Storage Gen1 계정은 필수 항목입니다.  [필수 조건](#prerequisites)을 참조하세요.
    * **루트 경로**: 클러스터 관련 파일이 저장되는 경로를 입력합니다. 스크린샷에서 __/clusters__ 폴더가 존재해야 하는 __/clusters/myhdiadlcluster/__ 이며 포털은 *myhdicluster* 폴더를 만듭니다.  *myhdicluster*는 클러스터 이름입니다.
    * **Data Lake Store 액세스**: Data Lake Storage Gen1 계정과 HDInsight 클러스터 간의 액세스를 구성합니다. 지침은 [Data Lake Storage Gen1 액세스 구성](#configure-data-lake-storage-gen1-access)을 참조하세요.
    * **추가 저장소 계정**: 클러스터에 대 한 추가 저장소 계정으로 Azure storage 계정을 추가 합니다. Data Lake Storage Gen1 계정을 기본 스토리지 유형으로 구성하고 다른 Data Lake Storage Gen1 계정에 대해 클러스터 권한을 제공하는 방식으로 Data Lake Storage Gen1 계정을 더 추가합니다. [Data Lake Storage Gen1 액세스 구성](#configure-data-lake-storage-gen1-access)을 참조하세요.

4. **Data Lake Store 액세스**에서 **선택**을 클릭한 다음 [HDInsight에서 Hadoop 클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터 만들기를 계속 진행합니다.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Data Lake Storage Gen1을 추가 스토리지로 사용하는 클러스터 만들기

다음 지침에 따라 Azure 스토리지 계정을 기본 스토리지로, Data Lake Storage Gen1 계정을 추가 스토리지로 사용하는 HDInsight 클러스터를 만듭니다.

Data Lake Storage Gen1 계정을 추가 저장소 계정으로 사용 하 여 HDInsight 클러스터를 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. HDInsight 클러스터를 만드는 방법에 대한 일반 정보는 [클러스터 만들기](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters)를 따릅니다.
3. **스토리지** 블레이드의 **기본 스토리지 형식** 아래에서 **Azure Storage**를 선택한 후 다음 정보를 입력합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "HDInsight 클러스터에 서비스 주체 추가")

    * **선택 방법** -Azure 구독의 일부인 저장소 계정을 지정 하려면 **내 구독**을 선택한 다음 저장소 계정을 선택 합니다. Azure 구독 외부에 있는 스토리지 계정을 지정하려면 **선택키**를 선택한 다음 외부 스토리지 계정에 대한 정보를 제공합니다.

    * **기본 컨테이너** -기본값을 사용 하거나 사용자 고유의 이름을 지정 합니다.
    * **추가 저장소 계정** -추가 저장소로 Azure storage 계정을 더 추가 합니다.
    * **Data Lake Store 액세스** -Data Lake Storage Gen1 계정과 HDInsight 클러스터 간의 액세스를 구성 합니다. 지침은 [Data Lake Storage Gen1 액세스 구성](#configure-data-lake-storage-gen1-access)을 참조 하세요.

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 액세스 구성

이 섹션에서는 Azure Active Directory 서비스 주체를 사용하여 HDInsight 클러스터에서 Data Lake Storage Gen1 액세스를 구성합니다.

### <a name="specify-a-service-principal"></a>서비스 주체 지정

Azure Portal에서 기존 서비스 주체를 사용하거나 새로 만들 수 있습니다.

Azure Portal에서 서비스 주체를 만들려면 다음을 수행 합니다.
1. Azure Active Directory를 사용 하 여 [서비스 주체 및 인증서 만들기를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 참조 하세요.

Azure Portal에서 기존 서비스 주체를 사용 하려면 다음을 수행 합니다.

1. 서비스 주체에는 저장소 계정에 대 한 소유자 권한이 있어야 합니다. [저장소 계정의 소유자가 되도록 서비스 사용자에 대 한 권한 설정을](#configure-serviceprincipal-permissions) 참조 하세요.
1. **Data Lake Store 액세스**를 선택 합니다.
1. **Data Lake Storage Gen1 액세스** 블레이드에서 **기존 사용**을 선택 합니다.
1. **서비스 주체**를 선택 하 고 서비스 주체를 선택 합니다.
1. 선택한 서비스 주체와 연결된 인증서(.pfx 파일)를 업로드한 다음 인증서 암호를 입력합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "HDInsight 클러스터에 서비스 주체 추가")

1. **액세스** 를 선택 하 여 폴더 액세스를 구성 합니다.  [파일 권한 구성](#configure-file-permissions)을 참조하세요.

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>저장소 계정의 소유자가 되는 서비스 사용자에 대 한 사용 권한 설정
1. 저장소 계정의 Access Control (IAM) 블레이드에서 역할 할당 추가를 클릭 합니다. 
2. 역할 할당 추가 블레이드에서 역할을 ' 소유자 '로 선택한 후 SPN을 선택 하 고 저장을 클릭 합니다.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>파일 권한 구성

이 구성은 계정이 기본 저장소 또는 추가 저장소 계정으로 사용 되는지에 따라 달라 집니다.

* 기본 스토리지로 사용됨

  * Data Lake Storage Gen1 계정의 루트 수준 권한
  * HDInsight 클러스터 스토리지의 루트 수준에서 권한 예를 들어 자습서의 앞부분에서 사용된 __/clusters__ 폴더입니다.

* 추가 스토리지로 사용

  * 파일 액세스가 필요한 폴더에서 권한

Data Lake Storage Gen1 계정 루트 수준에서 사용 권한을 할당 하려면 다음을 수행 합니다.

1. **Data Lake Storage Gen1 액세스** 블레이드에서 **액세스**를 선택 합니다. **파일 권한 선택** 블레이드가 열립니다. 그리고 구독의 모든 Data Lake Storage Gen1 계정이 나열됩니다.
1. 확인란이 표시되도록 Data Lake Storage Gen1 계정의 이름을 마우스로 가리킨 다음(이름을 클릭하면 안 됨) 확인란을 선택합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "HDInsight 클러스터에 서비스 주체 추가")

   기본적으로 __읽기__, __쓰기__및 __실행__ 이 모두 선택 되어 있습니다.

1. 페이지 아래쪽에서 **선택**을 클릭합니다.
1. **실행** 을 선택 하 여 사용 권한을 할당 합니다.
1. **완료**를 선택합니다.

HDInsight 클러스터 루트 수준에서 사용 권한을 할당 하려면:

1. **Data Lake Storage Gen1 액세스** 블레이드에서 **액세스**를 선택 합니다. **파일 권한 선택** 블레이드가 열립니다. 그리고 구독의 모든 Data Lake Storage Gen1 계정이 나열됩니다.
1. **파일 사용 권한 선택** 블레이드에서 콘텐츠를 표시 하는 Data Lake Storage Gen1 계정 이름을 선택 합니다.
1. 폴더 왼쪽의 확인란을 선택하여 HDInsight 클러스터 스토리지 루트를 선택합니다. 위의 스크린샷에 나와 있는 것처럼 클러스터 스토리지 루트는 Data Lake Storage Gen1을 기본 스토리지로 선택할 때 지정한 __/clusters__ 폴더입니다.
1. 폴더에 권한을 설정합니다.  기본적으로 읽기, 쓰기 및 실행이 모두 선택되어 있습니다.
1. 페이지 아래쪽에서 **선택**을 클릭합니다.
1. **실행**을 선택합니다.
1. **완료**를 선택합니다.

Data Lake Storage Gen1을 추가 스토리지로 사용하는 경우 HDInsight 클러스터에서 액세스하려는 폴더에만 권한을 할당해야 합니다. 예를 들어 아래 스크린샷에서는 Data Lake Storage Gen1 계정의 **mynewfolder** 폴더에만 액세스 권한을 제공합니다.

![HDInsight 클러스터에 서비스 주체 사용 권한 할당](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "HDInsight 클러스터에 서비스 주체 사용 권한 할당")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>클러스터 설정 확인

클러스터 설정을 완료한 후에 클러스터 블레이드에서 다음 단계 중 하나 또는 모두를 수행하여 결과를 확인합니다.

* 클러스터에 대 한 연결 된 저장소가 지정한 Data Lake Storage Gen1 계정 인지 확인 하려면 왼쪽 창에서 **저장소 계정** 을 선택 합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "HDInsight 클러스터에 서비스 주체 추가")

* 서비스 주체가 HDInsight 클러스터와 올바르게 연결 되었는지 확인 하려면 왼쪽 창에서 **Data Lake Storage Gen1 액세스** 를 선택 합니다.

    ![HDInsight 클러스터에 서비스 주체 추가](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "HDInsight 클러스터에 서비스 주체 추가")

## <a name="examples"></a>예

저장소로 Data Lake Storage Gen1를 사용 하 여 클러스터를 설정한 후에는 HDInsight 클러스터를 사용 하 여 Data Lake Storage Gen1에 저장 된 데이터를 분석 하는 방법에 대 한 다음 예제를 참조 하세요.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>기본 스토리지인 Data Lake Storage Gen1에서 데이터에 대한 Hive 쿼리 실행

Hive 쿼리를 실행하려면 Ambari 포털에서 [Hive 보기] 인터페이스를 사용합니다. [Ambari Hive 보기]를 사용하는 방법에 대한 지침은 [ HDInsight에서 Hadoop을 사용하여 Hive 보기 사용](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)을 참조하세요.

Data Lake Storage Gen1 계정에서 데이터를 사용하는 경우 다음과 같은 몇 가지 문자열을 변경합니다.

예를 들어 Data Lake Storage Gen1을 기본 스토리지로 사용하여 만든 클러스터에서 데이터 경로는 *adl://&lt;data_lake_storage_gen1_account_name&gt;/azuredatalakestore.net/path/to/file*입니다. Data Lake Storage Gen1 계정에 저장된 샘플 데이터에서 테이블을 만드는 Hive 쿼리는 다음 문과 같습니다.

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

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

Spark 클러스터를 사용하여 Data Lake Storage Gen1 계정에 저장된 데이터에서 Spark 작업을 실행할 수 있습니다. 자세한 내용은 [HDInsight Spark 클러스터를 사용 하 여 Data Lake Storage Gen1에서 데이터 분석](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)을 참조 하세요.

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Storm 토폴로지에서 Data Lake Storage Gen1 사용

Data Lake Storage Gen1 계정을 사용하면 Storm 토폴로지에서 데이터를 쓸 수 있습니다. 이 시나리오를 수행하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm에 Azure Data Lake Storage Gen1 사용](../hdinsight/storm/apache-storm-write-data-lake-store.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure HDInsight 클러스터에 Data Lake Storage Gen1 사용](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: HDInsight 클러스터를 만들어 Data Lake Storage Gen1 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
