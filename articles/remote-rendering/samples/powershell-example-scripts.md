---
title: 예제 PowerShell 스크립트
description: PowerShell 스크립트를 통해 프런트 엔드를 사용하는 방법을 보여주는 예제
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: 07055025eff9ab81c7321624daed9b4a6e993a60
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506514"
---
# <a name="example-powershell-scripts"></a>예제 PowerShell 스크립트

Azure Remote Rendering은 다음 두 가지 REST API를 제공합니다.

- [변환 REST API](../how-tos/conversion/conversion-rest-api.md)
- [세션 REST API](../how-tos/session-rest-api.md)

[ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에는 *Scripts* 폴더에 서비스의 REST API와 상호 작용하기 위한 샘플 스크립트가 포함되어 있습니다. 이 문서에서는 사용 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

샘플 스크립트를 실행하려면 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)의 기능을 설정해야 합니다.

1. Azure PowerShell 설치:
    1. 관리자 권한으로 PowerShell 창을 엽니다.
    1. `Install-Module -Name Az -AllowClobber`를 실행합니다.

1. 스크립트 실행에 대한 오류가 발생하면 [실행 정책](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)이 적절하게 설정되어 있는지 확인합니다.
    1. 관리자 권한으로 PowerShell 창을 엽니다.
    1. `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`를 실행합니다.

