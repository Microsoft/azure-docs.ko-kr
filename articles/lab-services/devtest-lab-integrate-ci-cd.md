---
title: Azure Pipelines에 Azure DevTest Labs 통합
description: Azure Pipelines 지속적인 통합 및 배달 파이프라인에 Azure DevTest Labs를 통합하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293218"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD 파이프라인에 Azure DevTest Labs 통합

*Azure DevTest Labs 작업* 확장을 사용 하 여 Azure Pipelines 지속적인 통합 및 지속적인 업데이트 (CI/CD) 빌드 및 릴리스 파이프라인을 Azure DevTest Labs와 통합할 수 있습니다. 확장은 다음을 비롯 한 몇 가지 작업을 설치 합니다. 

- VM (가상 컴퓨터) 만들기
- VM에서 사용자 지정 이미지 만들기
- VM 삭제 

이러한 작업을 통해 특정 테스트 작업에 대 한 *골든 이미지* VM을 신속 하 게 배포 하 고 테스트가 완료 되 면 vm을 삭제할 수 있습니다.

이 문서에서는 Azure DevTest Labs 작업을 사용 하 여 VM을 만들고 배포 하 고, 사용자 지정 이미지를 만든 다음, VM을 모두 단일 릴리스 파이프라인으로 삭제 하는 방법을 보여 줍니다. 일반적으로 고유한 사용자 지정 빌드에서 작업을 수행 하 고, 테스트 하 고, 파이프라인을 배포 합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>필수 조건

