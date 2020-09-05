---
title: PowerShell 스크립트 - 삭제된 파일 공유 삭제 취소
description: Azure PowerShell 스크립트를 사용하여 실수로 삭제한 파일 공유의 삭제를 취소하는 방법을 알아봅니다.
ms.topic: sample
ms.date: 02/02/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99cd980e13b3b9b9a7a450520218c76ccf26fd69
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079866"
---
# <a name="powershell-script-to-undelete-an-accidentally-deleted-file-share"></a>삭제된 파일 공유를 삭제 취소하는 PowerShell 스크립트

이 스크립트는 파일 공유를 실수로 삭제한 경우 삭제를 취소하는 데 도움이 됩니다. 파일 공유에 대한 일시 삭제 보안 기능은 14일 보존 기간 안에 파일 공유의 삭제를 취소하여 모든 파일 공유 콘텐츠, 스냅샷, 복구 지점을 복구하는 옵션을 제공합니다. 일시 삭제에 대한 자세한 정보를 보려면 이 [링크](../soft-delete-azure-file-share.md)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

```powershell
#Import-Module Az.Storage -MinimumVersion 1.7.0 -Scope Local
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId,
        [Parameter(Mandatory=$False)][System.Boolean] $ListOption,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
    )

Function Restore-DeletedFileShare
{
    Param(
        [Parameter(Mandatory=$True)][Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext] $Context,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
        )

    if ([string]::IsNullOrWhiteSpace($FileShareName))
    {
        Write-Error "Please specify the required input parameter: FileShareName" -ErrorAction Stop
    }

    $FileShareName = $FileShareName.ToLowerInvariant()

    Write-Verbose "Restoring a file share with the name: $FileShareName" -Verbose


    Write-Information -MessageData "Started: Creating SASToken to List File Shares" -InformationAction Continue

    $listToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Service -Permission "l" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to List File Shares" -InformationAction Continue

    Write-Information -MessageData "Started: Listing File Shares to find the deleted file share" -InformationAction Continue

    $listSharesUrl = [string]::Concat($Context.FileEndPoint, "?include=metadata,deleted&comp=list&api-version=2019-10-10&", $listToken.Substring(1))

    $listSharesResponse = Invoke-WebRequest $listSharesUrl -Method "GET" -Verbose

    if ($listSharesResponse.StatusCode -ne 200)
    {
        Write-Error "Request to list file shares failed." -ErrorAction Stop
    }

    Write-Verbose $listSharesResponse.RawContent -Verbose

    $listSharesResponseContent = $listSharesResponse.Content.Substring(3)

    Write-Information -MessageData "Completed: Listing File Shares to find the deleted file share" -InformationAction Continue

    Write-Information -MessageData "Started: Search for a deleted file share with the specified name" -InformationAction Continue

    $deletedFileShares = Select-Xml -Content $listSharesResponseContent -XPath "/EnumerationResults/Shares/Share[Deleted=""true"" and Name=""$FileShareName""]"

    $matchedCount = 0
    $deletedShareVersions = New-Object System.Collections.Generic.List[string]

    foreach($share in $deletedFileShares)
    {
        if($matchedCount -eq 0)
        {
          Write-Verbose $share.Node.InnerXml -Verbose

          Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found versions" -InformationAction Continue
        }

        $shareVer = $share.Node.Item("Version").InnerText
        $shareDelTime = $share.Node.Item("Properties").Item("DeletedTime").InnerText
        $retDays = $share.Node.Item("Properties").Item("RemainingRetentionDays").InnerText

        $deletedShareVersions.Add($share.Node.Item("Version").InnerText)

        Write-Information -MessageData "DeletedVersion: $shareVer, DeletedTime: $shareDelTime, RemainingRetentionDays: $retDays"  -InformationAction Continue

        $matchedCount++
    }

    if($ListOption -eq $True)
    {
       return;
    }

    if ($matchedCount -eq 0)
    {
        Write-Error "Deleted file share with the specified name was not found." -ErrorAction Stop
    }
    elseif($matchedCount -eq 1 -and ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or $deletedShareVersions.Contains($DeletedShareVersion)))
    {
      $DeletedShareVersion = $deletedShareVersions
    }
    elseif ($matchedCount -gt 1)
    {
      if ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or !$deletedShareVersions.Contains($DeletedShareVersion))
      {
        Write-Error "More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values." -ErrorAction Stop
      }
    }

    Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found version: $DeletedShareVersion" -InformationAction Continue

    Write-Information -MessageData "Started: Creating SASToken to Restore File Share" -InformationAction Continue

    $restoreToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Container -Permission "w" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to Restore File Share" -InformationAction Continue

    Write-Information -MessageData "Started: Restore File Share" -InformationAction Continue

 $restoreShareUrl = [string]::Concat($Context.FileEndPoint, $FileShareName, "?restype=share&comp=undelete&api-version=2019-10-10&", $restoreToken.Substring(1))

    $restoreHeaders = @{"x-ms-deleted-share-name" = $FileShareName; "x-ms-deleted-share-version" = $DeletedShareVersion}

    $restoreResponse = Invoke-WebRequest $restoreShareUrl -Headers $restoreHeaders -Method "PUT" -Verbose

    if ($restoreResponse.StatusCode -ne 201)
    {
        Write-Error "Request to restore a file share failed." -ErrorAction Stop
    }

    Write-Verbose $restoreResponse.RawContent -Verbose

    Write-Information -MessageData "Completed: Restore File Share" -InformationAction Continue
}

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName


Restore-DeletedFileShare $sa.Context $FileShareName $DeletedShareVersion
```