1. [Azure Storage 계정 준비](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Azure Remote Rendering 계정이 포함된 구독에 로그인:
    1. PowerShell 창을 엽니다.
    1. `Connect-AzAccount`를 실행하고 화면의 지시에 따릅니다.

    > [!NOTE]
    > 조직에 둘 이상의 구독이 있는 경우 SubscriptionId 및 Tenant 인수를 지정해야 할 수 있습니다. [Connect-AzAccount 설명서](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)에서 세부 정보를 확인하세요.

1. [Azure Remote Rendering GithHub 리포지토리](https://github.com/Azure/azure-remote-rendering)에서 *Scripts* 폴더를 다운로드합니다.

## <a name="configuration-file"></a>구성 파일

`.ps1` 파일 옆에 입력이 필요한 `arrconfig.json`이 있습니다.

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> 이중 백슬래시 "\\\\"를 사용하여 LocalAssetDirectoryPath 경로에서 백슬래시를 올바르게 이스케이프하고 inputFolderPath 및 inputAssetPath와 같은 다른 모든 경로에는 슬래시 "/"를 사용해야 합니다.

> [!CAUTION]
> 선택적 값을 작성하거나 키와 값을 모두 제거해야 합니다. 예를 들어 `"outputAssetFileName"` 매개 변수를 사용하지 않는 경우 `arrconfig.json` 내부의 전체 줄을 삭제해야 합니다.

### <a name="accountsettings"></a>accountSettings

`arrAccountId`와 `arrAccountKey`는 [Azure Remote Rendering 계정 만들기](../how-tos/create-an-account.md)를 참조하세요.
`region`은 [사용 가능한 지역 목록](../reference/regions.md)을 참조하세요.

### <a name="renderingsessionsettings"></a>renderingSessionSettings

**RenderingSession.ps1**을 실행하려면 이 구조를 입력해야 합니다.

- **vmSize:** 가상 머신의 크기를 선택합니다. [*표준*](../reference/vm-sizes.md) 또는 [*프리미엄*](../reference/vm-sizes.md)을 선택합니다. 렌더링 세션이 더 이상 필요하지 않으면 종료하십시오.
- **maxLeaseTime:** VM을 임대하려는 기간입니다. 임대가 만료되면 종료됩니다. 임대 시간은 나중에 연장할 수 있습니다(아래 참조).

### <a name="assetconversionsettings"></a>assetConversionSettings

**Conversion.ps1**을 실행하려면 이 구조를 입력해야 합니다.

자세한 내용은 [Azure Storage 계정 준비](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)를 참조하세요.

## <a name="script-renderingsessionps1"></a>스크립트: RenderingSession.ps1

이 스크립트는 렌더링 세션을 생성, 쿼리 및 중지하는 데 사용됩니다.

> [!IMPORTANT]
> arrconfig.json에서 *accountSettings* 및 *renderingSessionSettings* 섹션을 입력했는지 확인하십시오.

### <a name="create-a-rendering-session"></a>렌더링 세션 만들기

완전히 입력한 arrconfig.json을 사용하는 일반적인 사용법:

```PowerShell
.\RenderingSession.ps1
```

이 스크립트는 [session management REST API](../how-tos/session-rest-api.md)를 호출하여 지정된 설정으로 렌더링 VM을 실행합니다. 성공하면 *sessionId*를 검색합니다. 그런 다음, 세션이 준비되거나 오류가 발생할 때까지 세션 속성을 폴링합니다.

**다른 구성** 파일을 사용하려면:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

구성 파일에서 **개별 설정을 재정의**할 수 있습니다.

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

**폴링 없이 세션을 시작하려면** 다음을 사용합니다.

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

스크립트가 검색한 *sessionId*는 대부분의 다른 세션 명령에 전달되어야 합니다.

### <a name="retrieve-session-properties"></a>세션 속성 검색

세션의 속성을 가져오려면 다음을 실행합니다.

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

세션이 준비되거나 오류가 발생할 때까지 대기하려면 `-Poll`을 사용합니다.**

### <a name="list-active-sessions"></a>활성 세션 나열

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>세션 중지

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>세션 속성 변경

현재는 세션의 maxLeaseTime만 변경하도록 지원됩니다.

> [!NOTE]
> 임대 시간은 항상 세션 VM이 처음 생성된 시간부터 계산됩니다. 따라서 세션 임대를 1시간 연장하려면 *maxLeaseTime*을 한 시간 늘립니다.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>스크립트: Conversion.ps1

이 스크립트는 입력 모델을 Azure Remote Rendering 특정 런타임 형식으로 변환하는 데 사용됩니다.

> [!IMPORTANT]
> arrconfig.json에서 *accountSettings* 및 *assetConversionSettings* 섹션을 입력했는지 확인하십시오.

이 스크립트는 서비스에서 스토리지 계정을 사용하는 두 가지 옵션을 보여줍니다.

- Azure Remote Rendering 계정과 연결된 스토리지 계정
- SAS(공유 액세스 서명)을 통해 스토리지에 대한 액세스 제공

### <a name="linked-storage-account"></a>연결된 스토리지 계정

arrconfig.json을 완전히 입력하고 스토리지 계정을 연결하고 나면 다음 명령을 사용할 수 있습니다. 스토리지 계정을 연결하는 방법은 [계정 만들기](../how-tos/create-an-account.md#link-storage-accounts)에서 참조하세요.

연결된 스토리지 계정을 사용하면 공유 액세스 서명을 생성할 필요가 없기 때문에 변환 서비스를 사용하는 데 선호하는 방법입니다.

```PowerShell
.\Conversion.ps1
```

1. `assetConversionSettings.modelLocation`에 포함된 모든 파일을 지정된 `inputFolderPath` 아래의 입력 Blob 컨테이너에 업로드합니다.
1. [모델 변환 REST API](../how-tos/conversion/conversion-rest-api.md)를 호출하여 [모델 변환](../how-tos/conversion/model-conversion.md)이 시작됩니다.
1. 변환이 성공 또는 실패할 때까지 변환 상태를 폴링합니다.
1. 변환된 파일 위치의 세부 정보(스토리지 계정, 출력 컨테이너, 컨테이너의 파일 경로)가 출력됩니다.

### <a name="access-to-storage-via-shared-access-signatures"></a>공유 액세스 서명을 통해 스토리지에 액세스

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

그러면

1. `assetConversionSettings.localAssetDirectoryPath`에서 입력 Blob 컨테이너로 로컬 파일을 업로드합니다.
1. 입력 컨테이너에 대한 SAS URI를 생성합니다.
1. 출력 컨테이너에 대한 SAS URI를 생성합니다.
1. [모델 변환 REST API](../how-tos/conversion/conversion-rest-api.md)를 호출하여 [모델 변환](../how-tos/conversion/model-conversion.md)을 시작합니다.
1. 변환이 성공 또는 실패할 때까지 변환 상태를 폴링합니다.
1. 변환된 파일 위치의 세부 정보(스토리지 계정, 출력 컨테이너, 컨테이너의 파일 경로)가 출력됩니다.
1. 출력 Blob 컨테이너의 변환된 모델에 SAS URI를 출력합니다.

### <a name="additional-command-line-options"></a>추가 명령줄 옵션

**다른 구성** 파일을 사용하려면:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

**폴링 없이 모델 변환을 시작하려면** 다음을 사용합니다.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

다음 명령줄 스위치를 사용하여 구성 파일에서 **개별 설정을 재정의**할 수 있습니다.

* **Id:** GetConversionStatus와 사용되는 ConversionId
* **ArrAccountId:** accountSettings의 arrAccountId
* **ArrAccountKey:** accountSettings의 arrAccountKey에 대한 재정의
* **Region:** accountSettings 지역에 대한 재정의
* **ResourceGroup:** assetConversionSettings의 resourceGroup에 대한 재정의
* **StorageAccountName:** assetConversionSettings의 storageAccountName에 대한 재정의
* **BlobInputContainerName:** assetConversionSettings의 blobInputContainer에 대한 재정의
* **LocalAssetDirectoryPath:** assetConversionSettings의 localAssetDirectoryPath에 대한 재정의
* **InputAssetPath:** assetConversionSettings의 inputAssetPath에 대한 재정의
* **BlobOutputContainerName:** assetConversionSettings의 blobOutputContainerName에 대한 재정의
* **OutputFolderPath:** assetConversionSettings의 outputFolderPath에 대한 재정의
* **OutputAssetFileName:** assetConversionSettings의 outputAssetFileName에 대한 재정의

예를 들어 지정된 여러 옵션을 다음과 같이 결합할 수 있습니다

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>개별 변환 단계 실행

프로세스의 개별 단계를 실행하려면 다음을 사용합니다.

지정된 LocalAssetDirectoryPath의 데이터만 업로드합니다.

```PowerShell
.\Conversion.ps1 -Upload
```

Blob Storage에 이미 업로드된 모델의 변환 프로세스만 시작합니다(업로드 실행 안 함, 변환 상태 폴링 안 함). 스크립트는 *conversionId*를 반환합니다.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

다음을 사용하여 이 변환의 상태를 검색할 수 있습니다.

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

변환이 완료되거나 오류가 발생할 때까지 대기하려면 `-Poll`을 사용합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Unity를 사용하여 모델 렌더링](../quickstarts/render-model.md)
- [빠른 시작: 렌더링을 위해 모델 변환](../quickstarts/convert-model.md)
- [모델 변환](../how-tos/conversion/model-conversion.md)
