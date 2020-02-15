---
title: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용
description: Azure HDInsight 클러스터와 함께 Azure Data Lake Storage Gen2를 사용 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/03/2020
ms.openlocfilehash: aeb86823ddb25bbe0340630b55360806faef59e9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186877"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용

Azure Data Lake Storage Gen2은 Azure Blob storage를 기반으로 하는 빅 데이터 분석 전용 클라우드 저장소 서비스입니다. Data Lake Storage Gen2는 Azure Blob Storage 및 Azure Data Lake Storage Gen1의 기능을 결합 합니다. 결과 서비스는 저렴 하 고 계층화 된 저장소, 고가용성 및 재해 복구 기능과 함께 파일 시스템 의미 체계, 디렉터리 수준 및 파일 수준 보안, 확장성과 같은 Azure Data Lake Storage Gen1의 기능을 제공 합니다. Azure Blob storage에서.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 가용성

Data Lake Storage Gen2는 거의 모든 Azure HDInsight 클러스터 형식에 대 한 저장소 옵션으로 기본 및 추가 저장소 계정으로 사용할 수 있습니다. 그러나 HBase에는 Data Lake Storage Gen2 계정이 하나만 있을 수 있습니다.

Data Lake Storage Gen2 사용 하 여 클러스터 만들기 옵션을 전체적으로 비교 하려면 [Azure HDInsight 클러스터와 함께 사용 하기 위한 저장소 옵션 비교](hdinsight-hadoop-compare-storage-options.md)를 참조 하세요.

> [!Note]  
> Data Lake Storage Gen2를 **기본 저장소 형식**으로 선택한 후에는 Data Lake Storage Gen1 계정을 추가 저장소로 선택할 수 없습니다.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Azure Portal를 통해 Data Lake Storage Gen2를 사용 하 여 클러스터 만들기

저장소에 Data Lake Storage Gen2를 사용 하는 HDInsight 클러스터를 만들려면 다음 단계를 수행 하 여 Data Lake Storage Gen2 계정을 구성 합니다.

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID가 아직 없는 경우 지금 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 위에서 **리소스 만들기**를 클릭 합니다.
1. 검색 상자에 **사용자 할당** 을 입력 하 고 **사용자 할당 관리 id**를 클릭 합니다.
1. **만들기**를 클릭합니다.
1. 관리 id의 이름을 입력 하 고 올바른 구독, 리소스 그룹 및 위치를 선택 합니다.
1. **만들기**를 클릭합니다.

Azure HDInsight에서 관리 id가 작동 하는 방식에 대 한 자세한 내용은 [Azure hdinsight에서 관리 되는 id](hdinsight-managed-identities.md)를 참조 하세요.

