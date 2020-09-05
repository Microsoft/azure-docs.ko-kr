---
title: 블록 Blob에 대한 지정 시간 복원 설정 및 관리(미리 보기)
titleSuffix: Azure Storage
description: 지정 시간 복원(미리 보기)을 사용하여 블록 Blob을 이전 시점의 상태로 복원하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9a4c68454807cb26ac62799b598f146680e37c42
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230180"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>블록 Blob에 대한 지정 시간 복원 설정 및 관리(미리 보기)

지정 시간 복원(미리 보기)을 사용하여 블록 Blob을 이전 시점의 상태로 복원할 수 있습니다. 이 문서에서는 PowerShell을 사용하여 스토리지 계정에 대한 지정 시점 복원을 사용하도록 설정하는 방법을 설명합니다. PowerShell을 사용하여 복원 작업을 수행하는 방법도 보여줍니다.

자세한 내용 및 미리 보기에 등록하는 방법을 알아보려면 [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md)을 참조하세요.

> [!CAUTION]
> 지정 시간 복원은 블록 Blob에 대한 작업만 복원하도록 지원합니다. 컨테이너에 대한 작업은 복원할 수 없습니다. 지정 시간 복원 미리 보기 중에 [컨테이너 삭제](/rest/api/storageservices/delete-container) 작업을 호출하여 스토리지 계정에서 컨테이너를 삭제하면 해당 컨테이너는 복원 작업을 통해 복원할 수 없습니다. 미리 보기 중에는, Blob을 복원해야 할 수도 있으면 컨테이너를 삭제하는 대신 개별 Blob을 삭제하는 것이 좋습니다.

> [!IMPORTANT]
> 지정 시간 복원 미리 보기는 비프로덕션 용도로만 사용해야 합니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다.

## <a name="install-the-preview-module"></a>미리 보기 모듈 설치

PowerShell을 사용 하 여 Azure 지정 시간 복원을 구성 하려면 먼저 Az. Storage preview module version 1.14.1이-preview 이상을 설치 합니다. 최신 미리 보기 버전을 사용 하는 것이 좋지만 특정 시점 복원은 버전 1.14.1이-preview 이상에서 지원 됩니다. Az. Storage 모듈의 다른 버전을 제거 합니다.

다음 명령은 Az. Storage [2.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) 모듈을 설치 합니다.

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

위의 명령을 설치 하려면 버전 2.2.4.1 PowerShellGet 이상이 필요 합니다. 현재 로드 된 버전을 확인 하려면 다음을 수행 합니다.
```powershell
Get-Module PowerShellGet
```
Azure PowerShell을 설치하는 방법에 대한 자세한 내용은 [PowerShellGet을 사용하여 Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

## <a name="enable-and-configure-point-in-time-restore"></a>지정 시간 복원 활성화 및 구성

지정 시간 복원을 사용 하도록 설정 하 고 구성 하기 전에 저장소 계정에 대 한 필수 구성 요소 (일시 삭제, 변경 피드 및 blob 버전 관리)를 사용 하도록 설정 합니다. 이러한 각 기능을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Blob에 일시 삭제를 사용하도록 설정](soft-delete-enable.md)
- [변경 피드를 사용하거나 사용하지 않도록 설정](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)

PowerShell을 사용하여 Azure 지정 시간 복원을 구성하려면 Enable-AzStorageBlobRestorePolicy 명령을 호출합니다. 다음 예제는 일시 삭제를 사용하도록 설정하고, 일시 삭제 보존 기간을 설정하고, 변경 피드를 사용하도록 설정하고, 특정 시점 복원을 사용하도록 설정합니다. 예제를 실행하기 전에 Azure Portal 또는 Azure Resource Manager 템플릿을 사용하여 Blob 버전 관리도 사용하도록 설정합니다.

예제를 실행하는 경우 꺾쇠 괄호의 값을 고유한 값으로 바꿔야 합니다.

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>복원 작업 수행

복원 작업을 시작 하려면 복원 지점을 UTC **날짜/시간** 값으로 지정 하 여 **AzStorageBlobRange** 명령을 호출 합니다. 복원할 blob의 범위를 사전순으로 지정 하거나, 범위를 생략 하 여 저장소 계정의 모든 컨테이너에 있는 모든 blob를 복원할 수 있습니다. 복원 작업당 최대 10 개의 사전순 범위가 지원 됩니다. 페이지 blob 및 추가 blob은 복원에 포함 되지 않습니다. 복원 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

복원할 Blob 범위를 지정할 때는 다음 규칙에 유의해야 합니다.

