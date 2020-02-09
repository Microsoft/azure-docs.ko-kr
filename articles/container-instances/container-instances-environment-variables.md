---
title: 컨테이너 인스턴스에서 환경 변수 설정
description: Azure Container Instances에서 실행하는 컨테이너에서 환경 변수를 설정하는 방법 알아보기
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: d12d3204740f2971216636f9f5dd6403b17ecbff
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483192"
---
# <a name="set-environment-variables-in-container-instances"></a>컨테이너 인스턴스에서 환경 변수 설정

컨테이너 인스턴스에서 환경 변수를 설정하면 컨테이너가 실행하는 애플리케이션 또는 스크립트의 동적 구성을 제공할 수 있습니다. 이는 `--env`에 대한 `docker run` 명령줄 인수와 유사합니다. 

컨테이너에서 환경 변수를 설정하려면 컨테이너 인스턴스를 만들 때 지정합니다. 이 문서에서는 [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)및 [Azure Portal](#azure-portal-example)를 사용 하 여 컨테이너를 시작할 때 환경 변수를 설정 하는 예제를 보여 줍니다. 

예를 들어 Microsoft [aci-wordcount][aci-wordcount] container 이미지를 실행 하는 경우 다음 환경 변수를 지정 하 여 동작을 수정할 수 있습니다.

*NumWords*: STDOUT으로 전송된 단어 수입니다.

*MinLength*: 계산되는 단어의 최소 문자 수입니다. 숫자가 높을수록 "of" 및 "the"와 같은 일반적인 단어를 무시합니다.

환경 변수로 비밀을 전달해야 하는 경우 Azure Container Instances는 Windows와 Linux 컨테이너 모두에 사용 가능한 [보안 값](#secure-values)을 지원합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI 예제

[Wordcount][aci-wordcount] 컨테이너의 기본 출력을 보려면 다음 [az container create][az-container-create] 명령을 사용 하 여 먼저 실행 합니다 (지정 된 환경 변수 없음).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

출력을 수정하려면 `--environment-variables`NumWords*및*MinLength*변수에 대한 값을 지정하여 추가된* 인수로 두 번째 컨테이너를 시작합니다. (이 예제에서는 Bash 셸 또는 Azure Cloud Shell에서 CLI를 실행하고 있다고 가정합니다. Windows 명령 프롬프트를 사용하는 경우 `--environment-variables "NumWords"="5" "MinLength"="8"`과 같이 큰 따옴표를 사용하여 변수를 지정합니다.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

두 컨테이너의 상태가 모두 *종료* 됨으로 표시 되 면 ( [az container show][az-container-show] 를 사용 하 여 상태 확인), 출력을 보려면 [az container logs][az-container-logs] 를 사용 하 여 로그를 표시 합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

컨테이너의 출력은 환경 변수를 설정하여 두 번째 컨테이너의 스크립트 동작을 수정한 방법을 보여줍니다.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell 예제

PowerShell에서 환경 변수를 설정하는 것은 CLI와 유사하지만 `-EnvironmentVariable` 명령줄 인수를 사용합니다.

먼저이 [AzContainerGroup][new-Azcontainergroup] 명령을 사용 하 여 기본 구성에서 [aci wordcount][aci-wordcount] 컨테이너를 시작 합니다.

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

이제 다음 [AzContainerGroup][new-Azcontainergroup] 명령을 실행 합니다. 이 명령은 배열 변수, *를 채운 후* NumWords*및*MinLength`envVars` 환경 변수를 지정합니다.

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

컨테이너 상태가 모두 *종료* 되 면 ( [AzContainerInstanceLog][azure-instance-log] 를 사용 하 여 상태 확인) [AzContainerInstanceLog][azure-instance-log] 명령을 사용 하 여 로그를 가져옵니다.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

각 컨테이너의 출력은 환경 변수를 설정하여 컨테이너에서 실행되는 스크립트를 수정한 방법을 보여줍니다.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal 예제

Azure Portal에서 컨테이너를 시작할 때 환경 변수를 설정 하려면 컨테이너를 만들 때 **고급** 페이지에서 지정 합니다.

1. **고급** 페이지에서 *실패 시* **다시 시작 정책을** 로 설정 합니다.
2. **환경 변수**에서 첫 번째 변수의 값으로 `5` `NumWords`을 입력 하 고 두 번째 변수에 대한 `8` 값을 사용 하 여 `MinLength`을 입력 합니다. 
1. **검토 + 만들기** 를 선택 하 여 컨테이너를 확인 한 다음 배포 합니다.

![환경 변수 사용 단추 및 텍스트 상자를 표시하는 포털 페이지][portal-env-vars-01]

컨테이너의 로그를 보려면 **설정** 에서 **컨테이너**, **로그**를 차례로 선택 합니다. 이전 CLI 및 PowerShell 섹션에서 표시된 출력과 유사하게 스크립트의 동작이 환경 변수에서 수정된 방법을 볼 수 있습니다. 각각이 8개 문자의 최소 길이로 5개의 단어만 표시됩니다.

![컨테이너 로그 출력을 표시하는 포털][portal-env-vars-02]

## <a name="secure-values"></a>보안 값

보안 값이 있는 개체는 애플리케이션에 대한 암호 또는 키와 같은 중요한 정보를 저장하는 데 사용됩니다. 환경 변수에 대한 보안 값을 사용하면 컨테이너의 이미지에 포함하는 것보다 더 안전하고 더 유연적입니다. 또 다른 옵션은 [Azure Container Instances에서 비밀 볼륨 탑재](container-instances-volume-secret.md)에서 설명한 비밀 볼륨을 사용하는 것입니다.

보안 값을 사용하는 환경 변수는 컨테이너의 속성에 표시되지 않으며, 컨테이너 내에서만 액세스할 수 있습니다. 예를 들어 Azure Portal 또는 Azure CLI에서 본 컨테이너 속성은 보안 변수의 이름만 표시하고 값을 표시하지 않습니다.

변수의 유형에 대한 일반 `secureValue` 대신 `value` 속성을 지정하여 보안 환경 변수를 설정합니다. 다음 YAML에서 정의된 두 개의 변수는 두 가지의 변수 유형을 설명합니다.

### <a name="yaml-deployment"></a>YAML 배포

다음 코드 조각이 포함된 `secure-env.yaml` 파일을 만듭니다.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

YAML을 사용하여 컨테이너 그룹을 배포하려면 다음 명령을 실행합니다(필요에 따라 리소스 그룹 이름을 조정).

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>환경 변수 확인

[Az container show][az-container-show] 명령을 실행 하 여 컨테이너의 환경 변수를 쿼리 합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON 응답은 안전하지 않은 환경 변수의 키와 값을 모두 표시하지만, 보안 환경 변수의 이름만 표시합니다.

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

실행 중인 컨테이너에서 명령을 실행할 수 있도록 하는 [az container exec][az-container-exec] 명령을 사용 하 여 보안 환경 변수가 설정 되었는지 확인할 수 있습니다. 다음 명령을 실행하여 컨테이너에서 대화형 Bash 세션을 시작합니다.

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

컨테이너 내에서 대화형 셸을 연 후에는 `SECRET` 변수의 값에 액세스할 수 있습니다.

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>다음 단계

여러 컨테이너로 큰 데이터 세트를 처리하는 일괄 처리와 같은 작업 기반 시나리오는 런타임 시 사용자 지정 환경 변수를 활용할 수 있습니다. 작업 기반 컨테이너를 실행 하는 방법에 대한 자세한 내용은 [컨테이너 화 된 작업을 다시 시작 정책으로 실행](container-instances-restart-policy.md)을 참조 하세요.

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