- [Azure DevOps](https://dev.azure.com) 조직에 등록 또는 로그인 하 고 조직에 [프로젝트를 만듭니다](/vsts/organizations/projects/create-project) . 
  
- Visual Studio Marketplace에서 Azure DevTest Labs 작업 확장을 설치 합니다.
  
  1. [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)으로 이동합니다.
  1. **무료 다운로드**를 선택 합니다.
  1. 드롭다운에서 Azure DevOps 조직을 선택 하 고 **설치**를 선택 합니다. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Azure VM을 빌드하는 템플릿 만들기 

이 섹션에서는 요청 시 Azure VM을 만드는 데 사용 하는 Azure Resource Manager 템플릿을 만드는 방법에 대해 설명 합니다.

1. 구독에서 리소스 관리자 템플릿을 만들려면 [리소스 관리자 템플릿 사용](devtest-lab-use-resource-manager-template.md)의 절차를 따르세요.
   
1. Resource Manager 템플릿을 생성하기 전에, VM 만들기의 일부로 [WinRM 아티팩트](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)를 추가합니다. *Azure 파일 복사* 및 *대상 컴퓨터의 PowerShell* 과 같은 배포 작업에는 WinRM 액세스가 필요 합니다. WinRM 아티팩트에는 매개 변수로 호스트 이름이 필요 하며,이는 VM의 FQDN (정규화 된 도메인 이름) 이어야 합니다. 
   
   > [!NOTE]
   > WinRM을 공유 IP 주소와 함께 사용하는 경우 NAT 규칙을 추가하여 외부 포트를 WinRM 포트에 매핑해야 합니다. 공용 IP 주소를 사용 하 여 VM을 만드는 경우에는 NAT 규칙이 필요 하지 않습니다.
   
   
1. 템플릿을 *Createvmtemplate. json*이라는 파일로 컴퓨터에 저장 합니다.
   
1. 소스 제어 시스템에 템플릿을 체크 인 합니다.

## <a name="create-a-script-to-get-vm-properties"></a>VM 속성을 가져오는 스크립트 만들기

릴리스 파이프라인의 *대상 컴퓨터에서* *Azure 파일 복사* 또는 PowerShell과 같은 작업 단계를 실행 하는 경우 다음 스크립트는 VM에 앱을 배포 하는 데 필요한 값을 수집 합니다. 일반적으로 이러한 작업을 사용 하 여 Azure VM에 앱을 배포 합니다. 작업에는 VM 리소스 그룹 이름, IP 주소 및 FQDN과 같은 값이 필요 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

스크립트 파일을 만들려면 다음을 수행 합니다.

1. 텍스트 편집기를 열고 다음 스크립트를 붙여넣습니다.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. *Getlab Vmparams. ps1*와 같은 이름으로 파일을 저장 하 고 원본 제어 시스템에 체크 인 합니다. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Azure Pipelines에서 릴리스 파이프라인 만들기

새 릴리스 파이프라인을 만들려면 다음을 수행 합니다.

1. Azure DevOps 프로젝트 페이지의 왼쪽 탐색 영역에서 **파이프라인** > **릴리스** 를 선택 합니다.
1. **새 파이프라인**을 선택합니다.
1. **템플릿 선택**에서 아래로 스크롤하여 **빈 작업**을 선택 하 고 **적용**을 선택 합니다.

### <a name="add-and-set-variables"></a>변수 추가 및 설정

파이프라인 태스크는 Azure Portal에서 리소스 관리자 템플릿을 만들 때 VM에 할당 한 값을 사용 합니다. 

값에 대 한 변수를 추가 하려면: 

1. 파이프라인 페이지에서 **변수** 탭을 선택 합니다.
   
1. 각 변수에 대해 **추가** 를 선택 하 고 이름 및 값을 입력 합니다.
   
   |이름|값|
   |---|---|
   |*vmName*|리소스 관리자 템플릿에서 할당 한 VM 이름|
   |*userName*|VM에 액세스 하기 위한 사용자 이름|
   |*password*|사용자 이름의 암호입니다. 잠금 아이콘을 선택 하 여 암호를 숨기고 보호 합니다.

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs VM 만들기

다음 단계는 향후 배포에 사용할 골든 이미지 VM을 만드는 것입니다. *AZURE DEVTEST LABS Vm 만들기* 작업을 사용 하 여 Azure DevTest Labs 인스턴스 내에서 vm을 만듭니다.

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1 단계** **작업을 보려면**하이퍼링크 텍스트를 선택 하 고 **에이전트 작업**옆에 있는 더하기 기호 **+** 를 선택 합니다. 
   
1. **작업 추가**에서 **VM Azure DevTest Labs 만들기**를 선택 하 고 **추가**를 선택 합니다. 
   
1. 왼쪽 창에서 **AZURE DEVTEST LABS VM 만들기** 를 선택 합니다. 

1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   
   |필드|값|
   |---|---|
   |**Azure RM 구독**|드롭다운 목록에서 **사용 가능한 Azure 서비스 연결** 또는 **사용 가능한 azure 구독** 에서 서비스 연결 또는 구독을 선택 하 고 필요한 경우 **권한 부여** 를 선택 합니다.<br /><br />**참고:** Azure 구독에 대 한 제한 된 권한 연결을 만드는 방법에 대 한 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)을 참조 하세요.|
   |**랩 이름**|랩 VM을 만들 기존 랩의 이름을 선택 합니다.|
   |**템플릿 이름**|소스 코드 리포지토리에 저장 한 템플릿 파일의 전체 경로 및 이름을 입력 합니다. 기본 제공 속성을 사용 하 여 경로를 단순화할 수 있습니다. 예를 들면 다음과 같습니다.<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**템플릿 매개 변수**|앞에서 정의한 변수에 대 한 매개 변수를 입력 합니다.<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**출력 변수** > **랩 VM ID**|만든 랩 VM ID에 대 한 변수를 입력 합니다. 기본 인- **vmid**를 사용 하는 경우 후속 작업의 변수를 $ (을 (를) 사용 하 여 *$ ()* 로 참조할 수 있습니다.<br /><br />기본값 이외의 이름을 만들 수 있지만 후속 작업에서 올바른 이름을 사용 해야 합니다. 랩 VM ID는 다음과 같은 형식으로 작성할 수 있습니다.<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>DevTest Labs VM의 세부 정보를 수집 합니다.

이전에 만든 스크립트를 실행하여 DevTest Labs VM의 세부 정보를 수집합니다. 

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1 단계** **작업을 보려면**하이퍼링크 텍스트를 선택 하 고 **에이전트 작업**옆에 있는 더하기 기호 **+** 를 선택 합니다. 
   
1. **작업 추가**에서 **Azure PowerShell**를 선택 하 고 **추가**를 선택 합니다. 
   
1. 왼쪽 창에서 **Azure PowerShell 스크립트: FilePath** 를 선택 합니다. 
   
1. 오른쪽 창에서 다음과 같이 양식을 작성합니다.
   
   |필드|값|
   |---|---|
   |**Azure 연결 형식**|**Azure Resource Manager**를 선택 합니다.|
   |**Azure 구독**|서비스 연결 또는 구독을 선택 합니다.| 
   |**스크립트 유형**|**스크립트 파일 경로**를 선택 합니다.|
   |**스크립트 경로**|소스 코드 리포지토리에 저장 한 PowerShell 스크립트의 전체 경로 및 이름을 입력 합니다. 기본 제공 속성을 사용 하 여 경로를 단순화할 수 있습니다. 예를 들면 다음과 같습니다.<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**스크립트 인수**|이전 태스크에 의해 채워진 인 *랩 vmid* 변수의 이름을 입력 합니다. 예를 들면 다음과 같습니다.<br /><br />`-labVmId '$(labVMId)'`|

이 스크립트는 필요한 값을 수집 하 여 릴리스 파이프라인 내의 환경 변수에 저장 하므로 이후 단계에서 쉽게 참조할 수 있습니다.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>DevTest Labs VM에서 VM 이미지 만들기

다음 작업은 Azure DevTest Labs 인스턴스에 새로 배포 된 VM의 이미지를 만드는 것입니다. 그런 다음, 개발 작업을 실행하거나 일부 테스트를 실행하고자 할 때마다 필요에 따라 이미지를 사용하여 VM의 복사본을 만듭니다. 

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1 단계** **작업을 보려면**하이퍼링크 텍스트를 선택 하 고 **에이전트 작업**옆에 있는 더하기 기호 **+** 를 선택 합니다. 
   
1. **작업 추가**에서 **Azure DevTest Labs 사용자 지정 이미지 만들기**를 선택 하 고 **추가**를 선택 합니다. 
   
1. 아래와 같이 작업을 구성합니다.
   
   |필드|값|
   |---|---|
   |**Azure RM 구독**|서비스 연결 또는 구독을 선택 합니다.|
   |**랩 이름**|이미지가 생성 될 기존 랩의 이름을 선택 합니다.|
   |**사용자 지정 이미지 이름**|사용자 지정 이미지의 이름을 입력 합니다.|
   |**설명** (선택 사항)|나중에 올바른 이미지를 쉽게 선택할 수 있도록 설명을 입력 합니다.|
   |원본 **랩 vm** > **원본 랩 vm ID**|사용자가이 랩 변수의 기본 이름을 변경한 경우 여기에 입력 합니다. 기본값은 **$(labVMId)** 입니다.|
   |**출력 변수** > **사용자 지정 이미지 ID**|필요한 경우 변수의 기본 이름을 편집할 수 있습니다.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>DevTest Labs VM에 앱 배포 (선택 사항)

새 DevTest Labs VM에 앱을 배포 하는 작업을 추가할 수 있습니다. 앱을 배포 하는 데 일반적으로 사용 하는 작업은 *Azure 파일 복사* 및 *대상 컴퓨터의 PowerShell*입니다.

이러한 작업의 매개 변수에 필요한 VM 정보는 릴리스 파이프라인 내에서 **이라는**, **LabVMIpAddress**및 **lab vmfqdn** 이라는 세 가지 구성 변수에 저장 됩니다. 앱을 배포하지 않고 DevTest Labs VM 및 사용자 지정 이미지를 만들어 시험하려는 경우 이 단계를 건너뛸 수 있습니다.

### <a name="delete-the-vm"></a>VM 삭제

최종 작업은 Azure DevTest Labs 인스턴스에 배포 된 VM을 삭제 하는 것입니다. 일반적으로 배포된 VM에서 필요한 개발 작업을 실행하거나 테스트를 실행한 후 VM을 삭제합니다. 

1. 릴리스 파이프라인 **파이프라인** 탭에서 **1 단계** **작업을 보려면**하이퍼링크 텍스트를 선택 하 고 **에이전트 작업**옆에 있는 더하기 기호 **+** 를 선택 합니다. 
   
1. **작업 추가**에서 **VM Azure DevTest Labs 삭제**를 선택 하 고 **추가**를 선택 합니다. 
   
1. 아래와 같이 작업을 구성합니다.
   
   - **AZURE RM 구독**에서 서비스 연결 또는 구독을 선택 합니다. 
   - **랩 VM ID**의 경우, 사용자가 랩 vmid 변수의 기본 이름을 변경한 경우 여기에 입력 합니다. 기본값은 **$(labVMId)** 입니다.
   
### <a name="save-the-release-pipeline"></a>릴리스 파이프라인 저장

새 릴리스 파이프라인을 저장 하려면 다음을 수행 합니다.

1. 릴리스 파이프라인 페이지에서 **새 릴리스 파이프라인** 이름을 선택 하 고 파이프라인에 대 한 새 이름을 입력 합니다. 
   
1. 오른쪽 위에서 **저장** 아이콘을 선택 합니다. 

## <a name="create-and-run-a-release"></a>릴리스 만들기 및 실행

새 파이프라인을 사용 하 여 릴리스를 만들고 실행 하려면 다음을 수행 합니다.

1. 릴리스 파이프라인 페이지에서 오른쪽 위에 있는 **릴리스 만들기** 를 선택 합니다. 
   
1. **아티팩트**에서 최신 빌드를 선택 하 고 **만들기**를 선택 합니다.
   
1. 각 릴리스 단계에서 VM 만들기, 이미지 만들기 및 VM 삭제를 보려면 Azure Portal에서 DevTest Labs 인스턴스 보기를 새로 고칩니다.

사용자 지정 이미지를 사용 하 여 필요할 때마다 Vm을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Resource Manager 템플릿으로 다중 VM 환경을 만드는](devtest-lab-create-environment-from-arm.md) 방법을 알아봅니다.
- [공용 DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 DevTest Labs 자동화를 위한 더 많은 빠른 시작 Resource Manager 템플릿을 살펴봅니다.
- 필요한 경우 [Azure DevOps 문제 해결](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) 페이지를 참조 하세요.
 
