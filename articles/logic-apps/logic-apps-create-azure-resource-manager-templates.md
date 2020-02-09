---
title: 배포용 논리 앱 템플릿 만들기
description: 에서 배포를 자동화 하기 위한 Azure Resource Manager 템플릿을 만드는 방법에 대해 알아봅니다 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972641"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 배포를 자동화 하는 Azure Resource Manager 템플릿 만들기

논리 앱 만들기 및 배포를 자동화 하는 데 도움이 되도록이 문서에서는 논리 앱에 대 한 [Azure Resource Manager 템플릿을](../azure-resource-manager/management/overview.md) 만들 수 있는 방법에 대해 설명 합니다. 워크플로 정의 및 배포에 필요한 기타 리소스를 포함 하는 템플릿의 구조 및 구문에 대 한 개요는 [개요: Azure Resource Manager 템플릿을 사용 하 여 논리 앱에 대 한 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)를 참조 하세요.

Azure Logic Apps는 다시 사용할 수 있는 [미리 빌드된 논리 앱 Azure Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) 제공 합니다 .이 템플릿을 사용 하 여 논리 앱을 만들 뿐만 아니라 배포에 사용할 리소스와 매개 변수를 정의할 수 있습니다. 이 템플릿을 자체 비즈니스 시나리오에 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> 템플릿의 연결에서 논리 앱과 동일한 Azure 리소스 그룹 및 위치를 사용 하는지 확인 합니다.

Azure Resource Manager 템플릿에 대 한 자세한 내용은 다음 항목을 참조 하세요.

* [Azure Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/templates/template-syntax.md)
* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/templates/template-syntax.md)
* [클라우드 일관성을 위한 Azure Resource Manager 템플릿 개발](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Visual Studio를 사용 하 여 템플릿 만들기

가장 쉬운 방법으로 배포할 준비가 된 매개 변수가 있는 논리 앱 템플릿을 만드는 가장 쉬운 방법은 Visual Studio (무료 Community edition 이상) 및 Visual Studio 용 Azure Logic Apps 도구를 사용 하는 것입니다. 그런 다음 [Visual studio에서 논리 앱을 만들거나](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [기존 논리 앱을 찾아 Azure Portal에서 visual studio로 다운로드할](../logic-apps/manage-logic-apps-with-visual-studio.md)수 있습니다.

논리 앱을 다운로드 하 여 논리 앱에 대 한 정의와 연결 등의 기타 리소스를 포함 하는 템플릿을 가져옵니다. 또한이 템플릿은 논리 앱 및 기타 리소스를 배포 하는 데 사용 되는 값에 대 한 매개 변수를 *매개 변수화*정의 합니다. 별도의 매개 변수 파일에서 이러한 매개 변수의 값을 제공할 수 있습니다. 이렇게 하면 배포 요구 사항에 따라 이러한 값을 보다 쉽게 변경할 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.

* [Visual Studio로 논리 앱 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Visual Studio에서 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 템플릿 만들기

[LogicAppTemplate 모듈과](https://github.com/jeffhollan/LogicAppTemplateCreator)함께 Azure PowerShell를 사용 하 여 리소스 관리자 템플릿을 만들 수 있습니다. 이 오픈 소스 모듈은 논리 앱 및 논리 앱이 사용 하는 모든 연결을 먼저 평가 합니다. 그런 다음 모듈은 배포에 필요한 매개 변수를 사용 하 여 템플릿 리소스를 생성 합니다.

예를 들어 Azure Service Bus 큐에서 메시지를 수신 하 고 Azure SQL database에 데이터를 업로드 하는 논리 앱이 있다고 가정 합니다. 모듈은 모든 오케스트레이션 논리를 보존 하 고 SQL 및 Service Bus 연결 문자열을 매개 변수화 하 여 배포 요구 사항에 따라 해당 값을 제공 하 고 변경할 수 있습니다.

다음 샘플에서는 Azure Resource Manager 템플릿, Azure DevOps Azure Pipelines 및 Azure PowerShell를 사용 하 여 논리 앱을 만들고 배포 하는 방법을 보여 줍니다.

* [샘플: Azure Logic Apps에서 Azure Service Bus 큐에 연결](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure Logic Apps에서 Azure Storage 계정에 연결](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure Logic Apps에 대 한 함수 앱 작업 설정](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure Logic Apps에서 통합 계정에 연결](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>PowerShell 모듈 설치

1. 아직 설치 하지 않은 경우 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)를 설치 합니다.

1. [PowerShell 갤러리](https://www.powershellgallery.com/packages/LogicAppTemplate)에서 LogicAppTemplate 모듈을 설치 하는 가장 쉬운 방법은 다음 명령을 실행 합니다.

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   최신 버전으로 업데이트 하려면 다음 명령을 실행 합니다.

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

또는 수동으로 설치 하려면 [논리 앱 템플릿 작성자](https://github.com/jeffhollan/LogicAppTemplateCreator)용 GitHub의 단계를 따르세요.

### <a name="install-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트 설치

LogicAppTemplate 모듈에서 모든 Azure 테넌트 및 구독 액세스 토큰을 사용 하려면 Azure Resource Manager API를 호출 하는 간단한 명령줄 도구인 [Azure Resource Manager 클라이언트 도구](https://github.com/projectkudu/ARMClient)를 설치 합니다.

이 도구를 사용 하 여 `Get-LogicAppTemplate` 명령을 실행 하면 명령은 먼저 ARMClient 도구를 통해 액세스 토큰을 가져오고, 토큰을 PowerShell 스크립트로 파이프 하 고, 템플릿을 JSON 파일로 만듭니다. 도구에 대 한 자세한 내용은 [Azure Resource Manager 클라이언트 도구에 대 한이 문서](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)를 참조 하세요.

### <a name="generate-template-with-powershell"></a>PowerShell을 사용 하 여 템플릿 생성

LogicAppTemplate 모듈을 설치 하 고 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)하 여 템플릿을 생성 하려면 다음 PowerShell 명령을 실행 합니다.

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

[Azure Resource Manager 클라이언트 도구의](https://github.com/projectkudu/ARMClient)토큰에서 파이프에 대 한 권장 사항을 따르려면 `$SubscriptionId` AZURE 구독 ID 인 대신이 명령을 실행 합니다.

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

추출 후에는 다음 명령을 실행 하 여 템플릿에서 매개 변수 파일을 만들 수 있습니다.

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Azure Key Vault 참조 (정적 전용)를 사용 하 여 추출 하려면 다음 명령을 실행 합니다.

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| 매개 변수 | 필수 | Description |
|------------|----------|-------------|
| TemplateFile | 예 | 템플릿 파일에 대 한 파일 경로입니다. |
| KeyVault | 아닙니다. | 가능한 키 자격 증명 모음 값을 처리 하는 방법을 설명 하는 열거형입니다. 기본값은 `None`입니다. |
||||

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 템플릿 배포](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
