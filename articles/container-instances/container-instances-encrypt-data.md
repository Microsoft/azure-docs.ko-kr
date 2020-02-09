---
title: 배포 데이터 암호화
description: 컨테이너 인스턴스 리소스에 대해 유지 되는 데이터의 암호화 및 고객이 관리 하는 키를 사용 하 여 데이터를 암호화 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: 14a51ce103d831bcf1dfd52c892102f72531a4c8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934311"
---
# <a name="encrypt-deployment-data"></a>배포 데이터 암호화

클라우드에서 ACI (Azure Container Instances) 리소스를 실행 하는 경우 ACI 서비스는 컨테이너와 관련 된 데이터를 수집 하 고 유지 합니다. ACI는 클라우드에서 지속 될 때이 데이터를 자동으로 암호화 합니다. 이 암호화는 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 합니다. 또한 ACI는 사용자 고유의 키를 사용 하 여이 데이터를 암호화 하는 옵션을 제공 하므로 ACI 배포와 관련 된 데이터를 더 효율적으로 제어할 수 있습니다.

## <a name="about-aci-data-encryption"></a>ACI 데이터 암호화 정보 

ACI의 데이터는 256 비트 AES 암호화를 사용 하 여 암호화 및 암호 해독 됩니다. 모든 ACI 배포에 대해 사용 하도록 설정 되어 있으며,이 암호화를 활용 하기 위해 배포 나 컨테이너를 수정할 필요가 없습니다. 여기에는 배포, 환경 변수 및 컨테이너에 전달 되는 키에 대한 메타 데이터가 포함 되며, 컨테이너가 중지 된 후에도 계속 표시 될 수 있도록 로그가 지속 됩니다. 암호화는 컨테이너 그룹 성능에 영향을 주지 않으며 암호화에 대한 추가 비용은 없습니다.

## <a name="encryption-key-management"></a>암호화 키 관리

Microsoft 관리 키를 사용 하 여 컨테이너 데이터의 암호화를 사용 하거나 사용자 고유의 키를 사용 하 여 암호화를 관리할 수 있습니다. 다음 표에서는 이러한 옵션을 비교 합니다. 

|    |    Microsoft에서 관리 하는 키     |     고객 관리형 키     |
|----|----|----|
|    암호화/암호 해독 작업    |    Azure    |    Azure    |
|    키 저장소    |    Microsoft 키 저장소    |    Azure Key Vault    |
|    키 회전 책임    |    Microsoft    |    Customer    |
|    키 액세스    |    Microsoft만    |    Microsoft, 고객    |

문서의 나머지 부분에서는 사용자의 키 (고객 관리 키)를 사용 하 여 ACI 배포 데이터를 암호화 하는 데 필요한 단계에 대해 설명 합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>고객 관리 키를 사용 하 여 데이터 암호화

### <a name="create-service-principal-for-aci"></a>ACI에 대한 서비스 주체 만들기

