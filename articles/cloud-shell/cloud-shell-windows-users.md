---
title: Windows 사용자용 Azure Cloud Shell | Microsoft Docs
description: Linux 시스템에 익숙하지 않은 사용자를 위한 가이드
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095340"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Windows 사용자용 Azure Cloud Shell의 PowerShell

2018년 5월 Azure Cloud Shell의 PowerShell에 대한 변경이 [발표](https://azure.microsoft.com/blog/pscloudshellrefresh/)되었습니다.
Azure Cloud Shell의 PowerShell 환경은 이제 Linux 환경에서 [PowerShell Core 6](https://github.com/powershell/powershell)를 실행합니다.
이 변경으로 Windows PowerShell 환경에서 예상되는 것과 비교하여 Cloud Shell의 PowerShell 환경에서 몇 가지 차이점이 발생합니다.

## <a name="file-system-case-sensitivity"></a>파일 시스템 대/소문자 구분

파일 시스템은 Windows에서 대/소문자를 구분하지 않는 반면, Linux에서는 대/소문자를 구분합니다.
이전에 `file.txt` 및 `FILE.txt`는 같은 파일로 간주되었으나 이제는 다른 파일로 간주됩니다.
파일 시스템에서 `tab-completing` 동안 적절한 대/소문자를 사용해야 합니다.
`tab-completing` cmdlet 이름, 매개 변수 및 값과 같은 PowerShell 특정 환경은 대/소문자를 구분하지 않습니다.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell 별칭 대 Linux 유틸리티

일부 기존 PowerShell 별칭에는 `cat`,`ls`, `sort`, `sleep` 등과 같이 기본 제공 Linux 명령과 동일한 이름이 있습니다. PowerShell Core 6에서 기본 제공 Linux 명령과 충돌하는 별칭이 제거되었습니다.
다음은 제거된 일반적인 별칭과 상응하는 명령입니다.  

|제거된 별칭   |해당 명령   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>$HOME 유지

이전 사용자는 스크립트 및 다른 파일만 클라우드 드라이브에서 유지할 수 있었습니다.
이제 사용자의 $HOME 디렉터리도 세션 간에 유지됩니다.

## <a name="powershell-profile"></a>PowerShell 프로필

기본적으로 사용자의 PowerShell 프로필은 만들어지지 않습니다.
프로필을 만들려면 `$HOME/.config` 아래에 `PowerShell` 디렉터리를 만듭니다.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

`$HOME/.config/PowerShell`에서 `profile.ps1` 및/또는 `Microsoft.PowerShell_profile.ps1`과 같은 프로필 파일을 만들 수 있습니다.

## <a name="whats-new-in-powershell-core-6"></a>PowerShell Core 6의 새로운 기능

PowerShell Core 6의 새로운 기능에 대한 자세한 내용은 [PowerShell 문서](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) 및 [PowerShell Core 시작](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) 블로그 게시물을 참조하세요.
