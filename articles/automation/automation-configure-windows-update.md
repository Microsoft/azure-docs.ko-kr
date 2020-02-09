---
title: Azure 업데이트 관리에서 작동 하도록 Windows 업데이트 설정 구성
description: 이 문서에서는 Azure 업데이트 관리에서 작동 하도록 구성 하는 Windows 업데이트 설정에 대해 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 10/02/2019
ms.topic: conceptual
ms.openlocfilehash: f6377012a2afd0fb36486edf0af0ac3591b5d1f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366858"
---
# <a name="configure-windows-update-settings-for-update-management"></a>업데이트 관리에 대한 Windows 업데이트 설정 구성

Azure 업데이트 관리는 Windows 업데이트를 사용 하 여 Windows 업데이트를 다운로드 하 고 설치 합니다. 따라서 업데이트 관리은 Windows 업데이트에서 사용 하는 많은 설정을 합니다. 설정을 사용 하 여 비 Windows 업데이트를 사용 하는 경우에도 업데이트 관리에서 해당 업데이트를 관리 합니다. 업데이트 배포를 수행 하기 전에 업데이트 다운로드를 사용 하도록 설정 하려는 경우 업데이트 배포는 더 빠르고 효율적이 고 유지 관리 기간을 초과할 가능성이 줄어듭니다.

## <a name="pre-download-updates"></a>다운로드 전 업데이트

그룹 정책에서 업데이트 자동 다운로드를 구성 하려면 [자동 업데이트 구성 설정을](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3**으로 설정 합니다. 이 설정을 사용 하면 필요한 업데이트를 백그라운드에서 다운로드할 수 있지만 설치 하지는 않습니다. 이러한 방식으로 업데이트 관리 일정의 제어를 유지 하지만 업데이트 관리 유지 관리 기간 외에 업데이트를 다운로드할 수 있습니다. 이 동작은 업데이트 관리에서 "유지 관리 기간 초과" 오류를 방지 합니다.

업데이트의 자동 다운로드를 구성 하려는 시스템에서 다음 PowerShell 명령을 실행 하 여이 설정을 켤 수도 있습니다.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>자동 설치 사용 안 함

기본적으로 Azure Vm (가상 머신)에서 자동 업데이트 설치가 사용 됩니다. 이로 인해 업데이트를 설치 하도록 예약 하기 전에 업데이트 관리 수 있습니다. `1``NoAutoUpdate` 레지스트리 키를 설정 하 여이 동작을 사용 하지 않도록 설정할 수 있습니다. 다음 PowerShell 코드 조각에서는이 작업을 수행 하는 방법을 보여 줍니다.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>다시 부팅 설정 구성

**업데이트 배포** 설정에서 **다시 부팅 안 함** 을 지정한 경우에도 [다시 시작을 관리 하는 데 사용 되](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) 는 레지스트리 및 레지스트리 키를 [편집 하 여 자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) 에 나열 된 레지스트리 키로 인해 컴퓨터가 다시 부팅 될 수 있습니다. 이러한 레지스트리 키는 사용자 환경에 가장 적합 하도록 구성 해야 합니다.

## <a name="enable-updates-for-other-microsoft-products"></a>다른 Microsoft 제품에 대한 업데이트 사용

기본적으로 Windows 업데이트는 Windows용 업데이트만 제공합니다. **Windows 업데이트를 업데이트할 때 다른 Microsoft 제품에 대한 업데이트 제공** 설정을 사용 하도록 설정 하면 Microsoft SQL Server 및 기타 microsoft 소프트웨어에 대한 보안 패치를 비롯 한 다른 제품에 대한 업데이트도 수신 됩니다. 이 옵션은 그룹 정책을 통해 구성할 수 없습니다. 다른 Microsoft 업데이트를 사용 하도록 설정 하려는 시스템에서 다음 PowerShell 명령을 실행 합니다. 업데이트 관리는이 설정을 준수 합니다.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 구성 설정

업데이트 관리는 WSUS (Windows Server Update Services) 설정을 준수 합니다. 업데이트 관리 사용 하도록 구성할 수 있는 WSUS 설정은 아래에 나열 되어 있습니다.

### <a name="intranet-microsoft-update-service-location"></a>인트라넷 Microsoft 업데이트 서비스 위치

[인트라넷 Microsoft 업데이트 서비스 위치 지정](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)에서 업데이트를 검색 하 고 다운로드 하기 위한 원본을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Windows 업데이트 설정을 구성한 후에는 [Azure vm에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)의 지침에 따라 업데이트 배포를 예약할 수 있습니다.
