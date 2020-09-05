---
title: 자습서 - terraform을 사용하여 Azure Spring Cloud 인스턴스 프로비저닝
description: Terraform을 사용하여 Azure Spring Cloud 인스턴스를 프로비저닝합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 70a9d9f8b137f02e647d1ea0edddd409ec3cdaf3
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079195"
---
# <a name="tutorial-provision-an-azure-spring-cloud-instance-with-terraform"></a>자습서: Terraform을 사용하여 Azure Spring Cloud 인스턴스 프로비저닝

이 자습서에서는 Terraform을 사용하여 Azure Spring Cloud 인스턴스를 만듭니다. 이 절차에서는 다음 리소스를 만드는 과정을 안내합니다.

> [!div class="checklist"]
> * 리소스 그룹
> * Azure Spring Cloud 인스턴스
> * Log Analytics용 Azure Storage

> [!NOTE]
> Terraform별 지원의 경우 HashiCorp의 커뮤니티 지원 채널 중 하나를 Terraform에 사용하세요.
>
> * 질문, 사용 사례 및 유용한 패턴: [HashiCorp 커뮤니티 포털의 Terraform 섹션](https://discuss.hashicorp.com/c/terraform-core)
> * 공급자 관련 질문: [HashiCorp 커뮤니티 포털의 Terraform 공급자 섹션](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>필수 구성 요소

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

## <a name="create-configuration-file"></a>구성 파일 만들기

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/app-service/quickstart-java#use-azure-cloud-shell)을 엽니다.

1. Cloud Shell 편집기를 시작합니다.

    ```bash
    code main.tf
    ```

1. 이 단계의 구성은 Azure 리소스 그룹 및 Azure Spring Cloud 인스턴스를 비롯한 Azure 리소스를 모델링합니다.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

## <a name="apply-the-configuration"></a>구성 적용

이 섹션에서는 여러 Terraform 명령을 사용하여 구성을 실행합니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html) 명령은 작업 디렉터리를 초기화합니다. Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    terraform init
    ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) 명령은 구성 구문의 유효성을 검사하는 데 사용됩니다. `-out` 매개 변수는 결과를 파일로 보냅니다. 나중에 이 출력 파일을 사용하여 구성을 적용할 수 있습니다. Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    terraform plan --out plan.out
    ```

1. [terraform apply](https://www.terraform.io/docs/commands/apply.html) 명령은 구성을 적용하는 데 사용됩니다. 이 명령은 이전 단계의 출력 파일을 지정합니다. 이 명령은 Azure 리소스를 만듭니다. Cloud Shell에서 다음 명령을 실행합니다.

    ```bash
    terraform apply plan.out
    ```

1. Azure Portal 내에서 결과를 확인하려면 새 리소스 그룹으로 이동합니다. 새 **Azure Cosmos DB 인스턴스**는 새 리소스 그룹에 표시됩니다.

## <a name="update-configuration-to-config-logs-and-metrics"></a>구성 로그 및 메트릭으로 구성 업데이트

이 섹션에서는 Azure Storage 계정을 사용하여 Azure Spring Cloud에 대한 로그 및 메트릭을 사용하도록 구성을 업데이트하는 방법을 보여줍니다.

1. Cloud Shell 편집기를 시작합니다.

    ```bash
    code main.tf
    ```

1. 파일 끝에 다음 구성을 추가합니다.

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. 파일을 저장( **&lt;Ctrl>S**)하고 편집기를 종료( **&lt;Ctrl>Q**)합니다.

1. 이전 섹션에서와 같이 다음 명령을 실행하여 변경합니다.

    ```bash
    terraform plan --out plan.out
    ```

1. `terraform apply` 명령을 실행하여 구성을 적용합니다.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 문서에서 만든 리소스를 삭제합니다.

[terraform destroy](https://www.terraform.io/docs/commands/destroy.html) 명령을 실행하여 이 자습서에서 만든 Azure 리소스를 제거합니다.

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Terraform을 설치하고 구성하여 Azure 리소스를 프로비저닝합니다](https://docs.microsoft.com/azure/developer/terraform/getting-started-cloud-shell).