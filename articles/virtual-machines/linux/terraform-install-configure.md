---
title: Azure 리소스를 프로 비전 하기 위해 Terraform 설치 및 구성
description: Terraform을 설치하고 구성하여 Azure 리소스를 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: 02b6ddf723e47e4608e631aef96f8a12d5f68698
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369068"
---
# <a name="install-and-configure-terraform-to-provision-azure-resources"></a>Azure 리소스를 프로 비전 하기 위해 Terraform 설치 및 구성
 
Terraform은 [간단한 템플릿 언어](https://www.terraform.io/docs/configuration/syntax.html)를 사용하여 클라우드 인프라를 정의, 미리 보기 및 배포하는 쉬운 방법을 제공합니다. 이 문서에서는 Terraform을 사용하여 Azure의 리소스를 프로비전하는 데 필요한 단계를 설명합니다.

Azure에서 Terraform을 사용하는 방법에 대한 자세한 내용은 [Terraform 허브](/azure/terraform)를 참조하세요.
> [!NOTE]
> Terraform 특정 지원의 경우 커뮤니티 채널 중 하나를 사용 하 여 Terraform에 직접 문의 하세요.
>
>   • 커뮤니티 포털의 [Terraform 섹션](https://discuss.hashicorp.com/c/terraform-core) 에는 질문, 사용 사례 및 유용한 패턴이 포함 됩니다.
>
>   • 공급자 관련 질문에 대해서는 커뮤니티 포털의 [Terraform 공급자](https://discuss.hashicorp.com/c/terraform-providers) 섹션을 참조 하세요.



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform은 기본적으로 [Cloud Shell](/azure/terraform/terraform-cloud-shell)에 설치됩니다. Terraform을 로컬로 설치하려는 경우 다음 단계를 완료하고, 그렇지 않으면 [Azure에 대한 Terraform 액세스 설정](#set-up-terraform-access-to-azure)을 계속 진행합니다.

## <a name="install-terraform"></a>Terraform 설치

Terraform을 설치하려면 운영 체제에 적합한 패키지를 별도의 설치 디렉터리에 [다운로드](https://www.terraform.io/downloads.html)합니다. 다운로드에는 전역 경로를 정의해야 하는 단일 실행 파일이 포함됩니다. Linux 및 Mac에서 경로를 설정하는 방법에 대한 지침은 [이 웹 페이지](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)로 이동합니다. Windows에서 경로를 설정하는 방법에 대한 지침은 [이 웹 페이지](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)로 이동합니다.

`terraform` 명령을 사용하여 경로 구성을 확인합니다. 다음 예제 출력처럼 사용 가능한 Terraform 옵션 목록이 표시됩니다.

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Azure에 대한 Terraform 액세스 설정

Terraform에서 Azure로 리소스를 프로비전할 수 있도록 [Azure AD 서비스 사용자](/cli/azure/create-an-azure-service-principal-azure-cli)를 만듭니다. 서비스 사용자는 Azure 구독에서 리소스를 프로비전하는 권한을 Terraform 스크립트에 부여합니다.

여러 Azure 구독이 있는 경우 먼저 [az account list](/cli/azure/account#az-account-list) 를 사용 하 여 계정을 쿼리하여 구독 id 및 테넌트 id 값의 목록을 가져옵니다.

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

선택한 구독을 사용하려면 [az account set](/cli/azure/account#az-account-set) 명령을 사용하여 이 세션에 대한 구독을 설정합니다. 사용하려는 구독에서 반환된 `id` 필드 값을 보유하도록 `SUBSCRIPTION_ID` 환경 변수를 설정합니다.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

이제 Terraform에 사용할 서비스 사용자를 만들 수 있습니다. 다음과 같이 [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)를 사용하고, *범위*를 구독으로 설정합니다.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

`appId`, `password`, `sp_name` 및 `tenant`가 반환됩니다. `appId` 및 `password`를 기록해 둡니다.

## <a name="configure-terraform-environment-variables"></a>Terraform 환경 변수 구성

Azure AD 서비스 사용자를 사용하도록 Terraform을 구성하려면 다음 환경 변수를 설정합니다. 이 환경 변수는 [Azure Terraform 모듈](https://registry.terraform.io/modules/Azure)에 사용됩니다. Azure 공용이 아닌 Azure 클라우드에서 작업하는 경우 환경을 설정할 수도 있습니다.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

다음 샘플 셸 스크립트를 사용하여 해당 변수를 설정할 수 있습니다.

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>샘플 스크립트 실행

빈 디렉터리에 `test.tf` 파일을 만들고 다음 스크립트에 붙여넣습니다.

```hcl
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

파일을 저장하고 Terraform 배포를 초기화합니다. 이 단계에서는 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

```bash
terraform init
```

다음 예제와 유사하게 출력됩니다.

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

`terraform plan` 명령을 사용하면 Terraform 스크립트에서 완료할 작업을 미리 볼 수 있습니다. 리소스 그룹을 만들 준비가 완료되면 다음과 같이 Terraform 계획을 적용합니다.

```bash
terraform apply
```

다음 예제와 유사하게 출력됩니다.

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Terraform을 설치하고/Cloud Shell을 사용하여 Azure 자격 증명을 구성하고 Azure 구독에서 리소스 만들기를 시작했습니다. Azure에서 보다 완전한 Terraform 배포를 만들려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Terraform으로 Azure VM 만들기](terraform-create-complete-vm.md)
