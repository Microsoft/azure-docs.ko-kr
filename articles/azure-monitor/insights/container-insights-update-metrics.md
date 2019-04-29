---
title: Azure Monitor 메트릭에 대 한 컨테이너에 대 한 업데이트 하는 방법 | Microsoft Docs
description: 이 문서에서는 집계 된 메트릭을 탐색 하 고 경고를 지 원하는 사용자 지정 메트릭 기능은 사용할 수 있도록 컨테이너에 대 한 Azure Monitor를 업데이트 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: f0c9b84f31810a4b0cec93738f7ce327bc24d8d0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101611"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>메트릭을 사용 하도록 설정 하려면 컨테이너에 대 한 Azure Monitor를 업데이트 하는 방법
컨테이너에 대 한 azure Monitor는 Azure Kubernetes 서비스 (AKS) 클러스터 노드 및 pod에서 메트릭을 수집 하 고 Azure Monitor 메트릭 저장소에 쓰기에 대 한 지원을 소개 합니다. Azure portal 대시보드에서 성능 차트를 고정 하는 지원의 성능 차트에서 집계 계산 (Avg, Count, Max, Min, Sum)를 표시할 때 향상 된 적시성을 제공 하 고 메트릭 경고를 지원 합니다.이 변경이 됩니다.

다음과 같은 메트릭이이 기능의 일부로 사용 됩니다.

| 메트릭 네임스페이스 | 메트릭 | 설명 |
|------------------|--------|-------------|
| insights.container/nodes | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount | 이들은 *노드* 메트릭을 포함 *호스트* 차원에 있으며도 포함 합니다<br> 노드 이름에 대 한 값으로는 *호스트* 차원입니다. |
| insights.container/pods | podCount | 이들은 *pod* 메트릭 다음 차원-ControllerName, Kubernetes 네임 스페이스, 이름, 단계를 포함 합니다. |

Azure 포털, Azure PowerShell 또는 Azure CLI를 사용 하 여 이러한 새 기능을 지원 하도록 클러스터 업데이트를 수행할 수 있습니다. Azure PowerShell 및 CLI를 사용 하 여이 당 클러스터를 설정할 수 있습니다. 또는 구독에서 모든 클러스터에 대 한 합니다. AKS의 새로운 배포에서는이 구성 변경 내용과 기능에 자동으로 포함 됩니다.

할당을 처리 중 하나는 **모니터링 메트릭을 게시자** 역할 클러스터의 서비스 주체에 에이전트에 의해 데이터가 수집 되도록를 게시할 수 있는 클러스터 리소스입니다. 푸시 메트릭 리소스에 권한이 메트릭 게시자를 모니터링, 모든 상태 변경, 리소스를 업데이트 하거나 데이터를 읽을 수 없습니다 것입니다. 역할에 대 한 자세한 내용은 참조 [모니터링 메트릭을 게시자 역할](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)입니다.

## <a name="prerequisites"></a>필수 조건 
시작 하기 전에의 구성원 인지 확인 합니다 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** 노드의 컬렉션을 사용 하도록 설정 하 고 사용자 지정 성능 메트릭을 pod를 AKS 클러스터 리소스에 대 한 역할입니다. 

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 실행 해야 이상. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Azure portal에서 클러스터 업그레이드

컨테이너에 대 한 Azure Monitor에서 모니터링 되는 기존 AKS 클러스터를 선택 하 여 클러스터에서 직접 또는 Azure Monitor의 다중 클러스터 뷰에서 해당 상태를 볼 클러스터를 선택 후 **Insights** 왼쪽에서 창 포털의 위쪽에 배너가 표시 됩니다.

![Azure portal에서 AKS 클러스터 배너를 업그레이드 합니다.](./media/container-insights-update-metrics/portal-banner-enable-01.png)

클릭 **사용** 클러스터를 업그레이드 하는 프로세스를 시작 합니다. 이 프로세스를 완료 하려면 몇 초 정도 걸릴 수 있습니다 하 고 메뉴에서 알림 진행률을 추적할 수 있습니다.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Azure 명령 Shell의 Bash를 사용 하 여 모든 클러스터 업그레이드
Azure 명령 Shell의 Bash를 사용 하 여 구독에서 모든 클러스터를 업데이트 하려면 다음 단계를 수행 합니다.

