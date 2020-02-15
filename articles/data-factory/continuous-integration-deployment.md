---
title: Azure Data Factory에서 지속적인 통합 및 전달
description: 지속적인 통합 및 지속적인 업데이트를 사용하여 환경(개발, 테스트, 프로덕션) 간에 Data Factory 파이프라인을 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 7c9f22d27351b0f57c5a0158821f347073ae60b4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187817"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory에서 지속적인 통합 및 전달

## <a name="overview"></a>개요

연속 통합은 코드 베이스에 대 한 각 변경을 자동으로 가능한 한 빨리 테스트 하는 방법입니다. 연속 전달은 연속 통합 중에 발생 하는 테스트를 따르고 스테이징 또는 프로덕션 시스템에 변경 내용을 푸시합니다.

Azure Data Factory에서 CI/CD (지속적인 통합 및 배달)는 한 환경 (개발, 테스트, 프로덕션)에서 다른 환경으로 Data Factory 파이프라인을 이동 하는 것을 의미 합니다. Azure Resource Manager 템플릿과 Data Factory UX 통합을 사용 하 여 CI/CD를 수행할 수 있습니다.

Data Factory UX의 **ARM 템플릿** 드롭다운 메뉴에서 리소스 관리자 템플릿을 생성할 수 있습니다. **ARM 템플릿 내보내기**를 선택 하면 포털에서 데이터 팩터리에 대 한 리소스 관리자 템플릿과 모든 연결 문자열 및 기타 매개 변수를 포함 하는 구성 파일을 생성 합니다. 그런 다음 각 환경 (개발, 테스트, 프로덕션)에 대해 하나의 구성 파일을 만듭니다. 주 Resource Manager 템플릿 파일은 모든 환경에서 동일하게 유지됩니다.

이 기능에 대 한 9 분의 소개와 데모를 보려면 다음 비디오를 시청 하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 수명 주기

다음은 Azure Repos Git로 구성 된 Azure data factory의 CI/CD 수명 주기에 대 한 샘플 개요입니다. Git 리포지토리를 구성 하는 방법에 대 한 자세한 내용은 [Azure Data Factory에서 소스 제어](source-control.md)를 참조 하세요.

1.  Azure Repos Git를 사용 하 여 개발 데이터 팩터리가 생성 및 구성 됩니다. 모든 개발자는 파이프라인 및 데이터 집합과 같은 Data Factory 리소스를 작성할 수 있는 권한이 있어야 합니다.

1.  개발자는 해당 기능 분기를 변경할 때 가장 최근의 변경 내용으로 파이프라인 실행을 디버그 합니다. 파이프라인 실행을 디버그 하는 방법에 대 한 자세한 내용은 [Azure Data Factory를 사용 하 여 반복 개발 및 디버깅](iterative-development-debugging.md)을 참조 하세요.

1.  개발자는 변경 내용을 충족 하 고 나면 해당 기능 분기에서 마스터 또는 공동 작업 분기로 끌어오기 요청을 만들어 해당 변경 내용을 피어로 검토할 수 있습니다.

1.  끌어오기 요청을 승인 하 고 변경 내용을 master 분기에 병합 한 후에는 변경 내용을 개발 팩터리에 게시할 수 있습니다.

1.  팀이 변경 내용을 테스트 팩터리에 배포할 준비가 되 면 팀은 master 분기에서 리소스 관리자 템플릿을 내보냅니다.

1.  내보낸 리소스 관리자 템플릿은 테스트 팩터리 및 프로덕션 팩터리에 다른 매개 변수 파일을 사용 하 여 배포 됩니다.

## <a name="create-a-resource-manager-template-for-each-environment"></a>각 환경에 Resource Manager 템플릿 만들기

