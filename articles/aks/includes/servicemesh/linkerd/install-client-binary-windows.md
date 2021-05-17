---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81737185"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd linkerd 클라이언트 이진 파일 다운로드 및 설치

Windows의 PowerShell 기반 셸에서 `Invoke-WebRequest`을 사용하여 다음과 같이 Linkerd 릴리스를 다운로드합니다.

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe&quot; -OutFile &quot;linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

`linkerd` 클라이언트 이진 파일은 클라이언트 컴퓨터에서 실행되며 Linkerd 서비스 메시와 상호 작용할 수 있습니다. 다음 명령을 사용하여 Windows의 PowerShell 기반 셸에 Linkerd `linkerd` 클라이언트 이진 파일을 설치합니다. 이러한 명령은 `linkerd` 클라이언트 이진 파일을 Linkerd 폴더에 복사한 다음, `PATH`를 통해 즉시(현재 셸에서) 및 영구적으로(셸 다시 시작에서) 사용할 수 있습니다. 이러한 명령을 실행하는 데에는 상승된(관리자) 권한이 필요하지 않으며 셸을 다시 시작하지 않아도 됩니다.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```