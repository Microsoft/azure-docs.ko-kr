---
title: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용
description: Azure HDInsight 클러스터를 사용 하 여 Azure Data Lake 저장소 Gen2를 사용 하는 방법에 알아봅니다.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 6452e73b2429ab89a466b1c3b59cc892eca31205
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766932"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용

Azure Data Lake 저장소 Gen2는 전용 빅 데이터 분석을 Azure Blob storage를 기반으로 클라우드 저장소 서비스. Data Lake 저장소 Gen2 Azure Blob storage 및 Azure Data Lake 저장소 Gen1의 기능을 결합합니다. 결과 파일 시스템 의미 체계와 디렉터리 수준과 파일 수준 보안, 확장성, 저렴 한 비용, 계층화 된 저장소, 고가용성 및 재해 복구 기능와 함께 Azure 데이터 레이크 저장소 Gen1에서 기능을 제공 Azure Blob storage입니다.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 가용성

Data Lake 저장소 Gen2는 기본값 및 추가 저장소 계정으로 거의 모든 Azure HDInsight 클러스터 형식에 대 한 저장소 옵션으로 사용할 수 있습니다. 그러나 HBase, Data Lake 저장소 Gen2 계정은 하나만 있을 수 있습니다.

> [!Note] 
> Data Lake 저장소 Gen2로 선택한 후에 **기본 저장소 유형**, 추가 저장소로 Data Lake 저장소 Gen1 계정을 선택할 수 없습니다.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Azure portal을 통해 Data Lake 저장소 Gen2를 사용 하 여 클러스터 만들기

저장소에 Data Lake 저장소 Gen2를 사용 하는 HDInsight 클러스터를 만들려면 Data Lake 저장소 Gen2 계정을 구성 하려면 다음이 단계를 수행 합니다.

### <a name="create-a-user-managed-identity"></a>관리 되는 사용자 id 만들기

사용자 할당 관리 ID가 아직 없는 경우 지금 만듭니다. [Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)을 참조하세요. Azure HDInsight에서 관리 되는 identities 작업에 대 한 자세한 내용은 참조 하세요. [Azure HDInsight에서 id 관리](hdinsight-managed-identities.md)합니다.

