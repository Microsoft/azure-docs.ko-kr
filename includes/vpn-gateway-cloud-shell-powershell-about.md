---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045106"
---
이 문서에서는 PowerShell cmdlet을 사용합니다. cmdlet을 실행하려면 Azure Cloud Shell을 사용하면 됩니다. Azure Cloud Shell은 Azure에서 호스팅되고 브라우저를 통해 상호작용하는 셸 환경입니다. Azure Cloud Shell에는 Azure PowerShell cmdlet이 미리 설치되어 있습니다.

Azure Cloud Shell에서 이 문서에 포함된 코드를 실행하려면 Cloud Shell 세션을 열고 코드 블록의 **복사** 단추를 사용하여 코드를 복사한 다음, Windows 및 Linux에서는 __Ctrl+Shift+V__, macOS에서는 __Cmd+Shift+V__를 사용하여 Cloud Shell 세션에 붙여 넣습니다. 붙여넣은 텍스트는 자동으로 실행되지 않으므로, **Enter** 키를 눌러 코드를 실행합니다.

다음을 수행하여 Azure Cloud Shell을 시작할 수 있습니다.

|  |   |
|-----------------------------------------------|---|
| 코드 블록의 오른쪽 위 모서리에서 **사용**을 선택합니다. 이것으로 Cloud Shell로 텍스트가 자동 복사되는 것은 __아닙니다__. | ![Azure Cloud Shell에 대한 사용 예제](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| 브라우저에서 [shell.azure.com](https://shell.azure.com)을 엽니다. | [![Azure Cloud Shell 단추 시작](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| [Azure Portal](https://portal.azure.com) 오른쪽 위에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다. | ![Azure Portal의 Cloud Shell 단추](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**로컬로 PowerShell 실행**

Azure PowerShell cmdlet을 컴퓨터에 로컬에 설치하고 실행할 수도 있습니다. PowerShell cmdlet은 자주 업데이트 됩니다. 최신 버전을 실행 하지 않는 경우 예제에 입력된 값이 실패할 수 있습니다. 컴퓨터에 설치 된 Azure PowerShell 버전을 찾으려면 `Get-Module -ListAvailable Az` cmdlet을 사용 합니다. 설치 또는 업데이트 하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조 하세요.

PowerShell을 로컬로 실행 하는 경우 ' AzAccount '를 실행 하 여 Azure에 대한 연결을 만들어야 합니다.