1. Azure CLI를 사용 하 여 다음 명령을 실행 합니다.  에 대 한 값을 편집할 **subscriptionId** 에서 값을 사용 하는 **AKS 개요** AKS 클러스터에 대 한 페이지입니다.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://git.io/aks-mdm-onboarding-atscale | bash -s subscriptionId   
    ```

    구성 변경을 완료 하려면 몇 초 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Azure CLI를 사용 하 여 클러스터당 업그레이드
Azure CLI를 사용 하 여 구독의 특정 클러스터를 업데이트 하려면 다음 단계를 수행 합니다.

1. Azure CLI를 사용 하 여 다음 명령을 실행 합니다. 값을 편집 **subscriptionId**를 **resourceGroupName**, 및 **clusterName** 에 값을 사용 하는 **AKS 개요** 페이지는 AKS 클러스터입니다.  값을 가져올 **clientIdOfSPN**을 명령을 실행할 때 반환 됩니다 `az aks show` 아래 예와에서 같이 합니다.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ``` 

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 모든 클러스터 업그레이드
Azure PowerShell을 사용 하 여 구독의 모든 클러스터를 업데이트 하려면 다음 단계를 수행 합니다.

1. 복사 하 여 파일에 다음 스크립트를 붙여넣습니다.

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the all AKS clusters in specified subscription        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    #>

    param(
    [Parameter(mandatory = $true)]
    [string]$SubscriptionId 
    )


    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ( $null -eq $azAksModule ) -or ($null -eq $azResourcesModule)) {

    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command if not installed already`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule)   {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }   
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule)   {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }    
            }     
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    try {
    Write-Host("")
    Write-Host("Trying to get the current Az login context...")
    $account = Get-AzContext -ErrorAction Stop
    Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
    Write-Host("")
    }
    catch {
    Write-Host("")
    Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
    Write-Host("")
    }

    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    } 

    #
    #   get all the AKS clusters in specified subscription
    #
    Write-Host("getting all aks clusters in specified subscription ...")
    $allClusters = Get-AzAks -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
    Write-Host("")
    Write-Host("Failed to get Aks clusters in specified subscription. Please make sure that you have access to the existing clusters") -ForegroundColor Red
    Write-Host("")
    Stop-Transcript
    exit
    }
    Write-Host("Successfully got all aks clusters ...") -ForegroundColor Green

    $clustersCount = $allClusters.Id.Length

    Write-Host("Adding role assignment for the clusters ...")

    for ($index = 0 ; $index -lt $clustersCount ; $index++) {  

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    $servicePrincipalClientId = $allClusters.ServicePrincipalProfile[$index].ClientId
    $clusterResourceId = $allClusters.Id[$index]
    $clusterName = $allClusters.Name[$index]


    Write-Host("Adding role assignment for the cluster: $clusterResourceId, servicePrincipalClientId: $servicePrincipalClientId ...")

  
    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

        $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     
        if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {
            Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
        }
        else { 
        
            Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
        }

    }
    else {

        Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }   

    Write-Host("Completed adding role assignment for the cluster: $clusterName ...")
        
    }

    Write-Host("Completed adding role assignment for the aks clusters in subscriptionId :$SubscriptionId")   
    ```

2. 이 파일을 저장할 **onboard_metrics_atscale.ps1** 로컬 폴더에 있습니다.
3. Azure PowerShell을 사용 하 여 다음 명령을 실행 합니다.  에 대 한 값을 편집할 **subscriptionId** 에서 값을 사용 하는 **AKS 개요** AKS 클러스터에 대 한 페이지입니다.

    ```powershell
    .\onboard_metrics_atscale.ps1 subscriptionId
    ```
    구성 변경을 완료 하려면 몇 초 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 클러스터당 업그레이드
Azure PowerShell을 사용 하 여 특정 클러스터를 업데이트 하려면 다음 단계를 수행 합니다.

1. 복사 하 여 파일에 다음 스크립트를 붙여넣습니다.

    ```powershell
    <# 
    .DESCRIPTION 
        Adds the Monitoring Metrics Publisher role assignment to the specified AKS cluster        
      
    
    .PARAMETER SubscriptionId
        Subscription Id that the AKS cluster is in

    .PARAMETER resourceGroupName
        Resource Group name that the AKS cluster is in
            
     .PARAMETER clusterName
        Name of the AKS cluster.
    #>

    param(
       [Parameter(mandatory = $true)]
       [string]$SubscriptionId,
       [Parameter(mandatory = $true)]
       [string]$resourceGroupName,
       [Parameter(mandatory = $true)]
       [string] $clusterName
    )

    # checks the required Powershell modules exist and if not exists, request the user permission to install
    $azAccountModule = Get-Module -ListAvailable -Name Az.Accounts
    $azAksModule = Get-Module -ListAvailable -Name Az.Aks 
    $azResourcesModule = Get-Module -ListAvailable -Name Az.Resources

    if (($null -eq $azAccountModule) -or ($null -eq $azAksModule) -or ($null -eq $azResourcesModule)) {


    $currentPrincipal = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())

    if ($currentPrincipal.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
        Write-Host("Running script as an admin...")
        Write-Host("")
    }
    else {
        Write-Host("Please run the script as an administrator") -ForegroundColor Red
        Stop-Transcript
        exit
    }


    $message = "This script will try to install the latest versions of the following Modules : `
                Az.Resources, Az.Accounts and Az.Aks using the command`
                `'Install-Module {Insert Module Name} -Repository PSGallery -Force -AllowClobber -ErrorAction Stop -WarningAction Stop'
                `If you do not have the latest version of these Modules, this troubleshooting script may not run."
    $question = "Do you want to Install the modules and run the script or just run the script?"

    $choices = New-Object Collections.ObjectModel.Collection[Management.Automation.Host.ChoiceDescription]
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Yes, Install and run'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Continue without installing the Module'))
    $choices.Add((New-Object Management.Automation.Host.ChoiceDescription -ArgumentList '&Quit'))

    $decision = $Host.UI.PromptForChoice($message, $question, $choices, 0)

    switch ($decision) {
        0 { 

            if ($null -eq $azResourcesModule) {
                try {
                    Write-Host("Installing Az.Resources...")
                    Install-Module Az.Resources -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAccountModule) {
                try {
                    Write-Host("Installing Az.Accounts...")
                    Install-Module Az.Accounts -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules forAz.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    exit
                }
            }

            if ($null -eq $azAksModule) {
                try {
                    Write-Host("Installing Az.Aks...")
                    Install-Module Az.Aks -Repository PSGallery -Force -AllowClobber -ErrorAction Stop
                }
                catch {
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Aks in a new powershell window: eg. 'Install-Module Az.Aks -Repository PSGallery -Force'") -ForegroundColor Red 
                    exit
                }
            }
           
        }
        1 {

            if ($null -eq $azResourcesModule) {
                try {
                    Import-Module Az.Resources -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Resources...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Resources in a new powershell window: eg. 'Install-Module Az.Resources -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAccountModule) {
                try {
                    Import-Module Az.Accounts -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Accounts...") -ForegroundColor Red
                    Write-Host("Close other powershell logins and try installing the latest modules for Az.Accounts in a new powershell window: eg. 'Install-Module Az.Accounts -Repository PSGallery -Force'") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }
            }
            if ($null -eq $azAksModule) {
                try {
                    Import-Module Az.Aks -ErrorAction Stop
                }
                catch {
                    Write-Host("Could not import Az.Aks... Please reinstall this Module") -ForegroundColor Red
                    Stop-Transcript
                    exit
                }   
            }      
    
        }
        2 { 
            Write-Host("")
            Stop-Transcript
            exit
        }
     }
    }

    try {
       Write-Host("")
       Write-Host("Trying to get the current Az login context...")
       $account = Get-AzContext -ErrorAction Stop
       Write-Host("Successfully fetched current AzContext context...") -ForegroundColor Green
       Write-Host("")
    }
    catch {
      Write-Host("")
      Write-Host("Could not fetch AzContext..." ) -ForegroundColor Red
      Write-Host("")
    }


    if ($account.Account -eq $null) {
    try {
        Write-Host("Please login...")
        Connect-AzAccount -subscriptionid $SubscriptionId
    }
    catch {
        Write-Host("")
        Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
        Write-Host("")
        Stop-Transcript
        exit
     }
    }
    else {
    if ($account.Subscription.Id -eq $SubscriptionId) {
        Write-Host("Subscription: $SubscriptionId is already selected. Account details: ")
        $account
    }
    else {
        try {
            Write-Host("Current Subscription:")
            $account
            Write-Host("Changing to subscription: $SubscriptionId")
            Set-AzContext -SubscriptionId $SubscriptionId
        }
        catch {
            Write-Host("")
            Write-Host("Could not select subscription with ID : " + $SubscriptionId + ". Please make sure the ID you entered is correct and you have access to the cluster" ) -ForegroundColor Red
            Write-Host("")
            Stop-Transcript
            exit
        }
      }
    }

    #
    #   Check AKS cluster existance and access check
    #
    Write-Host("Checking aks cluster exists...")
    $cluster = Get-AzAks -ResourceGroupName $resourceGroupName -Name $clusterName  -ErrorVariable notPresent -ErrorAction SilentlyContinue
    if ($notPresent) {
       Write-Host("")
       Write-Host("Could not find Aks cluster. Please make sure that specified cluster exists: '" + $clusterName + "'is correct and you have access to the cluster") -ForegroundColor Red
       Write-Host("")
       Stop-Transcript
       exit
    }
    Write-Host("Successfully checked specified cluster exists details...") -ForegroundColor Green

    $servicePrincipalClientId = $cluster.ServicePrincipalProfile.clientId
    $clusterResourceId = $cluster.Id

    #
    #  Add Monitoring Metrics Publisher role assignment to the AKS cluster resource
    #

    New-AzRoleAssignment -ApplicationId $servicePrincipalClientId -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher"  -ErrorVariable assignmentError -ErrorAction SilentlyContinue

    if ($assignmentError) {

    $roleAssignment = Get-AzRoleAssignment -scope $clusterResourceId -RoleDefinitionName "Monitoring Metrics Publisher" -ErrorVariable getAssignmentError -ErrorAction SilentlyContinue     

    if ($assignmentError.Exception -match "role assignment already exists" -or ( $roleAssignment -and $roleAssignment.ObjectType -like "ServicePrincipal" )) {           
        Write-Host("Monitoring Metrics Publisher role assignment already exists on the cluster resource : '" + $clusterName + "'") -ForegroundColor Green 
    }
    else { 
        
        Write-Host("Failed to add Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "' , error : $assignmentError") -ForegroundColor Red      
    }

    }
    else {

    Write-Host("Successfully added Monitoring Metrics Publisher role assignment to cluster : '" + $clusterName + "'") -ForegroundColor Green 
   
    }
    ```

2. 이 파일을 저장할 **onboard_metrics.ps1** 로컬 폴더에 있습니다.
3. Azure PowerShell을 사용 하 여 다음 명령을 실행 합니다. 값을 편집 **subscriptionId**를 **resourceGroupName**, 및 **clusterName** 에 값을 사용 하는 **AKS 개요** 페이지는 AKS 클러스터입니다.

    ```powershell
    .\onboard_metrics.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    구성 변경을 완료 하려면 몇 초 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>업데이트를 확인 합니다. 
앞에서 설명한 방법 중 하나를 사용 하 여 업데이트를 시작한 후 Azure Monitor 메트릭 탐색기를 사용 하는에서 확인 합니다 **메트릭 네임 스페이스** 하는 **insights** 나열 됩니다. 계속 해 서를 설정을 시작 나타냅니다 인 경우 [메트릭 경고](../platform/alerts-metric.md) 차트를 고정 하거나 [대시보드](../../azure-portal/azure-portal-dashboards.md)합니다.  
