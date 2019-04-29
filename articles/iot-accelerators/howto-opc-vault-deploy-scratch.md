---
title: 부터-Azure 자격 증명 모음 OPC 모듈을 배포 하는 방법 | Microsoft Docs
description: 부터 OPC 자격 증명 모음을 배포 하는 방법입니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450332"
---
# <a name="deploy-opc-vault-from-scratch"></a>처음부터 OPC 자격 증명 모음 배포

OPC 자격 증명 모음은 클라우드 내 OPC UA 서버 및 클라이언트 애플리케이션에 대한 인증서 수명 주기를 구성, 등록 및 관리할 수 있는 마이크로서비스입니다. 이 문서에서는 처음부터 OPC 자격 증명 모음을 배포 하는 방법을 보여 줍니다.

## <a name="configuration-and-environment-variables"></a>구성 및 환경 변수

서비스 구성 appsettings.ini에서 ASP.NET Core 구성 어댑터를 사용 하 여 저장 됩니다. INI 형식 주석 사용 하 여 읽기 쉬운 형식으로 값을 저장할 수 있습니다.
또한 응용 프로그램 자격 증명 등 네트워킹 세부 정보 삽입 환경 변수를 지원합니다. (이 섹션에서는 원래 제목은 TODO 구성 및 환경 변수)입니다.

저장소의 구성 파일을 한 번 이상 생성 해야 하는 몇 가지 환경 변수를 참조 합니다. OS 및 IDE에 따라 환경 변수를 관리 하는 방법은 여러 가지가 있습니다.

- Windows 사용자에 대 한 env-변수-setup.cmd 스크립트 준비 하 고 한 번만 실행 해야 합니다. 를 실행 하면 설정을 터미널 세션 및 재부팅 간에 유지 됩니다.

- Linux 및 OSX 환경에 대 한 env-변수-설치 스크립트를 새 콘솔을 열 때마다 실행 해야 합니다. OS 및 터미널에 따라는 이러한 페이지는 데 도움이 되는 자세한 정보에 대 한 값을 전역적으로 유지 하는 가지가 있습니다.

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: 환경 변수 또한 Visual Studio 프로젝트 속성에서에서 설정할 수 있습니다, 그리고 왼쪽된 창에서 여러 변수를 추가할 수 있는 섹션으로 이동 "구성 속성" 및 "환경"을 선택 합니다.

- IntelliJ Rider: IntelliJ IDEA와 유사 하 게 각 실행 구성에서 환경 변수를 설정할 수 있습니다. https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>실행 하 고 Visual Studio를 사용 하 여 디버그

Visual Studio를 사용 하면 신속 하 게 IDE 외부에서 아무것도 구성 하지 않고 명령 프롬프트를 사용 하지 않고 응용 프로그램을 열 수 있습니다.

Visual Studio 2017을 사용 하 여 단계:

1. 사용 하 여 솔루션을 엽니다는 `iot-opc-gds-service.sln` 파일입니다.

1. 솔루션 로드 되 면 마우스 오른쪽 단추로 클릭 합니다 `WebService` 프로젝트, 선택 및 이동의 `Debug` 섹션입니다.

1. 동일한 섹션에서 필요한 환경 변수를 정의 합니다.

1. 키를 눌러 **F5**, 또는 **실행** 아이콘입니다. Visual Studio JSON 형식으로 서비스의 상태를 보여 주는 브라우저가 열립니다.

## <a name="run-and-debug-with-intellij-rider"></a>실행 하 고 IntelliJ Rider를 사용 하 여 디버그

1. 사용 하 여 솔루션을 엽니다는 `iot-opc-gds-service.sln` 파일입니다.

1. 솔루션 로드 되 면 이동할 `Run > Edit Configurations` 새 및 `.NET Project` 구성 합니다.

1. 구성에서 웹 서비스 프로젝트를 선택 합니다.

1. 설정을 저장 하 고 IDE 도구 모음에서 만든 configuration을 실행 합니다.

1. 서비스 및 웹 서비스가 실행 되 고 있는 URL과 같은 세부 정보를 사용 하 여 IntelliJ 실행 창에 메시지를 부트스트랩 서비스가 표시 되어야 로그 합니다.

## <a name="build-and-run-from-the-command-line"></a>명령줄에서 빌드 및 실행

자주 하는 작업에 대 한 몇 가지 스크립트를 포함 하는 스크립트 폴더:

- `build`: 모든 프로젝트를 컴파일 및 테스트를 실행 합니다.

- `compile`: 모든 프로젝트를 컴파일하십시오.

- `run`: 프로젝트를 컴파일하고 Windows 상승 된 권한에 대 한 웹 서비스를 실행 하 라는 메시지를 표시 하는 서비스를 실행 합니다.

환경 변수 설정에 대 한 스크립트를 확인합니다. OS를에서 환경 변수를 전역으로 설정 수도 있고 "환경 변수 설정" 스크립트를 사용 하 여 scripts 폴더에 있습니다.

### <a name="sandbox"></a>샌드박스

스크립트는.NET Core 및 Docker를 사용 하 여 개발 환경을 구성 하는 것을 가정 합니다. .NET Core 설치를 방지 하 및만 Docker를 설치 하 고 명령줄 매개 변수를 사용 하 여 수 `--in-sandbox` (또는 약식 `-s`) 예:

