---
title: Azure Cosmos DB에서 Azure Kubernetes를 사용하는 방법
description: Azure Cosmos DB(미리 보기)를 사용하는 Azure에서 Kubernetes 클러스터를 부트스트랩하는 방법 알아보기
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 2c6af53aeec5d40f603d65595d93527107c0d80a
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427709"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Azure Cosmos DB(미리 보기)에서 Azure Kubernetes를 사용하는 방법

Azure Cosmos DB의 etcd API를 사용하면 Azure Cosmos DB를 Azure Kubernetes의 백 엔드 저장소로 사용할 수 있습니다. Azure Cosmos DB는 마스터 노드의 API 서버가 로컬에 설치된 etcd에 액세스하는 것처럼 Azure Cosmos DB를 사용할 수 있도록 하는 etcd 유선 프로토콜을 구현합니다. Azure Cosmos DB의 etcd API는 현재 미리 보기로 제공됩니다. Kubernetes에 대한 백업 저장소로 Azure Cosmos etcd API를 사용하는 경우 다음과 같은 이점을 얻을 수 있습니다. 

* etcd를 수동으로 구성하고 관리할 필요가 없습니다.
* Cosmos로 보장되는 고가용성 etcd(단일 지역에서 99.99%, 여러 지역에서 99.999%)
* etcd의 탄력적 확장성
* 기본적으로 보안 유지 및 엔터프라이즈 수준의 서비스 지원
* 업계 최고의 포괄적인 SLA

Azure Cosmos DB의 etcd API에 대한 자세한 내용은 [개요](etcd-api-introduction.md) 문서를 참조하세요. 이 문서에서는 [Azure Kubernetes 엔진](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)(aks-engine)을 사용하여 로컬로 설치 및 구성된 etcd 대신 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)를 사용하는 Kubernetes 클러스터를 Azure에서 부트스트랩하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건

1. 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치합니다. 운영 체제에 해당하는 Azure CLI를 다운로드하고 설치할 수 있습니다.

