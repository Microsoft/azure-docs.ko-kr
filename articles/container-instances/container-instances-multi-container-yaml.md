---
title: 자습서 - 다중 컨테이너 그룹 배포 - YAML
description: 이 자습서에서는 Azure CLI에서 YAML 파일을 사용하여 Azure Container Instances에서 여러 컨테이너가 있는 컨테이너 그룹을 배포하는 방법을 알아봅니다.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f6ba9152003c68192ddc0fdffa5652bc0b6daa86
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750909"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>자습서: YAML 파일을 사용하여 다중 컨테이너 그룹 배포

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [리소스 관리자](container-instances-multi-container-group.md)
>

Azure Container Instances에서는 [컨테이너 그룹](container-instances-container-groups.md)을 사용하여 여러 컨테이너를 단일 호스트에 배포하도록 지원합니다. 로깅, 모니터링 또는 서비스에 두 번째 연결된 프로세스가 필요한 기타 구성용으로 애플리케이션 사이드카를 빌드할 때 컨테이너 그룹을 사용하면 유용합니다.

이 자습서에서는 Azure CLI를 사용하여 [YAML 파일](container-instances-reference-yaml.md)을 배포하여 간단한 두 컨테이너 사이드카 구성을 실행하는 단계를 따릅니다. YAML 파일은 인스턴스 설정을 지정하기 위한 간결한 형식을 제공합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * YAML 파일 구성
> * 컨테이너 그룹 배포
> * 컨테이너 로그 보기

> [!NOTE]
> 현재 다중 컨테이너 그룹은 Linux 컨테이너에 제한됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>YAML 파일 구성

Azure CLI에서 [az container create][az-container-create] 명령을 사용하여 다중 컨테이너 그룹을 배포하려면 YAML 파일에서 컨테이너 그룹 구성을 지정해야 합니다. 그런 후 YAML 파일을 매개 변수로 명령에 전달합니다.

먼저 다음 YAML을 **deploy-aci.yaml**이라는 새 파일에 복사합니다. Azure Cloud Shell에서 Visual Studio Code를 사용하여 작업 디렉터리에 파일을 만들 수 있습니다.

```
code deploy-aci.yaml
```

이 YAML 파일은 두 개의 컨테이너, 하나의 공용 IP 주소, 두 개의 노출된 포트가 있는 “myContainerGroup”이라는 컨테이너 그룹을 정의합니다. 컨테이너는 퍼블릭 Microsoft 이미지에서 배포됩니다. 그룹의 첫 번째 컨테이너는 인터넷 연결 웹 애플리케이션을 실행합니다. 두 번째 컨테이너인 사이드카는 컨테이너 그룹의 로컬 네트워크를 통해 첫 번째 컨테이너에서 실행되는 웹 애플리케이션에 주기적으로 HTTP 요청을 보냅니다.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

프라이빗 컨테이너 이미지 레지스트리를 사용하려면 사용자 환경에 맞게 수정된 값을 사용하여 컨테이너 그룹에 `imageRegistryCredentials` 속성을 추가합니다.

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>컨테이너 그룹 배포

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az container create][az-container-create] 명령을 사용하여 컨테이너 그룹을 배포하고, YAML 파일을 인수로 전달합니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다.

## <a name="view-deployment-state"></a>배포 상태 확인

배포 상태를 확인하려면 [az container show][az-container-show] 명령을 사용합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

실행 중인 애플리케이션을 보려면 사용하는 브라우저에서 공용 IP 주소로 이동합니다. 예를 들어 이 예제 출력의 IP는 `52.168.26.124`입니다.

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>컨테이너 로그 보기

[az container logs][az-container-logs] 명령을 사용하여 컨테이너의 로그 출력을 확인합니다. `--container-name` 인수는 로그를 가져올 컨테이너를 지정합니다. 이 예제에서는 `aci-tutorial-app` 컨테이너를 지정합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

출력:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

사이드카 컨테이너의 로그를 보려면 `aci-tutorial-sidecar` 컨테이너를 지정하여 비슷한 명령을 실행합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

출력:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

위 출력에 나와 있는 것처럼, 사이드카는 그룹 로컬 네트워크를 통해 주 웹 애플리케이션에 대한 HTTP 요청을 주기적으로 수행하여 해당 애플리케이션이 실행되고 있는지를 확인합니다. `200 OK` 이외의 HTTP 응답 코드가 수신된 경우에는 이 사이드카 예제를 확장하여 경고를 트리거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 YAML 파일을 사용하여 Azure Container Instances에서 다중 컨테이너 그룹을 배포했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 다중 컨테이너 그룹에 대한 YAML 파일 구성
> * 컨테이너 그룹 배포
> * 컨테이너 로그 보기

[Resource Manager 템플릿](container-instances-multi-container-group.md)을 사용하여 다중 컨테이너 그룹을 지정할 수도 있습니다. 컨테이너 그룹을 사용하여 추가 Azure 서비스 리소스를 배포해야 하는 경우 시나리오에 맞게 Resource Manager 템플릿을 쉽게 조정할 수 있습니다.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
