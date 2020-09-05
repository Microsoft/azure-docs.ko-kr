---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: c92d6569e3c92d3bad3575599283c7796bd78225
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068610"
---
## <a name="prerequisites"></a>필수 구성 요소

유일한 필수 구성 요소는 Azure Speech 구독입니다. 새 구독을 아직 만들지 않은 경우 [가이드](../get-started.md#new-resource)를 참조하세요.

## <a name="download-and-install"></a>다운로드 및 설치

#### <a name="windows-install"></a>[Windows 설치](#tab/windowsinstall)

Windows에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471) 또는 [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)을 설치합니다.
2. Speech CLI [zip 보관 파일](https://aka.ms/speech/spx-zips.zip)을 다운로드한 다음, 압축을 풉니다.
3. 다운로드에서 추출한 루트 디렉터리 `spx-zips`로 이동하여 필요한 하위 디렉터리(.NET Framework 4.7에 대해 `spx-net471` 또는 x64 CPU에서 .NET Core 3.0에 대해 `spx-netcore-win-x64`)를 추출합니다.

명령 프롬프트에서 디렉터리를 이 위치로 변경한 다음, `spx`를 입력하여 Speech CLI에 대한 도움말을 표시합니다.

> [!NOTE]
> Windows에서 Speech CLI는 로컬 컴퓨터에서 명령 프롬프트에 사용할 수 있는 글꼴만 표시할 수 있습니다.
> [Windows 터미널](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701)은 Speech CLI에서 대화형으로 생성된 모든 글꼴을 지원합니다.
> 파일로 출력하면 메모장과 같은 텍스트 편집기나 Microsoft Edge와 같은 웹 브라우저도 모든 글꼴을 표시할 수 있습니다.

> [!NOTE]
> Powershell은 명령을 찾을 때 로컬 디렉터리를 확인하지 않습니다. Powershell에서 디렉터리를 `spx` 위치로 변경하고 `.\spx`를 입력하여 도구를 호출합니다.
> 경로에 이 디렉터리를 추가하면 Powershell 및 Windows 명령 프롬프트가 `.\` 접두사를 포함하지 않고 모든 디렉터리에서 `spx`를 찾습니다.

#### <a name="linux-install"></a>[Linux 설치](#tab/linuxinstall)

x64 CPU에서 Linux에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)을 설치합니다.
2. Speech CLI [zip 보관 파일](https://aka.ms/speech/spx-zips.zip)을 다운로드한 다음, 압축을 풉니다.
3. 다운로드에서 추출한 루트 디렉터리 `spx-zips`로 이동하여 `spx-netcore-30-linux-x64`를 새 `~/spx` 디렉터리로 추출합니다.
4. 터미널에서 다음 명령을 입력합니다.
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

`spx`를 입력하여 Speech CLI에 대한 도움말을 표시합니다.

#### <a name="docker-install"></a>[Docker 설치](#tab/dockerinstall)

Docker 컨테이너 내에서 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. [플랫폼에 대한 Docker Desktop](https://www.docker.com/get-started)을 설치하고 실행합니다.
1. 새 명령 프롬프트 또는 터미널에서 다음 명령을 입력합니다. `docker pull msftspeech/spx`
1. 다음 명령을 입력합니다. Speech CLI에 대한 도움말 정보가 표시되어야 합니다. `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>컨테이너에 디렉터리 탑재

Speech CLI 도구는 구성 설정을 파일로 저장하고 명령(도움말 명령 제외)을 수행할 때 이러한 파일을 로드합니다.
Docker 컨테이너 내에서 Speech CLI를 사용하는 경우 도구에서 구성 설정을 저장하거나 찾을 수 있도록 컨테이너에서 로컬 디렉터리를 탑재해야 합니다. 그러면 도구에서 음성의 오디오 파일 등 명령에 필요한 모든 파일을 읽거나 쓸 수 있습니다.

Windows에서 다음 명령을 입력하여 컨테이너 내에서 사용할 수 있는 로컬 디렉터리 Speech CLI를 만듭니다.

`mkdir c:\spx-data`

또는 Linux 또는 Mac에서 터미널에 다음 명령을 입력하여 디렉터리를 만들고 절대 경로를 확인합니다.

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Speech CLI를 호출할 때 절대 경로를 사용합니다.

### <a name="run-speech-cli-in-the-container"></a>컨테이너에서 Speech CLI 실행

이 설명서에서는 비 Docker 설치에서 사용되는 Speech CLI `spx` 명령을 보여 줍니다.
Docker 컨테이너에서 `spx` 명령을 호출하는 경우, Speech CLI에서 구성 값을 저장하고 찾고 파일을 읽고 쓸 수 있는 파일 시스템에 컨테이너의 디렉터리를 탑재해야 합니다.
Windows에서 명령은 다음과 같이 시작됩니다.

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

Linux 또는 Mac에서 명령은 다음과 유사하게 시작됩니다.

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> `/ABSOLUTE_PATH`를 위 섹션의 `pwd` 명령에 표시된 절대 경로로 바꿉니다.

컨테이너에 설치된 `spx` 명령을 사용하려면 항상 위에 표시된 전체 명령과 요청의 매개 변수를 차례로 입력합니다.
예를 들어 Windows에서 이 명령은 키를 설정합니다.

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Docker 컨테이너 내에서 Speech CLI를 실행하는 경우에는 컴퓨터의 마이크 또는 스피커를 사용할 수 없습니다.
> 이러한 디바이스를 사용하려면 Docker 컨테이너 외부에서 기록/재생을 위해 오디오 파일을 Speech CLI로 전달합니다.
> Speech CLI 도구는 위 단계에서 설정한 로컬 디렉터리에 액세스할 수 있습니다.

***

## <a name="create-subscription-config"></a>구독 구성 만들기

Speech CLI 사용을 시작하려면 먼저 Speech 구독 키 및 지역 정보를 입력해야 합니다. 지역 식별자를 찾으려면 [지역 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) 페이지를 참조하세요. 구독 키와 지역 식별자가 있으면(예: `eastus`, `westus`) 다음 명령을 실행합니다.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

구독 인증은 이제 향후 SPX 요청에 대해 저장됩니다. 이러한 저장된 값 중 하나를 제거해야 하는 경우 `spx config @region --clear` 또는 `spx config @key --clear`를 실행합니다.