## <a name="how-to-use-the-script-in-different-scenarios"></a>다른 시나리오에서 이 스크립트를 사용하는 방법

### <a name="prerequisites"></a>필수 구성 요소

1. 스크립트를 실행하기 전에 [이 링크](/powershell/azure/install-az-ps)에서 최신 Azure PowerShell Az 모듈을 설치합니다.
2. 다음 세부 정보는 스크립트의 다른 매개 변수에 대한 값으로 전달해야 하므로 보관해 둡니다.

    * **-SubscriptionId** - 파일 공유가 있는 구독의 ID입니다.
    * **-ResourceGroupName** - 파일 공유를 호스트하는 스토리지 계정의 리소스 그룹입니다.
    * **-StorageAccountName** - 파일 공유를 호스트하는 스토리지 계정의 이름입니다.
    * **-FileShareName** - 삭제를 취소할 파일 공유의 이름입니다.

### <a name="execution-steps"></a>실행 단계

1. 위의 스크립트를 사용자가 선택한 이름으로 머신에 저장합니다. 이 예제에서는 *Undelete.ps1*로 저장했습니다.
2. 요구 사항에 맞는 시나리오에 따라 스크립트를 실행합니다.

#### <a name="scenario-1"></a>시나리오 1

삭제를 취소하려는 파일 공유와 이름이 같은 여러 개의 삭제된 버전이 없습니다.

다음 예에서는 스토리지 계정 *afsshare*에 있는 파일 공유 *share1*의 삭제를 취소합니다.

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

출력에 `Completed:Restore File Share` 메시지가 표시되어야 합니다.

#### <a name="scenario-2"></a>시나리오 2

삭제를 취소하려는 파일 공유와 이름이 같은 여러 개의 삭제된 버전이 있습니다.

다음 예에서는 *share1* 파일 공유의 버전을 삭제 취소합니다.

##### <a name="step-1"></a>1단계

파일 공유 이름을 제공하여 다음과 같이 스크립트를 실행합니다.

```PowerShell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

```Output
Completed: Search for a deleted file share with the specified name and Found versions
DeletedVersion: 01D5D7F77ACC7864, DeletedTime: Fri, 31 Jan 2020 05:30:33 GMT, RemainingRetentionDays: 14
DeletedVersion: 01D5D7F7A76CAF42, DeletedTime: Fri, 31 Jan 2020 05:31:25 GMT, RemainingRetentionDays: 14
Restore-DeletedFileShare : More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values.
```

##### <a name="step-2"></a>2단계

1단계 출력에서 삭제를 취소하려는 버전을 선택하고 **-DeletedShareVersion** 매개 변수의 값으로 전달합니다.

다음 예에서는 *share1* 파일 공유의 *01D5D7F77ACC7864* 버전을 삭제 취소합니다.

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare-StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1 -DeletedShareVersion 01D5D7F77ACC7864
```