- `build --in-sandbox`: Docker 컨테이너 내에서 빌드 작업을 실행 (짧은 양식 `build -s`).

- `compile --in-sandbox`: Docker 컨테이너 내부 컴파일 작업을 실행 (짧은 양식 `compile -s`).

- `run --in-sandbox`: Docker 컨테이너 내에서 서비스를 시작 (짧은 양식 `run -s`).

샌드박스에 사용 되는 Docker 이미지를 Docker 허브에 호스팅되어 [여기](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet)합니다.

## <a name="package-the-application-to-a-docker-image"></a>Docker 이미지에 응용 프로그램 패키지

`scripts` 폴더 서비스를 Docker 이미지로 패키징하고 하는 데 필요한 파일을 사용 하 여 docker 하위 폴더를 포함 합니다.

- `Dockerfile`: Docker 이미지 사양입니다.
- `build`: Docker 컨테이너를 빌드하고 로컬 레지스트리에서 이미지를 저장 합니다.
- `run`: 로컬 레지스트리에 저장 된 이미지에서 Docker 컨테이너를 실행 합니다.
- `content`: 진입점 스크립트를 포함 하 여 이미지를 복사 하는 파일이 있는 폴더입니다.

## <a name="azure-iot-hub-setup"></a>Azure IoT Hub 설정

마이크로 서비스를 사용 하려면 개발 및 통합 테스트에 대 한 Azure IoT Hub를 설정 합니다.

이 설치 된 일부 Bash 스크립트를 포함 하는 프로젝트:

- 새 IoT Hub를 만듭니다. `./scripts/iothub/create-hub.sh`

- 기존 허브를 나열 합니다. `./scripts/iothub/list-hubs.sh`

- IoT Hub 세부 정보 (예: 키)를 표시 합니다. `./scripts/iothub/show-hub.sh`

및에서는 여러 Azure 구독 했습니다.

- 구독 목록을 보여 줍니다. `./scripts/iothub/list-subscriptions.sh`

- 현재 구독을 변경 합니다. `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>개발 설정

### <a name="net-setup"></a>.NET 설치

프로젝트 워크플로 통해 관리 됩니다 [.NET Core](https://dotnet.github.io) 1.x 설치 사용자 환경에서 모든 스크립트를 실행 하 고 IDE 예상 대로 작동 하는지 확인 해야 합니다.

제공 된 [Java 버전](https://github.com/Azure/iot-opc-gds-service-dotnet) 이 프로젝트와 다른 Azure IoT PCS 구성 요소입니다.

### <a name="ide"></a>IDE

다음은 Azure IoT PCS에서 작동 하는 데 사용할 수 있는 Ide의 일부입니다.

- [Visual Studio](https://www.visualstudio.com)
- [Mac용 Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Git 설치

프로젝트 코드 변경이 수락 하기 전에 몇 가지 확인을 자동화 하는 Git 후크를 포함 합니다. 테스트를 수동으로 실행할 수도 있고 CI 플랫폼에서 테스트를 실행할 수 있습니다. 다음 Git 후크를 자동으로 코드 변경 내용이 GitHub에 보내기 전에 모든 테스트를 실행 하 고 개발 워크플로 속도를 사용 합니다.

언제 든 지 원하는 후크를 제거 하는 경우 설치 파일을 삭제 하기만 하면 `.git/hooks`합니다. 사전 커밋 후크를 사용 하 여 무시할 수 있습니다는 `--no-verify` 옵션입니다.

#### <a name="pre-commit-hook-with-sandbox"></a>샌드박스를 사용 하 여 사전 커밋 후크

포함 된 후크를 설정 하려면 Windows/Linux/MacOS 콘솔을 열고 실행 합니다.

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

이 구성 파일을 체크 인할 때를 사용 하 여 Git는 응용 프로그램이 모든 테스트를 통과, 빌드 및 모든 개발 요구 사항으로 구성 된 Docker 컨테이너 내에서 테스트 실행을 확인 합니다.

#### <a name="pre-commit-hook-without-sandbox"></a>샌드박스 없이 사전 커밋 후크

> [!NOTE] 
> 샌드박스 없이 후크 필요 [.NET Core](https://dotnet.github.io) 시스템 경로에 있습니다.

포함 된 후크를 설정 하려면 Windows/Linux/MacOS 콘솔을 열고 실행 합니다.

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
이 구성 파일을 체크 인할 때를 사용 하 여 Git 응용 프로그램 전달 OS에 설치 된 도구를 사용 하 여 워크스테이션에서 빌드 및 테스트를 실행할 모든 테스트를 확인 합니다.

프로젝트 코드 스타일에 대 한 지침:

- 여기서 줄 길이 명령줄 편집기 및 코드 검토 하는 데 최대 80 자 제한 됩니다.

- 코드에는 네 개의 공백 사용 하 여 들여쓰기를 차단합니다. 탭 문자를 피해 야 합니다.

- 텍스트 파일에는 Unix 끝 줄 형식 (LF) 사용합니다.

- 으로 관리 되는 종속성 주입 [Autofac](https://autofac.org)합니다.

- 웹 서비스 Api 필드 메타 데이터를 제외 하 고 CamelCased 됩니다.

## <a name="next-steps"></a>다음 단계

이제부터 OPC 자격 증명 모음을 배포 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [부터 OPC 쌍 배포](howto-opc-twin-deploy-modules.md)