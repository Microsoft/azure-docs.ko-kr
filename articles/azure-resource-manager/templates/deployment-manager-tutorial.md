---
title: Azure Deployment Manager를 사용하여 템플릿 배포
description: Azure Deployment Manager에서 Resource Manager 템플릿을 사용하여 Azure 리소스를 배포하는 방법에 대해 알아봅니다.
author: mumian
ms.date: 12/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 43291bdaa277c06262be2d7bb5ba8d3f61ff35ea
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86056891"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>자습서: Azure Deployment Manager에서 Resource Manager 템플릿 사용(공개 미리 보기)

[Azure Deployment Manager](./deployment-manager-overview.md)를 사용하여 여러 지역에 애플리케이션을 배포하는 방법에 대해 알아봅니다. 보다 빠른 방법을 원하는 경우 [Azure Deployment Manager 빠른 시작](https://github.com/Azure-Samples/adm-quickstart)이 구독에 필요한 구성을 만들고 아티팩트를 사용자 지정하여 여러 지역에 애플리케이션을 배포합니다. 빠른 시작은 이 자습서에서와 동일한 작업을 수행합니다.

Deployment Manager를 사용하려면 두 개의 템플릿을 만들어야 합니다.

* **토폴로지 템플릿**: 애플리케이션을 구성하는 Azure 리소스와 이러한 리소스를 배포할 위치를 설명합니다.
* **롤아웃 템플릿**: 애플리케이션을 배포할 때 수행하는 단계를 설명합니다.

> [!IMPORTANT]
> 구독이 새 Azure 기능을 테스트하기 위한 카나리아로 표시된 경우 Azure Deployment Manager를 사용하여 카나리아 지역에 배포하는 것만 가능합니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 시나리오 이해
> * 자습서 파일 다운로드
> * 아티팩트 준비
> * 사용자 정의 관리 ID 만들기
> * 서비스 토폴로지 템플릿 ID 만들기
> * 롤아웃 템플릿 만들기
> * 템플릿 배포
> * 배포 확인
> * 최신 버전 배포
> * 리소스 정리

추가 리소스:

* [Azure Deployment Manager REST API 참조](/rest/api/deploymentmanager/).
* [자습서: Azure Deployment Manager에서 상태 확인 사용](./deployment-manager-tutorial-health-check.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* [Azure Resource Manager 템플릿](overview.md)을 개발한 경험이 있어야 합니다.
* Azure PowerShell. 자세한 내용은 [Azure PowerShell 시작](/powershell/azure/get-started-azureps)을 참조하세요.
* Deployment Manager cmdlet이 있어야 합니다. 이러한 시험판 cmdlet을 설치하려면 최신 버전의 PowerShellGet이 필요합니다. 최신 버전을 가져오려면 [PowerShellGet 설치](/powershell/scripting/gallery/installing-psget)를 참조하세요. PowerShellGet이 설치되면 PowerShell 창을 닫습니다. 새로운 관리자 권한 PowerShell 창을 열고, 다음 명령을 사용합니다.

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>시나리오 이해

서비스 토폴로지 템플릿은 서비스를 구성하는 Azure 리소스와 이러한 리소스를 배포할 위치를 설명합니다. 서비스 토폴로지 정의에 구성되는 계층 구조는 다음과 같습니다.

* 서비스 토폴로지
  * Services
    * 서비스 단위

다음 다이어그램에서는 이 자습서에서 사용되는 서비스 토폴로지를 보여 줍니다.

![Azure Deployment Manager 자습서 시나리오 다이어그램](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

미국 서부 및 동부 지역에 할당된 두 가지 서비스가 있습니다.  각 서비스에는 웹 애플리케이션 프런트 엔드와 스토리지 계정 백 엔드의 두 가지 서비스 단위가 있습니다. 서비스 단위 정의에는 웹 애플리케이션 및 스토리지 계정을 만들기 위한 템플릿 및 매개 변수 파일에 대한 링크가 포함됩니다.

## <a name="download-the-tutorial-files"></a>자습서 파일 다운로드

1. 이 자습서에서 사용되는 [템플릿 및 아티팩트](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip)를 다운로드합니다.
2. 대상 위치의 컴퓨터에 파일의 압축을 풉니다.

루트 폴더 아래에 다음 두 개의 폴더가 있습니다.

* **ADMTemplates**: 다음과 같은 Deployment Manager 템플릿이 포함되어 있습니다.
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: 템플릿 아티팩트와 이진 아티팩트가 모두 포함되어 있습니다. [아티팩트 준비](#prepare-the-artifacts)를 참조하세요.

두 집합의 템플릿이 있습니다.  한 집합은 서비스 토폴로지 및 롤아웃을 배포하는 데 사용되는 Deployment Manager 템플릿이며, 다른 하나의 집합은 서비스 단위에서 호출되어 웹 서비스와 스토리지 계정을 만듭니다.

## <a name="prepare-the-artifacts"></a>아티팩트 준비

ArtifactStore 다운로드 폴더에는 다음 두 개의 폴더가 있습니다.

![Azure Deployment Manager 자습서 아티팩트 소스 다이어그램](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* **templates** 폴더: 템플릿 아티팩트가 포함되어 있습니다. **1.0.0.0** 및 **1.0.0.1**은 이진 아티팩트의 두 버전을 나타냅니다. 각 버전 내에는 각 서비스(Service East U.S. 및 Service West U.S.)에 대한 폴더가 있습니다. 각 서비스에는 스토리지 계정을 만들기 위한 템플릿과 매개 변수 파일의 쌍과 웹 애플리케이션을 만들기 위한 또 다른 쌍이 있습니다. 웹 애플리케이션 템플릿은 웹 애플리케이션 파일이 포함된 압축 패키지를 호출합니다. 압축된 파일은 binaries 폴더에 저장된 이진 아티팩트입니다.
* **binaries** 폴더: 이진 아티팩트가 포함되어 있습니다. **1.0.0.0** 및 **1.0.0.1**은 이진 아티팩트의 두 버전을 나타냅니다. 각 버전에는 미국 서부 지역과 동부 지역에 웹 애플리케이션을 만들기 위한 하나의 Zip 파일이 각각 있습니다.

두 버전(1.0.0.0 및 1.0.0.1)은 [수정 버전 배포](#deploy-the-revision)에 해당하는 것입니다. 템플릿 아티팩트와 이진 아티팩트에 모두 두 버전이 있지만, 두 버전 간에는 이진 아티팩트만 다릅니다. 실제로 이진 아티팩트는 템플릿 아티팩트에 비해 더 자주 업데이트됩니다.

1. 텍스트 편집기에서 **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json**을 엽니다. 스토리지 계정을 만들기 위한 기본 템플릿입니다.
2. **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**을 엽니다.

    ![Azure Deployment Manager 자습서에서는 웹 애플리케이션 템플릿을 만듭니다.](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    템플릿에서 웹 애플리케이션의 파일이 포함된 배포 패키지를 호출합니다. 이 자습서에서 압축된 패키지에는 index.html 파일만 포함되어 있습니다.
3. **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**을 엽니다.

    ![Azure Deployment Manager 자습서에서는 웹 애플리케이션 템플릿 매개 변수인 containerRoot를 만듭니다.](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    deployPackageUri의 값은 배포 패키지의 경로입니다. 매개 변수에는 **$containerRoot** 변수가 포함되어 있습니다. $containerRoot의 값은 아티팩트 소스 SAS 위치, 아티팩트 루트 및 deployPackageUri를 연결하여 [롤아웃 템플릿](#create-the-rollout-template)에 제공됩니다.
4. **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**을 엽니다.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    이 html에서는 위치와 버전 정보를 보여 줍니다. 1\.0.0.1 폴더의 이진 파일에서 "Version 1.0.0.1"을 표시합니다. 서비스가 배포되면 이러한 페이지를 찾아볼 수 있습니다.
5. 다른 아티팩트 파일을 확인합니다. 그러면 시나리오를 더 잘 이해할 수 있습니다.

템플릿 아티팩트는 서비스 토폴로지 템플릿에서 사용되며, 이진 아티팩트는 롤아웃 템플릿에서 사용됩니다. 토폴로지 템플릿과 롤아웃 템플릿은 모두 Resource Manager를 템플릿 및 배포에 사용되는 이진 아티팩트로 가리키는 데 사용되는 리소스인 아티팩트 소스 Azure 리소스를 정의합니다. 자습서를 간소화하기 위해 하나의 스토리지 계정을 사용하여 템플릿 아티팩트와 이진 아티팩트를 모두 저장합니다. 두 아티팩트 소스는 모두 동일한 스토리지 계정을 가리킵니다.

다음 PowerShell 스크립트를 실행하여 리소스 그룹을 만들고, 스토리지 컨테이너를 만들고, Blob 컨테이너를 만들고, 다운로드한 파일을 업로드한 다음, SAS 토큰을 만듭니다.

> [!IMPORTANT]
> PowerShell 스크립트의 **projectName**은 이 자습서에서 배포되는 Azure 서비스의 이름을 생성하는 데 사용합니다. Azure 서비스마다 이름에 대한 요구 사항이 다릅니다. 배포가 성공하도록 소문자와 숫자로만 구성된 12자 미만의 이름을 선택합니다.
> 프로젝트 이름의 복사본을 저장합니다. 이 자습서 전체에서 동일한 projectName을 사용합니다.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

SAS 토큰을 사용하여 URL의 복사본을 만듭니다. 이 URL은 두 매개 변수 파일(토폴로지 매개 변수 파일 및 롤아웃 매개 변수 파일)의 필드를 채우는 데 필요합니다.

Azure Portal에서 컨테이너를 열고 **이진** 및 **템플릿** 폴더와 파일이 모두 업로드되었는지 확인합니다.

## <a name="create-the-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID 만들기

이 자습서의 뒷부분에서 롤아웃을 배포합니다. 사용자가 할당한 관리 ID는 배포 작업(예: 웹 애플리케이션 및 스토리지 계정 배포)을 수행하는 데 필요합니다. 이 ID에는 서비스를 배포할 Azure 구독에 대한 액세스 권한이 부여되어야 하며 아티팩트 배포를 수행할 수 있는 충분한 권한이 있어야 합니다.

사용자가 할당한 관리 ID를 만들고 구독에 대한 액세스 제어를 구성해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. [사용자가 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 만듭니다.
3. 포털의 왼쪽 메뉴에서 **구독**을 선택한 다음, 구독을 선택합니다.
4. **액세스 제어(IAM)** , **역할 할당 추가**를 차례로 선택합니다.
5. 다음 값을 입력하거나 선택합니다.

    ![Azure Deployment Manager 자습서 - 사용자가 할당한 관리 ID 액세스 제어](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **역할**: 아티팩트 배포(웹 애플리케이션 및 스토리지 계정)를 수행할 수 있는 충분한 권한을 부여합니다. 이 자습서에서는 **기여자**를 선택합니다. 실제로는 최소 권한으로 제한하는 것이 좋습니다.
    * **할당된 액세스 권한**: **사용자가 할당한 관리 ID**를 선택합니다.
    * 자습서 앞부분에서 만든 사용자가 할당한 관리 ID를 선택합니다.
6. **저장**을 선택합니다.

## <a name="create-the-service-topology-template"></a>서비스 토폴로지 템플릿 ID 만들기

**\ADMTemplates\CreateADMServiceTopology.json**을 엽니다.

### <a name="the-parameters"></a>parameters

템플릿에 포함되는 매개 변수는 다음과 같습니다.

* **projectName**: 이 이름은 Deployment Manager 리소스에 대한 이름을 만드는 데 사용됩니다. 예를 들어 "jdoe"를 사용하는 경우 서비스 토폴로지 이름은 **jdoe**ServiceTopology입니다.  리소스 이름은 템플릿의 variables 섹션에 정의됩니다.
* **azureResourcelocation**: 이 자습서를 간소화하기 위해 달리 지정하지 않는 한 모든 리소스에서 이 위치를 공유합니다.
* **artifactSourceSASLocation**: 서비스 단위 템플릿 및 매개 변수 파일이 배포를 위해 저장되는 Blob 컨테이너에 대한 SAS URI입니다.  [아티팩트 준비](#prepare-the-artifacts)를 참조하세요.
* **templateArtifactRoot**: 템플릿과 매개 변수가 저장되는 Blob 컨테이너의 오프셋 경로입니다. 기본값은 **templates/1.0.0.0**입니다. [아티팩트 준비](#prepare-the-artifacts)에서 설명한 폴더 구조를 변경하려는 경우가 아니면 이 값을 변경하지 마세요. 이 자습서에서는 상대 경로를 사용합니다.  전체 경로는 **artifactSourceSASLocation**, **templateArtifactRoot** 및 **templateArtifactSourceRelativePath**(또는 **parametersArtifactSourceRelativePath**)를 연결하여 생성됩니다.
* **targetSubscriptionID**: Deployment Manager 리소스를 배포하고 요금이 청구되는 구독 ID입니다. 이 자습서에서는 사용자의 구독 ID를 사용합니다.

### <a name="the-variables"></a>variables

variables 섹션에서는 리소스 이름, 두 서비스 **Service WUS** 및 **Service EUS**에 대한 Azure 위치, 아티팩트 경로를 정의합니다.

![Azure Deployment Manager 자습서 - 토폴로지 템플릿 변수](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

아티팩트 경로를 스토리지 계정에 업로드한 폴더 구조와 비교합니다. 아티팩트 경로는 상대 경로입니다. 전체 경로는 **artifactSourceSASLocation**, **templateArtifactRoot** 및 **templateArtifactSourceRelativePath**(또는 **parametersArtifactSourceRelativePath**)를 연결하여 생성됩니다.

### <a name="the-resources"></a>resources

루트 수준에는 두 가지 리소스, 즉 *아티팩트 소스*와 *서비스 토폴로지*가 정의되어 있습니다.

아티팩트 소스 정의는 다음과 같습니다.

![Azure Deployment Manager 자습서 - 토폴로지 템플릿 리소스 아티팩트 소스](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

다음 스크린샷에는 서비스 토폴로지, 서비스 및 서비스 단위 정의의 일부만 표시되어 있습니다.

![Azure Deployment Manager 자습서 - 토폴로지 템플릿 리소스 서비스 토폴로지](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId**는 아티팩트 소스 리소스를 서비스 토폴로지 리소스와 연결하는 데 사용됩니다.
* **dependsOn**: 모든 서비스 토폴로지 리소스가 아티팩트 소스 리소스에 따라 달라집니다.
* **artifacts**는 템플릿 아티팩트를 가리킵니다.  여기서는 상대 경로가 사용됩니다. 전체 경로는 artifactSourceSASLocation(아티팩트 소스에서 정의됨), artifactRoot(아티팩트 소스에서 정의됨) 및 templateArtifactSourceRelativePath(또는 parametersArtifactSourceRelativePath)를 연결하여 생성됩니다.

### <a name="topology-parameters-file"></a>토폴로지 매개 변수 파일

토폴로지 템플릿에 사용되는 매개 변수 파일을 만듭니다.

1. Visual Studio 코드 또는 텍스트 편집기에서 **\ADMTemplates\CreateADMServiceTopology.Parameters**를 엽니다.
2. 매개 변수 값을 다음과 같이 채웁니다.

    * **projectName**: 4-5자의 문자열을 입력합니다. 이 이름은 고유한 Azure 리소스 이름을 만드는 데 사용됩니다.
    * **azureResourceLocation**: Azure 위치를 잘 모르는 경우 이 자습서에서는 **centralus**를 사용합니다.
    * **artifactSourceSASLocation**: 서비스 단위 템플릿 및 매개 변수 파일이 배포를 위해 저장되는 루트 디렉터리(Blob 컨테이너)의 SAS URI를 입력합니다.  [아티팩트 준비](#prepare-the-artifacts)를 참조하세요.
    * **templateArtifactRoot**: 아티팩트의 폴더 구조를 변경하지 않는 한 이 자습서에서는 **templates/1.0.0.0**을 사용합니다.

> [!IMPORTANT]
> 토폴로지 템플릿과 롤아웃 템플릿은 몇 가지 공통 매개 변수를 공유합니다. 이러한 매개 변수에는 동일한 값이 있어야 합니다. 이러한 매개 변수는 **projectName**, **azureResourceLocation** 및 **artifactSourceSASLocation**입니다(이 자습서에서는 두 아티팩트 소스에서 동일한 스토리지 계정을 공유함).

## <a name="create-the-rollout-template"></a>롤아웃 템플릿 만들기

**\ADMTemplates\CreateADMRollout.json**을 엽니다.

### <a name="the-parameters"></a>parameters

템플릿에 포함되는 매개 변수는 다음과 같습니다.

![Azure Deployment Manager 자습서 - 롤아웃 템플릿 매개 변수](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **projectName**: 이 이름은 Deployment Manager 리소스에 대한 이름을 만드는 데 사용됩니다. 예를 들어 "jdoe"를 사용하는 경우 롤아웃 이름은 **jdoe**Rollout입니다.  이름은 템플릿의 variables 섹션에 정의됩니다.
* **azureResourcelocation**: 이 자습서를 간소화하기 위해 달리 지정하지 않는 한 모든 Deployment Manager 리소스에서 이 위치를 공유합니다.
* **artifactSourceSASLocation**: 서비스 단위 템플릿 및 매개 변수 파일이 배포를 위해 저장되는 루트 디렉터리(Blob 컨테이너)의 SAS URI입니다.  [아티팩트 준비](#prepare-the-artifacts)를 참조하세요.
* **binaryArtifactRoot**:  기본값은 **binaries/1.0.0.0**입니다. [아티팩트 준비](#prepare-the-artifacts)에서 설명한 폴더 구조를 변경하려는 경우가 아니면 이 값을 변경하지 마세요. 이 자습서에서는 상대 경로를 사용합니다.  전체 경로는 CreateWebApplicationParameters.json에 지정된 **artifactSourceSASLocation**, **binaryArtifactRoot** 및 **deployPackageUri**를 연결하여 생성됩니다.  [아티팩트 준비](#prepare-the-artifacts)를 참조하세요.
* **managedIdentityID**: 배포 작업을 수행하는 사용자가 할당한 관리 ID입니다. [사용자가 할당한 관리 ID 만들기](#create-the-user-assigned-managed-identity)를 참조하세요.

### <a name="the-variables"></a>variables

variables 섹션에서는 리소스 이름을 정의합니다. 서비스 토폴로지 이름, 서비스 이름 및 서비스 단위 이름이 [토폴로지 템플릿](#create-the-service-topology-template)에 정의된 이름과 일치하는지 확인합니다.

![Azure Deployment Manager 자습서 - 롤아웃 템플릿 변수](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>resources

루트 수준에는 세 가지 리소스, 즉 소스, 단계 및 롤아웃이 정의되어 있습니다.

아티팩트 소스 정의는 토폴로지 템플릿에 정의된 것과 동일합니다.  자세한 내용은 [서비스 토폴로지 템플릿 만들기](#create-the-service-topology-template)를 참조하세요.

다음 스크린샷에서는 wait(대기) 단계 정의를 보여 줍니다.

![Azure Deployment Manager 자습서 - 롤아웃 템플릿 리소스 wait 단계](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

duration(기간)은 [ISO 8601 표준](https://en.wikipedia.org/wiki/ISO_8601#Durations)을 사용합니다. **PT1M**(반드시 대문자임)은 1분 대기의 예입니다.

다음 스크린샷에서는 롤아웃 정의의 일부만 보여 줍니다.

![Azure Deployment Manager 자습서 - 롤아웃 템플릿 리소스 롤아웃](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: 롤아웃 리소스가 아티팩트 소스 리소스 및 정의된 모든 단계에 따라 달라집니다.
* **artifactSourceId**: 아티팩트 소스 리소스를 롤아웃 리소스와 연결하는 데 사용됩니다.
* **targetServiceTopologyId**: 서비스 토폴로지 리소스를 롤아웃 리소스에 연결하는 데 사용됩니다.
* **deploymentTargetId**: 서비스 토폴로지 리소스의 서비스 단위 리소스 ID입니다.
* **preDeploymentSteps** 및 **postDeploymentSteps**: 배포 단계가 포함됩니다. 템플릿에서 대기 단계가 호출됩니다.
* **dependsOnStepGroups**: 단계 그룹 간의 종속성을 구성합니다.

### <a name="rollout-parameters-file"></a>롤아웃 매개 변수 파일

롤아웃 템플릿에 사용되는 매개 변수 파일을 만듭니다.

1. Visual Studio 코드 또는 임의의 텍스트 편집기에서 **\ADMTemplates\CreateADMRollout.Parameters**를 엽니다.
2. 매개 변수 값을 다음과 같이 채웁니다.

    * **projectName**: 4-5자의 문자열을 입력합니다. 이 이름은 고유한 Azure 리소스 이름을 만드는 데 사용됩니다.
    * **azureResourceLocation**: Azure 위치를 지정합니다.
    * **artifactSourceSASLocation**: 서비스 단위 템플릿 및 매개 변수 파일이 배포를 위해 저장되는 루트 디렉터리(Blob 컨테이너)의 SAS URI를 입력합니다.  [아티팩트 준비](#prepare-the-artifacts)를 참조하세요.
    * **binaryArtifactRoot**: 아티팩트의 폴더 구조를 변경하지 않는 한 이 자습서에서는 **binaries/1.0.0.0**을 사용합니다.
    * **managedIdentityID**: 사용자 할당 관리 ID를 입력합니다. [사용자가 할당한 관리 ID 만들기](#create-the-user-assigned-managed-identity)를 참조하세요. 구문은 다음과 같습니다.

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> 토폴로지 템플릿과 롤아웃 템플릿은 몇 가지 공통 매개 변수를 공유합니다. 이러한 매개 변수에는 동일한 값이 있어야 합니다. 이러한 매개 변수는 **projectName**, **azureResourceLocation** 및 **artifactSourceSASLocation**입니다(이 자습서에서는 두 아티팩트 소스에서 동일한 스토리지 계정을 공유함).

## <a name="deploy-the-templates"></a>템플릿 배포

Azure PowerShell을 사용하여 템플릿을 배포할 수 있습니다.

1. 스크립트를 실행하여 서비스 토폴로지를 배포합니다.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    [아티팩트 준비](#prepare-the-artifacts) 스크립트를 실행한 것과 다른 PowerShell 세션에서 이 스크립트를 실행하는 경우, 먼저 **$resourceGroupName** 및 **$filePath**를 포함하는 변수를 다시 채워야 합니다.

    > [!NOTE]
    > `New-AzResourceGroupDeployment`는 비동기 호출입니다. 성공 메시지는 성공적으로 배포가 시작되었다는 의미일 뿐입니다. 배포를 확인하려면 이 절차의 2단계와 4단계를 참조하세요.

2. Azure Portal을 사용하여 서비스 토폴로지와 밑줄 표시된 리소스가 성공적으로 만들어졌는지 확인합니다.

    ![Azure Deployment Manager 자습서 - 배포된 서비스 토폴로지 리소스](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    리소스를 보려면 **숨겨진 형식 표시**를 선택해야 합니다.

3. <a id="deploy-the-rollout-template"></a>롤아웃 템플릿을 배포합니다.

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. 다음 PowerShell 스크립트를 사용하여 롤아웃 진행률을 확인합니다.

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    이 cmdlet을 실행하려면 먼저 Deployment Manager PowerShell cmdlet이 설치되어 있어야 합니다. 필수 조건을 참조하세요. -Verbose 스위치를 사용하여 전체 출력을 볼 수 있습니다.

    다음 샘플에서는 실행 중 상태를 보여 줍니다.

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    롤아웃이 성공적으로 배포되면 각 서비스마다 하나씩 둘 이상의 리소스 그룹이 표시됩니다.

## <a name="verify-the-deployment"></a>배포 확인

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. 롤아웃 배포에서 만든 새 리소스 그룹 아래에 새로 만들어진 웹 애플리케이션으로 이동합니다.
3. 웹 브라우저에서 웹 애플리케이션을 엽니다. index.html 파일에서 위치와 버전을 확인합니다.

## <a name="deploy-the-revision"></a>수정 버전 배포

웹 애플리케이션에 대한 새 버전(1.0.0.1)이 있는 경우입니다. 다음 절차를 사용하여 웹 애플리케이션을 다시 배포할 수 있습니다.

1. CreateADMRollout.Parameters.json을 엽니다.
2. **binaryArtifactRoot**를 **binaries/1.0.0.1**로 업데이트합니다.
3. [템플릿 배포](#deploy-the-rollout-template)의 지침에 따라 롤아웃을 다시 배포합니다.
4. [배포 확인](#verify-the-deployment)에서 설명한 대로 배포를 확인합니다. 웹 페이지에는 1.0.0.1 버전이 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드를 사용하여 범위를 이 자습서에서 만든 리소스 그룹으로 좁힙니다. 다음과 같이 3-4개가 있습니다.

    * **&lt;projectName>rg**: Deployment Manager 리소스가 포함되어 있습니다.
    * **&lt;projectName>ServiceWUSrg**: ServiceWUS에서 정의한 리소스가 포함되어 있습니다.
    * **&lt;projectName>ServiceEUSrg**: ServiceEUS에서 정의한 리소스가 포함되어 있습니다.
    * 사용자 정의 관리 ID에 대한 리소스 그룹
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.
5. 마지막 두 단계를 반복하여 이 자습서에서 만든 다른 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Deployment Manager를 사용하는 방법을 알아보았습니다. Azure Deployment Manager에 상태 모니터링을 통합하려면 [자습서: Azure Deployment Manager에서 상태 확인 사용](./deployment-manager-tutorial-health-check.md)을 참조하세요.