1. Azure Kubernetes 엔진의 [최신 버전](https://github.com/Azure/aks-engine/releases)을 설치합니다. 다른 운영 체제에 대한 설치 지침은 [Azure Kubernetes 엔진](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) 페이지에서 사용할 수 있습니다. 연결된 설명서의 **AKS 엔진 설치** 섹션의 단계만 수행하면 됩니다. 다운로드한 후, zip 파일의 압축을 풉니다.

   Azure Kubernetes 엔진(**aks-engine**)은 Azure에서 Kubernetes 클러스터용 Azure Resource Manager 템플릿을 생성합니다. aks-engine에 입력하는 항목은 오케스트레이터, 기능 및 에이전트를 포함하여 원하는 클러스터를 설명하는 클러스터 정의 파일입니다. 입력 파일의 구조는 Azure Kubernetes Service의 공용 API와 비슷합니다.

1. Azure Cosmos DB의 etcd API는 현재 미리 보기로 제공됩니다. [https://aka.ms/cosmosetcdapi-signup](https://aka.ms/cosmosetcdapi-signup )에서 미리 보기 버전을 사용하도록 가입합니다. 양식을 제출하면 구독은 Azure Cosmos etcd API를 사용하기 위한 허용 목록에 추가됩니다. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용하여 클러스터 배포

1. 명령 프롬프트 창을 열고 다음 명령을 사용하여 Azure에 로그인합니다.

   ```azurecli-interactive
   az login 
   ```

1. 둘 이상의 구독이 있는 경우 Azure Cosmos DB etcd API의 허용 목록에 추가된 구독으로 전환합니다. 다음 명령을 사용하여 필요한 구독으로 전환할 수 있습니다.

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. 다음으로 Azure Kubernetes 클러스터에 필요한 리소스를 배포할 새 리소스 그룹을 만듭니다. "centralus" 지역에 리소스 그룹을 만들어야 합니다. 리소스 그룹이 반드시 "centralus" 지역에 있어야 하는 것은 아니지만, Azure Cosmos etcd API는 현재 "centralus" 지역에서만 배포할 수 있습니다. 따라서 Kubernetes 클러스터를 Cosmos etcd 인스턴스와 함께 배치하는 것이 가장 좋습니다.

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. 그런 다음, 동일한 리소스 그룹의 일부인 리소스와 통신할 수 있도록 Azure Kubernetes 클러스터의 서비스 주체를 만듭니다. Azure CLI, PowerShell 또는 Azure Portal을 사용하여 서비스 주체를 만들 수 있으며, 이 예제에서는 CLI를 사용하여 서비스 주체를 만듭니다.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   예를 들어, 이 명령은 서비스 주체의 세부 정보를 출력합니다.
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   **appId** 및 **password** 필드의 매개 변수는 다음 단계에서 사용하므로 기록해 둡니다. 

1. 명령 프롬프트에서 Azure Kubernetes 엔진 실행 파일이 있는 폴더로 이동합니다. 예를 들어, 명령 프롬프트에서 다음과 같이 폴더로 이동할 수 있습니다.

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. 원하는 텍스트 편집기를 열고 Azure Cosmos DB etcd API 사용하여 Azure Kubernetes 클러스터를 배포하는 Resource Manager 템플릿을 정의합니다. 텍스트 편집기에 다음 JSON 정의를 복사하고 파일을 `apiModel.json`으로 저장합니다.

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   JSON/클러스터 정의 파일에서 적어둘 키 매개 변수는 **"cosmosEtcd": true**입니다. 이 매개 변수는 "masterProfile" 속성에 있으며 배포에 일반 etcd 대신 Azure Cosmos etcd API를 사용하도록 지정합니다. 

1. 다음 명령을 사용하여 Azure Cosmos DB를 사용하는 Azure Kubernetes 클러스터를 배포합니다.

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Azure Kubernetes 엔진은 Azure Kubernetes의 원하는 모양, 크기 및 구성을 대략적으로 나타내는 클러스터 정의를 사용합니다. 클러스터 정의를 통해 사용할 수 있는 몇 가지 기능이 있습니다. 이 예제에서는 다음 매개 변수를 사용합니다.

   * **subscription-id:** Azure Cosmos DB etcd API가 사용하도록 설정된 Azure 구독 ID입니다.
   * **client-id:** 서비스 주체의 appId입니다. `appId`는 4단계에서 출력으로 반환되었습니다.
   * **Client-secret:** 서비스 주체의 암호 또는 임의로 생성된 암호입니다. 이 값은 4단계의 'password' 매개 변수에서 출력으로 반환되었습니다. 
   * **dnsPrefix:** 지역 고유 DNS 이름입니다. 이 값은 호스트 이름의 일부를 구성합니다(예제 값은 -myprod1, staging임).
   * **location:**  클러스터를 배포해야 하는 위치입니다. 현재, "centralus"만 지원됩니다.

   > [!Note]
   > Azure Cosmos etcd API는 현재 "centralus" 지역에서만 배포할 수 있습니다. 
 
   * **api-model:** 템플릿 파일의 정규화된 경로입니다.
   * **force-overwrite:** 이 옵션은 출력 디렉터리의 기존 파일을 자동으로 덮어쓰는 데 사용됩니다.
 
   다음 명령은 예제 배포를 보여 줍니다.

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>배포 확인

템플릿 배포를 완료하려면 몇 분이 걸립니다. 배포를 완료하면 명령 프롬프트에 다음 출력이 표시됩니다.

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

이제 리소스 그룹은 가상 머신, Azure Cosmos 계정(API etcd), 가상 네트워크, 가용성 집합 등의 리소스와 Kubernetes 클러스터에 필요한 기타 리소스를 포함합니다. 

Azure Cosmos 계정의 이름은 k8s에 지정된 DNS 접두사가 붙은 항목과 일치합니다. Azure Cosmos 계정은 자동으로 **EtcdDB**라는 데이터베이스와 **EtcdData**라는 컨테이너로 프로비지넝됩니다. 이 컨테이너는 모든 etcd 관련 데이터를 저장합니다. 컨테이너는 특정 개수의 요청 단위로 프로비저닝되며, 워크로드에 따라 [처리량 크기를 조정(증가/감소)](scaling-throughput.md)할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos 데이터베이스, 컨테이너 및 항목으로 작업](databases-containers-items.md)하는 방법 알아보기
* [프로비전된 처리량 비용을 최적화](optimize-cost-throughput.md)하는 방법 알아보기

