---
title: 빠른 시작 - 레지스트리 만들기 - PowerShell
description: PowerShell을 사용하여 Azure Container Registry에서 프라이빗 Docker 레지스트리를 만드는 방법을 빠르게 알아봅니다.
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc, devx-track-azurepowershell
ms.openlocfilehash: 91d4209ccf558bf7c8038d8a753ec038428bc484
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076466"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 프라이빗 컨테이너 레지스트리 만들기

Azure Container Registry는 관리되는 프라이빗 Docker 컨테이너 레지스트리 서비스로, Docker 컨테이너 이미지를 빌드, 저장 및 제공하는 데 사용됩니다. 이 빠른 시작에서는 PowerShell을 사용하여 Azure 컨테이너 레지스트리를 만드는 방법을 알아봅니다. 그런 다음, Docker 명령을 사용하여 컨테이너 이미지를 레지스트리로 푸시하고, 마지막으로 레지스트리에서 이미지를 끌어와서 실행합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 빠른 시작에서는 Azure PowerShell 모듈이 필요합니다. 설치된 버전을 확인하려면 `Get-Module -ListAvailable Az` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 [macOS][docker-mac], [Windows][docker-windows] 및 [Linux][docker-linux] 시스템용 패키지를 제공합니다.

Azure Cloud Shell에는 필요한 Docker 구성 요소(`dockerd` 데몬) 중 일부가 없기 때문에 이 빠른 시작에 Cloud Shell을 사용할 수 없습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Connect-AzAccount][Connect-AzAccount] 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

Azure에 인증되었으면 [New-AzResourceGroup][New-AzResourceGroup]을 사용하여 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리적 컨테이너입니다.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>컨테이너 레지스트리 만들기

다음으로, [New-AzContainerRegistry][New-AzContainerRegistry] 명령을 사용하여 새 리소스 그룹에 컨테이너 레지스트리를 만듭니다.

레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 다음은 "myContainerRegistry007"이라는 레지스트리를 만드는 예제입니다. 명령에서 *myContainerRegistry007*을 원하는 대로 바꾸고 명령을 실행하여 레지스트리를 만듭니다.

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

이 빠른 시작에서는 Azure Container Registry에 대해 배우기 시작하는 개발자를 위해 비용 최적화된 옵션인 *기본* 레지스트리를 만듭니다. 사용 가능한 서비스 계층에 대한 자세한 내용은 [컨테이너 레지스트리 서비스 계층][container-registry-skus]을 참조하세요.

## <a name="log-in-to-registry"></a>레지스트리에 로그인

컨테이너 이미지를 푸시하고 끌어오려면 레지스트리에 로그인해야 합니다. 프로덕션 시나리오에서는 개별 ID 또는 서비스 주체를 사용하여 컨테이너 레지스트리에 액세스하지만, 이 빠른 시작에서는 간단한 진행을 위해 [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] 명령을 사용하여 레지스트리에서 관리 사용자를 활성화합니다.

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

다음으로, [docker login][docker-login]을 실행하여 로그인합니다.

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 리소스가 더 이상 필요 없으면 [Remove-AzResourceGroup][Remove-AzResourceGroup] 명령을 사용하여 리소스 그룹, 컨테이너 레지스트리 및 저장된 컨테이너 이미지를 제거합니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure Container Registry를 만들고, 컨테이너 이미지를 푸시하고, 레지스트리에서 이미지를 끌어와서 실행했습니다. Azure Container Registry 자습서를 계속 진행하여 ACR에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry 자습서][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry 작업 자습서][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
