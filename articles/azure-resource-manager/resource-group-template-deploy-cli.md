---
title: Azure CLI 및 템플릿으로 리소스 배포 | Microsoft Docs
description: Azure Resource Manager 및 Azure CLI를 사용 하 여 Azure에 리소스를 배포 합니다. 리소스는 Resource Manager 템플릿에 정의됩니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 92476f9ac48c168c3bbe85d4da49b6afe034c117
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730506"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>리소스 관리자 템플릿과 Azure CLI로 리소스 배포

이 문서에서는 Resource Manager 템플릿과 함께 Azure CLI를 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다. Azure 솔루션 배포 및 관리와 관련된 개념이 익숙하지 않은 경우 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Azure CLI가 설치되어 있지 않으면 [Cloud Shell](#deploy-template-from-cloud-shell)을 사용할 수 있습니다.

## <a name="deployment-scope"></a>배포 범위

Azure 구독 또는 구독 내에서 리소스 그룹 배포를 대상으로 지정할 수 있습니다. 대부분의 경우에서 리소스 그룹 배포를 대상으로 수 있습니다. 구독 배포를 사용 하 여 정책 및 역할 할당은 구독에서 적용 하 합니다. 또한 리소스 그룹을 만들고 리소스를 배포할 구독 배포를 사용 합니다. 배포의 범위에 따라 다른 명령을 사용할 수 있습니다.

배포 하는 **리소스 그룹**를 사용 하 여 [az 그룹 배포 만들기](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

배포 하는 **구독**를 사용 하 여 [az 배포 만들기](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

이 문서의 예제에서는 리소스 그룹 배포를 사용합니다. 구독 배포에 대 한 자세한 내용은 참조 하세요. [구독 수준에서 리소스 그룹 및 리소스를 만드는](deploy-to-subscription.md)합니다.

## <a name="deploy-local-template"></a>로컬 템플릿 배포

Azure에 리소스를 배포할 때 다음을 수행합니다.

1. Azure 계정에 로그인
2. 배포된 리소스에 대한 컨테이너 역할을 하는 리소스 그룹을 만듭니다. 리소스 그룹의 이름은 영숫자, 마침표, 밑줄, 하이픈 및 괄호만 포함할 수 있습니다. 최대 90자까지 가능합니다. 마침표로 끝날 수 없습니다.
3. 만들려는 리소스를 정의하는 템플릿을 리소스 그룹에 배포

템플릿에는 템플릿 배포를 사용자 지정할 수 있도록 하는 매개 변수가 포함될 수 있습니다. 예를 들어 특정 환경(예: 개발, 테스트 및 프로덕션)에 맞게 조정되는 값을 제공할 수 있습니다. 샘플 템플릿은 저장소 계정 SKU에 대한 매개 변수를 정의합니다. 

다음 예제에서는 리소스 그룹을 만들고 로컬 컴퓨터에서 템플릿을 배포합니다.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 메시지가 표시됩니다.

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>원격 템플릿 배포

로컬 컴퓨터에 Resource Manager 템플릿을 저장하는 대신, 외부 위치에 저장할 수 있습니다. 원본 제어 리포지토리(예: GitHub)에 템플릿을 저장할 수 있습니다. 또는 조직에서 공유 액세스에 대한 Azure Storage 계정에 저장할 수 있습니다.

외부 템플릿을 배포하려면 **template-uri** 매개 변수를 사용합니다. 예제의 URI를 사용하여 GitHub에서 샘플 템플릿을 배포합니다.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

앞의 예제에서는 템플릿에 중요한 데이터가 포함되어 있지 않으므로 대부분의 시나리오에 적합한 이 템플릿에 대해 공개적으로 액세스할 수 있는 URI가 필요합니다. 중요한 데이터(예: 관리자 암호)를 지정해야 하는 경우 해당 값을 안전한 매개 변수로 전달합니다. 그러나 템플릿에 공개적으로 액세스할 수 있도록 하지 않으려면 개인 저장소 컨테이너에 저장하여 보호할 수 있습니다. SAS(공유 액세스 서명) 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 개인 템플릿 배포](resource-manager-cli-sas-token.md)를 참조하세요.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Cloud Shell에서 다음 명령을 사용합니다.

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

이 기능은 라고도 *오류 발생 시 롤백*합니다. 배포가 실패하면 배포 기록에서 이전에 성공한 배포를 자동으로 다시 배포할 수 있습니다. 재배포를 지정하려면 배포 명령에 `--rollback-on-error` 매개 변수를 사용합니다. 이 기능은 인프라 배포에 대 한 알려진된 좋은 상태로 가져왔습니다이 되돌려야 할 경우에 유용 합니다. 주의 및 제한 사항이 있습니다.

- 재배포는 동일한 매개 변수를 사용 하 여 이전에 실행 했던 대로 실행 됩니다. 매개 변수를 변경할 수 없습니다.
- 이전 배포를 사용 하 여 실행 되는 [전체 모드](./deployment-modes.md#complete-mode)합니다. 이전 배포에 포함 되지 모든 리소스가 삭제 되 고 모든 리소스 구성을 이전 상태로 설정 됩니다. 완벽 하 게 이해 해야 합니다 [배포 모드](./deployment-modes.md)합니다.
- 리소스에만 영향을 줍니다 재배포, 데이터 변경에 영향을 받지 않습니다.
- 이 기능은 리소스 그룹 배포의 경우 구독 수준 배포에만 지원 됩니다. 구독 수준 배포에 대 한 자세한 내용은 참조 하세요. [구독 수준에서 리소스 그룹 및 리소스를 만드는](./deploy-to-subscription.md)합니다.

이 옵션을 사용하려면 배포가 배포 기록에서 식별될 수 있도록 고유한 이름을 지정해야 합니다. 고유한 이름이 없으면 현재 실패한 배포가 기록에서 이전에 성공한 배포를 덮어쓸 수 있습니다. 루트 수준 배포에만 이 옵션을 사용할 수 있습니다. 중첩된 템플릿의 배포는 다시 배포할 수 없습니다.

성공한 마지막 배포를 다시 배포하려면 `--rollback-on-error` 매개 변수를 플래그로 추가합니다.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

특정 배포를 다시 배포하려면 `--rollback-on-error` 매개 변수를 사용하고 배포의 이름을 제공합니다.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

지정된 배포는 분명히 성공했을 것입니다.

## <a name="parameters"></a>매개 변수

매개 변수 값을 전달하려면 인라인 매개 변수 또는 매개 변수 파일을 사용할 수 있습니다. 이 문서의 이전 예제에서는 인라인 매개 변수를 보여 줍니다.

### <a name="inline-parameters"></a>인라인 매개 변수입니다.

인라인 매개 변수를 전달하려면 `parameters`에 값을 제공합니다. 예를 들어 Bash 셸에서 문자열 및 배열을 템플릿에 전달하려면 다음을 사용합니다.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

파일의 콘텐츠를 가져와서 해당 콘텐츠를 인라인 매개 변수로 제공할 수도 있습니다.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

파일에서 매개 변수 값을 가져오면 구성 값을 제공해야 하는 경우에 유용합니다. 예를 들어 [Linux 가상 머신에 대한 Cloud-Init 값](../virtual-machines/linux/using-cloud-init.md)을 제공할 수 있습니다.

arrayContent.json 형식은 다음과 같습니다.

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>매개 변수 파일

매개 변수를 스크립트에 인라인 값으로 전달하는 것보다는, 매개 변수 값이 포함된 JSON 파일을 사용하는 것이 더 쉬울 수 있습니다. 매개 변수 파일은 로컬 파일이어야 합니다. 외부 매개 변수 파일은 Azure CLI에서 사용할 수 없습니다.

매개 변수 파일은 다음과 같은 형식이어야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

매개 변수 섹션에는 템플릿에 정의된 매개 변수(storageAccountType)와 일치하는 매개 변수 이름이 포함되어 있습니다. 매개 변수 파일에는 매개 변수의 값이 포함됩니다. 이 값은 배포 동안 템플릿에 자동으로 전달됩니다. 둘 이상의 매개 변수 파일을 만든 후 시나리오에 적합한 매개 변수 파일을 전달할 수 있습니다. 

앞의 예제를 복사하고 `storage.parameters.json`이라는 파일로 저장합니다.

로컬 매개 변수 파일을 전달하려면 `@`을 사용하여 storage.parameters.json이라는 로컬 파일을 지정합니다.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>매개 변수 우선 순위

동일한 배포 작업에서 인라인 매개 변수 및 로컬 매개 변수 파일을 사용할 수 있습니다. 예를 들어 로컬 매개 변수 파일에서 일부 값을 지정하고 배포하는 동안 인라인으로 다른 값을 추가할 수 있습니다. 로컬 매개 변수 파일 및 인라인에서 매개 변수에 대한 값을 제공하는 경우 인라인 값이 우선합니다.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>템플릿 배포 테스트

리소스를 실제로 배포하지 않고 템플릿과 매개 변수 값을 테스트하려면 [az 그룹 배포 유효성 검사](/cli/azure/group/deployment#az-group-deployment-validate)를 사용합니다. 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

오류가 감지되지 않으면 이 명령은 테스트 배포에 대한 정보를 반환합니다. 특히 **error** 값은 null입니다.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

오류가 감지되면 명령은 오류 메시지를 반환합니다. 예를 들어, 저장소 계정 SKU에 대해 잘못된 값을 전달하면 다음 오류가 반환됩니다.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

템플릿에 구문 오류가 있는 경우 명령은 템플릿을 구문 분석할 수 없다는 오류를 반환합니다. 메시지는 줄 번호 및 구문 분석 오류의 위치를 나타냅니다.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>다음 단계

- 이 문서의 예제에서는 리소스를 기본 구독의 리소스 그룹으로 배포합니다. 다른 구독을 사용하려면 [여러 Azure 구독 관리](/cli/azure/manage-azure-subscriptions-azure-cli)를 참조하세요.
- 리소스 그룹에 있지만 템플릿에 정의되지 않은 리소스를 처리하는 방법을 지정하려면 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.
- 템플릿에서 매개 변수를 정의하는 방법을 이해하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조하세요.
- 일반적인 배포 오류를 해결하는 방법은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](resource-manager-common-deployment-errors.md)을 참조하세요.
- SAS 토큰이 필요한 템플릿을 배포하는 데 관한 내용은 [SAS 토큰으로 개인 템플릿 배포](resource-manager-cli-sas-token.md)를 참조하세요.
- 둘 이상의 지역에서 서비스를 안전하게 출시하려면 [Azure Deployment Manager](deployment-manager-overview.md)를 참조하세요.