1. **Arm 템플릿** 목록에서 **arm 템플릿 내보내기** 를 선택 하 여 개발 환경에서 데이터 팩터리에 대 한 리소스 관리자 템플릿을 내보냅니다.

   ![리소스 관리자 템플릿 내보내기](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 테스트 및 프로덕션 데이터 팩터리에서 **ARM 템플릿 가져오기**를 선택 합니다. 이 작업을 통해 Azure Portal로 이동합니다. 여기서 내보낸 템플릿을 가져올 수 있습니다. **편집기에서 사용자 고유의 템플릿 빌드** 를 선택 하 여 리소스 관리자 템플릿 편집기를 엽니다.

   ![사용자 고유의 템플릿 빌드](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. **파일 로드**를 선택 하 고 생성 된 리소스 관리자 템플릿을 선택 합니다.

   ![템플릿 편집](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 설정 섹션에서 연결 된 서비스 자격 증명과 같은 구성 값을 입력 합니다. 완료 되 면 **구매** 를 선택 하 여 리소스 관리자 템플릿을 배포 합니다.

   ![설정 섹션](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>연결 문자열

연결 문자열을 구성 하는 방법에 대 한 자세한 내용은 커넥터의 문서를 참조 하세요. 예를 들어 Azure SQL Database의 경우 [Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사](connector-azure-sql-database.md)를 참조하세요. 연결 문자열을 확인 하려면 Data Factory UX에서 리소스에 대 한 코드 보기를 열 수 있습니다. 코드 보기에서 연결 문자열의 암호 또는 계정 키 부분이 제거 됩니다. 코드 보기를 열려면 여기에 강조 표시 된 아이콘을 선택 합니다.

![코드 보기를 열어 연결 문자열 확인](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Azure Pipelines 릴리스를 사용 하 여 연속 통합 자동화

다음은 데이터 팩터리를 여러 환경으로 배포 하는 작업을 자동화 하는 Azure Pipelines 릴리스를 설정 하기 위한 가이드입니다.

![Azure Pipelines를 사용하는 지속적인 통합 다이어그램](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>요구 사항

-    [Azure Resource Manager 서비스 끝점](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)을 사용 하는 Visual Studio Team Foundation Server 또는 Azure Repos에 연결 된 Azure 구독입니다.

-   Azure Repos Git 통합을 사용 하 여 구성 된 데이터 팩터리입니다.

-   각 환경에 대 한 암호를 포함 하는 [Azure key vault](https://azure.microsoft.com/services/key-vault/) .

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines 릴리스 설정

1.  [Azure DevOps](https://dev.azure.com/)에서 데이터 팩터리를 사용 하 여 구성 된 프로젝트를 엽니다.

1.  페이지의 왼쪽에서 **파이프라인**을 선택 하 고 **릴리스**를 선택 합니다.

    ![파이프라인, 릴리스를 선택 합니다.](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **새 파이프라인**을 선택 하거나 기존 파이프라인이 있는 경우 **새로 만들기** , **새 릴리스 파이프라인**을 차례로 선택 합니다.

1.  **빈 작업** 템플릿을 선택 합니다.

    ![빈 작업 선택](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **스테이지 이름** 상자에 사용자 환경의 이름을 입력 합니다.

1.  **아티팩트 추가**를 선택 하 고 데이터 팩터리를 사용 하 여 구성 된 리포지토리를 선택 합니다. **기본 분기**에 대해 **adf_publish** 를 선택 합니다. **기본 버전**의 경우 **기본 분기에서 최신**을 선택 합니다.

    ![아티팩트 추가](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager 배포 작업을 추가합니다.

    a.  스테이지 보기에서 **스테이지 작업 보기**를 선택 합니다.

    ![스테이지 보기](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  새 태스크를 만듭니다. **Azure 리소스 그룹 배포**를 검색 한 다음 **추가**를 선택 합니다.

    다.  배포 작업에서 대상 데이터 팩터리에 대 한 구독, 리소스 그룹 및 위치를 선택 합니다. 필요한 경우 자격 증명을 제공 합니다.

    d.  **작업** 목록에서 **리소스 그룹 만들기 또는 업데이트**를 선택 합니다.

    e.  **템플릿** 상자 옆의 줄임표 단추 ( **...** )를 선택 합니다. 이 문서의 [각 환경에 대 한 리소스 관리자 템플릿 만들기](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) 섹션에서 **ARM 템플릿 가져오기** 를 사용 하 여 만든 Azure Resource Manager 템플릿을 찾아봅니다. Adf_publish 분기의 <FactoryName> 폴더에서이 파일을 찾습니다.

    f.  **템플릿 매개 변수 재정의** 필드 를 클릭 하 **여 매개** 변수 파일을 선택 합니다. 사용자가 선택한 파일은 복사본을 만들었는지 아니면 기본 파일 ARMTemplateParametersForFactory를 사용 하는지에 따라 달라 집니다.

    g.  **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에 대상 데이터 팩터리에 대 한 정보를 입력 합니다. Azure Key Vault에서 제공 하는 자격 증명의 경우 큰따옴표 사이에 암호의 이름을 입력 합니다. 예를 들어 비밀의 이름이 cred1 인 경우이 값으로 **"$ (cred1)"** 를 입력 합니다.

    h. **배포 모드**에 대해 **증분** 을 선택 합니다.

    > [!WARNING]
    > **배포 모드**에 대해 **완료** 를 선택 하면 리소스 관리자 템플릿에 정의 되지 않은 대상 리소스 그룹의 모든 리소스를 포함 하 여 기존 리소스가 삭제 될 수 있습니다.

    ![Data Factory Prod 배포](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  릴리스 파이프라인을 저장합니다.

1. 릴리스를 트리거하려면 **릴리스 만들기**를 선택 합니다.

   ![릴리스 만들기 선택](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> CI/CD 시나리오에서 서로 다른 환경의 IR (통합 런타임) 형식이 동일 해야 합니다. 예를 들어 개발 환경에 자체 호스팅 IR이 있는 경우 동일한 IR도 테스트 및 프로덕션과 같은 다른 환경에서 자체 호스트 되는 형식 이어야 합니다. 마찬가지로, 여러 단계에서 통합 런타임을 공유 하는 경우 개발, 테스트, 프로덕션 등의 모든 환경에서 통합 런타임을 연결 된 자체 호스트로 구성 해야 합니다.

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault에서 비밀 가져오기

Azure Resource Manager 템플릿에 전달 해야 하는 암호가 있는 경우 Azure Pipelines 릴리스와 Azure Key Vault를 사용 하는 것이 좋습니다.

비밀을 처리 하는 방법에는 두 가지가 있습니다.

1.  매개 변수 파일에 비밀을 추가합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)을 참조하세요.

    게시 분기에 업로드 되는 매개 변수 파일의 복사본을 만듭니다. 다음 형식을 사용 하 여 Key Vault에서 가져오려는 매개 변수의 값을 설정 합니다.

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    이 메서드를 사용하는 경우 키 자격 증명 모음에서 자동으로 암호를 끌어옵니다.

    매개 변수 파일은 게시 분기에 포함되어야 합니다.

-  이전 섹션에서 설명한 Azure Resource Manager 배포 작업 앞에 [Azure Key Vault 작업](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) 을 추가 합니다.

    1.  **작업** 탭에서 새 작업을 만듭니다. **Azure Key Vault** 를 검색 하 고 추가 합니다.

    1.  Key Vault 작업에서 키 자격 증명 모음을 만든 구독을 선택 합니다. 필요한 경우 자격 증명을 제공 하 고 키 자격 증명 모음을 선택 합니다.

    ![Key Vault 태스크 추가](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines 에이전트에 권한 부여

   올바른 사용 권한이 설정 되어 있지 않으면 액세스 거부 오류가 발생 하 여 Azure Key Vault 태스크가 실패할 수 있습니다. 릴리스에 대 한 로그를 다운로드 하 고 명령을 포함 하는 ps1 파일을 찾아 Azure Pipelines 에이전트에 대 한 권한을 부여 합니다. 명령을 직접 실행할 수 있습니다. 또는 파일에서 보안 주체 ID를 복사 하 고 Azure Portal에 직접 액세스 정책을 추가할 수 있습니다. `Get` 및 `List`는 필요한 최소 권한입니다.

### <a name="update-active-triggers"></a>활성 트리거 업데이트

활성 트리거를 업데이트하려고 하면 배포에 실패할 수 있습니다. 활성 트리거를 업데이트 하려면 수동으로 중지 한 후 배포 후에 다시 시작 해야 합니다. Azure PowerShell 작업을 사용 하 여이 작업을 수행할 수 있습니다.

1.  릴리스의 **작업** 탭에서 **Azure PowerShell** 작업을 추가 합니다. 작업 버전 4. *를 선택 합니다. 

1.  팩터리가 있는 구독을 선택 합니다.

1.  스크립트 **파일 경로** 를 스크립트 유형으로 선택 합니다. 이렇게 하려면 PowerShell 스크립트를 리포지토리에 저장 해야 합니다. 다음 PowerShell 스크립트를 사용 하 여 트리거를 중지할 수 있습니다.

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

`Start-AzDataFactoryV2Trigger` 함수를 사용 하 여 비슷한 단계를 완료 하 여 배포 후에 트리거를 다시 시작할 수 있습니다.

### <a name="sample-pre--and-post-deployment-script"></a>샘플 배포 전 및 배포 후 스크립트

다음 샘플 스크립트를 사용 하 여 배포 전에 트리거를 중지 하 고 나중에 다시 시작할 수 있습니다. 스크립트에는 제거된 리소스를 삭제하는 코드도 포함됩니다. 스크립트를 Azure DevOps git 리포지토리에 저장 하 고 버전 4. *를 사용 하 여 Azure PowerShell 작업을 통해 참조 합니다.

배포 전 스크립트를 실행 하는 경우 **스크립트 인수** 필드에서 다음 매개 변수의 변형을 지정 해야 합니다.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


배포 후 스크립트를 실행 하는 경우 **스크립트 인수** 필드에서 다음 매개 변수의 변형을 지정 해야 합니다.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

    ![Azure PowerShell task](media/continuous-integration-deployment/continuous-integration-image11.png)

다음은 배포 전 및 후에 사용할 수 있는 스크립트입니다. 삭제 된 리소스 및 리소스 참조를 계정으로 합니다.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager 템플릿에서 사용자 지정 매개 변수 사용

GIT 모드의 경우 템플릿 및 하드 코드 된 속성에서 매개 변수화 된 속성을 설정 하도록 리소스 관리자 템플릿의 기본 속성을 재정의할 수 있습니다. 이러한 시나리오에서는 기본 매개 변수화 템플릿을 재정의할 수 있습니다.

* 자동화 된 CI/CD를 사용 하 고 리소스 관리자 배포 중에 일부 속성을 변경 하려고 하지만 속성은 기본적으로 매개 변수화 되지 않습니다.
* 팩터리는 기본 리소스 관리자 템플릿이 허용 되는 최대 매개 변수 (256)를 초과 하기 때문에 잘못 되었습니다.

이러한 조건에서 기본 매개 변수화 템플릿을 재정의 하려면 data factory git 통합에 대 한 루트 폴더로 지정 된 폴더에 이름이 arm 인 파일을 만듭니다. 정확한 파일 이름을 사용 해야 합니다. Data Factory는 현재 공동 작업 분기 뿐만 아니라 Azure Data Factory 포털에 있는 분기에서이 파일을 읽습니다. UI에서 **ARM 템플릿 내보내기** 를 선택 하 여 변경 내용을 테스트할 수 있는 private 분기에서 파일을 만들거나 편집할 수 있습니다. 그런 다음 공동 작업 분기에 파일을 병합할 수 있습니다. 파일이 없는 경우 기본 템플릿이 사용 됩니다.

### <a name="syntax-of-a-custom-parameters-file"></a>사용자 지정 매개 변수 파일의 구문

다음은 사용자 지정 매개 변수 파일을 만들 때 따라야 할 몇 가지 지침입니다. 이 파일은 트리거, 파이프라인, 연결 된 서비스, 데이터 집합, 통합 런타임 등의 각 엔터티 형식에 대 한 섹션으로 구성 됩니다.
* 관련 엔터티 형식 아래에 속성 경로를 입력 합니다.
* 속성 이름을 `*`로 설정 하면 그 아래에 있는 모든 속성을 매개 변수화 하는 것을 알 수 있습니다 (재귀적이 아닌 첫 번째 수준 까지만). 이 구성에 대 한 예외를 제공할 수도 있습니다.
* 속성의 값을 문자열로 설정 하면 속성을 매개 변수화 하는 것을 알 수 있습니다.  `<action>:<name>:<stype>`형식을 사용 합니다.
   *  `<action>` 는 다음 문자 중 하나를 사용할 수 있습니다.
      * `=` 는 현재 값을 매개 변수의 기본값으로 유지 한다는 의미입니다.
      * `-` 는 매개 변수의 기본값을 유지 하지 않음을 의미 합니다.
      * `|` 는 연결 문자열이 나 키에 대 한 Azure Key Vault의 비밀에 대 한 특별 한 사례입니다.
   * `<name>` 는 매개 변수의 이름입니다. 비어 있는 경우 속성의 이름을 사용 합니다. 값이 `-` 문자로 시작 하는 경우에는 이름이 짧아집니다. 예를 들어 `AzureStorage1_properties_typeProperties_connectionString` `AzureStorage1_connectionString`으로 줄어듭니다.
   * `<stype>` 는 매개 변수의 형식입니다.  `<stype>` 비어 있는 경우 기본 유형은 `string`입니다. 지원 되는 값: `string`, `bool`, `number`, `object`및 `securestring`.
* 정의 파일에 배열을 지정 하면 템플릿의 일치 하는 속성이 배열 임을 나타냅니다. Data Factory 배열의 integration runtime 개체에 지정 된 정의를 사용 하 여 배열의 모든 개체를 반복 합니다. 두 번째 개체, 문자열은 각 반복에 대한 매개 변수의 이름으로 사용되는 속성의 이름이 됩니다.
* 정의는 리소스 인스턴스와 관련 될 수 없습니다. 모든 정의는 해당 형식의 모든 리소스에 적용 됩니다.
* 기본적으로 Key Vault 암호와 같은 모든 보안 문자열과 연결 문자열, 키, 토큰 등의 보안 문자열이 매개 변수화 됩니다.
 
### <a name="sample-parameterization-template"></a>샘플 매개 변수화 템플릿

매개 변수화 템플릿의 예는 다음과 같습니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
다음은 리소스 유형별로 분할 된 이전 템플릿의 구성 방법에 대 한 설명입니다.

#### <a name="pipelines"></a>파이프라인
    
* `activities/typeProperties/waitTimeInSeconds` 경로에 있는 모든 속성에 매개 변수가 있습니다. `waitTimeInSeconds` 라는 코드 수준 속성 (예: `Wait` 활동)이 있는 파이프라인의 모든 활동은 기본 이름을 가진 숫자로 매개 변수화 됩니다. 그러나 리소스 관리자 템플릿에는 기본값이 없습니다. 리소스 관리자 배포 중에 필수 입력이 됩니다.
* 마찬가지로 `headers` (예: `Web` 활동) 라는 속성은 형식 `object` (JObject)로 매개 변수화 됩니다. 이 값은 원본 팩터리의 값과 동일한 기본값을 갖습니다.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* `typeProperties` 경로 아래에 있는 모든 속성은 해당 기본값을 사용 하 여 매개 변수화 됩니다. 예를 들어 `IntegrationRuntimes` 형식 속성에는 `computeProperties` 및 `ssisProperties`라는 두 가지 속성이 있습니다. 두 속성 유형 모두 해당 기본값과 유형 (개체)을 사용 하 여 생성 됩니다.

#### <a name="triggers"></a>트리거

* `typeProperties`에서는 두 개의 속성을 매개 변수화 합니다. 첫 번째 값은 `maxConcurrency`이며 기본값을 갖도록 지정 되 고`string`형식입니다. `<entityName>_properties_typeProperties_maxConcurrency`기본 매개 변수 이름이 있습니다.
* 또한 `recurrence` 속성은 매개 변수가 있습니다. 이 수준에서 해당 수준의 모든 속성은 기본값으로 매개 변수화 되도록 지정 되며 기본값 및 매개 변수 이름이 사용 됩니다. 단, `number`형식으로 매개 변수화 되는 `interval` 속성은 예외입니다. 매개 변수 이름은 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`접미사로 붙습니다. 마찬가지로 `freq` 속성은 문자열이 며 문자열로 매개 변수화 됩니다. 그러나 `freq` 속성은 기본값이 없는 매개 변수화 됩니다. 이름이 단축 되 고 접미사가 붙습니다. `<entityName>_freq`)을 입력합니다.

#### <a name="linkedservices"></a>LinkedServices

* 연결 된 서비스는 고유 합니다. 연결 된 서비스 및 데이터 집합에는 다양 한 형식이 있으므로 유형별 사용자 지정을 제공할 수 있습니다. 이 예에서는 `AzureDataLakeStore`형식의 모든 연결 된 서비스에 대해 특정 템플릿이 적용 됩니다. 다른 모든 경우 (`*`을 통해) 다른 템플릿이 적용 됩니다.
* `connectionString` 속성은 `securestring` 값으로 매개 변수화 됩니다. 기본값은 없습니다. `connectionString`접미사로 사용 되는 약식 매개 변수 이름이 포함 됩니다.
* 속성 `secretAccessKey`는 Amazon S3 연결 된 서비스의 경우와 같이 `AzureKeyVaultSecret` 되는 경우입니다. 자동으로 Azure Key Vault 암호로 매개 변수화 되 고 구성 된 Key Vault에서 인출 됩니다. 키 자격 증명 모음을 매개 변수화 할 수도 있습니다.

#### <a name="datasets"></a>데이터 세트

* 데이터 집합에 대해 유형별 사용자 지정을 사용할 수 있지만 명시적으로 \*수준 구성을 사용 하지 않고도 구성을 제공할 수 있습니다. 위의 예제에서 `typeProperties` 아래의 모든 데이터 집합 속성은 매개 변수화 됩니다.

### <a name="default-parameterization-template"></a>기본 매개 변수화 템플릿

다음은 현재 기본 매개 변수화 템플릿입니다. 몇 개의 매개 변수만 추가 해야 하는 경우 기존 매개 변수화 구조를 잃지 않기 때문에이 템플릿을 직접 편집 하는 것이 좋을 수 있습니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

다음 예제에서는 기본 매개 변수화 템플릿에 단일 값을 추가 하는 방법을 보여 줍니다. Databricks 연결 된 서비스에 대 한 기존 Azure Databricks 대화형 클러스터 ID를 매개 변수 파일에 추가 하려고 합니다. 이 파일은 `Microsoft.DataFactory/factories/linkedServices`의 속성 필드에 `existingClusterId`을 추가 하는 것을 제외 하 고 이전 파일과 동일 합니다.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>연결된 Resource Manager 템플릿

데이터 팩터리에 대해 CI/CD를 설정한 경우 팩터리가 증가 함에 따라 Azure Resource Manager 템플릿 제한을 초과할 수 있습니다. 예를 들어 한 한도는 리소스 관리자 템플릿에서 최대 리소스 수입니다. 팩터리에 대 한 전체 리소스 관리자 템플릿을 생성 하는 동안 많은 팩터리를 수용 하기 위해 이제 Data Factory 연결 된 리소스 관리자 템플릿을 생성 합니다. 이 기능을 사용 하면 전체 팩터리 페이로드가 여러 파일로 분할 되어 제한에 의해 제한 되지 않습니다.

Git를 구성한 경우에는 연결 된 템플릿이 생성 되어 linkedTemplates 이라는 새 폴더의 adf_publish 분기에 전체 리소스 관리자 템플릿과 함께 저장 됩니다.

![연결된 Resource Manager 템플릿 폴더](media/continuous-integration-deployment/linked-resource-manager-templates.png)

연결 된 리소스 관리자 템플릿은 일반적으로 마스터에 연결 된 마스터 템플릿과 자식 템플릿 집합으로 구성 됩니다. 부모 템플릿을 ArmTemplate_master 이라고 하며, 자식 템플릿은 ArmTemplate_0. json, ArmTemplate_1 등의 패턴으로 이름이 지정 됩니다. 

전체 리소스 관리자 템플릿 대신 연결 된 템플릿을 사용 하려면 ArmTemplateForFactory (전체 리소스 관리자 템플릿) 대신 ArmTemplate_master를 가리키도록 CI/CD 작업을 업데이트 합니다. 또한 Azure에서 배포 하는 동안 액세스할 수 있도록 연결 된 템플릿을 저장소 계정에 업로드 해야 하는 리소스 관리자. 자세한 내용은 [VSTS를 사용 하 여 연결 된 리소스 관리자 템플릿 배포](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)를 참조 하세요.

배포 작업 전후에 CI/CD 파이프라인에 Data Factory 스크립트를 추가해야 합니다.

Git이 구성 되지 않은 경우 **Arm 템플릿** 목록에서 **arm 템플릿 내보내기** 를 통해 연결 된 템플릿에 액세스할 수 있습니다.

## <a name="hotfix-production-branch"></a>핫픽스 프로덕션 분기

공장을 프로덕션에 배포 하 고 즉시 해결 해야 하는 버그가 있는 경우 현재 공동 작업 분기를 배포할 수 없는 경우 핫픽스를 배포 해야 할 수 있습니다. 이 접근 방식은 빠른 수정 엔지니어링 또는 QFE 라고 합니다.

1.  Azure DevOps에서 프로덕션에 배포 된 릴리스로 이동 합니다. 배포 된 마지막 커밋을 찾습니다.

2.  커밋 메시지에서 공동 작업 분기의 커밋 ID를 가져옵니다.

3.  해당 커밋에서 새 핫픽스 분기를 만듭니다.

4.  Azure Data Factory UX로 이동 하 여 핫픽스 분기로 전환 합니다.

5.  Azure Data Factory UX를 사용 하 여 버그를 수정 합니다. 변경 내용을 테스트 합니다.

6.  수정이 확인 되 면 **ARM 템플릿 내보내기** 를 선택 하 여 핫픽스 리소스 관리자 템플릿을 가져옵니다.

7.  Adf_publish 분기에이 빌드를 수동으로 확인 합니다.

8.  Adf_publish 체크 인에 따라 자동으로 트리거되도록 릴리스 파이프라인이 구성 된 경우 새 릴리스가 자동으로 시작 됩니다. 그렇지 않으면 수동으로 릴리스를 큐에 대기 합니다.

9.  핫픽스 릴리스를 테스트 및 프로덕션 팩터리에 배포 합니다. 이 릴리스에는 이전 프로덕션 페이로드와 5 단계에서 만든 수정 사항이 포함 되어 있습니다.

10. 이후 릴리스에 동일한 버그가 포함 되지 않도록 핫픽스의 변경 내용을 개발 분기에 추가 합니다.

## <a name="best-practices-for-cicd"></a>CI/CD에 대한 모범 사례

데이터 팩터리에 Git 통합을 사용 하 고 변경 내용을 개발에서 테스트로 이동 하는 CI/CD 파이프라인을 사용 하는 경우 다음 모범 사례를 따르는 것이 좋습니다.

-   **Git 통합**. Git 통합을 사용 하 여 개발 데이터 팩터리만 구성 해야 합니다. 테스트 및 프로덕션에 대 한 변경 사항은 CI/CD를 통해 배포 되며 Git 통합이 필요 하지 않습니다.

-   **Data Factory CI/CD 스크립트**. CI/CD의 리소스 관리자 배포 단계 전에 트리거를 중지 하 고 다시 시작 하 고 정리를 수행 하는 등의 특정 작업을 완료 해야 합니다. 배포 전후에 PowerShell 스크립트를 사용 하는 것이 좋습니다. 자세한 내용은 [활성 트리거 업데이트](#update-active-triggers)를 참조 하세요.

-   **통합 런타임 및 공유**. 통합 런타임은 자주 변경 되지 않으며 CI/CD의 모든 단계에서 유사 합니다. 따라서 Data Factory는 CI/CD의 모든 단계에서 동일한 이름 및 유형의 통합 런타임을 사용할 것으로 예상 합니다. 모든 단계에서 통합 런타임을 공유 하려면 공유 통합 런타임을 포함 하기 위해 삼항 팩터리를 사용 하는 것이 좋습니다. 모든 환경에서이 공유 팩터리를 연결 된 통합 런타임 형식으로 사용할 수 있습니다.

-   **Key Vault**. Azure Key Vault에 따라 연결 된 서비스를 사용 하는 경우 다른 환경에 대해 별도의 키 자격 증명 모음을 유지 하 여이를 활용할 수 있습니다. 각 키 자격 증명 모음에 대 한 별도의 권한 수준을 구성할 수도 있습니다. 예를 들어 팀 멤버에 게 프로덕션 암호에 대 한 사용 권한을 부여 하지 않으려고 할 수 있습니다. 이 접근 방식을 따를 경우 모든 단계에서 동일한 비밀 이름을 유지 하는 것이 좋습니다. 동일한 이름을 유지 하는 경우 리소스 관리자 템플릿 매개 변수 중 하나인 키 자격 증명 모음 이름만 변경 되므로 CI/CD 환경에서 리소스 관리자 템플릿을 변경할 필요가 없습니다.

## <a name="unsupported-features"></a>지원되지 않는 기능

- 기본적으로 Data Factory는 커밋 또는 리소스의 선택적 게시를 cherry-pick 수 없습니다. 게시에는 데이터 팩터리에 대 한 모든 변경 내용이 포함 됩니다.

    - 데이터 팩터리 엔터티는 서로 종속 됩니다. 예를 들어 트리거는 파이프라인에 따라 다르며 파이프라인은 데이터 집합 및 기타 파이프라인에 종속 됩니다. 리소스 하위 집합을 선택적으로 게시 하면 예기치 않은 동작 및 오류가 발생할 수 있습니다.
    - 드물지만 선택적 게시를 사용 해야 하는 경우 핫픽스를 사용 하는 것이 좋습니다. 자세한 내용은 [핫픽스 프로덕션 분기](#hotfix-production-branch)를 참조 하세요.

-   프라이빗 분기에서 게시할 수 없습니다.

-   현재 Bitbucket에서 프로젝트를 호스트할 수 없습니다.
