---
title: Azure Cloud Shell 제한 사항 | Microsoft Docs
description: Azure Cloud Shell의 제한 사항 개요
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 8fd88221818d28c227c33719c03e522e815a408b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097055"
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell의 제한 사항

Azure Cloud Shell에는 다음과 같이 알려진 제한 사항이 있습니다.

## <a name="general-limitations"></a>일반적인 제한 사항

### <a name="system-state-and-persistence"></a>시스템 상태 및 지속성

Cloud Shell 세션을 제공하는 컴퓨터는 일시적이며 세션이 20분 동안 비활성화된 후 재순환됩니다. Cloud Shell은 Azure 파일 공유를 탑재해야 합니다. 따라서 Cloud Shell에 액세스하도록 구독에서 저장소 리소스를 설정할 수 있어야 합니다. 기타 고려 사항은 다음과 같습니다.

* 탑재된 저장소에서 `$Home` 디렉터리 내 수정 사항만 유지됩니다.
* Azure 파일 공유는 [할당된 지역](persisting-shell-storage.md#mount-a-new-clouddrive) 내에서만 탑재될 수 있습니다.
  * Bash에서 `ACC_LOCATION`로 설정된 해당 지역을 찾으려면 `env`을 실행합니다.

### <a name="browser-support"></a>브라우저 지원

Cloud Shell은 Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox 및 Apple Safari의 최신 버전을 지원합니다. Safari는 개인 모드에서 지원되지 않습니다.

### <a name="copy-and-paste"></a>복사 및 붙여넣기

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>지정된 사용자에 대해 셸이 하나만 활성화될 수 있습니다.

사용자는 **Bash** 또는 **PowerShell**을 이용하여 한 번에 한 가지 유형의 셸만을 시작할 수 있습니다. 그러나 PowerShell 또는 Bash의 인스턴스는 동시에 여러 개 실행할 수 있습니다. 메뉴를 사용 하 여 PowerShell 또는 Bash 간에 교환이 일어나면 기존 세션을 종료 하는 Cloud Shell 다시 시작 하면 됩니다. 또는 입력 하 여 PowerShell 내에서 bash를 실행할 수 있습니다 `bash`를 입력 하 여 bash 내에서 PowerShell을 실행할 수 있습니다 `pwsh`합니다.

### <a name="usage-limits"></a>사용 제한

Cloud Shell은 대화형 사용 사례를 위한 것입니다. 따라서 비대화형 세션을 오래 실행하면 경고 없이 종료됩니다.

## <a name="bash-limitations"></a>Bash 제한 사항

### <a name="user-permissions"></a>사용자 권한

권한은 sudo 액세스 권한이 없는 일반 사용자로 설정됩니다. 사용자 `$Home` 디렉터리 외부에서의 설치는 유지되지 않습니다.

### <a name="editing-bashrc-or-profile"></a>.Bashrc 또는 $PROFILE 편집

Cloud Shell에 예기치 않은 오류가 발생할 수 있습니다.bashrc 또는 이렇게 PowerShell의 $PROFILE 파일을 편집 하는 경우에 주의 수행 합니다.

## <a name="powershell-limitations"></a>PowerShell 제한 사항

### <a name="azuread-module-name"></a>`AzureAD` 모듈 이름

`AzureAD` 모듈 이름은 현재 `AzureAD.Standard.Preview`이며, 이 모듈은 동일한 기능을 제공합니다.

### <a name="sqlserver-module-functionality"></a>`SqlServer` 모듈 기능

Cloud Shell에 포함된 `SqlServer` 모듈은 PowerShell Core에 대해 평가판 지원만 제공합니다. 특히 `Invoke-SqlCmd`는 아직 사용할 수 없습니다.

### <a name="default-file-location-when-created-from-azure-drive"></a>Azure 드라이브에서 만들 때 기본 파일 위치:

PowerShell cmdlet을 사용 하 여, 사용자가 만들 수 없습니다 azure 파일: 드라이브입니다. 사용자가 vim 또는 nano 등의 다른 도구를 사용하여 새 파일을 만들 때 파일은 기본적으로 `$HOME` 폴더에 저장됩니다. 

### <a name="gui-applications-are-not-supported"></a>GUI 애플리케이션은 지원되지 않습니다.

와 같은 오류 메시지를 볼 사용자가 Windows 대화 상자를 만들 수 있는 명령을 실행 하는 경우: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`합니다.

### <a name="large-gap-after-displaying-progress-bar"></a>진행률 표시줄을 표시한 후 큰 간격이 생깁니다.

사용자가 `Azure:` 드라이브에 있는 동안 탭 완성 기능처럼 진행률 표시줄을 표시하는 작업을 수행할 경우, 커서가 제대로 설정되지 않고, 진행률 표시줄이 이전에 있던 위치에 간격이 나타날 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Cloud Shell 문제 해결](troubleshooting.md) <br>
[Bash에 대한 빠른 시작](quickstart.md) <br>
[PowerShell에 대한 빠른 시작](quickstart-powershell.md)