첫 번째 단계는 [Azure 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 에 Azure Container Instances 서비스에 대한 사용 권한을 부여 하기 위해 할당 된 서비스 주체가 있는지 확인 하는 것입니다. 

다음 CLI 명령은 Azure 환경에서 ACI SP를 설정 합니다.

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

이 명령을 실행 하면 "displayName": "Azure Container Instance Service"로 설정 된 서비스 사용자가 표시 되어야 합니다.

### <a name="create-a-key-vault-resource"></a>Key Vault 리소스 만들기

[Azure Portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)또는 [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell)을 사용 하 여 Azure Key Vault를 만듭니다. 

키 자격 증명 모음의 속성에 대해 다음 지침을 사용 합니다. 
* 이름: 고유한 이름이 필요 합니다. 
* 구독: 구독을 선택 합니다.
* 리소스 그룹에서 기존 리소스 그룹을 선택 하거나 새로 만들기를 선택 하 고 리소스 그룹 이름을 입력 합니다.
* 위치 풀 다운 메뉴에서 위치를 선택합니다.
* 다른 옵션을 기본값으로 그대로 두거나 추가 요구 사항에 따라 선택할 수 있습니다.

> [!IMPORTANT]
> 고객 관리 키를 사용 하 여 ACI 배포 템플릿을 암호화할 때는 다음 두 가지 속성을 키 자격 증명 모음에 설정 하 고, 일시 삭제 하 고, 제거 하지 않는 것이 좋습니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않지만 PowerShell 또는 기존 키 자격 증명 모음에서 Azure CLI를 사용 하 여 사용 하도록 설정할 수 있습니다.

### <a name="generate-a-new-key"></a>새 키 생성 

키 자격 증명 모음을 만든 후 Azure Portal의 리소스로 이동 합니다. 리소스 블레이드의 왼쪽 탐색 메뉴에 있는 설정에서 **키**를 클릭 합니다. "키에 대한 보기"에서 "생성/가져오기"를 클릭 하 여 새 키를 생성 합니다. 이 키와 요구 사항에 따라 다른 기본 설정에 대한 고유한 이름을 사용 합니다. 

![새 키 생성](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>액세스 정책 설정

ACI 서비스에서 키에 액세스할 수 있도록 하는 새 액세스 정책을 만듭니다.

* 키가 생성 되 면 키 자격 증명 모음 리소스 블레이드로 돌아가서 설정에서 **액세스 정책**을 클릭 합니다.
* 키 자격 증명 모음에 대한 "액세스 정책" 페이지에서 **액세스 정책 추가**를 클릭 합니다.
* 키 사용 *권한을 설정 하* 여 **Get** 및 **래핑 해제 키** ![키 사용 권한을 설정](./media/container-instances-encrypt-data/set-key-permissions.png)
* *보안 주체 선택*에서 **Azure Container Instance 서비스** 를 선택 합니다.
* 아래쪽의 **추가** 를 클릭 합니다. 

이제 액세스 정책이 키 자격 증명 모음 액세스 정책에 표시 됩니다.

![새 액세스 정책](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>JSON 배포 템플릿 수정

> [!IMPORTANT]
> 고객이 관리 하는 키로 배포 데이터를 암호화 하는 것은 현재 롤아웃 중인 최신 API 버전 (2019-12-01)에서 사용할 수 있습니다. 배포 템플릿에서이 API 버전을 지정 합니다. 이 문제에 문제가 있는 경우 Azure 지원에 문의 하세요.

키 자격 증명 모음 키 및 액세스 정책이 설정 되 면 ACI 배포 템플릿에 다음 속성을 추가 합니다. [자습서: 리소스 관리자 템플릿을 사용 하 여 다중 컨테이너 그룹 배포](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)에서 템플릿을 사용 하 여 ACI 리소스를 배포 하는 방법에 대해 자세히 알아보세요. 
* `resources`에서 `apiVersion`를 `2012-12-01`로 설정 합니다.
* 배포 템플릿의 컨테이너 그룹 속성 섹션 아래에 다음 값을 포함 하는 `encryptionProperties`를 추가 합니다.
  * `vaultBaseUrl`: key vault의 DNS 이름은 포털에 있는 주요 자격 증명 모음 리소스의 개요 블레이드에서 찾을 수 있습니다.
  * `keyName`: 이전에 생성 된 키의 이름입니다.
  * `keyVersion`: 키의 현재 버전입니다. 키 자체 (키 자격 증명 모음 리소스의 설정 섹션에 있는 "키" 아래)를 클릭 하 여 찾을 수 있습니다.
* 컨테이너 그룹 속성에서 값 `Standard`를 사용 하 여 `sku` 속성을 추가 합니다. `sku` 속성은 API 버전 2019-12-01에 필요 합니다.

다음 템플릿 코드 조각에서는 배포 데이터를 암호화 하는 다음과 같은 추가 속성을 보여 줍니다.

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

다음은 [자습서: 리소스 관리자 템플릿을 사용 하 여 다중 컨테이너 그룹 배포](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)의 템플릿에서 적용 되는 전체 템플릿입니다. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>리소스 배포

바탕 화면에서 템플릿 파일을 만들고 편집한 경우에는 파일을 끌어서 Cloud Shell 디렉터리에 업로드할 수 있습니다. 

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] 명령을 사용하여 템플릿을 배포합니다.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. 배포가 완료 되 면 ACI 서비스에서 유지 하는 것과 관련 된 모든 데이터가 사용자가 제공한 키로 암호화 됩니다.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create