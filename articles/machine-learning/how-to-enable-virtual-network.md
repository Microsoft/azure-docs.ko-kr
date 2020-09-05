---
title: 네트워크 격리 및 프라이버시
titleSuffix: Azure Machine Learning
description: 격리된 Azure Virtual Network를 사용하여 Azure Machine Learning에서 실험/학습은 물론 유추/채점 작업을 보호합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: 343063c74912b234268757c36384ea876c068c21
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146824"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>개인 가상 네트워크를 사용 하 여 &를 학습 하는 동안 네트워크 격리
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Virtual Network (vnet) 내에서 Azure Machine Learning 교육 및 유추 작업을 격리 하 여 machine learning 수명 주기를 보호 하는 방법을 알아봅니다. Azure Machine Learning은 계산 [대상이](concept-compute-target.md)라고도 하는 계산 리소스에 대 한 다른 Azure 서비스를 사용 하 여 모델을 학습 하 고 배포 합니다. 대상은 가상 네트워크 내에서 만들 수 있습니다. 예를 들어 Azure Machine Learning 컴퓨팅을 사용하여 모델을 교육한 다음, 이 모델을 AKS(Azure Kubernetes Service)에 배포할 수 있습니다. 

__가상 네트워크__는 공용 인터넷에서 Azure 리소스를 격리하는 보안 경계의 역할을 합니다. Azure 가상 네트워크를 온-프레미스 네트워크에 연결할 수도 있습니다. 네트워크에 조인하면 모델을 안전하게 교육하고, 배포한 모델에 액세스하여 유추할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ Azure Machine Learning [작업 영역](how-to-manage-workspace.md)

