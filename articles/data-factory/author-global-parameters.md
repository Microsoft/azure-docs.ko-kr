---
title: 글로벌 매개 변수
description: 각 Azure Data Factory 환경의 전역 매개 변수 설정
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/31/2020
ms.openlocfilehash: 96fba5c27115dab65f26be80ce03bef35abcdb92
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230836"
---
# <a name="global-parameters-in-azure-data-factory"></a>Azure Data Factory의 전역 매개 변수

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

전역 매개 변수는 모든 식에서 파이프라인에 의해 사용 될 수 있는 데이터 팩터리에 대 한 상수입니다. 동일한 매개 변수 이름 및 값을 가진 파이프라인이 여러 개 있는 경우에 유용 합니다. CI/CD (지속적인 통합 및 배포 프로세스)를 사용 하 여 데이터 팩터리를 승격 하는 경우 각 환경에서 이러한 매개 변수를 재정의할 수 있습니다. 

## <a name="creating-global-parameters"></a>전역 매개 변수 만들기

전역 매개 변수를 만들려면 [ *관리* ] 섹션의 [ *전역 매개 변수* ] 탭으로 이동 하세요. **새로** 만들기를 선택 하 여 만들기-탐색을 엽니다.

![전역 매개 변수 만들기](media/author-global-parameters/create-global-parameter-1.png)

Side-by-side 탐색에서 이름을 입력 하 고, 데이터 형식을 선택 하 고, 매개 변수 값을 지정 합니다.

![전역 매개 변수 만들기](media/author-global-parameters/create-global-parameter-2.png)

전역 매개 변수를 만든 후에는 매개 변수의 이름을 클릭 하 여 해당 매개 변수를 편집할 수 있습니다. 한 번에 여러 매개 변수를 변경 하려면 **모두 편집**을 선택 합니다.

![전역 매개 변수 만들기](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>파이프라인에서 전역 매개 변수 사용

모든 [파이프라인 식](control-flow-expression-language-functions.md)에는 전역 매개 변수를 사용할 수 있습니다. 파이프라인이 데이터 집합 또는 데이터 흐름과 같은 다른 리소스를 참조 하는 경우 해당 리소스의 매개 변수를 통해 전역 매개 변수 값을 전달할 수 있습니다. 전역 매개 변수는로 참조 됩니다 `pipeline().globalParameters.<parameterName>` .

![전역 매개 변수 사용](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> CI/CD의 글로벌 매개 변수

연속 통합 및 배포 솔루션에서 글로벌 매개 변수를 통합 하는 방법에는 두 가지가 있습니다.

* ARM 템플릿에 전역 매개 변수 포함
* PowerShell 스크립트를 통해 전역 매개 변수 배포

대부분의 사용 사례에서 ARM 템플릿에 전역 매개 변수를 포함 하는 것이 좋습니다. 이는 [CI/CD 문서](continuous-integration-deployment.md)에 설명 된 솔루션과 기본적으로 통합 됩니다. 전역 매개 변수는 환경에서 환경으로 변경 되는 경우가 많기 때문에 기본적으로 ARM 템플릿 매개 변수로 추가 됩니다. 관리 허브에서 ARM 템플릿에 전역 매개 변수를 포함 하도록 설정할 수 있습니다.

![ARM 템플릿에 포함](media/author-global-parameters/include-arm-template.png)

ARM 템플릿에 전역 매개 변수를 추가 하면 다른 환경에서 고객이 관리 하는 키 또는 git 구성과 같은 다른 팩터리 수준 설정을 재정의할 수 있는 팩터리 수준 설정이 추가 됩니다. UAT 또는 PROD와 같은 높은 수준의 환경에서 이러한 설정을 사용 하도록 설정한 경우 아래 강조 표시 된 단계에서 PowerShell 스크립트를 통해 전역 매개 변수를 배포 하는 것이 좋습니다.

### <a name="deploying-using-powershell"></a>PowerShell을 사용 하 여 배포

다음 단계에서는 PowerShell을 통해 전역 매개 변수를 배포 하는 방법을 간략하게 설명 합니다. 이는 대상 팩터리에 고객 관리 키와 같은 팩터리 수준 설정이 있는 경우에 유용 합니다.

팩터리를 게시 하거나 전역 매개 변수를 사용 하 여 ARM 템플릿을 내보낼 때 *Globalparameters* 라는 폴더가 * 에your-factory-name_GlobalParameters.js*이라는 파일로 생성 됩니다. 이 파일은 게시 된 팩터리의 각 전역 매개 변수 형식 및 값을 포함 하는 JSON 개체입니다.

![전역 매개 변수 게시](media/author-global-parameters/global-parameters-adf-publish.png)

테스트 또는 PROD와 같은 새 환경에 배포 하는 경우에는이 전역 매개 변수 파일의 복사본을 만들고 적절 한 환경 관련 값을 덮어써야 합니다. 원래 전역 매개 변수 파일을 다시 게시 하면 파일은 덮어쓰므로 다른 환경의 복사본은 그대로 유지 됩니다.

예를 들어 ' ADF-DEV ' 라는 팩터리가 있고 값이 ' t r u e ' 인 ' environment ' 라는 문자열 형식의 전역 매개 변수를 사용 하는 경우 *ADF-DEV_GlobalParameters.json* 이라는 파일을 게시 하면이 생성 됩니다. ' ADF_TEST ' 이라는 테스트 팩터리에 배포 하는 경우 JSON 파일의 복사본을 만들고 (예:에 ADF-TEST_GlobalParameters.js), 매개 변수 값을 환경 관련 값으로 바꿉니다. ' Environment ' 매개 변수에 ' test ' 값이 있을 수 있습니다. 

![전역 매개 변수 배포](media/author-global-parameters/powershell-task.png)

아래 PowerShell 스크립트를 사용 하 여 글로벌 매개 변수를 추가 환경으로 승격 합니다. ARM 템플릿 배포 전에 Azure PowerShell DevOps 작업을 추가 합니다. DevOps 작업에서 새 매개 변수 파일, 대상 리소스 그룹 및 대상 데이터 팩터리의 위치를 지정 해야 합니다.

> [!NOTE]
> PowerShell을 사용 하 여 전역 매개 변수를 배포 하려면 Az 모듈의 버전 4.4.0 이상을 사용 해야 합니다.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>다음 단계

* Azure Data Factory의 [지속적인 통합 및 배포 프로세스](continuous-integration-deployment.md) 에 대 한 자세한 정보
* [제어 흐름 식 언어](control-flow-expression-language-functions.md) 를 사용 하는 방법 알아보기