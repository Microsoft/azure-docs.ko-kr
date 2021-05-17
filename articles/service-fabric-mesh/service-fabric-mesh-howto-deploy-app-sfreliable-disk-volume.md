---
title: Service Fabric Mesh를 사용하는 Service Fabric Reliable Disk 볼륨
description: Azure CLI를 사용하여 컨테이너 내부에서 볼륨에 기반한 Service Fabric Reliable Disk를 탑재하여 Azure Service Fabric Mesh 애플리케이션에 상태를 저장하는 방법을 알아봅니다.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: ac65693f2513338695e07cd8a19acb13333e7281
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625789"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Azure Service Fabric Mesh 애플리케이션에서 볼륨에 기반한 고가용성 Service Fabric Reliable Disk 탑재 

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

컨테이너 앱을 사용하여 상태를 유지하는 일반적인 방법은Azure File Storage와 같은 원격 스토리지 또는 Azure Cosmos DB와 같은 데이터베이스를 사용하는 것입니다. 이 경우 원격 저장소에 대한 상당한 읽기 및 쓰기 네트워크 지연이 발생합니다.

이 문서에서는 Service Fabric Mesh 애플리케이션의 컨테이너 내에서 볼륨을 탑재하여 고가용성 Service Fabric Reliable Disk에 상태를 저장하는 방법을 보여줍니다.
Service Fabric Reliable Disk는 고가용성 Service Fabric 클러스터 내에서 복제된 로컬 읽기 및 쓰기에 대한 볼륨을 제공합니다. 이렇게 하면 읽기에 대한 네트워크 호출을 제거하고 쓰기에 대한 네트워크 대기 시간이 줄어듭니다. 컨테이너가 다시 시작하거나 다른 노드로 이동할 경우 새 컨테이너 인스턴스가 기존 볼륨과 동일하게 표시됩니다. 따라서 효율적이고 가용성이 높습니다.

이 예제에서 카운터 애플리케이션에는 브라우저에 카운터 값을 표시하는 웹 페이지가 포함된 ASP.NET Core 서비스가 있습니다.

`counterService`는 주기적으로 파일에서 카운터 값을 읽고 이를 증분하여 파일에 다시 씁니다. 파일은 Service Fabric Reliable Disk에서 지원하는 볼륨에 탑재되는 폴더에 저장됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 작업을 완료할 수 있습니다. 이 문서에서 Azure CLI를 사용하려면 `az --version`이 `azure-cli (2.0.43)` 이상을 반환하는지 확인합니다.  다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치 또는 업데이트합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure에 로그인하고 구독을 선택합니다.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

애플리케이션을 배포할 리소스 그룹을 만듭니다. 다음 명령은 미국 동부 위치에서 이름이 `myResourceGroup`인 리소스 그룹을 만듭니다. 아래 명령에서 리소스 그룹 이름을 변경하는 경우, 다음에 나오는 모든 명령에서 리소스 그룹 이름을 변경해야 합니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>템플릿 배포

>[!NOTE]
> 2020년 11월 2일부터 Docker Free 계획 계정에서 Docker Hub에 대한 익명 및 인증된 요청에 [다운로드 속도 제한이 적용](https://docs.docker.com/docker-hub/download-rate-limit/)되며 IP 주소에 의해 적용됩니다. 
> 
> 이러한 템플릿은 Docker Hub의 퍼블릭 이미지를 사용합니다. 요금이 제한될 수 있다는 점에 유의하세요. 자세한 내용은 [Docker Hub를 사용하여 인증](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)을 참조하세요.

다음 명령은 [counter.sfreliablevolume.linux.json 템플릿](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json)을 사용하여 Linux 애플리케이션을 배포합니다. Windows 애플리케이션을 배포하려면 [counter.sfreliablevolume.windows.json 템플릿](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json)을 사용합니다. 컨테이너 이미지가 크면 배포 시간이 더 오래 걸릴 수 있습니다.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

명령을 사용하여 배포 상태를 확인할 수도 있습니다.

```azurecli-interactive
az deployment group show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

`Microsoft.ServiceFabricMesh/gateways`와 리소스 형식 같은 게이트웨이 리소스의 이름을 확인합니다. 이 앱의 공용 IP 주소 가져오는 데 사용됩니다.

## <a name="open-the-application"></a>애플리케이션을 엽니다.

애플리케이션이 성공적으로 배포되면 앱에 대한 게이트웨이 리소스의 ipAddress를 가져옵니다. 위의 섹션에서 알게 게이트웨이 이름을 사용합니다.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

출력에 서비스 엔드포인트에 대한 공용 IP 주소인 `ipAddress` 속성이 있어야 합니다. 브라우저에서 속성을 엽니다. 카운터 값이 1초마다 업데이트되는 웹 페이지가 표시됩니다.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>애플리케이션에서 볼륨을 사용할 수 있는지 확인

애플리케이션은 `counter/counterService` 폴더 내 볼륨에서 `counter.txt`라는 이름의 파일을 만듭니다. 이 파일의 콘텐츠는 웹 페이지에 표시되는 카운터 값입니다.

## <a name="delete-the-resources"></a>리소스 삭제

Azure에서 더 이상 사용하지 않는 리소스는 자주 삭제합니다. 이 예제와 관련한 리소스를 삭제하려면 다음 명령을 통해 배포된 리소스 그룹을 삭제합니다(리소스 그룹과 관련한 모든 항목 삭제).

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)에서 Service Fabric Reliable Volume Disk 샘플 애플리케이션을 봅니다.
- Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.