![사용자 할당 관리 ID 만들기](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정 만들기

Azure Data Lake Storage Gen2 스토리지 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 위에서 **리소스 만들기**를 클릭 합니다.
1. 검색 상자에 **저장소** 를 입력 하 고 **저장소 계정**을 클릭 합니다.
1. **만들기**를 클릭합니다.
1. **저장소 계정 만들기** 화면에서 다음을 수행 합니다.
    1. 올바른 구독 및 리소스 그룹을 선택 합니다.
    1. Data Lake Storage Gen2 계정의 이름을 입력 합니다. 저장소 계정 명명 규칙에 대 한 자세한 내용은 [Azure 리소스에 대 한 명명 규칙](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage)을 참조 하세요.
    1. **고급** 탭을 클릭 합니다.
    1. **Data Lake Storage Gen2**에서 **계층적 네임 스페이스** 옆에 있는 **사용** 을 클릭 합니다.
    1. **검토 + 만들기**를 클릭합니다.
    1. **만들기**

저장소 계정을 만드는 동안 기타 옵션에 대 한 자세한 내용은 [빠른 시작: Azure Data Lake Storage Gen2 저장소 계정 만들기](../storage/blobs/data-lake-storage-quickstart-create-account.md)를 참조 하세요.

![Azure Portal에서 스토리지 계정을 만드는 과정을 보여 주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정에서 관리 되는 id에 대 한 사용 권한 설정

저장소 계정의 **저장소 Blob 데이터 소유자** 역할에 관리 되는 id를 할당 합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 저장소 계정을 선택 하 고 **액세스 제어 (IAM)** 를 선택 하 여 계정에 대 한 액세스 제어 설정을 표시 합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.

    ![스토리지 액세스 제어 설정을 보여 주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. **+ 역할 할당 추가** 단추를 선택 하 여 새 역할을 추가 합니다.
1. **역할 할당 추가** 창에서 **저장소 Blob 데이터 소유자** 역할을 선택 합니다. 그런 다음, 관리 ID 및 스토리지 계정이 있는 구독을 선택합니다. 다음으로, 앞에서 만든 사용자 할당 관리 ID를 검색하여 찾습니다. 마지막으로 관리 되는 id를 선택 하면 **선택한 구성원**아래에 나열 됩니다.

    ![RBAC 역할을 할당하는 방법을 보여주는 스크린샷](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. **저장**을 선택합니다. 선택한 사용자 할당 id는 이제 선택한 역할 아래에 나열 됩니다.
1. 이 초기 설정이 완료되면 포털을 통해 클러스터를 만들 수 있습니다. 클러스터가 스토리지 계정과 동일한 Azure 영역에 있어야 합니다. 클러스터 만들기 메뉴의 **스토리지** 섹션에서 다음 옵션을 선택합니다.

    * **기본 저장소 형식**으로 **Azure Data Lake Storage Gen2**를 선택 합니다.
    * **저장소 계정 선택**에서 새로 만든 Data Lake Storage Gen2 Storage 계정을 검색 하 여 선택 합니다.

        ![Azure HDInsight에서 Data Lake Storage Gen2를 사용하기 위한 스토리지 설정](./media/hdinsight-hadoop-use-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)

    * **Id**에서 올바른 구독과 새로 만든 사용자 할당 관리 id를 선택 합니다.

        ![HDInsight에서 Data Lake Storage Gen2 사용을 위한 id 설정](./media/hdinsight-hadoop-use-data-lake-storage-gen2/managed-identity-cluster-creation.png)

> [!NOTE]
> * 보조 Data Lake Storage Gen2 계정을 추가 하려면 저장소 계정 수준에서 앞서 만든 관리 되는 id를 추가 하려는 새 Data Lake Storage Gen2 저장소 계정에 할당 하면 됩니다. HDInsight에서 "추가 저장소 계정" 블레이드를 통해 보조 Data Lake Storage Gen2 계정을 추가 하는 것은 지원 되지 않습니다.
> * HDInsight에서 사용 하는 Azure storage 계정에서 RA-GRS 또는 ZRS를 사용 하도록 설정할 수 있습니다. 그러나 RA-GRS 또는 RA-ZRS 보조 끝점에 대 한 클러스터를 만드는 것은 지원 되지 않습니다.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Azure CLI를 통해 Data Lake Storage Gen2를 사용 하 여 클러스터 만들기

[샘플 템플릿 파일을 다운로드](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) 하 고 [샘플 매개 변수 파일을 다운로드할](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)수 있습니다. 템플릿 및 Azure CLI 코드 조각을 사용 하기 전에 다음 자리 표시자를 올바른 값으로 바꿉니다.

| 자리 표시자 | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 구독의 ID입니다. |
| `<RESOURCEGROUPNAME>` | 새 클러스터 및 저장소 계정을 만들 리소스 그룹입니다. |
| `<MANAGEDIDENTITYNAME>` | Azure Data Lake Storage Gen2 계정에 대 한 권한을 부여 받을 관리 id의 이름입니다. |
| `<STORAGEACCOUNTNAME>` | 생성 될 새 Azure Data Lake Storage Gen2 계정입니다. |
| `<CLUSTERNAME>` | HDInsight 클러스터의 이름입니다. |
| `<PASSWORD>` | SSH 및 Ambari 대시보드를 사용 하 여 클러스터에 로그인 하는 데 사용할 암호를 선택 합니다. |

아래 코드 조각은 다음과 같은 초기 단계를 수행 합니다.

1. Azure 계정에 로그인 합니다.
1. 만들기 작업을 수행할 활성 구독을 설정 합니다.
1. 새 배포 작업에 대 한 새 리소스 그룹을 만듭니다.
1. 사용자 할당 관리 id를 만듭니다.
1. Azure CLI에 확장을 추가 하 여 Data Lake Storage Gen2 기능을 사용 합니다.
1. `--hierarchical-namespace true` 플래그를 사용 하 여 새 Data Lake Storage Gen2 계정을 만듭니다.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

그런 다음 포털에 로그인 합니다. [Azure Portal 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md)의 3 단계에서 설명한 대로 저장소 계정의 **저장소 Blob 데이터 참가자** 역할에 새 사용자 할당 관리 id를 추가 합니다.

사용자 할당 관리 id의 역할을 할당 한 후에는 다음 코드 조각을 사용 하 여 템플릿을 배포 합니다.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Azure PowerShell를 통해 Data Lake Storage Gen2를 사용 하 여 클러스터 만들기

Azure Data Lake Storage Gen2를 사용 하 여 HDInsight 클러스터를 만드는 데 PowerShell을 사용 하는 것은 현재 지원 되지 않습니다.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight의 Data Lake Storage Gen2에 대 한 액세스 제어

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2는 어떤 종류의 권한을 지원하나요?

Data Lake Storage Gen2에서는 RBAC (역할 기반 액세스 제어) 및 POSIX와 같은 액세스 제어 목록 (Acl)을 모두 지 원하는 액세스 제어 모델을 사용 합니다. Data Lake Storage Gen1는 데이터에 대 한 액세스를 제어 하기 위한 액세스 제어 목록만 지원 합니다.

RBAC는 역할 할당을 사용 하 여 Azure 리소스에 대 한 사용자, 그룹 및 서비스 사용자에 게 사용 권한 집합을 효과적으로 적용 합니다. 일반적으로 이러한 Azure 리소스는 최상위 리소스 (예: Azure storage 계정)로 제한 됩니다. Azure Storage 및 Data Lake Storage Gen2에 대해서도이 메커니즘은 파일 시스템 리소스로 확장 되었습니다.

 RBAC를 사용 하는 파일 사용 권한에 대 한 자세한 내용은 [AZURE rbac (역할 기반 액세스 제어)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)를 참조 하세요.

Acl을 사용 하는 파일 사용 권한에 대 한 자세한 내용은 [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조 하십시오.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake Storage Gen2 내 데이터에 대 한 액세스를 제어 어떻게 할까요?

HDInsight 클러스터의 Data Lake Storage Gen2 파일에 액세스 하는 기능은 관리 되는 id를 통해 제어 됩니다. 관리 id는 Azure AD (Azure Active Directory)에 등록 된 id로, Azure에서 자격 증명을 관리 합니다. 관리 id를 사용 하면 Azure AD에서 서비스 주체를 등록 하거나 인증서와 같은 자격 증명을 유지 관리할 필요가 없습니다.

Azure 서비스에는 두 가지 유형의 관리 id 인 시스템 할당 및 사용자 할당이 있습니다. HDInsight는 사용자 할당 관리 id를 사용 하 여 Data Lake Storage Gen2에 액세스 합니다. 사용자 할당 관리 ID는 독립 실행형 Azure 리소스로 생성됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다.

사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 id가 작동 하는 방식](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)을 참조 하세요.

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Hive 또는 다른 서비스를 사용 하 여 Data Lake Storage Gen2에서 데이터를 쿼리할 수 있도록 Azure AD 사용자에 대 한 권한을 설정 어떻게 할까요??

사용자가 데이터를 쿼리할 수 있는 권한을 설정 하려면 Azure AD 보안 그룹을 Acl에서 할당 된 주체로 사용 합니다. 개별 사용자 또는 서비스 사용자에 게 파일 액세스 권한을 직접 할당 하지 않습니다. Azure AD 보안 그룹을 사용 하 여 권한 흐름을 제어 하는 경우 Acl을 전체 디렉터리 구조에 다시 적용 하지 않고 사용자 또는 서비스 사용자를 추가 하 고 제거할 수 있습니다. 적절한 Azure AD 보안 그룹에서 사용자를 추가 또는 제거하기만 하면 됩니다. Acl은 상속 되지 않으므로 Acl을 다시 적용 하려면 모든 파일 및 하위 디렉터리에서 ACL을 업데이트 해야 합니다.

## <a name="access-files-from-the-cluster"></a>클러스터에서 파일 액세스

여러 가지 방법으로 HDInsight 클러스터에서 Data Lake Storage Gen2 파일에 액세스할 수 있습니다.

* **정규화된 이름 사용**. 이 방법의 경우 액세스할 파일에 대한 전체 경로를 제공합니다.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **줄인 경로 형식 사용**. 이 방법을 사용 하 여 클러스터 루트로 경로를 다음과 같이 바꿉니다.

    ```
    abfs:///<file.path>/
    ```

* **상대 경로 사용**. 이 방법의 경우 액세스할 파일에 대한 상대 경로만 제공합니다.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>데이터 액세스 예제

예는 클러스터의 헤드 노드에 대 한 [ssh 연결](./hdinsight-hadoop-linux-use-ssh-unix.md) 을 기반으로 합니다. 이 예제에서는 세 가지 URI 스키마를 모두 사용 합니다. `CONTAINERNAME` 및 `STORAGEACCOUNT`를 관련 값으로 바꿉니다.

#### <a name="a-few-hdfs-commands"></a>몇 개의 hdfs 명령

1. 로컬 저장소에서 간단한 파일을 만듭니다.

    ```bash
    touch testFile.txt
    ```

1. 클러스터 저장소에 디렉터리를 만듭니다.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 로컬 저장소에서 클러스터 저장소로 데이터를 복사 합니다.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. 클러스터 저장소에 디렉터리 콘텐츠를 나열 합니다.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive 테이블 만들기

설명을 위해 세 가지 파일 위치가 표시 됩니다. 실제 실행의 경우 `LOCATION` 항목 중 하나만 사용 합니다.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>다음 단계

* [Data Lake Storage Gen2 미리 보기와 Azure HDInsight 통합 - ACL 및 보안 업데이트](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [자습서: Azure HDInsight에서 대화형 쿼리를 사용 하 여 데이터 추출, 변환 및 로드](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
