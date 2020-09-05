---
title: 엔터프라이즈 보안
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning: 인증, 권한 부여, 네트워크 보안, 데이터 암호화 및 모니터링을 안전하게 사용합니다.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/19/2020
ms.openlocfilehash: ed95cf0b98edd8a6775c980876a6092c00e3a68d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918590"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure Machine Learning에 대한 엔터프라이즈 보안

이 문서에서는 Azure Machine Learning에 사용할 수 있는 보안 기능에 대해 알아봅니다.

클라우드 서비스를 사용하는 경우에는 필요한 사용자에게만 액세스를 제한하는 것이 가장 좋습니다. 먼저, 서비스에 사용되는 인증 및 권한 부여 모델을 파악합니다. 클라우드를 통해 온-프레미스 네트워크의 리소스에 안전하게 조인하거나 네트워크 액세스를 제한할 수도 있습니다. 데이터 암호화도 중요하며, 미사용 데이터 및 서비스간에 이동하는 데이터 모두에 중요합니다. 마지막으로 서비스를 모니터링하고 모든 활동에 대한 감사 로그를 생성할 수 있어야 합니다.

> [!NOTE]
> 이 문서의 정보는 Azure Machine Learning Python SDK 버전 1.0.83.1 이상에서 작동합니다.

## <a name="authentication"></a>인증

다단계 인증은 Azure AD(Azure Active Directory)에서 사용하도록 구성된 경우 지원됩니다. 인증 프로세스는 다음과 같습니다.

1. 클라이언트가 Azure AD에 로그인하여 Azure Resource Manager 토큰을 얻습니다.  사용자 및 서비스 주체가 완전히 지원됩니다.
1. 클라이언트가 Azure Resource Manager와 모든 Azure Machine Learning에 토큰을 제공합니다.
1. Machine Learning Service가 사용자 컴퓨팅 대상(예: Machine Learning 컴퓨팅)에 Machine Learning Service 토큰을 제공합니다. 이 토큰은 실행이 완료된 후 사용자 컴퓨팅 대상이 Machine Learning Service로 콜백되는 데 사용됩니다. 범위는 작업 영역으로 제한됩니다.