![사용자 할당 관리 ID 만들기](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정 만들기

Azure Data Lake Storage Gen2 스토리지 계정을 만듭니다. 있는지 확인 합니다 **계층 구조 네임 스페이스** 옵션을 사용 합니다. 자세한 내용은 [빠른 시작: Azure Data Lake Storage Gen2 스토리지 계정 만들기](../storage/blobs/data-lake-storage-quickstart-create-account.md)를 참조하세요.

![Azure Portal에서 스토리지 계정을 만드는 과정을 보여 주는 스크린샷](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Data Lake 저장소 Gen2 계정에 관리 되는 id에 대 한 사용 권한 설정

관리 되는 id를 할당 합니다 **저장소 Blob 데이터 소유자** 저장소 계정에는 역할입니다. 자세한 내용은 [RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 관리(미리 보기)](../storage/common/storage-auth-aad-rbac.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 저장소 계정을 선택한 다음 선택 **액세스 제어 (IAM)** 계정에 대 한 액세스 제어 설정을 표시 합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
    
    ![스토리지 액세스 제어 설정을 보여 주는 스크린샷](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. 선택 된 **역할 할당 추가 +** 새 역할을 추가 하려면 단추입니다.
1. 에 **역할 할당 추가** 창에서 합니다 **저장소 Blob 데이터 소유자** 역할입니다. 그런 다음, 관리 ID 및 스토리지 계정이 있는 구독을 선택합니다. 다음으로, 앞에서 만든 사용자 할당 관리 ID를 검색하여 찾습니다. 마지막으로, 관리 서비스 id를 선택 하 고 그 아래에 나열 됩니다 **선택한 멤버**합니다.
    
    ![RBAC 역할을 할당하는 방법을 보여주는 스크린샷](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. **저장**을 선택합니다. 선택한 사용자 할당 id는 이제 선택한 역할 아래에 나열 됩니다.
1. 이 초기 설정이 완료되면 포털을 통해 클러스터를 만들 수 있습니다. 클러스터가 스토리지 계정과 동일한 Azure 영역에 있어야 합니다. 클러스터 만들기 메뉴의 **스토리지** 섹션에서 다음 옵션을 선택합니다.
        
    * 에 대 한 **기본 저장소 유형**를 선택 **Azure Data Lake 저장소 Gen2**합니다.
    * 아래 **저장소 계정을 선택**, 검색 및 새로 만든된 Data Lake 저장소 Gen2 저장소 계정을 선택 합니다.
        
        ![Azure HDInsight에서 Data Lake Storage Gen2를 사용하기 위한 스토리지 설정](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * 아래 **Identity**, 올바른 구독을 선택 하 고 새로 만든 사용자 할당 관리 id입니다.
        
        ![Azure HDInsight에서 Data Lake Storage Gen2를 사용하기 위한 ID 설정](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> 보조 Data Lake 저장소 Gen2 계정, 저장소 계정 수준에 추가 하려면 추가 하려는 새 Data Lake 저장소 Gen2 저장소 계정에 이전에 만든 관리 되는 id를 할당 하면 됩니다. 알아두세요는 "추가 저장소 계정" 블레이드를 통해 보조 Data Lake 저장소 Gen2 계정을 추가 하 여 HDInsight에서 지원 되지 않습니다. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Azure CLI를 통해 Data Lake 저장소 Gen2를 사용 하 여 클러스터 만들기

할 수 있습니다 [샘플 템플릿 파일을 다운로드](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) 하 고 [샘플 매개 변수 파일을 다운로드할](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)합니다. 서식 파일을 사용 하기 전에 문자열을 바꾸기 `<SUBSCRIPTION_ID>` 실제 Azure 구독 ID를 사용 하 여 또한 문자열을 바꿀 `<PASSWORD>` 모두 클러스터에 로그인을 사용 하는 암호 및 SSH 암호를 설정 하려면 선택한 암호를 사용 하 여 합니다.

아래 코드 조각에는 다음 초기 단계를 수행합니다.

1. Azure 계정에 로그인 합니다.
1. 만들기 작업은 수행 되어야 하는 활성 구독을 설정 합니다.
1. 라는 새 배포 작업에 대 한 새 리소스 그룹을 만듭니다 `hdinsight-deployment-rg`합니다.
1. 명명 된 사용자 할당 관리 되는 id를 만드는 `test-hdinsight-msi`합니다.
1. Data Lake 저장소 Gen2에 기능을 사용 하는 Azure cli 확장을 추가 합니다.
1. 라는 새 Data Lake 저장소 Gen2 계정을 만듭니다 `hdinsightadlsgen2`를 사용 하 여는 `--hierarchical-namespace true` 플래그입니다.

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

그런 다음 포털에 로그인 합니다. 새 사용자 할당 관리 되는 id를 추가 합니다 **Storage Blob 데이터 기여자** 아래 3 단계에 설명 된 대로 저장소 계정에서 역할 [Azure portal을 사용 하 여](hdinsight-hadoop-use-data-lake-storage-gen2.md)입니다.

사용자 할당 관리 되는 id에 대 한 역할을 할당 한 후 다음 코드 조각을 사용 하 여 템플릿을 배포 합니다.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight에서 Data Lake 저장소 Gen2 대 한 액세스 제어

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2는 어떤 종류의 권한을 지원하나요?

Data Lake 저장소 Gen2 역할 기반 액세스 제어 (RBAC)와 같은 POSIX 액세스 제어 목록 (Acl)을 지 원하는 액세스 제어 모델을 사용 합니다. Data Lake 저장소 Gen1 지원 액세스 제어 목록 데이터에 대 한 액세스 제어에 대해서만 합니다.

RBAC 역할 할당을 사용 하 여 사용자, 그룹 및 Azure 리소스에 대 한 서비스 주체 사용 권한 집합을 효과적으로 적용. 일반적으로 이러한 Azure 리소스는 최상위 리소스 (예를 들어 Azure storage 계정)로 제한 됩니다. Azure Storage 및 또한 Data Lake 저장소 Gen2,이 메커니즘에는 파일 시스템 리소스를 확장 되었습니다.

 RBAC 사용 하 여 파일 사용 권한에 대 한 자세한 내용은 참조 하세요. [Azure 역할 기반 액세스 제어 (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)합니다.

Acl 사용 하 여 파일 사용 권한에 대 한 자세한 내용은 참조 하세요. [파일 및 디렉터리의 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)합니다.

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake 저장소 Gen2에서 내 데이터에 대 한 액세스를 제어 하는 방법

Data Lake 저장소 Gen2의 파일에 액세스 하 여 HDInsight 클러스터의 기능을 통해 관리 되는 id 제어 됩니다. 관리 되는 id는 Azure Active Directory (Azure AD) 자격 증명은 Azure에서 관리에 등록 id입니다. 관리 되는 id를 사용 하 여 Azure AD에서 서비스 주체를 등록 또는 인증서와 같은 자격 증명을 유지 관리할 필요가 없습니다.

Azure 서비스에서 관리 되는 id의 형식이 두 가지: 시스템 할당 및 사용자 할당 합니다. HDInsight는 Data Lake 저장소 Gen2 액세스 하려면 사용자 할당 관리 되는 id를 사용합니다. 사용자 할당 관리 ID는 독립 실행형 Azure 리소스로 생성됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다.

사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다. 관리 되는 id에 대 한 자세한 내용은 참조 하세요. [Azure 리소스 작업에 대 한 관리 되는 id 하는 방법?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)합니다.

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>설정 하는 방법에 대 한 Azure AD 사용자가 권한을 Data Lake 저장소 Gen2에서 데이터를 쿼리 Hive 또는 기타 서비스를 사용 하 여?

데이터를 쿼리 사용자에 대 한 권한을 설정 하려면 Azure AD 보안 그룹 Acl에 할당 된 보안 주체로 사용 합니다. 개별 사용자 또는 서비스 주체에 액세스 권한을 직접 할당 하지 마십시오. Azure AD 보안 그룹 사용 권한 흐름 제어를 사용 하 여 추가 수 있으며 전체 디렉터리 구조에 Acl을 다시 적용 하지 않고 사용자 또는 서비스 주체를 제거할 수 있습니다. 적절한 Azure AD 보안 그룹에서 사용자를 추가 또는 제거하기만 하면 됩니다. Acl을 다시 적용 하려면 모든 파일 및 하위 디렉터리에 대 한 ACL을 업데이트 해야 하므로 Acl 상속 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Data Lake Storage Gen2 미리 보기와 Azure HDInsight 통합 - ACL 및 보안 업데이트](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
