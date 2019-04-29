---
title: Azure Cloud Shell 포함 | Microsoft Docs
description: Azure Cloud Shell을 포함하는 방법을 알아봅니다.
services: cloud-shell
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
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614304"
---
# <a name="embed-azure-cloud-shell"></a>Azure Cloud Shell 포함

Cloud Shell을 포함하면 개발자 및 콘텐츠 작성자가 전용 URL, [shell.azure.com](https://shell.azure.com)에서 Cloud Shell을 직접 열 수 있습니다. 이렇게 하면 Cloud Shell 인증, 도구 및 최신 Azure CLI/Azure PowerShell 도구의 전체 기능을 사용자가 즉시 사용할 수 있습니다.

일반 크기 단추

[![일반 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)

큰 크기 단추

[![큰 시작](https://shell.azure.com/images/launchcloudshell@2x.png "Azure Cloud Shell 시작")](https://shell.azure.com)

## <a name="how-to"></a>방법

다음을 복사하여 Cloud Shell의 시작 단추를 markdown 파일에 통합합니다.

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

팝업 Cloud Shell을 포함할 HTML은 다음과 같습니다.
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>환경 사용자 지정

URL을 확장하여 특정 셸 환경을 설정합니다.

|환경   |URL   |
|---|---|
|가장 최근에 사용한 셸   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>다음 단계
[Cloud Shell의 Bash 빠른 시작](quickstart.md)<br>
[Cloud Shell의 PowerShell 빠른 시작](quickstart-powershell.md)