- 시작 범위와 끝 범위에 지정된 컨테이너 패턴은 최소 3자 이상을 포함해야 합니다. 컨테이너 이름을 Blob 이름과 구분하는 데 사용되는 슬래시(/)는 이 최솟값에 포함되지 않습니다.
- 복원 작업당 최대 10 개 범위를 지정할 수 있습니다.
- 와일드카드 문자는 지원되지 않습니다. 표준 문자로 처리됩니다.
- 복원 작업에 전달된 범위에서 명시적으로 지정하여 `$root` 및 `$web` 컨테이너에서 Blob을 복원할 수 있습니다. `$root` 및 `$web` 컨테이너는 명시적으로 지정된 경우에만 복원됩니다. 다른 시스템 컨테이너는 복원할 수 없습니다.

> [!IMPORTANT]
> 복원 작업을 수행 하는 경우 Azure Storage은 작업 기간 동안 복원 되는 범위의 blob에 대 한 데이터 작업을 차단 합니다. 읽기, 쓰기 및 삭제 작업은 기본 위치에서 차단 됩니다. 이러한 이유로 복원 작업이 진행 되는 동안 Azure Portal의 컨테이너 나열 등의 작업이 예상 대로 수행 되지 않을 수 있습니다.
>
> 저장소 계정이 지리적으로 복제 되는 경우 보조 위치에서 읽기 작업은 복원 작업 중에 진행할 수 있습니다.

### <a name="restore-all-containers-in-the-account"></a>계정의 모든 컨테이너 복원

저장소 계정의 모든 컨테이너와 blob을 복원 하려면 **AzStorageBlobRange** 명령을 호출 하 고 매개 변수를 생략 합니다 `-BlobRestoreRange` . 다음 예는 스토리지 계정의 컨테이너를 현재 시점 12시간 전의 상태로 복원합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>단일 블록 Blob 범위 복원

Blob 범위를 복원 하려면 **AzStorageBlobRange** 명령을 호출 하 고 매개 변수에 대 한 사전순의 컨테이너 및 blob 이름을 지정 `-BlobRestoreRange` 합니다. 범위의 시작은 포함되고 범위의 끝은 제외됩니다.

예를 들어 *sample-container*라는 단일 컨테이너에 Blob을 복원하려면 *sample-container*로 시작하고 *sample-container1*로 끝나는 범위를 지정하면 됩니다. 시작 및 끝 범위에 명명된 컨테이너가 존재할 필요는 없습니다. 범위의 끝은 제외되기 때문에 스토리지 계정에 *sample-container1*이라는 컨테이너가 포함되어 있어도 *sample-container*라는 컨테이너만 복원됩니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

복원할 컨테이너에서 Blob의 하위 집합을 지정하려면 슬래시(/)를 사용하여 컨테이너 이름을 Blob 패턴과 구분합니다. 예를 들어 다음 범위는 단일 컨테이너에서 이름이 *d*~*f* 문자로 시작하는 Blob을 선택합니다.

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

그런 다음 **AzStorageBlobRange** 명령에 범위를 제공 합니다. `-TimeToRestore` 매개 변수에 UTC **DateTime** 값을 제공하여 복원 지점을 지정합니다. 다음 예제는 지정된 범위의 Blob을 현재 시점에서 3일 전의 상태로 복원합니다.

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>여러 블록 Blob 범위 복원

여러 블록 Blob 범위를 복원하려면 `-BlobRestoreRange` 매개 변수에 범위 배열을 지정합니다. 복원 작업당 최대 10 개의 범위가 지원 됩니다. 다음 예에서는 *container1* 및 *container4*의 전체 콘텐츠를 복원 하는 두 범위를 지정 합니다.

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>비동기적으로 블록 blob 복원

복원 작업을 비동기적으로 실행 하려면 `-AsJob` **AzStorageBlobRange** 에 대 한 호출에 매개 변수를 추가 하 고 호출 결과를 변수에 저장 합니다. **AzStorageBlobRange** 명령은 **AzureLongRunningJob**형식의 개체를 반환 합니다. 이 개체의 **State** 속성을 확인 하 여 복원 작업이 완료 되었는지 여부를 확인할 수 있습니다. **상태** 속성의 값이 **실행** 중이거나 **완료**된 것일 수 있습니다.

다음 예제에서는 복원 작업을 비동기적으로 호출 하는 방법을 보여 줍니다.

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

복원 작업이 실행 된 후에 복원 작업이 완료 될 때까지 대기 하려면 다음 예제와 같이 [대기 작업](/powershell/module/microsoft.powershell.core/wait-job) 명령을 호출 합니다.

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>알려진 문제
- 추가 blob이 있는 복원의 하위 집합의 경우 복원이 실패 합니다. 지금은 계정에 추가 blob이 있는 경우 복원을 수행 하지 마세요.

## <a name="next-steps"></a>다음 단계

- [블록 Blob에 대한 지정 시간 복원(미리 보기)](point-in-time-restore-overview.md)
- [일시 삭제](soft-delete-overview.md)
- [변경 피드(미리 보기)](storage-blob-change-feed.md)
- [Blob 버전 관리](versioning-overview.md)
