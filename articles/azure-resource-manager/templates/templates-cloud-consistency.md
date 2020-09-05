---
title: 클라우드에서 템플릿 재사용
description: 서로 다른 클라우드 환경에서 일관되게 작동하는 Azure Resource Manager 템플릿을 개발합니다. Azure Stack을 위해 새 템플릿을 만들거나, 기존 템플릿을 업데이트합니다.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18
ms.openlocfilehash: 9355482c26cabb96fc6292bab5d542f36aec6a8c
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509759"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>클라우드 일관성을 위한 ARM 템플릿 개발

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Azure의 핵심 이점은 일관성입니다. 한 위치에 대한 개발 투자를 다른 위치에서 다시 사용할 수 있습니다. ARM (Azure Resource Manager) 템플릿을 통해 글로벌 Azure, Azure 소 버린 클라우드 및 Azure Stack를 비롯 한 여러 환경에서 배포가 일관적이 고 반복 가능 합니다. 그러나 클라우드 간에 템플릿을 재사용하려면 이 가이드에서 설명하는 클라우드 특정 종속성을 고려해야 합니다.

Microsoft는 여러 위치에서 다음을 비롯한 지능형 엔터프라이즈 지원 클라우드 서비스를 제공합니다.

* 전 세계 지역으로 확장되고 있는 Microsoft 관리 데이터 센터 네트워크에서 지원되는 전역 Azure 플랫폼
* Azure 독일, Azure Government, Azure 중국 21Vianet과 같은 격리 된 소 버린 클라우드. 소버린 클라우드는 전역 Azure 고객이 액세스할 수 있는 우수한 기능을 대부분 포함하는 일관성 있는 플랫폼을 제공합니다.
* 조직의 데이터 센터에서 Azure 서비스를 제공할 수 있게 해주는 하이브리드 클라우드 플랫폼인 Azure Stack 엔터프라이즈는 고유한 데이터 센터에서 Azure Stack을 설정하거나, 자체 시설(호스트된 지역이라고도 함)에서 Azure Stack을 실행하는 서비스 공급자의 Azure 서비스를 이용할 수 있습니다.

이러한 모든 클라우드의 핵심은, Azure Resource Manager가 제공하는 API를 통해 다양한 사용자 인터페이스와 Azure 플랫폼 간에 통신할 수 있다는 것입니다. 이 API는 강력한 코드 인프라 기능을 제공합니다. Azure 클라우드 플랫폼에서 사용할 수 있는 모든 리소스 종류는 Azure Resource Manager를 통해 배포 및 구성할 수 있습니다. 단일 템플릿을 사용하여 전체 애플리케이션을 배포하고 작동 종료 상태로 구성할 수 있습니다.

![Azure 환경](./media/templates-cloud-consistency/environments.png)

데이터 센터에서 전역 Azure, 소버린 클라우드, 호스트 클라우드 및 클라우드의 일관성을 유지하면 Azure Resource Manager를 활용하는 데 도움이 됩니다. 템플릿 기반 리소스 배포 및 구성을 설정할 때 이러한 클라우드 간에 개발 투자를 재사용할 수 있습니다.

그러나 전역 클라우드, 소버린 클라우드, 호스트 클라우드 및 하이브리드 클라우드가 일관성 있는 서비스를 제공한다 해도 모든 클라우드가 동일하지는 않습니다. 따라서 특정 클라우드에서만 사용 가능한 기능에 대한 종속성이 있는 템플릿을 만들 수 있습니다.

이 가이드의 나머지 부분에서는 Azure Stack에 대 한 기존 ARM 템플릿 새로 개발 또는 업데이트를 계획할 때 고려해 야 할 영역을 설명 합니다. 일반적으로 검사 목록에는 다음 항목이 포함되어야 합니다.

* 템플릿의 함수, 엔드포인트, 서비스 및 기타 리소스를 대상 배포 위치에서 사용할 수 있는지 확인합니다.
* 중첩된 템플릿 및 구성 아티팩트를 액세스 가능한 위치에 저장하여 클라우드 간에 액세스를 보장합니다.
* 링크 및 요소를 하드 코딩하는 대신 동적 참조를 사용합니다.
* 사용하는 템플릿 매개 변수가 대상 클라우드에서 작동하는지 확인합니다.
* 대상 클라우드에서 리소스 특정 속성을 사용할 수 있는지 확인합니다.

ARM 템플릿에 대 한 소개는 [템플릿 배포](overview.md)를 참조 하세요.

## <a name="ensure-template-functions-work"></a>템플릿 함수가 작동하는지 확인