[![Azure Machine Learning 인증](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

자세한 내용은 [Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정](how-to-setup-authentication.md)을 참조하세요. 이 문서는 서비스 주체 및 자동화된 워크플로 사용을 비롯한 인증에 대한 정보와 예제를 제공합니다.

### <a name="authentication-for-web-service-deployment"></a>웹 서비스 배포를 위한 인증

Azure Machine Learning은 웹 서비스에 두 가지 형태의 인증(키 및 토큰)을 지원합니다. 각 웹 서비스는 한 번에 한 가지 형태의 인증만 사용할 수 있습니다.

|인증 방법|Description|Azure Container Instances|AKS|
|---|---|---|---|
|키|키는 정적 이므로 새로 고칠 필요가 없습니다. 키를 수동으로 다시 생성할 수 있습니다.|기본적으로 사용 안 함| 기본적으로 사용|
|토큰|토큰은 지정된 기간이 지나면 만료되며 새로 고쳐야 합니다.| 사용할 수 없음| 기본적으로 사용 안 함 |

코드 예제는 [웹 서비스 인증 섹션](how-to-setup-authentication.md#web-service-authentication)을 참조하세요.

## <a name="authorization"></a>권한 부여

여러 작업 영역을 만들 수 있고 각 작업 영역을 여러 사용자와 공유할 수 있습니다. 작업 영역을 공유하는 경우 사용자에게 다음 역할을 할당하여 작업 영역에 대한 액세스를 제어할 수 있습니다.

* 소유자
* 참가자
* 판독기

다음 표에는 몇 가지 주요 Azure Machine Learning 작업과 이를 수행할 수 있는 역할이 나와 있습니다.

| Azure Machine Learning 작업 | 소유자 | 참가자 | 판독기 |
| ---- |:----:|:----:|:----:|
| 작업 영역 만들기 | ✓ | ✓ | |
| 작업 영역 공유 | ✓ | |  |
| 작업 영역을 Enterprise Edition으로 업그레이드 | ✓ | |
| 컴퓨팅 대상 만들기 | ✓ | ✓ | |
| 컴퓨팅 대상 연결 | ✓ | ✓ | |
| 데이터 저장소 연결 | ✓ | ✓ | |
| 실험 실행 | ✓ | ✓ | |
| 실행/메트릭 보기 | ✓ | ✓ | ✓ |
| 모델 등록 | ✓ | ✓ | |
| 이미지 만들기 | ✓ | ✓ | |
| 웹 서비스 배포 | ✓ | ✓ | |
| 모델/이미지 보기 | ✓ | ✓ | ✓ |
| 웹 서비스 호출 | ✓ | ✓ | ✓ |

기본 제공 역할이 요구에 맞지 않으면 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할은 계산을 만들거나, 실행을 제출 하거나, 데이터 저장소를 등록 하거나, 모델을 배포 하는 등 작업 영역 내의 모든 작업을 제어 하는 데 사용할 수 있습니다. 사용자 지정 역할에는 클러스터, 데이터 저장소, 모델, 끝점과 같은 작업 영역의 다양 한 리소스에 대 한 읽기, 쓰기 또는 삭제 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용하도록 설정할 수 있습니다. 자세한 내용은 [Azure Machine Learning 작업 영역에서 사용자 및 역할 관리](how-to-assign-roles.md)를 참조하세요.

> [!WARNING]
> Azure Machine Learning은 Azure Active Directory B2B(Business-to-Business) 협업에서 지원되지만 현재 Azure Active Directory B2C(Business-to-Consumer) 협업에서는 지원되지 않습니다.

### <a name="securing-compute-targets-and-data"></a>컴퓨팅 대상 및 데이터 보안

소유자와 기여자는 작업 영역에 연결된 모든 컴퓨팅 대상과 데이터 저장소를 사용할 수 있습니다.  

각 작업 영역에는 작업 영역과 이름이 동일하며 연결되어 있는 시스템이 할당한 관리 ID도 있습니다. 관리 ID에는 작업 영역에서 사용되는 연결된 리소스에 대해 다음과 같은 사용 권한이 있습니다.

관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조하세요.

| 리소스 | 사용 권한 |
| ----- | ----- |
| 작업 영역 | 참가자 |
| 스토리지 계정 | Storage Blob 데이터 기여자 |
| 주요 자격 증명 모음 | 모든 키, 비밀, 인증서에 대한 액세스 |
| Azure Container Registry | 참가자 |
| 작업 영역을 포함하는 리소스 그룹 | 참가자 |
| 키 자격 증명 모음이 포함된 리소스 그룹(작업 영역이 포함된 것과 다른 경우) | 참가자 |

이전 표에 언급된 리소스의 관리 ID에 대한 액세스를 관리자가 취소하지 않는 것이 좋습니다. 키 다시 동기화를 사용하여 액세스를 복원할 수 있습니다.

Azure Machine Learning은 모든 작업 영역 지역에 대한 구독에서 기여자 수준 액세스 권한으로 추가 애플리케이션(이름이 `aml-` 또는 `Microsoft-AzureML-Support-App-`으로 시작)을 만듭니다. 예를 들어, 미국 동부에 작업 영역이 하나 있고 동일한 구독의 북유럽에 하나가 있으면 해당 애플리케이션은 두 개가 보입니다. 이러한 애플리케이션을 통해 Azure Machine Learning에서 컴퓨팅 리소스를 관리할 수 있습니다.

## <a name="network-security"></a>네트워크 보안

Azure Machine Learning은 다른 Azure 서비스에 의존하여 컴퓨팅 리소스를 얻습니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 가상 네트워크 컴퓨팅 대상을 만들 수 있습니다. 예를 들어, Azure Data Science Virtual Machine을 사용하여 모델을 학습한 다음, AKS에 모델을 배포할 수 있습니다.  

자세한 내용은 [격리된 가상 네트워크에서 실험을 안전하게 실행하고 유추하는 방법](how-to-enable-virtual-network.md)을 참조하세요.

작업 영역에 대해 Azure Private Link를 사용하도록 설정할 수도 있습니다. Private Link를 사용하면 Azure Virtual Network에서 작업 영역으로의 통신을 제한할 수 있습니다. 자세한 내용은 [Private Link를 구성하는 방법](how-to-configure-private-link.md)을 참조하세요.

## <a name="data-encryption"></a>데이터 암호화.

> [!IMPORTANT]
> __학습__중에 프로덕션 등급 암호화의 경우 계산 클러스터 Azure Machine Learning를 사용 하는 것이 좋습니다. __유추__중 프로덕션 등급 암호화의 경우 Azure Kubernetes Service를 사용 하는 것이 좋습니다.
>
> Azure Machine Learning 계산 인스턴스는 개발/테스트 환경입니다. 이를 사용 하는 경우 파일 공유에 전자 필기장 및 스크립트와 같은 파일을 저장 하는 것이 좋습니다. 데이터를 데이터 저장소에 저장 해야 합니다.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

> [!IMPORTANT]
> 작업 영역에 중요한 데이터가 포함된 경우 작업 영역을 만드는 동안 [hbi_workspace 플래그](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)를 설정하는 것이 좋습니다. `hbi_workspace`작업 영역을 만들 때만 플래그를 설정할 수 있습니다. 기존 작업 영역에 대해서는 변경할 수 없습니다.

`hbi_workspace`플래그는 [microsoft에서 진단 목적으로 수집](#microsoft-collected-data) 하 고 [microsoft에서 관리 하는 환경에서 추가 암호화](../security/fundamentals/encryption-atrest.md)를 사용 하도록 설정 하는 데이터의 양을 제어 합니다. 또한 다음 작업을 수행할 수 있습니다.

* 해당 구독에 이전 클러스터를 만들지 않은 경우 Azure Machine Learning 계산 클러스터에서 로컬 스크래치 디스크의 암호화를 시작 합니다. 그렇지 않으면 컴퓨팅 클러스터의 스크래치 디스크를 암호화가 가능하도록 지원 티켓을 생성해야 합니다. 
* 실행 사이에 로컬 스크래치 디스크를 정리합니다
* 키 자격 증명 모음을 사용 하 여 저장소 계정, 컨테이너 레지스트리 및 SSH 계정에 대 한 자격 증명을 실행 계층에서 계산 클러스터로 안전 하 게 전달 합니다.
* IP 필터링이 가능하도록 설정하여 AzureMachineLearningService 이외의 외부 서비스에서 기본 배치 풀을 호출할 수 없도록 합니다.

#### <a name="azure-blob-storage"></a>Azure Blob 스토리지

Azure Machine Learning은 Azure Machine Learning 작업 영역 및 구독과 연결된 Azure Blob 스토리지 계정에 스냅샷, 출력 및 로그를 저장합니다. Azure Blob Storage에 저장된 모든 데이터는 Microsoft 관리형 키를 사용하여 미사용 상태에서 암호화됩니다.

Azure Blob Storage에 저장된 데이터에 자체 키를 사용하는 방법에 대한 자세한 내용은 [Azure Key Vault에서 고객 관리형 키를 사용하여 Azure Storage 암호화](../storage/common/storage-encryption-keys-portal.md)를 참조하세요.

일반적으로 학습 데이터는 Azure Blob Storage에 저장되므로 학습 컴퓨팅 대상에 액세스할 수 있습니다. 이 스토리지는 Azure Machine Learning으로 관리되지 않지만 컴퓨팅 대상에 원격 파일 시스템으로 탑재됩니다.

키를 __순환 또는 철회__해야 하는 경우 언제든지 그렇게 할 수 있습니다. 키를 순환하면 스토리지 계정은 새 키(최신 버전)를 사용하여 미사용 데이터를 암호화하기 시작합니다. 키를 철회(비활성화)하면 스토리지 계정이 실패한 요청을 처리합니다. 순환 또는 철회가 적용되려면 대개 1시간이 걸립니다.

액세스 키 다시 생성에 대한 자세한 내용은 [스토리지 액세스 키 다시 생성](how-to-change-storage-access-key.md)을 참조하세요.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning은 Azure Cosmos DB 인스턴스에 메트릭과 메타데이터를 저장합니다. 이 인스턴스는 Azure Machine Learning으로 관리되는 Microsoft 구독과 연결되어 있습니다. Azure Cosmos DB에 저장된 모든 데이터는 Microsoft 관리형 키를 사용하여 미사용 상태에서 암호화됩니다.

자체(고객 관리형) 키를 사용하여 Azure Cosmos DB 인스턴스를 암호화하려면 작업 영역에서 사용할 전용 Cosmos DB 인스턴스를 만들면 됩니다. 실행 기록 정보와 같은 데이터를 Microsoft 구독에서 호스트되는 다중 테넌트 Cosmos DB 인스턴스 외부에 저장하려는 경우에는 이 방법을 사용하는 것이 좋습니다. 

고객 관리형 키를 사용하여 구독에서 Cosmos DB 인스턴스를 프로비저닝하려면 다음 작업을 수행합니다.

* 아직 수행 하지 않은 경우 MachineLearning 및 Microsoft.DocumentDB 리소스 공급자를 구독에 등록 합니다.

* Azure Machine Learning 작업 영역을 만들 때는 다음 매개 변수를 사용합니다. 두 매개 변수 모두 필수이며 SDK, CLI, REST API 및 Resource Manager 템플릿에서 지원됩니다.

    * `resource_cmk_uri`: 이 매개 변수는 [키의 버전 정보](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)를 포함하여 키 자격 증명 모음에서 고객 관리형 키의 전체 리소스 URI입니다. 

    * `cmk_keyvault`: 이 매개 변수는 구독에 있는 키 자격 증명 모음의 리소스 ID입니다. 이 키 자격 증명 모음은 Azure Machine Learning 작업 영역에 사용할 동일한 지역 및 구독에 있어야 합니다. 
    
        > [!NOTE]
        > 이 키 자격 증명 모음 인스턴스는 작업 영역을 프로비저닝할 때 Azure Machine Learning에서 생성된 키 자격 증명 모음과 다를 수 있습니다. 작업 영역에 동일한 키 자격 증명 모음 인스턴스를 사용하려면 [key_vault parameter](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)를 사용하여 작업 영역을 프로비저닝하는 동안 동일한 키 자격 증명 모음을 전달합니다. 

이 Cosmos DB 인스턴스는 구독의 Microsoft 관리형 리소스 그룹에 생성됩니다. 관리형 리소스 그룹의 이름은 `<AML Workspace Resource Group Name><GUID>` 형식으로 지정됩니다.

> [!IMPORTANT]
> * Cosmos DB 인스턴스를 삭제해야 하는 경우에는 이 인스턴스를 사용하는 Azure Machine Learning 작업 영역을 삭제해야 합니다. 
> * Cosmos DB 계정의 기본 [__요청 단위__](../cosmos-db/request-units.md)는 __8000__으로 설정되어 있습니다. 이 값을 변경하는 것은 지원되지 않습니다. 

키를 __순환 또는 철회__해야 하는 경우 언제든지 그렇게 할 수 있습니다. 키를 순환하면 Cosmos DB는 새 키(최신 버전)를 사용하여 미사용 데이터를 암호화하기 시작합니다. 키를 철회(비활성화)하면 Cosmos DB가 실패한 요청을 처리합니다. 순환 또는 철회가 적용되려면 대개 1시간이 걸립니다.

Cosmos DB의 고객 관리형 키에 대한 자세한 내용은 [Azure Cosmos DB 계정에 대한 고객 관리형 키 구성](../cosmos-db/how-to-setup-cmk.md)을 참조하세요.

#### <a name="azure-container-registry"></a>Azure Container Registry

레지스트리(Azure Container Registry)의 모든 컨테이너 이미지는 미사용 상태에서 암호화됩니다. Azure는 이미지를 저장하기 전에 이미지를 자동으로 암호화하고 Azure Machine Learning이 이미지를 끌어올 때 암호를 해독합니다.

자체(고객 관리형) 키를 사용하여 Azure Container Registry를 암호화하려면 작업 영역을 프로비저닝하는 동안 자체 ACR을 생성하여 첨부하거나 작업 영역을 프로비저닝할 때 생성되는 기본 인스턴스를 암호화해야 합니다.

> [!IMPORTANT]
> Azure Machine Learning Azure Container Registry에서 관리자 계정을 사용 하도록 설정 해야 합니다. 컨테이너 레지스트리를 만들 때 기본적으로이 설정은 사용 되지 않습니다. 관리자 계정을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [관리자 계정](/azure/container-registry/container-registry-authentication#admin-account)을 참조 하십시오.
>
> 작업 영역에 대해 Azure Container Registry가 만들어지면 이를 삭제하지 마세요. 삭제하면 Azure Machine Learning 작업 영역이 중단됩니다.

기존 Azure Container Registry를 사용하여 작업 영역을 만드는 예는 다음 문서를 참조하세요.

* [Azure CLI를 사용하여 Azure Machine Learning의 작업 영역 만들기](how-to-manage-workspace-cli.md)
* [Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning의 작업 영역 만들기](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

고객 관리형 키를 사용하여 배포된 ACI(Azure Container Instance) 리소스를 암호화할 수 있습니다. ACI에 사용되는 고객 관리형 키는 작업 영역의 Azure Key Vault에 저장할 수 있습니다. 키 생성에 대한 자세한 내용은 [고객 관리형 키를 사용하여 데이터 암호화](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)를 참조하세요.

Azure Container Instance에 모델을 배포할 때 키를 사용하려면 `AciWebservice.deploy_configuration()`을 사용하여 새 배포 구성을 만듭니다. 다음 매개 변수를 사용하여 키 정보를 제공합니다.

* `cmk_vault_base_url`: 키가 포함된 키 자격 증명 모음의 URL입니다.
* `cmk_key_name`: 키의 이름입니다.
* `cmk_key_version`: 키 버전입니다.

배포 구성을 만들고 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 참조
* [배포 위치 및 방법](how-to-deploy-and-where.md)
* [Azure Container Instances에 모델 배포](how-to-deploy-azure-container-instance.md)

ACI에서 고객 관리형 키를 사용하는 방법에 대한 자세한 내용은 [고객 관리형 키를 사용하여 데이터 암호화](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)를 참조하세요.

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

언제든지 고객 관리형 키를 사용하여 배포된 Azure Kubernetes Service 리소스를 암호화할 수 있습니다. 자세한 내용은 [Azure Kubernetes Service의 BYOK(Bring Your Own Key)](../aks/azure-disk-customer-managed-keys.md)를 참조하세요. 

이 프로세스를 통해 Kubernetes 클러스터에서 배포된 가상 머신의 데이터 및 OS 디스크를 모두 암호화할 수 있습니다.

> [!IMPORTANT]
> 이 프로세스는 AKS K8 버전 1.17 이상에서만 작동합니다. 2020년 1월 13일에 AKS 1.17에 대한 지원이 Azure Machine Learning에 추가되었습니다.

#### <a name="machine-learning-compute"></a>Machine Learning 컴퓨팅

Azure Storage에 저장된 각 컴퓨팅 노드의 OS 디스크는 Azure Machine Learning 스토리지 계정에서 Microsoft 관리형 키로 암호화됩니다. 컴퓨팅 대상은 사용 후 삭제되며 대기 중인 실행이 없으면 일반적으로 클러스터가 축소됩니다. 기본 가상 머신의 프로비저닝이 해제되고 OS 디스크가 삭제됩니다. OS 디스크에는 Azure Disk Encryption이 지원되지 않습니다.

각 가상 머신에는 OS 작업을 위한 로컬 임시 디스크도 있습니다. 필요하면 디스크를 사용하여 학습 데이터를 준비할 수 있습니다. 디스크는 `hbi_workspace` 매개 변수를 `TRUE`로 설정하여 작업 영역에 대해 기본적으로 암호화됩니다. 이 환경은 실행 기간 동안만 수명이 지속되며 암호화 지원은 시스템 관리형 키로만 제한됩니다.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks는 Azure Machine Learning 파이프라인에서 사용할 수 있습니다. 기본적으로 Azure Databricks에 사용되는 DBFS(Databricks 파일 시스템)는 Microsoft 관리형 키를 사용하여 암호화됩니다. 고객 관리형 키를 사용하도록 Azure Databricks를 구성하려면 [기본(루트) DBFS에서 고객 관리형 키 구성](/azure/databricks/security/customer-managed-keys-dbfs)을 참조하세요.

### <a name="encryption-in-transit"></a>전송 중 암호화

Azure Machine Learning은 TLS를 사용하여 다양한 Azure Machine Learning 마이크로서비스 간의 내부 통신을 보호합니다. 모든 Azure Storage 액세스는 보안 채널을 통해 수행됩니다.

점수 매기기 끝점에 대 한 외부 호출을 보호 하기 위해 Azure Machine Learning는 TLS를 사용 합니다. 자세한 내용은 [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)를 참조하세요.

### <a name="using-azure-key-vault"></a>Azure Key Vault 사용

Azure Machine Learning은 작업 영역과 연결된 Azure Key Vault 인스턴스를 사용하여 다양한 종류의 자격 증명을 저장합니다.

* 연결된 스토리지 계정 연결 문자열
* Azure Container Repository 인스턴스에 대한 암호
* 데이터 저장소에 대한 연결 문자열

Azure HDInsight 및 VM과 같은 컴퓨팅 대상에 대한 SSH 암호 및 키는 Microsoft 구독과 연결된 별도의 키 자격 증명 모음에 저장됩니다. Azure Machine Learning에는 사용자가 제공한 암호나 키가 저장되지 않습니다. 대신 자체 SSH 키를 생성하고, 권한을 부여하고, 저장한 후 실험을 실행할 VM 및 HDInsight에 연결합니다.

각 작업 영역에는 작업 영역과 이름이 동일하며 연결되어 있는 시스템이 할당한 관리 ID가 있습니다. 관리 ID는 키 자격 증명 모음의 모든 키, 비밀 및 인증서에 액세스할 수 있습니다.

## <a name="data-collection-and-handling"></a>데이터 수집 및 처리

### <a name="microsoft-collected-data"></a>Microsoft에 수집되는 데이터

Microsoft는 진단을 목적으로 리소스 이름(예: 데이터 세트 이름 또는 기계 학습 실험 이름)이나 작업 환경 변수와 같은 비 사용자 식별 정보를 수집할 수 있습니다. 이러한 모든 데이터는 Microsoft 소유 구독에서 호스트되는 스토리지에 Microsoft 관리형 키를 사용하여 저장되며 [Microsoft의 표준 개인 정보 취급 방침 및 데이터 처리 표준](https://privacy.microsoft.com/privacystatement)을 따릅니다.

또한 환경 변수에 중요한 정보(예: 계정 키 비밀)를 저장하지 않는 것이 좋습니다. 환경 변수는 당사가 기록, 암호화 및 저장합니다. 마찬가지로 [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 이름을 지정할 때 사용자 이름 또는 비밀 프로젝트 이름과 같은 민감한 정보를 포함하지 마십시오. Microsoft 지원 엔지니어가 액세스할 수 있는 원격 분석 로그에 이런 정보가 나타날 수 있습니다.

작업 영역을 프로비저닝하는 동안 `hbi_workspace` 매개 변수를 `TRUE`로 설정하면 진단 데이터가 수집되지 않도록 옵트아웃할 수 있습니다. 이 기능은 AzureML Python SDK, CLI, REST API 또는 Azure Resource Manager 템플릿을 사용할 때 지원됩니다.

### <a name="microsoft-generated-data"></a>Microsoft에서 생성된 데이터

자동화된 Machine Learning과 같은 서비스를 사용하는 경우 Microsoft는 여러 모델을 학습시키기 위해 전처리된 일시적 데이터를 생성할 수 있습니다. 이런 데이터는 사용자 작업 영역의 데이터 저장소에 저장되므로 액세스 제어 및 암호화를 적절하게 적용할 수 있습니다.

[배포된 엔드포인트에서 기록된 진단 정보](how-to-enable-app-insights.md)를 Azure Application Insights 인스턴스로 암호화할 수도 있습니다.

## <a name="monitoring"></a>모니터링

### <a name="metrics"></a>메트릭

Azure Monitor 메트릭을 사용하여 Azure Machine Learning 작업 영역에 대한 메트릭을 보고 모니터링할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 작업 영역을 선택한 다음, **메트릭**을 선택합니다.

[![작업 영역에 대한 예제 메트릭을 보여주는 스크린샷](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

메트릭에는 실행, 배포 및 등록에 대한 정보가 포함됩니다.

자세한 내용은 [Azure Monitor의 메트릭](/azure/azure-monitor/platform/data-platform-metrics)을 참조하세요.

### <a name="activity-log"></a>활동 로그

작업 영역의 활동 로그를 보면 작업 영역에서 수행되는 다양한 작업을 확인할 수 있습니다. 로그에는 작업 이름, 이벤트 초기자 및 타임스탬프와 같은 기본 정보가 포함됩니다.

이 스크린샷은 작업 영역에 대한 활동 로그를 보여줍니다.

[![작업 영역에 대한 활동 로그를 보여주는 스크린샷](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

채점 요청 세부 정보는 Application Insights에 저장됩니다. Application Insights는 작업 영역을 만들 때 구독에 생성됩니다. 기록된 정보에는 다음과 같은 필드가 포함됩니다.

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure Machine Learning 작업 영역의 일부 작업은 활동 로그에 정보를 기록하지 않습니다. 예를 들어, 학습 실행 시작과 모델 등록은 기록되지 않습니다.
>
> 이러한 작업 중 일부는 작업 영역의 **작업** 영역에 표시되지만 이 알림은 작업을 시작한 사용자를 나타내지 않습니다.

## <a name="data-flow-diagrams"></a>데이터 흐름 다이어그램

### <a name="create-workspace"></a>작업 영역 만들기

다음 다이어그램은 작업 영역 생성 워크플로를 보여줍니다.

* 지원되는 Azure Machine Learning 클라이언트(Azure CLI, Python SDK, Azure Portal) 중 하나에서 Azure AD에 로그인하고 적절한 Azure Resource Manager 토큰을 요청합니다.
* Azure Resource Manager를 호출하여 작업 영역을 만듭니다. 
* Azure Resource Manager는 Azure Machine Learning 리소스 공급자에게 연락하여 작업 영역을 프로비저닝합니다.

작업 영역을 만드는 동안 사용자의 구독에서 추가 리소스가 생성됩니다.

* 키 자격 증명 모음(비밀 저장용)
* Azure 스토리지 계정(BLOB 및 파일 공유 포함)
* Azure Container Registry(유추/채점 및 실험을 위한 Docker 이미지 저장용)
* Application Insights(원격 분석 저장용)

사용자는 필요에 따라 작업 영역(예: Azure Kubernetes Service 또는 VM)에 연결된 다른 컴퓨팅 대상을 프로비저닝할 수도 있습니다.

[![작업 영역 생성 워크플로](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>소스 코드 저장(학습 스크립트)

다음 다이어그램은 코드 스냅샷 워크플로를 보여줍니다.

Azure Machine Learning과 연결된 작업 영역은 소스 코드(학습 스크립트)를 포함하는 디렉터리(실험)입니다. 스크립트는 로컬 머신과 클라우드(구독의 Azure Blob Storage)에 저장됩니다. 코드 스냅샷은 기록 감사를 위한 실행 또는 검사에 사용됩니다.

[![코드 스냅샷 워크플로](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>학습

다음 다이어그램은 학습 워크플로를 보여줍니다.

* Azure Machine Learning은 이전 섹션에서 저장한 코드 스냅샷의 스냅샷 ID를 사용하여 호출됩니다.
* Azure Machine Learning은 실행 ID(선택 사항)와 Machine Learning Service 토큰을 만들며, 이것은 나중에 Machine Learning 컴퓨팅/VM과 같은 컴퓨팅 대상이 Machine Learning Service와 통신하는 데 사용됩니다.
* 관리되는 컴퓨팅 대상(예: Machine Learning 컴퓨팅) 또는 관리되지 않는 컴퓨팅 대상(예: VM) 중 하나를 선택하여 학습 작업을 실행할 수 있습니다. 두 시나리오에 대한 데이터 흐름은 다음과 같습니다.
   * VM/HDInsight에 Microsoft 구독의 키 자격 증명 모음에 있는 SSH 자격 증명을 사용하여 액세스합니다. Azure Machine Learning이 컴퓨팅 대상에서 관리 코드를 실행하여 다음이 수행됩니다.

   1. 환경을 준비합니다. (Docker는 VM 및 로컬 컴퓨터에 대한 옵션입니다. Machine Learning 컴퓨팅에 대한 다음 단계를 참조하면 Docker 컨테이너에서 실험을 실행하는 방식을 이해할 수 있습니다.)
   1. 코드를 다운로드합니다.
   1. 환경 변수 및 구성을 설정합니다.
   1. 사용자 스크립트를 실행합니다(이전 섹션에서 언급한 코드 스냅샷).

   * Machine Learning 컴퓨팅, 작업 영역 관리 ID를 통해 액세스합니다.
Machine Learning 컴퓨팅은 관리되는 컴퓨팅 대상(즉, Microsoft에서 관리됨)이므로 Microsoft 구독 하에 실행됩니다.

   1. 원격 Docker 구성이 시작됩니다(필요한 경우).
   1. 관리 코드가 사용자의 Azure Files 공유에 기록됩니다.
   1. 컨테이너가 초기 명령으로 시작됩니다. 즉, 이전 단계에서 설명한 관리 코드입니다.

#### <a name="querying-runs-and-metrics"></a>실행 및 메트릭 쿼리

이 단계는 아래 흐름 다이어그램에서 학습 컴퓨팅 대상이 실행 메트릭을 Cosmos DB 데이터베이스의 스토리지에서 Azure Machine Learning에 다시 쓸 때 발생합니다. 클라이언트는 Azure Machine Learning을 호출할 수 있습니다. Machine Learning은 Cosmos DB 데이터베이스에서 메트릭을 차례로 끌어와서 클라이언트에 다시 반환합니다.

[![학습 워크플로](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>웹 서비스 만들기

다음 다이어그램은 유추 워크플로를 보여줍니다. 추론 또는 모델 채점은 배포된 모델이 예측(주로 프로덕션 데이터에 대한)에 사용되는 단계입니다.

세부 정보는 다음과 같습니다.

* 사용자가 Azure Machine Learning SDK와 같은 클라이언트를 사용하여 모델을 등록합니다.
* 사용자가 모델, 점수 파일 및 기타 모델 종속성을 사용하여 이미지를 만듭니다.
* Docker 이미지가 생성되어 Azure Container Registry에 저장됩니다.
* 이전 단계에서 생성된 이미지를 사용하여 웹 서비스가 컴퓨팅 대상(Container Instances/AKS)에 배포됩니다.
* 채점 요청 정보가 사용자의 구독에 있는 Application Insights에 저장됩니다.
* 원격 분석도 Microsoft/Azure 구독으로 푸시됩니다.

[![유추 워크플로](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="next-steps"></a>다음 단계

* [TLS를 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 Machine Learning 모델 사용](how-to-consume-web-service.md)
* [Azure Firewall과 함께 Azure Machine Learning 사용](how-to-access-azureml-behind-firewall.md)
* [Azure Virtual Network에서 Azure Machine Learning 사용](how-to-enable-virtual-network.md)
* [추천 시스템 빌드 모범 사례](https://github.com/Microsoft/Recommenders)
* [Azure에서 실시간 추천 API 빌드](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
