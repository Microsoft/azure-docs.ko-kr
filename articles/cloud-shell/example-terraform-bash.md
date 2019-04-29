---
title: Azure Cloud Shell에서 Bash의 Terraform을 사용하여 배포 | Microsoft Docs
description: Azure Cloud Shell에서 Bash의 Terraform을 사용하여 배포
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: a08a4e7df6cf0493ab1aa6aced1abf888a61072a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119115"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Azure Cloud Shell에서 Bash의 Terraform을 사용하여 배포
이 문서에서는 Terraform [AzureRM 공급자](https://www.terraform.io/docs/providers/azurerm/index.html)로 리소스 그룹을 만드는 과정을 안내합니다. 

[Hashicorp Terraform](https://www.terraform.io/)은 API를 선언적 구성 파일로 체계화하는 공개 소스 도구로, 팀 구성원간에 공유하여 편집, 검토 및 버전 관리할 수 있습니다. Microsoft AzureRM 공급자는 AzureRM API를 통해 Azure Resource Manager에서 지원하는 리소스와 상호 작용하는 데 사용됩니다. 

## <a name="automatic-authentication"></a>자동 인증
Terraform은 기본적으로 Cloud Shell의 Bash에 설치됩니다. 또한 Cloud Shell은 기본 Azure CLI 구독을 자동으로 인증하여 Terraform Azure 모듈을 통해 리소스를 배포합니다.

Terraform은 설정된 기본 Azure CLI 구독을 사용합니다. 기본 구독을 업데이트하려면 다음을 실행합니다.

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>연습
### <a name="launch-bash-in-cloud-shell"></a>Cloud Shell의 Bash 시작
1. 원하는 위치에서 Cloud Shell 시작
2. 기본 구독이 설정되어 있는지 확인합니다.

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Terraform 템플릿 만들기
원하는 텍스트 편집기로 main.tf라는 이름의 새 Terraform 템플릿을 만듭니다.

```
vim main.tf
```

다음 코드를 Cloud Shell에 복사/붙여넣습니다.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

파일을 저장하고 텍스트 편집기를 종료합니다.

### <a name="terraform-init"></a>Terraform Init
`terraform init`를 실행하여 시작합니다.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

[terraform init 명령](https://www.terraform.io/docs/commands/init.html)은 Terraform 구성 파일을 포함하는 작업 디렉터리를 초기화하는 데 사용됩니다. `terraform init` 명령은 새 Terraform 구성을 쓰거나 버전 제어로부터 기존 인증서를 복제한 후 실행해야 하는 첫 번째 명령입니다. 이 명령을 여러 번 실행하는 것이 안전합니다.

### <a name="terraform-plan"></a>Terraform 계획
`terraform plan`을 사용하여 Terraform 템플릿에서 만들 리소스를 미리 봅니다.

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

[Terraform Plan 명령](https://www.terraform.io/docs/commands/plan.html)은 실행 계획을 만드는 데 사용됩니다. Terraform은 명시적으로 비활성화된 경우를 제외하고 새로 고침을 수행한 다음 구성 파일에 지정된 원하는 상태를 달성하는 데 필요한 작업을 결정합니다. 이 계획은 -out을 사용하여 저장한 다음 테라 폼에 적용하여 사전 계획된 작업만 실행되도록 할 수 있습니다.

### <a name="terraform-apply"></a>Terraform Apply
`terraform apply`을 사용하여 Azure 리소스를 프로비전합니다.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Terraform Apply 명령](https://www.terraform.io/docs/commands/apply.html)은 원하는 구성 상태에 도달하는 데 필요한 변경 사항을 적용하기 위해 사용됩니다.

### <a name="verify-deployment-with-azure-cli"></a>Azure CLI로 배포 확인
리소스가 프로비저닝에 성공했는지 확인하려면 `az group show -n myRgName`을 실행합니다.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Terraform 파괴로 정리
[Terraform destroy 명령](https://www.terraform.io/docs/commands/destroy.html)으로 만든 리소스 그룹을 정리하여 Terraform에서 만든 인프라를 정리합니다.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Terraform을 통해 Azure 리소스를 성공적으로 만들었습니다. Cloud Shell에 대해 계속 학습하려면 다음 단계를 방문합니다.

## <a name="next-steps"></a>다음 단계
[Terraform Azure 공급자에 대한 자세한 내용](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Cloud Shell의 Bash 빠른 시작](quickstart.md)