ARM 템플릿의 기본 구문은 JSON입니다. 템플릿은 JSON의 상위 집합을 사용하여 식과 함수로 구문을 확장합니다. 템플릿 언어 프로세서는 추가 템플릿 함수를 지원하기 위해 자주 업데이트됩니다. 사용 가능한 템플릿 함수에 대 한 자세한 설명은 [ARM 템플릿 함수](template-functions.md)를 참조 하세요.

Azure Resource Manager에 도입된 새 템플릿 함수는 소버린 클라우드 또는 Azure Stack에서 즉시 사용할 수 없습니다. 템플릿을 성공적으로 배포하려면 템플릿에 참조된 모든 함수를 대상 클라우드에서 사용할 수 있어야 합니다.

Azure Resource Manager 기능은 항상 전역 Azure에 먼저 도입됩니다. 다음 PowerShell 스크립트를 사용하여 새로 도입된 템플릿 함수를 Azure Stack에서도 사용할 수 있는지 여부를 확인할 수 있습니다.

1. GitHub 리포지토리의 복제본을 만듭니다 [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. 리포지토리의 로컬 복제본이 있으면 PowerShell을 사용하여 대상의 Azure Resource Manager에 연결합니다.

1. psm1 모듈을 가져오고 Test-AzureRmTemplateFunctions cmdlet을 실행합니다.

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

스크립트는 각각 고유한 템플릿 함수만 포함하는 여러 개의 최소화된 템플릿을 배포합니다. 스크립트 출력은 지원되는 템플릿 함수와 사용할 수 없는 템플릿 함수를 보고합니다.

## <a name="working-with-linked-artifacts"></a>연결된 아티팩트 작업

템플릿은 연결된 아티팩트에 대한 참조와, 다른 템플릿에 연결하는 배포 리소스를 포함할 수 있습니다. 연결된 템플릿(중첩된 템플릿이라고도 함)은 런타임에 리소스 관리자에 의해 검색됩니다. 템플릿에 VM(가상 머신) 확장의 아티팩트에 대한 참조가 포함될 수도 있습니다. 이러한 아티팩트는 템플릿 배포 중 VM 확장의 구성을 위해 VM 내부에서 실행되는 VM 확장에 의해 검색됩니다.

다음 섹션에서는 기본 배포 템플릿 외부에 있는 아티팩트를 포함하는 템플릿을 개발할 때 클라우드 일관성을 위해 고려할 사항을 설명합니다.

### <a name="use-nested-templates-across-regions"></a>지역 간에 중첩된 템플릿 사용

템플릿은 각각 특정 용도가 있으며 배포 시나리오 간에 재사용할 수 있는 작은 재사용 가능 템플릿으로 분해할 수 있습니다. 배포를 실행하려면 기본 또는 마스터 템플릿으로 알려진 단일 템플릿을 지정합니다. 가상 네트워크, VM, 웹앱 등 배포할 리소스를 지정합니다. 기본 템플릿은 다른 템플릿에 대한 링크를 포함할 수도 있습니다. 즉, 템플릿을 중첩할 수 있습니다. 마찬가지로, 중첩된 템플릿은 다른 템플릿에 대한 링크를 포함할 수 있습니다. 최대 5개 수준의 깊이까지 중첩할 수 있습니다.

다음 코드는 templateLink 매개 변수가 중첩된 템플릿을 참조하는 방법을 보여 줍니다.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager는 런타임에 기본 템플릿을 평가하고, 중첩된 각 템플릿을 검색 및 평가합니다. 중첩된 템플릿이 모두 검색되면 템플릿이 평면화되고 추가 처리가 시작됩니다.

### <a name="make-linked-templates-accessible-across-clouds"></a>연결된 템플릿을 클라우드 간에 액세스할 수 있도록 설정

사용하는 연결된 템플릿을 저장하는 방법 및 위치를 고려합니다. 런타임에 Azure Resource Manager가 연결된 템플릿을 모두 페치하므로, 직접 액세스할 수 있어야 합니다. 일반적인 방법은 GitHub를 사용하여 중첩된 템플릿을 저장하는 것입니다. GitHub 리포지토리는 URL을 통해 공개적으로 액세스할 수 있는 파일을 포함할 수 있습니다. 이 방법은 퍼블릭 클라우드 및 소버린 클라우드에서 효과적이지만, Azure Stack 환경이 아웃바운드 인터넷 액세스 없이 회사 네트워크 또는 연결이 끊긴 원격 위치에 있을 수도 있습니다. 이러한 경우, Azure Resource Manager가 중첩된 템플릿을 검색하지 못합니다.

클라우드 간 배포에 더 효과적인 방법은 대상 클라우드가 액세스할 수 있는 위치에 연결된 템플릿을 저장하는 것입니다. CI/CD(지속적인 통합/지속적인 업데이트) 파이프라인에서 모든 배포 아티팩트를 유지 관리하고 배포하는 것이 이상적입니다. 또는 Azure Resource Manager가 검색할 수 있는 Blob Storage 컨테이너에 중첩된 템플릿을 저장할 수 있습니다.

각 클라우드의 Blob Storage는 서로 다른 엔드포인트 FQDN(정규화된 도메인 이름)을 사용하므로 연결된 템플릿의 위치를 두 매개 변수로 지정하여 템플릿을 구성합니다. 매개 변수는 배포 시 사용자 입력을 수락할 수 있습니다. 일반적으로 템플릿은 여러 사용자가 작성해서 공유하므로, 이러한 매개 변수에 대해 표준 이름을 사용하는 것이 좋습니다. 명명 규칙을 통해 지역, 클라우드 및 작성자 간에 템플릿 재사용을 더 용이하게 만들 수 있습니다.

다음 코드에서 `_artifactsLocation`은 모든 배포 관련 아티팩트를 포함하는 단일 위치를 가리키는 데 사용됩니다. 기본값이 제공되었습니다. 배포 시 `_artifactsLocation`에 대해 입력 값이 지정되지 않으면 기본값이 사용됩니다. `_artifactsLocationSasToken`이 `sasToken`에 대한 입력으로 사용됩니다. `_artifactsLocation`이 보안되지 않는 시나리오(예: 공용 GitHub 리포지토리)에서는 기본값이 빈 문자열이어야 합니다.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

템플릿 전체에서 링크는 기본 URI(`_artifactsLocation` 매개 변수)와 아티팩트 상대 경로 및 `_artifactsLocationSasToken`을 결합하여 생성됩니다. 다음 코드는 uri 템플릿 함수를 사용하여 중첩된 템플릿에 대한 링크를 지정하는 방법을 보여 줍니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

이 방법을 사용하면 `_artifactsLocation` 매개 변수의 기본값이 사용됩니다. 다른 위치에서 연결된 템플릿을 검색해야 하는 경우, 배포 시 매개 변수 입력을 사용하여 기본값을 재정의할 수 있습니다. 템플릿 자체는 변경할 필요가 없습니다.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>링크를 하드 코딩하는 대신 _artifactsLocation 사용

중첩된 템플릿에 사용되는 것 외에도 `_artifactsLocation` 매개 변수의 URL은 배포 템플릿의 모든 관련 아티팩트에 대한 기준으로 사용됩니다. 일부 VM 확장에는 템플릿 외부에서 저장된 스크립트에 대한 링크가 포함되어 있습니다. 이러한 확장의 경우, 링크를 하드 코딩하면 안 됩니다. 예를 들어, 사용자 지정 스크립트 및 PowerShell DSC 확장은 다음과 같이 GitHub의 외부 스크립트에 연결될 수 있습니다.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

스크립트에 대한 링크를 하드 코딩하면 템플릿이 다른 위치에 배포되지 않을 수 있습니다. VM 리소스 구성 중에 VM 내부에서 실행 중인 VM 에이전트는 VM 확장에 연결된 모든 스크립트의 다운로드를 시작한 다음, VM의 로컬 디스크에 스크립트를 저장합니다. 이 방법은 앞의 “지역 간에 중첩된 템플릿 사용” 섹션에서 설명한 중첩된 템플릿 링크처럼 작동합니다.

리소스 관리자는 런타임에 중첩된 템플릿을 검색합니다. VM 확장의 경우, VM 에이전트가 외부 아티팩트 검색을 수행합니다. 아티팩트 검색의 개시 장치가 다르다는 점을 제외하면 템플릿 정의의 솔루션은 동일합니다. VM 확장 스크립트를 포함한 모든 아티팩트가 저장되는 기본 경로인 기본값이 적용된 _artifactsLocation 매개 변수와 sasToken에 대한 입력을 제공하는 `_artifactsLocationSasToken` 매개 변수를 사용합니다.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

아티팩트의 절대 URI를 구성하려는 경우, concat 템플릿 함수 대신 uri 템플릿 함수를 사용하는 것이 좋습니다. VM 확장의 스크립트에 대한 하드 코딩된 링크를 uri 템플릿 함수로 바꾸면 템플릿의 이 기능이 클라우드 일관성을 위해 구성됩니다.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

이 방법을 사용하면 구성 스크립트를 포함한 모든 배포 아티팩트를 템플릿 자체와 동일한 위치에 저장할 수 있습니다. 모든 링크의 위치를 변경하려면 _artifactsLocation 매개 변수_에 대해 다른 기준 URL을 지정하기만 하면 됩니다.

## <a name="factor-in-differing-regional-capabilities"></a>지역별 기능 차이 고려

Azure에 도입되는 새로운 서비스 및 업데이트의 민첩한 개발 및 지속적인 흐름 때문에 서비스나 업데이트의 가용성은 [지역에 따라 다를 수 있습니다](https://azure.microsoft.com/regions/services/). 엄격한 내부 테스트를 수행한 후 기존 서비스에 대한 업데이트 또는 새로운 서비스는 일반적으로 유효성 검사 프로그램에 참여하는 소규모 대상 고객에게 도입됩니다. 고객 유효성 검사에 성공하면 서비스 또는 업데이트가 일부 Azure 지역에서만 제공된 다음, 더 많은 지역에 도입되고 소버린 클라우드에 배포되며 Azure Stack 고객에게도 제공될 수 있습니다.

Azure 지역 및 클라우드에 따라 사용 가능한 서비스가 다를 수 있음을 알고 있으면 템플릿에 대해 몇 가지 사전 대응형 결정을 내릴 수 있습니다. 먼저 클라우드에 사용할 수 있는 리소스 공급자를 조사하는 것이 좋습니다. 리소스 공급자는 Azure 서비스에 사용할 수 있는 리소스 및 작업 집합을 알려 줍니다.

템플릿은 리소스를 배포하고 구성합니다. 리소스 종류는 리소스 공급자가 제공합니다. 예를 들어, 컴퓨팅 리소스 공급자(Microsoft.Compute)는 virtualMachines, availabilitySets 등의 여러 리소스 종류를 제공합니다. 각 리소스 공급자가 공통 계약에 정의된 Azure Resource Manager용 API를 제공하므로 모든 리소스 공급자에서 일관성 있는 통합 작성 환경을 사용할 수 있습니다. 그러나 전역 Azure에서 사용할 수 있는 리소스 공급자를 소버린 클라우드 또는 Azure Stack 지역에서는 사용하지 못할 수도 있습니다.

![리소스 공급자](./media/templates-cloud-consistency/resource-providers.png)

지정된 클라우드에서 사용할 수 있는 리소스 공급자를 확인하려면 Azure [CLI](/cli/azure/install-azure-cli)(명령줄 인터페이스)에서 다음 스크립트를 실행합니다.

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

다음 PowerShell cmdlet을 사용하여 사용 가능한 리소스 공급자를 확인할 수도 있습니다.

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>모든 리소스 종류의 버전 확인

속성 집합은 모든 리소스 종류에 공통적이지만, 리소스마다 고유한 특정 속성도 있습니다. 새 API 버전을 통해 때때로 새로운 기능 및 관련 속성이 기존 리소스 종류에 추가됩니다. 템플릿의 리소스에는 고유한 API 버전 속성(`apiVersion`)이 있습니다. 이 버전 관리는 템플릿의 기존 리소스 구성이 플랫폼의 변경 내용에 영향을 받지 않도록 합니다.

전역 Azure의 기존 리소스 종류에 도입된 새 API 버전을 모든 지역, 소버린 클라우드 또는 Azure Stack에서 즉시 사용할 수 있는 것은 아닙니다. 클라우드에 사용할 수 있는 리소스 공급자, 리소스 종류 및 API 버전 목록을 보려면 Azure Portal에서 리소스 탐색기를 사용합니다. 모든 서비스 메뉴에서 리소스 탐색기를 검색합니다. 리소스 탐색기의 공급자 노드를 확장하여 해당 클라우드에서 사용 가능한 모든 리소스 공급자, 해당 리소스 종류 및 API 버전을 반환합니다.

Azure CLI에서 지정된 클라우드의 모든 리소스 종류에 사용할 수 있는 API 버전을 나열하려면 다음 스크립트를 실행합니다.

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

다음 PowerShell cmdlet을 사용할 수도 있습니다.

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>매개 변수를 사용하여 리소스 위치 참조

템플릿은 항상 지역에 있는 리소스 그룹에 배포됩니다. 배포 자체 외에도, 템플릿의 각 리소스에는 배포할 지역을 지정하는 데 사용하는 위치 속성이 있습니다. 클라우드 일관성을 위한 템플릿을 개발하려면 각 Azure Stack에 고유한 위치 이름이 포함될 수 있으므로 리소스 위치를 참조하는 동적 방법이 필요합니다. 일반적으로 리소스는 리소스 그룹과 동일한 지역에 배포되지만, 지역 간 애플리케이션 가용성 등의 시나리오를 지원하려면 전체 지역에 리소스를 분산하는 것이 유용할 수 있습니다.

템플릿에서 리소스 속성을 지정할 때 지역 이름을 하드 코딩할 수는 있지만, 이 방법을 사용할 경우, 다른 Azure Stack 환경에는 지역 이름이 존재하지 않을 가능성이 크기 때문에 해당 템플릿을 배포하지 못할 수 있습니다.

여러 지역을 수용하려면 입력 매개 변수 위치를 기본값과 함께 템플릿에 추가합니다. 배포 중에 값이 지정되지 않으면 기본값이 사용됩니다.

템플릿 함수 `[resourceGroup()]`은 다음 키/값 쌍이 포함된 개체를 반환합니다.

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

입력 매개 변수의 defaultValue에서 개체의 위치 키를 참조하면 Azure Resource Manager는 런타임에 `[resourceGroup().location]` 템플릿 함수를 템플릿이 배포된 리소스 그룹의 위치 이름으로 바꿉니다.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

이 템플릿 함수를 사용하면 지역 이름을 미리 알지 못해도 모든 클라우드에 템플릿을 배포할 수 있습니다. 또한 템플릿의 특정 리소스 위치가 리소스 그룹 위치와 다를 수 있습니다. 이 경우, 동일한 템플릿의 다른 리소스는 초기 위치 입력 매개 변수를 계속 사용하는 반면, 해당 특정 리소스에 대해 추가 입력 매개 변수를 사용하여 이 설정을 구성할 수 있습니다.

### <a name="track-versions-using-api-profiles"></a>API 프로필을 사용하여 버전 추적

Azure Stack에 있는 사용 가능한 모든 리소스 공급자 및 관련 API 버전을 추적하는 것은 매우 어려울 수 있습니다. 예를 들어, 이 문서를 작성할 당시, Azure의 **Microsoft.Compute/availabilitySets**에 대한 최신 API 버전은 `2018-04-01`이고 Azure 및 Azure Stack에 공통적으로 사용 가능한 API 버전은 `2016-03-30`입니다. 모든 Azure 및 Azure Stack 위치에서 공유되는, **Microsoft.Storage/storageAccounts**에 대한 공통 API 버전은 `2016-01-01`이고 Azure의 최신 API 버전은 `2018-02-01`입니다.

따라서 리소스 관리자는 템플릿에 API 프로필 개념을 도입했습니다. API 프로필이 없으면 템플릿의 각 리소스가 해당 특정 리소스의 API 버전을 설명하는 `apiVersion` 요소로 구성됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

API 프로필 버전은 Azure 및 Azure Stack에 공통적인 리소스 종류당 단일 API 버전의 별칭으로 사용됩니다. 템플릿의 각 리소스에 대해 API 버전을 지정하는 대신, `apiProfile`이라는 새 루트 요소에만 API 프로필 버전을 지정하고 개별 리소스에 대한 `apiVersion` 요소를 생략합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

API 프로필을 통해 전체 위치에서 API 버전을 사용할 수 있으므로, 특정 위치에서 사용할 수 있는 apiVersions를 수동으로 확인할 필요가 없습니다. API 프로필에서 참조하는 API 버전이 Azure Stack 환경에 있도록 하려면 Azure Stack 운영자가 지원 정책에 따라 솔루션을 최신 상태로 유지해야 합니다. 6개월 이상 업데이트하지 않은 시스템 버전은 비규격으로 간주되며, Azure Stack 환경을 업데이트해야 합니다.

API 프로필은 템플릿의 필수 요소가 아닙니다. 요소를 추가해도, `apiVersion`이 지정되지 않은 리소스에만 사용됩니다. 이 요소는 점진적인 변경을 허용하지만, 기존 템플릿을 변경할 필요는 없습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>엔드포인트 참조 확인

리소스에 플랫폼의 다른 서비스에 대한 참조가 포함될 수 있습니다. 예를 들어, 공용 IP에는 공용 DNS 이름이 할당될 수 있습니다. 퍼블릭 클라우드, 소버린 클라우드 및 Azure Stack 솔루션에는 고유한 엔드포인트 네임스페이스가 있습니다. 대부분의 경우, 템플릿에서 필요한 리소스에 대한 입력은 접두사뿐입니다. 런타임에 Azure Resource Manager가 엔드포인트 값을 접두사에 추가합니다. 일부 엔드포인트 값은 템플릿에서 명시적으로 지정해야 합니다.

> [!NOTE]
> 클라우드 일관성을 위한 템플릿을 개발하려면 엔드포인트 네임스페이스를 하드 코딩하면 안 됩니다.

다음 두 예는 리소스를 만들 때 명시적으로 지정해야 하는 공통 엔드포인트 네임스페이스입니다.

* 스토리지 계정(Blob, 큐, 테이블 및 파일)
* 데이터베이스 및 Azure Cache for Redis에 대한 연결 문자열

배포가 완료되면 사용자를 위한 정보로 템플릿 출력에 엔드포인트 네임스페이스를 사용할 수도 있습니다. 다음은 일반적인 예입니다.

* 스토리지 계정(Blob, 큐, 테이블 및 파일)
* 연결 문자열(MySql, SQLServer, SQLAzure, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* 공용 IP 주소의 domainNameLabel
* Cloud Services

일반적으로, 템플릿에 하드 코딩된 엔드포인트를 사용하면 안 됩니다. 모범 사례는 reference 템플릿 함수를 사용하여 엔드포인트를 동적으로 검색하는 것입니다. 예를 들어, 가장 일반적으로 하드 코딩되는 엔드포인트는 스토리지 계정의 엔드포인트 네임스페이스입니다. 각 스토리지 계정에는 스토리지 계정 이름과 엔드포인트 네임스페이스를 연결하여 생성된 고유 FQDN이 있습니다. mystorageaccount1이라는 Blob Storage 계정은 클라우드에 따라 다른 FQDN을 생성합니다.

* 전역 Azure 클라우드에 만든 경우, **mystorageaccount1.blob.core.windows.net**
* Azure 중국 21Vianet 클라우드에서 만든 **mystorageaccount1.blob.core.chinacloudapi.cn** .

다음 reference 템플릿 함수는 스토리지 리소스 공급자에서 엔드포인트 네임스페이스를 검색합니다.

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

스토리지 계정 엔드포인트의 하드 코딩된 값을 `reference` 템플릿 함수로 바꾸면 엔드포인트 참조를 변경하지 않고도 동일한 템플릿을 사용하여 다른 환경에 배포할 수 있습니다.

### <a name="refer-to-existing-resources-by-unique-id"></a>고유 ID로 기존 리소스 참조

동일한 클라우드의 동일한 테넌트에 있는 동일한 구독 또는 다른 구독과 동일한 리소스 그룹 또는 다른 리소스 그룹의 기존 리소스를 참조할 수도 있습니다. 리소스 속성을 검색하려면 리소스 자체의 고유 식별자를 사용해야 합니다. 다음 코드에서 볼 수 있듯이, `resourceId` 템플릿 함수는 SQL Server 같은 리소스의 고유 ID를 검색합니다.

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

그런 다음, `reference` 템플릿 함수 내에서 `resourceId` 함수를 사용하여 데이터베이스 속성을 검색할 수 있습니다. 반환 개체에는 전체 엔드포인트 값이 포함된 `fullyQualifiedDomainName` 속성이 있습니다. 이 값은 런타임에 검색되며, 클라우드 환경 특정 엔드포인트 네임스페이스를 제공합니다. 엔드포인트 네임스페이스를 하드 코딩하지 않고 연결 문자열을 정의하려면 다음과 같이 연결 문자열에서 직접 반환 개체의 속성을 참조할 수 있습니다.

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>리소스 속성 고려

Azure Stack 환경의 특정 리소스에는 템플릿에서 고려해야 하는 고유한 속성이 있습니다.

### <a name="ensure-vm-images-are-available"></a>VM 이미지를 사용할 수 있는지 확인

Azure는 다양한 VM 이미지를 제공합니다. Microsoft 및 파트너가 이러한 이미지를 만들고 배포 준비를 합니다. 이미지는 플랫폼에서 VM의 토대를 형성합니다. 그러나 클라우드 일치 템플릿은 전역 Azure, Azure 소버린 클라우드 또는 Azure Stack 솔루션에서 사용할 수 있는 VM 이미지의 사용 가능한 매개 변수(특히 publisher, offer 및 SKU)만 참조해야 합니다.

특정 위치에서 사용 가능한 VM 이미지 목록을 검색하려면 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az vm image list -all
```

`-Location` 매개 변수를 통해 원하는 위치를 지정하여 Azure PowerShell cmdlet [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher)를 사용하면 동일한 목록을 검색할 수 있습니다. 예를 들어:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

이 명령은 몇 분 후에 전역 Azure 클라우드의 서유럽 지역에서 사용 가능한 모든 이미지를 반환합니다.

Azure Stack이 이러한 VM 이미지를 사용할 수 있도록 설정하면 사용 가능한 모든 스토리지가 사용됩니다. 가장 작은 배율 단위까지 수용하기 위해, Azure Stack을 사용하여 환경에 추가할 이미지를 선택할 수 있습니다.

다음 코드 샘플에서는 ARM 템플릿에서 게시자, 제품 및 SKU 매개 변수를 참조 하는 일관 된 접근 방식을 보여 줍니다.

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>로컬 VM 크기 확인

클라우드 일관성을 위한 템플릿을 개발하려면 원하는 VM 크기를 모든 대상 환경에서 사용할 수 있도록 해야 합니다. VM 크기는 성능 특성 및 기능의 그룹화입니다. 일부 VM 크기는 VM이 실행되는 하드웨어에 따라 달라집니다. 예를 들어, GPU 최적화 VM을 배포하려면 하이퍼바이저를 실행하는 하드웨어에 하드웨어 GPU가 있어야 합니다.

Microsoft에서 특정 하드웨어 종속성이 있는 새 VM 크기를 도입하는 경우, 해당 VM 크기는 일반적으로 Azure 클라우드의 일부 지역에서만 먼저 제공된 다음, 나중에 다른 지역 및 클라우드에도 제공됩니다. 배포할 각 클라우드에 VM 크기가 있는지 확인하려면 다음 Azure CLI 명령을 통해 사용 가능한 크기를 검색합니다.

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Azure PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

사용 가능한 서비스의 전체 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)을 참조하세요.

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Azure Stack에서 Azure Managed Disks 사용 확인

관리 디스크는 Azure 테넌트의 스토리지를 처리합니다. 명시적으로 스토리지 계정을 만들고 VHD(가상 하드 디스크)의 URI를 지정하는 대신, VM을 배포할 때 관리 디스크를 사용하여 암시적으로 이러한 작업을 수행할 수 있습니다. 관리 디스크는 동일한 가용성 집합에 있는 VM의 모든 디스크를 여러 다른 스토리지 단위에 배치하여 가용성을 향상합니다. 또한 훨씬 짧은 가동 중지 시간으로 기존 VHD를 표준 스토리지에서 Premium Storage로 변환할 수 있습니다.

관리 디스크는 Azure Stack에 대한 로드맵에 있지만 현재 지원되지 않습니다. 지원될 때까지, 다음과 같이 VM 리소스에 대한 템플릿에서 `vhd` 요소를 통해 명시적으로 VHD를 지정하면 Azure Stack용 클라우드 일치 템플릿을 개발할 수 있습니다.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

반면, 템플릿에서 관리 디스크 구성을 지정하려면 디스크 구성에서 `vhd` 요소를 제거합니다.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

동일한 변경 내용이 [데이터 디스크](../../virtual-machines/using-managed-disks-template-deployments.md)에도 적용됩니다.

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Azure Stack에서 VM 확장을 사용할 수 있는지 확인

클라우드 일관성을 위한 또 다른 고려 사항은 [가상 머신 확장](../../virtual-machines/extensions/features-windows.md)을 사용하여 VM 내의 리소스를 구성하는 것입니다. Azure Stack에서 모든 VM 확장을 사용할 수 있는 것은 아닙니다. 템플릿은 VM 확장 전용 리소스를 지정하고, 템플릿 내에서 종속성과 조건을 만들 수 있습니다.

예를 들어, Microsoft SQL Server를 실행하는 VM을 구성하려는 경우, VM 확장에서 템플릿 배포의 일부로 SQL Server를 구성할 수 있습니다. SQL Server를 실행하는 VM에서 데이터베이스를 만들도록 구성된 애플리케이션 서버도 배포 템플릿에 포함되어 있을 경우, 발생하는 사항을 고려합니다. 애플리케이션 서버에 VM 확장을 사용하는 것 외에도 SQL Server VM 확장 리소스의 성공적인 반환에 대한 애플리케이션 서버의 종속성을 구성할 수 있습니다. 이 방법을 사용하면 애플리케이션 서버에 데이터베이스를 만들도록 지시할 때 SQL Server를 실행하는 VM이 구성되어 있고 사용할 수 있습니다.

플랫폼이 종속성에 필요한 논리를 처리하는 동시에, 템플릿의 선언적 방법을 사용하여 리소스의 종료 상태와 상호 종속성을 정의할 수 있습니다.

#### <a name="check-that-vm-extensions-are-available"></a>VM 확장을 사용할 수 있는지 확인

많은 유형의 VM 확장이 있습니다. 클라우드 일관성을 위한 템플릿을 개발하는 경우, 템플릿의 모든 대상 지역에서 사용할 수 있는 확장만 사용해야 합니다.

특정 지역(이 예제에서는 `myLocation`)에 사용할 수 있는 VM 확장 목록을 검색하려면 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az vm extension image list --location myLocation
```

Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) cmdlet을 실행하고 `-Location`을 사용하여 가상 머신 이미지의 위치를 지정할 수도 있습니다. 예를 들어:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>버전을 사용할 수 있는지 확인

VM 확장은 자사 리소스 관리자 리소스이므로 고유한 API 버전이 있습니다. 다음 코드에서 볼 수 있듯이, VM 확장 유형은 Microsoft.Compute 리소스 공급자의 중첩된 리소스입니다.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

템플릿의 모든 대상 위치에 VM 확장 리소스의 API 버전이 있어야 합니다. 위치 종속성은 앞의 “모든 리소스 종류의 버전 확인” 섹션에서 설명한 리소스 공급자 API 버전 가용성처럼 작동합니다.

VM 확장 리소스에 사용 가능한 API 버전 목록을 검색하려면 다음과 같이 [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) cmdlet에 **Microsoft.Compute** 리소스 공급자를 사용합니다.

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

가상 머신 확장 집합에 VM 확장을 사용할 수도 있습니다. 동일한 위치 조건이 적용됩니다. 클라우드 일관성을 위한 템플릿을 개발하려면 배포할 모든 위치에서 API 버전을 사용할 수 있는지 확인합니다. 확장 집합에 대한 VM 확장 리소스의 API 버전을 검색하려면 이전과 동일한 cmdlet을 사용하되 가상 머신 확장 집합 리소스 종류를 다음과 같이 지정합니다.

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

각 특정 확장의 버전도 관리됩니다. 이 버전은 VM 확장의 `typeHandlerVersion` 속성에 표시됩니다. 템플릿을 배포할 위치에서 템플릿 VM 확장의 `typeHandlerVersion` 요소에 지정된 버전을 사용할 수 있는지 확인합니다. 예를 들어, 다음 코드는 버전 1.7을 지정합니다.

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

특정 VM 확장에 사용할 수 있는 버전 목록을 검색하려면 [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) cmdlet을 사용합니다. 다음 예제에서는 **myLocation**에서 PowerShell DSC(Desired State Configuration) VM 확장에 사용 가능한 버전을 검색합니다.

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

게시자 목록을 가져오려면 [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) 명령을 사용합니다. 형식을 요청하려면 [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) 명령을 사용합니다.

## <a name="tips-for-testing-and-automation"></a>테스트 및 자동화에 대한 팁

템플릿을 작성하는 동안 모든 관련 설정, 기능 및 제한 사항을 추적하는 것은 어렵습니다. 일반적인 방법은 다른 위치를 대상으로 지정하기 전에 단일 클라우드에 대해 템플릿을 개발하고 테스트하는 것입니다. 그러나 작성 프로세스에서 더 빨리 테스트를 수행할수록 개발 팀이 수행해야 하는 문제 해결 및 코드 재작성이 줄어듭니다. 위치 종속성으로 인해 실패한 배포의 문제를 해결하려면 시간이 오래 걸릴 수 있습니다. 이러한 이유로 제작 주기에서 가능한 한 빨리 자동화 된 테스트를 권장 합니다. 궁극적으로 필요한 개발 시간과 리소스가 감소하고, 클라우드 일치 아티팩트의 가치가 더욱 증가할 것입니다.

다음 이미지는 IDE(통합 개발 환경)를 사용하는 팀의 일반적인 개발 프로세스 예를 보여 줍니다. 타임라인의 단계마다 다른 테스트 유형이 실행됩니다. 여기서는 두 개발자가 동일한 솔루션에서 작업 중이지만, 이 시나리오는 단일 개발자 또는 대규모 팀에도 똑같이 적용됩니다. 일반적으로 각 개발자가 중앙 리포지토리의 로컬 복사본을 만들어, 동일한 파일에서 작업하는 다른 사용자에게 영향을 주지 않고 로컬 복사본에서 작업할 수 있습니다.

![워크플로](./media/templates-cloud-consistency/workflow.png)

테스트 및 자동화에 대한 다음 팁에 유의하세요.

* 테스트 도구를 활용합니다. 예를 들어, Visual Studio Code 및 Visual Studio에는 템플릿의 유효성 검사에 도움이 되는 IntelliSense 및 기타 기능이 포함되어 있습니다.
* 로컬 IDE에서 개발하는 동안 코드 품질을 개선하려면 단위 테스트 및 통합 테스트를 사용하여 정적 코드 분석을 수행합니다.
* 초기 개발 중에 경험을 개선하려면 단위 테스트 및 통합 테스트에서 문제가 발견되었을 때 경고만 하고 테스트를 진행해야 합니다. 이렇게 하면 처리할 문제를 확인하고 변경 순서의 우선 순위를 지정할 수 있습니다. 이를 TDD(테스트 기반 배포)라고도 합니다.
* 일부 테스트는 Azure Resource Manager에 연결하지 않고 수행할 수 있습니다. 템플릿 배포 테스트와 같은 기타 테스트의 경우, 오프라인 상태에서 수행할 수 없는 특정 작업을 수행하려면 리소스 관리자가 필요합니다.
* 유효성 검사 API에 대한 배포 템플릿 테스트는 실제 배포와 다릅니다. 또한 로컬 파일에서 템플릿을 배포하는 경우에도 템플릿의 중첩된 템플릿에 대한 참조는 리소스 관리자가 직접 검색하고, VM 확장에서 참조되는 아티팩트는 배포된 VM 내에서 실행되는 VM 에이전트가 검색합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager 템플릿 고려 사항](/azure-stack/user/azure-stack-develop-templates)
* [ARM 템플릿에 대 한 모범 사례](template-syntax.md)
