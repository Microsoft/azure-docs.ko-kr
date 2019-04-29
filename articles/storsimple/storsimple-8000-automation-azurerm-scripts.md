---
title: Azure Resource Manager 스크립트를 사용하여 StorSimple 디바이스 관리 | Microsoft Docs
description: Azure Resource Manager 스크립트를 사용하여 StorSimple 작업을 자동화하는 방법 알아보기
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 646b862733e8727c9c8729f1ac038fa88cfa0580
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726420"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Azure Resource Manager SDK 기반 스크립트를 사용하여 StorSimple 디바이스 관리

이 문서에서는 Azure Resource Manager SDK 기반 스크립트를 사용하여 StorSimple 8000 시리즈 디바이스를 관리하는 방법을 설명합니다. 이러한 스크립트를 실행하도록 환경을 구성하는 단계를 안내하는 샘플 스크립트도 포함되어 있습니다.

이 문서는 Azure Portal에서 실행되는 StorSimple 8000 시리즈 디바이스에만 적용됩니다.

## <a name="sample-scripts"></a>샘플 스크립트

다음 샘플 스크립트는 다양한 StorSimple 작업을 자동화하는 데 사용할 수 있습니다.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Azure Resource Manager SDK 기반 예제 스크립트 표

| Azure Resource Manager 스크립트                    | 설명                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | 이 스크립트를 사용하여 StorSimple 디바이스에 서비스 데이터 암호화 키를 변경할 권한을 부여합니다.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | 이 스크립트는 8010 또는 8020 StorSimple Cloud Appliance를 만듭니다. 이 클라우드 어플라이언스를 구성하고 StorSimple 데이터 관리자 서비스에 등록할 수 있습니다.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | 이 스크립트는 StorSimple 볼륨을 만들거나 수정합니다.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | 이 스크립트는 StorSimple 디바이스 관리자 서비스에 등록된 디바이스에 대한 모든 백업을 나열합니다.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | 이 스크립트는 StorSimple 디바이스에 대한 모든 백업 정책을 나열합니다.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | 이 스크립트는 StorSimple 디바이스 관리자 서비스에서 실행 중인 모든 StorSimple 작업을 가져옵니다.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | 이 스크립트는 업데이트 서버를 검색하고 StorSimple 디바이스에 설치할 수 있는 업데이트가 있는지 알려줍니다.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | 이 스크립트는 StorSimple 디바이스에 사용 가능한 업데이트를 설치합니다.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | 이 스크립트는 수동 클라우드 스냅숏을 시작하고 지정된 보존 기간(일)보다 오래된 클라우드 스냅숏을 삭제합니다.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | 이 Azure Automation Runbook PowerShell 스크립트는 모든 백업 작업의 상태를 보고합니다.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | 이 스크립트는 단일 백업 개체를 삭제합니다.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | 이 스크립트는 StorSimple 디바이스에서 수동 백업을 시작합니다.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | 이 스크립트는 StorSimple 디바이스 관리자 서비스에 등록된 모든 8010/8020 StorSimple Cloud Appliance에 대한 서비스 데이터 암호화 키를 업데이트합니다.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | 이 스크립트는 백업 정책과 연결된 모든 일정을 분석한 후 누락된 백업을 강조 표시합니다. 또한 사용 가능한 백업 목록을 사용하여 백업 카탈로그를 확인합니다.             |




## <a name="configure-and-run-a-sample-script"></a>샘플 스크립트 구성 및 실행

이 섹션은 예제 스크립트를 작성하고 스크립트를 실행하는 데 필요한 다양한 단계를 자세히 설명합니다.

### <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목이 있어야 합니다.

*   Azure PowerShell을 설치되어 있습니다. Azure PowerShell 모듈을 설치하려면
    * Windows 환경에서 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)의 단계를 따릅니다. StorSimple용 Windows Server 호스트를 사용하는 경우 여기에 Azure PowerShell을 설치할 수 있습니다.
    * Linux 또는 MacOS 환경에서는 [MacOS 또는 Linux에서 Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux)의 단계를 따릅니다.

Azure PowerShell 사용에 대한 자세한 내용은 [Azure PowerShell 사용 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

### <a name="run-azure-powershell-script"></a>Azure PowerShell 스크립트 실행

이 예제에서 사용되는 스크립트는 StorSimple 디바이스의 모든 작업을 나열합니다. 여기에는 성공 또는 실패한 작업과 진행 중인 작업이 모두 포함됩니다. 다음 단계에 따라 스크립트를 다운로드하고 실행합니다.

1. Azure PowerShell을 시작합니다. 새 폴더를 만들고 디렉터리를 해당 새 폴더로 변경합니다.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. 이전 단계에서 만든 폴더에서 [NuGet CLI를 다운로드](https://www.nuget.org/downloads)합니다. 다양한 버전의 _nuget.exe_가 있습니다. 사용하는 SDK에 해당하는 버전을 선택합니다. 각 다운로드 링크는 _.exe_ 파일을 직접 가리킵니다. 브라우저에서 실행하지 말고, 마우스 오른쪽 단추를 클릭하여 컴퓨터에 파일을 저장해야 합니다.

    또한 다음 명령을 실행하여 이전에 만든 동일한 폴더에 스크립트를 다운로드한 후 저장할 수도 있습니다.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. 종속 SDK를 다운로드합니다.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. 샘플 Github 프로젝트에서 스크립트를 다운로드합니다.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. 스크립트를 실행합니다. 인증하라는 메시지가 뜨면 Azure 자격 증명을 제공합니다. 이 스크립트는 StorSimple 디바이스의 모든 작업에 대한 필터링된 작업 목록을 출력합니다.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>샘플 출력

샘플 스크립트가 실행되면 다음과 같은 출력이 표시됩니다. 출력에는 등록된 디바이스에서 실행된 작업 중에서 2017년 9월 25일에 시작해서 2017년 10월 2일에 완료된 모든 작업이 포함됩니다.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>다음 단계

[StorSimple 디바이스 관리자 서비스](storsimple-8000-manager-service-administration.md)를 사용하여 StorSimple 디바이스를 관리합니다.