+ [Azure Virtual Network 서비스](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 및 [IP 네트워킹](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)에 대한 일반적인 실무 지식

+ 컴퓨팅 리소스에 사용할 기존 가상 네트워크 및 서브넷

+ 가상 네트워크 또는 서브넷에 리소스를 배포 하려면 사용자 계정에 Azure 역할 기반 액세스 제어 (RBAC)에서 다음 작업을 수행할 수 있는 권한이 있어야 합니다.

    - 가상 네트워크 리소스에 대 한 "Microsoft. Network/virtualNetworks/join/action".
    - 서브넷 리소스에 대 한 "Microsoft. Network/virtualNetworks/subnet/join/action".

    네트워크를 사용 하는 RBAC에 대 한 자세한 내용은 [네트워킹 기본 제공 역할](/azure/role-based-access-control/built-in-roles#networking) 을 참조 하세요.

## <a name="private-endpoints"></a>프라이빗 엔드포인트

[Azure Private Link를 활성화](how-to-configure-private-link.md)하고 프라이빗 엔드포인트를 사용하여 작업 영역에 연결할 수도 있습니다. 프라이빗 엔드포인트는 가상 네트워크 내에 있는 일련의 개인 IP 주소입니다. [프라이빗 엔드포인트를 설정하는 방법을 알아보세요.](how-to-configure-private-link.md)



> [!TIP]
> 가상 네트워크와 Private Link를 함께 결합하여 작업 영역과 다른 Azure 리소스 간의 통신을 보호할 수 있습니다. 단, 일부 조합에는 Enterprise Edition 작업 영역이 필요합니다. Enterprise Edition이 필요한 시나리오를 이해하려면 다음 표를 참조하세요.
>
> | 시나리오 | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | 가상 네트워크 또는 Private Link 없음 | ✔ | ✔ |
> | Private Link가 없는 작업 영역. 가상 네트워크의 기타 리소스(Azure Container Registry 제외) | ✔ | ✔ |
> | Private Link가 없는 작업 영역. Private Link가 있는 기타 리소스 | ✔ | |
> | Private Link가 있는 작업 영역. 가상 네트워크의 기타 리소스(Azure Container Registry 제외) | ✔ | ✔ |
> | Private Link가 있는 기타 리소스 및 작업 영역 | ✔ | |
> | Private Link가 있는 작업 영역. 가상 네트워크 또는 Private Link가 없는 기타 리소스 | ✔ | ✔ |
> | 가상 네트워크의 Azure Container Registry | ✔ | |
> | 작업 영역에 대한 고객 관리 키 | ✔ | |
> 

> [!WARNING]
> 
> Azure Machine Learning 컴퓨팅 인스턴스 미리 보기는 Private Link가 활성화된 작업 영역에서 지원되지 않습니다.

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning studio

데이터가 가상 네트워크에 저장 된 경우 작업 영역 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 studio에서 데이터에 액세스 권한을 부여 해야 합니다.

> [!IMPORTANT]
> 대부분의 스튜디오는 가상 네트워크에 저장 된 데이터와 함께 작동 하지만 통합 된 노트북은 __그렇지 않습니다__. 통합 된 노트북은 가상 네트워크에 있는 저장소 사용을 지원 하지 않습니다. 대신, 계산 인스턴스에서 Jupyter 노트북을 사용할 수 있습니다. 자세한 내용은 [Compute Instance 노트북의 데이터 액세스](#access-data-in-a-compute-instance-notebook) 섹션을 참조 하세요.

Studio 액세스 권한을 부여 하지 못한 경우이 오류가 표시 되 `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` 고 다음 작업을 사용 하지 않도록 설정 합니다.

* Studio에서 데이터를 미리 봅니다.
* 디자이너에서 데이터를 시각화 합니다.
* AutoML 실험을 제출 합니다.
* 레이블 지정 프로젝트를 시작 합니다.

스튜디오는 가상 네트워크의 다음 데이터 저장소 형식에서 데이터를 읽을 수 있도록 지원 합니다.

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="add-resources-to-the-virtual-network"></a>가상 네트워크에 리소스 추가 

동일한 가상 네트워크에 작업 영역 및 저장소 계정을 추가 하 여 서로 액세스할 수 있도록 합니다.

1. 작업 영역을 가상 네트워크에 연결 하려면 [Azure 개인 링크를 사용 하도록 설정](how-to-configure-private-link.md)합니다. 이 기능은 현재 미리 보기 상태 이며 미국 동부 및 미국 서 부 2 지역에서 사용할 수 있습니다.

1. 저장소 계정을 가상 네트워크에 연결 하려면 [방화벽 및 가상 네트워크 설정을 구성](#use-a-storage-account-for-your-workspace)합니다.

### <a name="configure-a-datastore-to-use-managed-identity"></a>관리 id를 사용 하도록 데이터 저장소 구성

작업 영역 및 저장소 서비스 계정을 가상 네트워크에 추가한 후에는 관리 id를 사용 하 여 데이터에 액세스 하도록 데이터 저장소를 구성 해야 합니다. 이러한 단계는 Azure 리소스 기반 액세스 제어 (RBAC)를 사용 하 여 저장소 서비스에 작업 영역 관리 id를 __판독기__ 로 추가 합니다. __읽기 권한자__ 액세스를 통해 작업 영역에서 방화벽 설정을 검색 하 고 데이터가 가상 네트워크를 떠나지 않도록 할 수 있습니다.

1. 스튜디오에서 __Datastores__를 선택 합니다.

1. 새 데이터 저장소를 만들려면 __+ 새 데이터 저장소__를 선택 합니다. 기존 항목을 업데이트 하려면 데이터 저장소를 선택 하 고 __자격 증명 업데이트__를 선택 합니다.

1. 데이터 저장소 설정에서 __작업 영역 관리 id를 사용 하 여 저장소에 액세스할 Azure Machine Learning 서비스 허용__에 대해 __예__ 를 선택 합니다.

> [!NOTE]
> 이러한 변경 내용을 적용 하는 데 최대 10 분이 걸릴 수 있습니다.

### <a name="azure-blob-storage-blob-data-reader"></a>Azure Blob storage Blob 데이터 판독기

__Azure blob storage__의 경우 blob storage에서 데이터를 읽을 수 있도록 작업 영역 관리 Id도 [blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 로 추가 됩니다.


### <a name="azure-machine-learning-designer-default-datastore"></a>Azure Machine Learning designer 기본 데이터 저장소

디자이너는 작업 영역에 연결 된 저장소 계정을 사용 하 여 기본적으로 출력을 저장 합니다. 그러나 사용자가 액세스할 수 있는 모든 데이터 저장소에 출력을 저장 하도록 지정할 수 있습니다. 환경에서 가상 네트워크를 사용 하는 경우 이러한 컨트롤을 사용 하 여 데이터가 안전 하 게 유지 되 고 액세스 가능한 지 확인할 수 있습니다.

파이프라인에 대 한 새 기본 저장소를 설정 하려면 다음을 수행 합니다.

1. 파이프라인 초안에서 파이프라인 제목 근처의 **설정 기어 아이콘** 을 선택 합니다.
1. **기본 데이터 저장소 선택**을 선택 합니다.
1. 새 데이터 저장소를 지정 합니다.

모듈 별로 기본 데이터 저장소를 재정의할 수도 있습니다. 이를 통해 각 개별 모듈의 저장소 위치를 제어할 수 있습니다.

1. 출력을 지정 하려는 모듈을 선택 합니다.
1. **출력 설정** 섹션을 확장 합니다.
1. **기본 출력 설정 재정의**를 선택 합니다.
1. **출력 설정 설정**을 선택 합니다.
1. 새 데이터 저장소를 지정 합니다.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 access control

RBAC 및 POSIX 스타일의 Acl (액세스 제어 목록)을 모두 사용 하 여 가상 네트워크 내에서 데이터 액세스를 제어할 수 있습니다.

RBAC를 사용 하려면 [Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할에 작업 영역 관리 id를 추가 합니다. 자세한 내용은 [역할 기반 액세스 제어](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)를 참조하세요.

Acl을 사용 하려면 다른 보안 원칙과 마찬가지로 작업 영역 관리 id에 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조 하세요.


### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 access control

Azure Data Lake Storage Gen1는 POSIX 스타일의 액세스 제어 목록만 지원 합니다. 다른 보안 주체와 마찬가지로 리소스에 작업 영역 관리 id 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md)을 참조 하세요.


### <a name="azure-sql-database-contained-user"></a>포함 된 사용자 Azure SQL Database

관리 id를 사용 하 여 Azure SQL Database에 저장 된 데이터에 액세스 하려면 관리 되는 id에 매핑되는 SQL 포함 된 사용자를 만들어야 합니다. 외부 공급자에서 사용자를 만드는 방법에 대 한 자세한 내용은 [AZURE AD id에 매핑된 포함 된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조 하세요.

SQL 포함 된 사용자를 만든 후에는 [Grant t-sql 명령을](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)사용 하 여 해당 사용자에 게 사용 권한을 부여 합니다.

### <a name="connect-to-the-studio"></a>스튜디오에 연결

가상 네트워크 내의 리소스 (예: 계산 인스턴스 또는 가상 머신)에서 studio에 액세스 하는 경우 가상 네트워크에서 스튜디오로의 아웃 바운드 트래픽을 허용 해야 합니다. 

예를 들어 NSG (네트워크 보안 그룹)를 사용 하 여 아웃 바운드 트래픽을 제한 하는 경우 __AzureFrontDoor__의 __서비스 태그__ 대상에 규칙을 추가 합니다.

## <a name="use-a-storage-account-for-your-workspace"></a>작업 영역에 대한 스토리지 계정 사용

> [!IMPORTANT]
> Azure Machine Learning의 _기본 스토리지 계정_이나 기본이 아닌 스토리지 계정 모두를 가상 네트워크에 배치할 수 있습니다.
>
> 기본 스토리지 계정은 작업 영역을 만들 때 자동으로 프로비저닝됩니다.
>
> 기본이 아닌 스토리지 계정의 경우 [`Workspace.create()`함수](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)의 `storage_account` 매개 변수를 사용하여 Azure 리소스 ID로 사용자 지정 스토리지 계정을 지정할 수 있습니다.

가상 네트워크의 작업 영역에 Azure storage 서비스를 사용 하려면 다음 단계를 사용 합니다.

1. 가상 네트워크 뒤에 컴퓨팅 리소스(예: Machine Learning 컴퓨팅 인스턴스 또는 클러스터)를 만들거나 컴퓨팅 리소스를 작업 영역(예: HDInsight 클러스터, 가상 머신 또는 Azure Kubernetes 서비스 클러스터)에 연결합니다. 컴퓨팅 리소스는 실험 또는 모델 배포용으로 사용할 수 있습니다.

   자세한 내용은 이 문서에서 [Machine Learning 컴퓨팅 사용](#amlcompute), [가상 머신 또는 HDInsight 클러스터 사용](#vmorhdi), [Azure Kubernetes 서비스 사용](#aksvnet) 섹션을 참조하세요.

1. Azure Portal에서 작업 영역에서 사용 하려는 저장소 서비스로 이동 합니다.

   [![Azure Machine Learning 작업 영역에 연결된 스토리지](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Storage 서비스 계정 페이지에서 __방화벽 및 가상 네트워크__를 선택 합니다.

   ![Azure Portal에 있는 Azure Storage 페이지의 "방화벽 및 가상 네트워크" 영역](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __방화벽 및 가상 네트워크__ 페이지에서 다음 작업을 수행합니다.
    - __선택한 네트워크__를 선택합니다.
    - __가상 네트워크__에서 __기존 가상 네트워크 추가__ 링크를 선택합니다. 이 작업을 수행 하면 계산이 있는 가상 네트워크가 추가 됩니다 (1 단계 참조).

        > [!IMPORTANT]
        > 스토리지 계정은 학습 또는 유추에 사용되는 컴퓨팅 인스턴스 또는 클러스터와 동일한 가상 네트워크 및 서브넷에 있어야 합니다.

    - __신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다__ 확인란을 선택합니다.

    > [!IMPORTANT]
    > Azure Machine Learning SDK를 사용하는 경우 개발 환경에서 Azure Storage 계정에 연결할 수 있어야 합니다. 스토리지 계정이 가상 네트워크 내에 있는 경우 방화벽에서 개발 환경 IP 주소의 액세스를 반드시 허용해야 합니다.
    >
    > 스토리지 계정에 액세스가 가능하도록 설정하려면 개발 클라이언트의 웹 브라우저에서 스토리지 계정에 대한 __방화벽 및 가상 네트워크__를 방문하세요. 그런 다음, __클라이언트 IP 주소 추가__ 확인란을 사용하여 클라이언트의 IP 주소를 __주소 범위__에 추가합니다. __주소 범위__ 필드를 사용하여 개발 환경의 IP 주소를 수동으로 입력할 수도 있습니다. 클라이언트의 IP 주소가 추가되고 나면 SDK를 사용하여 스토리지 계정에 액세스할 수 있습니다.

   [![Azure Portal의 "방화벽 및 가상 네트워크" 창](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>데이터 저장소 및 데이터 집합 사용

이 섹션에서는 SDK 환경의 데이터 저장소 및 데이터 집합 사용에 대해 설명 합니다. 스튜디오 환경에 대 한 자세한 내용은 [Machine Learning studio](#machine-learning-studio)섹션을 참조 하세요.

기본적으로 Azure Machine Learning는 SDK를 사용 하 여 데이터에 액세스 하려고 할 때 데이터 유효성 및 자격 증명 확인을 수행 합니다. 데이터가 가상 네트워크 뒤에 있는 경우 데이터에 액세스할 수 없고 해당 검사에 실패 하는 Azure Machine Learning. 이를 방지 하려면 유효성 검사를 건너뛰는 데이터 저장소 및 데이터 집합을 만들어야 합니다.

### <a name="use-a-datastore"></a>데이터 저장소 사용

 Azure Data Lake Store Gen1 및 Azure Data Lake Store Gen2는 기본적으로 유효성 검사를 건너뛰고 추가 작업이 필요 하지 않습니다. 그러나 다음 서비스의 경우 유사한 구문을 사용 하 여 데이터 저장소 유효성 검사를 건너뛸 수 있습니다.

- Azure Blob Storage
- Azure 파일 공유
- PostgreSQL
- Azure SQL Database

다음 코드 샘플은 새 Azure Blob 데이터 저장소 및 집합을 만듭니다 `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-a-dataset"></a>데이터 세트 사용

데이터 집합 유효성 검사를 건너뛰는 구문은 다음과 같은 데이터 집합 형식에 대해 유사 합니다.
- 구분 된 파일
- JSON 
- Parquet
- SQL
- 파일

다음 코드는 새 JSON 데이터 집합을 만들고를 설정 `validate=False` 합니다.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>컴퓨팅 클러스터 및 인스턴스 

가상 네트워크에서 [관리형 Azure Machine Learning __컴퓨팅 대상__](concept-compute-target.md#azure-machine-learning-compute-managed) 또는 [Azure Machine Learning 컴퓨팅 __인스턴스__](concept-compute-instance.md) 중에 하나를 사용하려면 다음 네트워크 요구 사항을 충족해야 합니다.

> [!div class="checklist"]
> * 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
> * 컴퓨팅 인스턴스 또는 클러스터에 지정된 서브넷에는 할당되지 않은 IP 주소가 대상 VM 수를 수용할 만큼 충분히 있어야 합니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 컴퓨팅 클러스터가 부분적으로 할당됩니다.
> * 가상 네트워크의 구독 또는 리소스 그룹에 대한 보안 정책이나 잠금이 가상 네트워크를 관리하는 사용자 권한을 제한하는지 확인합니다. 트래픽을 제한하여 가상 네트워크를 보호하려는 경우 컴퓨팅 서비스를 위해 일부 포트를 열어둡니다. 자세한 내용은 [필수 포트](#mlcports) 섹션을 참조하세요.
> * 하나의 가상 네트워크에 다수의 컴퓨팅 인스턴스나 클러스터를 배치하려는 경우 하나 이상의 리소스에 대해 할당량 증가를 요청해야 할 수 있습니다.
> * 작업 영역의 Azure Storage 계정도 가상 네트워크에서 보호되는 경우, Azure Machine Learning 컴퓨팅 인스턴스나 클러스터와 동일한 가상 네트워크에 있어야 합니다. 
> * 컴퓨팅 인스턴스 Jupyter 기능이 작동하려면 웹 소켓 통신이 비활성화되어 있지 않아야 합니다.

> [!TIP]
> Machine Learning 컴퓨팅 인스턴스나 클러스터는 __가상 네트워크를 포함하는 리소스 그룹__에 추가 네트워킹 리소스를 자동으로 할당합니다. 각 컴퓨팅 인스턴스 또는 클러스터에 대해 서비스는 다음 리소스를 할당합니다.
> 
> * 네트워크 보안 그룹 1개
> * 공용 IP 주소 1개
> * 부하 분산 장치 1개
> 
> 클러스터의 경우 이러한 리소스는 클러스터가 0개 노드로 축소될 때마다 삭제(재생성)되지만, 인스턴스의 경우 인스턴스가 완전히 삭제될 때까지 리소스가 유지됩니다(중지하면 리소스가 제거되지 않음). 
> 이러한 리소스는 구독의 [리소스 할당량](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)으로 제한됩니다.


### <a name="required-ports"></a><a id="mlcports"></a> 필수 포트

공용 인터넷으로/에서 네트워크 트래픽을 제한 하 여 가상 네트워크를 보호 하려는 경우 Azure Batch 서비스에서 인바운드 통신을 허용 해야 합니다.

Batch 서비스는 VM에 연결된 NIC(네트워크 인터페이스) 수준에서 NSG(네트워크 보안 그룹)를 추가합니다. 이러한 NSG는 다음 트래픽을 허용하도록 인바운드 및 아웃바운드 규칙을 자동으로 구성합니다.

- __BatchNodeManagement__의 __서비스 태그__ 포트 29876 및 29877의 인바운드 TCP 트래픽

    ![BatchNodeManagement 서비스 태그를 사용하는 인바운드 규칙](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (선택 사항) 원격 액세스를 허용하는 포트 22의 인바운드 TCP 트래픽 이 포트는 공용 IP에서 SSH를 사용하여 연결하려는 경우에만 사용합니다.

- 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽

- 인터넷에 대한 모든 포트의 아웃바운드 트래픽

- __AzureMachineLearning__의 __서비스 태그__ 포트 44224의 컴퓨팅 인스턴스 인바운드 TCP 트래픽

> [!IMPORTANT]
> Batch 구성 NSG에서 인바운드 또는 아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. NSG가 컴퓨팅 노드에 대한 통신을 차단하는 경우 컴퓨팅 서비스는 컴퓨팅 노드의 상태를 사용 불가로 설정합니다.
>
> Azure Batch 서비스는 자체 NSG를 구성하기 때문에 서브넷 수준에서 NSG를 지정할 필요는 없습니다. 그러나 Azure Machine Learning 계산을 포함 하는 서브넷에 연결 된 NSGs 또는 방화벽이 있는 경우 앞에 나열 된 트래픽만 허용 해야 합니다.

Azure Portal의 NSG 규칙 구성은 다음 이미지에 나와 있습니다.

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Machine Learning 컴퓨팅에 대한 인바운드 NSG 규칙" border="true":::



![Machine Learning 컴퓨팅에 대한 아웃바운드 NSG 규칙](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> 가상 네트워크에서 아웃바운드 연결 제한

기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 다음 단계를 사용합니다.

- NSG 규칙을 사용하여 아웃바운드 인터넷 연결을 거부합니다.

- __컴퓨팅 인스턴스__ 또는 __컴퓨팅 클러스터__의 경우 다음 항목에 대한 아웃바운드 트래픽을 제한합니다.
   - Azure Storage, __Storage.RegionName__의 __서비스 태그__를 사용합니다. 여기서 `{RegionName}`은 Azure 지역의 이름입니다.
   - Azure Container Registry, __AzureContainerRegistry.RegionName__의 __서비스 태그__를 사용합니다. 여기서 `{RegionName}`은 Azure 지역의 이름입니다.
   - Azure Machine Learning, __AzureMachineLearning__의 __서비스 태그__를 사용합니다.
   - Azure Resource Manager, __AzureResourceManager__의 __서비스 태그__를 사용합니다.
   - Azure Active Directory, __AzureActiveDirectory__의 __서비스 태그__를 사용합니다.

Azure Portal의 NSG 규칙 구성은 다음 이미지에 나와 있습니다.

[![Machine Learning 컴퓨팅에 대한 아웃바운드 NSG 규칙](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Microsoft에서 제공 하는 기본 Docker 이미지를 사용 하 고 사용자 관리 종속성을 사용 하도록 계획 하는 경우 다음 __서비스 태그도__사용 해야 합니다.
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> 이 구성은 학습 스크립트의 일부에 다음 코드 조각과 비슷한 코드가 있는 경우 필요합니다.
>
> __RunConfig 교육__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Estimator 교육__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>강제 터널링

Azure Machine Learning compute를 사용 하 여 [강제 터널링](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) 을 사용 하는 경우 계산 리소스가 포함 된 서브넷에서 공용 인터넷과의 통신을 허용 해야 합니다. 이 통신은 작업 예약 및 Azure Storage 액세스에 사용 됩니다.

다음 두 가지 방법으로이 작업을 수행할 수 있습니다.

* [VIRTUAL NETWORK NAT](../virtual-network/nat-overview.md)를 사용 합니다. NAT 게이트웨이는 가상 네트워크에 있는 하나 이상의 서브넷에 대해 아웃 바운드 인터넷 연결을 제공 합니다. 자세한 내용은 [NAT 게이트웨이 리소스를 사용 하 여 가상 네트워크 디자인](../virtual-network/nat-gateway-resource.md)을 참조 하세요.

* 계산 리소스를 포함 하는 서브넷에 [UDRs (사용자 정의 경로)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) 를 추가 합니다. 리소스가 있는 지역에서 Azure Batch 서비스에 사용되는 각 IP 주소에 대한 UDR을 설정합니다. 이러한 UDR을 사용하면 Batch 서비스가 작업 예약을 위해 컴퓨팅 노드와 통신할 수 있습니다. 리소스가 있는 Azure Machine Learning Service의 IP 주소도 추가합니다. 컴퓨팅 인스턴스에 액세스하는 데 필요하기 때문입니다. Batch 서비스 및 Azure Machine Learning Service의 IP 주소 목록을 가져오려면 다음 방법 중 하나를 사용합니다.

    * [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드하고 파일에서 `BatchNodeManagement.<region>` 및 `AzureMachineLearning.<region>`을 검색합니다. 여기서 `<region>`은 Azure 지역입니다.

    * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용하여 정보를 다운로드합니다. 다음 예는 IP 주소 정보를 다운로드하고 미국 동부 2 지역에 대한 정보를 필터링합니다.

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > 미국 버지니아, 미국-애리조나 지역 또는 중국 동부-2 지역을 사용 하는 경우 이러한 명령은 IP 주소를 반환 하지 않습니다. 대신, 다음 링크 중 하나를 사용 하 여 IP 주소 목록을 다운로드 합니다.
        >
        > * [Azure Government에 대 한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure 중국의 azure IP 범위 및 서비스 태그](https://www.microsoft.com//download/details.aspx?id=57062)
    
    UDR을 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 __다음 홉 유형__을 __인터넷__으로 설정합니다. 다음 이미지는 Azure Portal에서 UDR의 예를 보여 줍니다.

    ![주소 접두사에 대한 UDR 예](./media/how-to-enable-virtual-network/user-defined-route.png)

    사용자가 정의 하는 UDRs 외에도 온-프레미스 네트워크 어플라이언스를 통해 Azure Storage에 대 한 아웃 바운드 트래픽을 허용 해야 합니다. 특히이 트래픽의 Url은 `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` 및 형식 `<account>.blob.core.windows.net` 입니다. 

    자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)를 참조하세요.


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>가상 네트워크에서 컴퓨팅 클러스터 만들기

Machine Learning 컴퓨팅 클러스터를 만들려면 다음 단계를 사용하세요.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인한 다음, 구독과 작업 영역을 선택합니다.

1. 왼쪽에서 __Compute__를 선택합니다.

1. 가운데에서 __학습 클러스터__를 선택한 다음, __+__ 를 선택합니다.

1. __새 학습 클러스터__ 대화 상자에서 __고급 설정__ 섹션을 확장합니다.

1. 이 컴퓨팅 리소스가 가상 네트워크를 사용하도록 구성하려면 __가상 네트워크 구성__ 섹션에서 다음 작업을 수행합니다.

    1. __리소스 그룹__ 드롭다운 목록에서 가상 네트워크가 포함된 리소스 그룹을 선택합니다.
    1. __가상 네트워크__ 드롭다운 목록에서 서브넷이 포함된 가상 네트워크를 선택합니다.
    1. __서브넷__ 드롭다운 목록에서 사용할 서브넷을 선택합니다.

   ![Machine Learning 컴퓨팅에 대한 가상 네트워크 설정](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Azure Machine Learning SDK를 사용하여 Machine Learning 컴퓨팅 클러스터를 만들 수도 있습니다. 다음 코드에서는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 Machine Learning 컴퓨팅 클러스터를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

만들기 프로세스가 완료되면 실험에서 클러스터를 사용하여 모델을 학습시킵니다. 자세한 내용은 [학습에 계산 대상 사용](how-to-set-up-training-targets.md)을 참조 하세요.

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>계산 인스턴스 노트북에서 데이터 액세스

Azure 계산 인스턴스에서 노트북을 사용 하는 경우 노트북이 동일한 가상 네트워크 및 서브넷 뒤에 있는 계산 리소스에서 실행 되 고 있는지 확인 해야 합니다. 

계산 인스턴스는 생성 하는 동안 동일한 가상 네트워크에 있도록 구성 해야 합니다. **고급 설정**  >  **가상 네트워크 구성**합니다. 기존 계산 인스턴스를 가상 네트워크에 추가할 수 없습니다.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

가상 네트워크의 AKS(Azure Kubernetes 서비스)를 작업 영역에 추가하려면 다음 단계를 사용합니다.

> [!IMPORTANT]
> 다음 절차를 시작하기 전에 [AKS(Azure Kubernetes Service)에서 고급 네트워킹 구성](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) 방법의 필수 구성 요소에 따라 클러스터의 IP 주소 지정을 계획하세요.
>
> AKS 인스턴스와 Azure 가상 네트워크는 동일한 지역에 있어야 합니다. 가상 네트워크의 작업 영역에 사용되는 Azure Storage 계정에 보안을 적용하는 경우 AKS 인스턴스와 동일한 가상 네트워크에 있어야 합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인한 다음, 구독과 작업 영역을 선택합니다.

1. 왼쪽에서 __Compute__를 선택합니다.

1. 가운데에서 __유추 클러스터__를 선택한 다음, __+__ 를 선택합니다.

1. __새 유추 클러스터__ 대화 상자에서 __네트워크 구성__ 아래 __고급__을 선택합니다.

1. 컴퓨팅 리소스가 가상 네트워크를 사용하도록 구성하려면 다음 작업을 수행합니다.

    1. __리소스 그룹__ 드롭다운 목록에서 가상 네트워크가 포함된 리소스 그룹을 선택합니다.
    1. __가상 네트워크__ 드롭다운 목록에서 서브넷이 포함된 가상 네트워크를 선택합니다.
    1. __서브넷__ 드롭다운 목록에서 서브넷을 선택합니다.
    1. __Kubernetes 서비스 주소 범위__ 상자에 Kubernetes 서비스 주소 범위를 입력합니다. 이 주소 범위는 CIDR(Classless Inter-Domain Routing) 표기법 IP 범위를 사용하여 클러스터에 사용할 수 있는 IP 주소를 정의합니다. 서브넷 IP 범위(예: 10.0.0.0/16)와 겹치지 않아야 합니다.
    1. __Kubernetes DNS 서비스 IP 주소__ 상자에 Kubernetes DNS 서비스 IP 주소를 입력합니다. 이 IP 주소는 Kubernetes DNS 서비스에 할당됩니다. 이 주소는 Kubernetes 서비스 주소 범위(예: 10.0.0.10)에 속해야 합니다.
    1. __Docker 브리지 주소__ 상자에 Docker 브리지 주소를 입력합니다. 이 IP 주소는 Docker 브리지에 할당됩니다. 서브넷 IP 범위 또는 Kubernetes 서비스 주소 범위(예: 172.17.0.1/16)에 속하지 않아야 합니다.

   ![Azure Machine Learning: Machine Learning 컴퓨팅 가상 네트워크 설정](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 가상 네트워크를 제어하는 NSG 그룹에 채점 엔드포인트에 대한 인바운드 보안 규칙이 활성화되어 있어야 합니다. 그래야 가상 네트워크 외부에서 호출될 수 있습니다.
   > [!IMPORTANT]
   > NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.

   [![인바운드 보안 규칙](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Azure Machine Learning SDK를 사용하여 가상 네트워크에 Azure Kubernetes Service를 추가할 수도 있습니다. 가상 네트워크에 AKS 클러스터가 이미 있는 경우 [AKS에 배포하는 방법](how-to-deploy-and-where.md)의 설명에 따라 작업 영역에 연결할 수 있습니다. 다음 코드는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 AKS 인스턴스를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

만들기 프로세스가 완료되면 가상 네트워크 뒤에서 AKS 클러스터에 유추 또는 모델 채점을 수행할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법](how-to-deploy-and-where.md)을 참조하세요.

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Azure Kubernetes Service에서 개인 IP 사용

기본적으로 공용 IP 주소는 AKS 배포에 할당됩니다. 가상 네트워크 내에서 AKS를 사용하는 경우, 개인 IP 주소를 대신 사용할 수 있습니다. 개인 IP 주소는 가상 네트워크 또는 조인된 네트워크 내에서만 액세스할 수 있습니다.

개인 IP 주소는 AKS가 _내부 부하 분산 장치_를 사용하도록 구성하여 활성화합니다. 

#### <a name="network-contributor-role"></a>네트워크 참가자 역할

> [!IMPORTANT]
> 이전에 만든 가상 네트워크를 제공 하 여 AKS 클러스터를 만들거나 연결 하는 경우 AKS 클러스터에 대 한 SP (서비스 사용자) 또는 관리 _id를 가상_ 네트워크를 포함 하는 리소스 그룹에 부여 해야 합니다. 내부 부하 분산 장치를 개인 IP로 변경 하기 전에이 작업을 수행 해야 합니다.
>
> 네트워크 참가자로 id를 추가 하려면 다음 단계를 사용 합니다.

1. AKS에 대 한 서비스 주체 또는 관리 id ID를 찾으려면 다음 Azure CLI 명령을 사용 합니다. `<aks-cluster-name>`을 클러스터의 이름으로 바꿉니다. `<resource-group-name>`을 _AKS 클러스터를 포함_하는 리소스 그룹의 이름으로 바꿉니다.

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    이 명령이 값을 반환 하는 경우 `msi` 다음 명령을 사용 하 여 관리 되는 id의 보안 주체 ID를 식별 합니다.

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 가상 네트워크를 포함 하는 리소스 그룹의 ID를 찾으려면 다음 명령을 사용 합니다. `<resource-group-name>`을 _가상 네트워크를 포함_하는 리소스 그룹의 이름으로 바꿉니다.

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. 서비스 주체 또는 관리 되는 id를 네트워크 참가자로 추가 하려면 다음 명령을 사용 합니다. 를 `<SP-or-managed-identity>` 서비스 주체 또는 관리 id에 대해 반환 된 id로 바꿉니다. 을 `<resource-group-id>` 가상 네트워크를 포함 하는 리소스 그룹에 대해 반환 된 ID로 바꿉니다.

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
AKS에서 내부 부하 분산 장치를 사용하는 방법에 대한 자세한 내용은 [Azure Kubernetes Service에서 내부 부하 분산 장치 사용](/azure/aks/internal-lb)을 참조하세요.

#### <a name="enable-private-ip"></a>개인 IP 사용

> [!IMPORTANT]
> Azure Kubernetes 서비스 클러스터를 만들 때는 개인 IP를 사용하도록 설정할 수 없습니다. 기존 클러스터에 대한 업데이트로 활성화해야 합니다.

다음 코드 조각은 __새 AKS 클러스터를 만든__ 다음, 개인 IP/내부 부하 분산 장치를 사용하도록 업데이트하는 방법을 보여줍니다.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

명령에 참조된 `body.json` 파일의 내용은 다음 JSON 문서와 유사합니다.

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

__기존 클러스터__ 를 작업 영역에 연결 하는 경우 연결 작업 후에 부하 분산 장치를 구성할 때까지 기다려야 합니다.

클러스터를 연결 하는 방법에 대 한 자세한 내용은 [기존 AKS 클러스터 연결](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)을 참조 하세요.

기존 클러스터를 연결한 후에는 개인 IP를 사용 하도록 클러스터를 업데이트할 수 있습니다.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="use-azure-container-instances-aci"></a>ACI(Azure Container Instances) 사용

Azure Container Instances는 모델을 배포할 때 동적으로 생성됩니다. Azure Machine Learning이 가상 네트워크 내에 ACI를 만들 수 있도록 설정하려면 배포에 사용되는 서브넷에 __서브넷 위임__을 활성화해야 합니다.

> [!WARNING]
> 가상 네트워크에서 Azure Container Instances를 사용 하는 경우 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 리소스 그룹에 있어야 합니다.
>
> 가상 네트워크 내에서 Azure Container Instances를 사용 하는 경우 작업 영역에 대 한 Azure Container Registry (ACR)는 가상 네트워크에도 있을 수 없습니다.

가상 네트워크의 ACI를 작업 영역으로 사용하려면 다음 단계를 사용하세요.

1. 가상 네트워크에서 서브넷 위임을 사용하도록 설정하려면 [서브넷 위임 추가 또는 제거](../virtual-network/manage-subnet-delegation.md) 문서의 정보를 참조하세요. 가상 네트워크를 만들 때 위임을 사용하도록 설정하거나 기존 네트워크에 추가할 수 있습니다.

    > [!IMPORTANT]
    > 위임을 사용하도록 설정하는 경우 `Microsoft.ContainerInstance/containerGroups`을 __서비스에 서브넷 위임__ 값으로 사용합니다.

2. [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)을 사용하여 모델을 배포하고 `vnet_name` 및 `subnet_name` 매개 변수를 사용합니다. 이 매개 변수를 위임을 사용하도록 설정한 가상 네트워크 이름 및 서브넷으로 설정합니다.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall과 함께 Azure Machine Learning을 사용하는 방법에 대한 자세한 내용은 [Azure Firewall 뒤의 Azure Machine Learning 작업 영역 사용](how-to-access-azureml-behind-firewall.md)을 참조하세요.

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> ACR(Azure Container Registry)은 가상 네트워크 내에 배치할 수 있지만 다음과 같은 필수 구성 요소를 충족해야 합니다.
>
> * Azure Machine Learning 작업 영역이 Enterprise edition이어야 합니다. 업그레이드에 대한 자세한 내용은 [Enterprise edition으로 업그레이드](how-to-manage-workspace.md#upgrade)를 참조하세요.
> * Azure Machine Learning 작업 영역 지역이 [privated 링크 사용 지역](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)이어야 합니다. 
> * Azure Container Registry가 프리미엄 버전이어야 합니다. 업그레이드에 대한 자세한 내용은 [SKU 변경](/azure/container-registry/container-registry-skus#changing-skus)을 참조하세요.
> * Azure Container Registry가 스토리지 계정 및 학습 또는 유추에 사용되는 컴퓨팅 대상과 동일한 가상 네트워크 및 서브넷에 있어야 합니다.
> * Azure Machine Learning 작업 영역에 [Azure Machine Learning 컴퓨팅 클러스터](how-to-create-attach-compute-sdk.md#amlcompute)가 포함되어야 합니다.
>
>     ACR이 가상 네트워크 뒤에 있으면 Azure Machine Learning에서 ACR을 사용하여 Docker 이미지를 직접 빌드할 수 없습니다. 대신 컴퓨팅 클러스터가 이미지를 빌드하는 데 사용됩니다.

1. 작업 영역에 대한 Azure Container Registry 이름을 찾으려면 다음 방법 중 하나를 사용합니다.

    __Azure Portal__

    작업 영역의 개요 섹션에 __레지스트리__ 값은 Azure Container Registry에 연결됩니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="작업 영역에 대한 Azure Container Registry" border="true":::

    __Azure CLI__

    [Azure CLI용 Machine Learning 확장을 설치](reference-azure-machine-learning-cli.md)했으면 `az ml workspace show` 명령을 사용하여 작업 영역 정보를 표시할 수 있습니다.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    이 명령은 `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`와 비슷한 값을 반환합니다. 문자열의 마지막 부분은 작업 영역에 대한 Azure Container Registry의 이름입니다.

1. 가상 네트워크에 대한 액세스를 제한하려면 [레지스트리에 대한 네트워크 액세스 구성](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)의 단계를 참조하세요. 가상 네트워크를 추가할 때 Azure Machine Learning 리소스에 대한 가상 네트워크와 서브넷을 선택합니다.

1. Azure Machine Learning Python SDK를 사용하여 Docker 이미지를 빌드하는 컴퓨팅 클러스터를 구성합니다. 다음 코드 조각은 수행하는 방법을 보여줍니다.

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > 스토리지 계정, 컴퓨팅 클러스터 및 Azure Container Registry는 모두 가상 네트워크의 동일한 서브넷에 있어야 합니다.
    
    자세한 내용은 [update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) 메서드 참조를 확인하세요.

1. 다음 Azure Resource Manager 템플릿을 적용해야 합니다. 이 템플릿을 사용하면 작업 영역이 ACR과 통신할 수 있습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>Key Vault 인스턴스 

작업 영역과 연결된 키 자격 증명 모음 인스턴스는 Azure Machine Learning에서 다음 자격 증명을 저장하는 데 사용됩니다.
* 연결된 스토리지 계정 연결 문자열
* Azure Container Repository 인스턴스에 대한 암호
* 데이터 저장소에 대한 연결 문자열

가상 네트워크 뒤 Azure Key Vault에서 Azure Machine Learning 실험 기능을 사용하려면 다음 단계를 사용하세요.

1. 작업 영역과 연결된 키 자격 증명 모음으로 이동합니다.

   [![Azure Machine Learning 작업 영역과 연결된 키 자격 증명 모음](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. __Key Vault__ 페이지의 왼쪽 창에서 __방화벽 및 가상 네트워크__를 선택합니다.

   ![Key Vault 창의 "방화벽 및 가상 네트워크" 섹션](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. __방화벽 및 가상 네트워크__ 페이지에서 다음 작업을 수행합니다.
    - __다음에서 액세스 허용__에서 __선택한 네트워크__를 선택합니다.
    - __가상 네트워크__에서 __기존 가상 네트워크 추가__를 선택하여 실험 컴퓨팅이 상주하는 가상 네트워크를 추가합니다.
    - __신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 바이패스하도록 허용하시겠습니까?__ 에 __예__를 선택합니다.

   [![Key Vault 창의 "방화벽 및 가상 네트워크" 섹션](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

작업 영역이 있는 가상 네트워크에서 Azure Databricks를 사용하려면 다음 요구 사항을 충족해야 합니다.

> [!div class="checklist"]
> * 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
> * 작업 영역의 Azure Storage 계정도 가상 네트워크에서 보호되는 경우, Azure Databricks 클러스터와 동일한 가상 네트워크에 있어야 합니다.
> * Azure Databricks에 사용되는 __databricks-private__ 및 __databricks-public__ 서브넷 외에 가상 네트워크에 대해 생성된 __기본__ 서브넷도 필요합니다.

가상 네트워크에서 Azure Databricks를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network에서 Azure Databricks 배포](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)를 참조하세요.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>가상 머신 또는 HDInsight 클러스터

> [!IMPORTANT]
> Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다.

작업 영역이 있는 가상 네트워크에서 가상 머신 또는 Azure HDInsight 클러스터를 사용하려면 다음 단계를 사용하세요.

1. Azure Portal 또는 Azure CLI를 사용하여 VM 또는 HDInsight 클러스터를 만들고 이 클러스터를 Azure 가상 네트워크에 배치합니다. 자세한 내용은 다음 문서를 참조하세요.
    * [Linux VM용 Azure 가상 네트워크 만들기 및 관리](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure 가상 네트워크를 사용하여 HDInsight 확장](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Azure Machine Learning이 VM 또는 클러스터에서 SSH 포트와 통신할 수 있도록 하려면 네트워크 보안 그룹의 원본 항목을 구성합니다. SSH 포트는 일반적으로 포트 22입니다. 이 원본의 트래픽을 허용하려면 다음 작업을 수행합니다.

    * __원본__ 드롭다운 목록에서 __서비스 태그__를 선택합니다.

    * __원본 서비스 태그__ 드롭다운 목록에서 __AzureMachineLearning__을 선택합니다.

    * __원본 포트 범위__ 드롭다운 목록에서 __*__ 를 선택합니다.

    * __대상__ 드롭다운 목록에서 __임의__를 선택합니다.

    * __대상 포트 범위__ 드롭다운 목록에서 __22__를 선택합니다.

    * __프로토콜__에서 __임의__를 선택합니다.

    * __동작__에서 __허용__을 선택합니다.

   ![가상 네트워크 내의 VM 또는 HDInsight 클러스터에서 실험을 수행하기 위한 인바운드 규칙](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    네트워크 보안 그룹에 대해 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.


    기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 [가상 네트워크에서 아웃바운드 연결 제한](#limiting-outbound-from-vnet) 섹션을 참조하세요.

1. VM 또는 HDInsight 클러스터를 Azure Machine Learning 작업 영역에 연결합니다. 자세한 내용은 [모델 학습에 계산 대상 사용](how-to-set-up-training-targets.md)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

* [모델 학습에 컴퓨팅 대상 사용](how-to-set-up-training-targets.md)
* [프라이빗 엔드포인트 설정](how-to-configure-private-link.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
