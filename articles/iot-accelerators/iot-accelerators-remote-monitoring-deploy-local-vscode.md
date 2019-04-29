---
title: 로컬로 원격 모니터링 솔루션 배포(Visual Studio Code) - Azure | Microsoft Docs
description: 이 방법 가이드에서는 테스트 및 개발을 위해 Visual Studio Code를 사용하여 원격 모니터링 솔루션 가속기를 로컬 머신에 배포하는 방법을 보여줍니다.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 946f815cecea4cc172fac35c0b260d795317e6e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446179"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>로컬에 원격 모니터링 솔루션 가속기 배포 - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

이 문서에서는 테스트 및 개발을 위해 원격 모니터링 솔루션 가속기를 로컬 컴퓨터에 배포하는 방법을 보여줍니다. Visual Studio Code에서 마이크로 서비스를 실행하는 방법에 대해 알아봅니다. 로컬 마이크로 서비스 배포는 IoT Hub, Cosmos DB, Azure Streaming Analytics, Azure Time Series Insights 등과 같은 클라우드 서비스를 사용합니다.

## <a name="prerequisites"></a>필수 조건

원격 모니터링 솔루션 가속기에 사용되는 Azure 서비스를 배포하려면 활성 Azure 구독이 필요합니다.

계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

### <a name="machine-setup"></a>머신 설치

로컬 배포를 완료하기 위해 로컬 개발 컴퓨터에 설치되어 있어야 하는 도구는 다음과 같습니다.

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code의 C# 확장](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - 이 소프트웨어는 스크립트에서 Azure 리소스를 만드는 데 사용하는 PCS CLI에 대한 필수 구성 요소입니다. Node.js v10은 사용하지 마세요.

> [!NOTE]
> Visual Studio Code는 Windows, Mac 및 Ubuntu에서 사용할 수 있습니다.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>마이크로 서비스 실행

이 섹션에서는 원격 모니터링 마이크로 서비스를 실행합니다. 웹 UI를 고유하게 실행하고, Docker에서 디바이스 시뮬레이션 서비스를 실행하고, Visual Studio Code에서 마이크로 서비스를 실행합니다.

### <a name="build-the-code"></a>코드 빌드

명령 프롬프트에서 azure-iot-pcs-remote-monitoring-dotnet\services로 이동한 후 다음 명령을 실행하여 코드를 작성합니다.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>그 외의 모든 마이크로 서비스를 로컬 머신에 배포

다음 단계는 Visual Studio 2017에서 원격 모니터링 마이크로 서비스를 실행하는 방법을 보여줍니다.

1. Visual Studio Code를 시작합니다.
1. VS Code의 로컬 복사본에서 **azure-iot-pcs-remote-monitoring-dotnet** 모듈을 엽니다.
1. scripts\local\launch\idesettings\vscode\.에서 파일 **launch.json** 및 **tasks.json**을 복사합니다. 새 폴더 **azure-iot-pcs-remote-monitoring-dotnet\.vscode**를 만들고 이 폴더에 해당 파일을 붙여넣습니다.
1. VS Code의 디버그 패널을 열고 **모든 마이크로 서비스 실행** 구성을 실행합니다. 이 구성은 Docker에서 디바이스 시뮬레이션 마이크로 서비스를 실행하고 디버거에서 다른 마이크로 서비스를 실행합니다.

예를 들어, **디버그 콘솔**의 **Auth** 서비스 출력은 다음과 같습니다.

[![Deploy-Local-Auth-Service](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>웹 UI 실행

이 단계에서는 웹 UI를 시작합니다. 로컬 복사본의 **azure-iot-pcs-remote-monitoring-dotnet\webui** 폴더로 이동한 후 다음 명령을 실행합니다.

```cmd
npm install
npm start
```

시작이 완료 되 면 브라우저 페이지를 표시 하는 **http:\//localhost:3000 / 대시보드**합니다. 이 페이지의 오류는 예상된 것입니다. 애플리케이션을 오류 없이 표시하려면 다음 단계를 완료합니다.

### <a name="configure-and-run-nginx"></a>NGINX 구성 및 실행

로컬 머신에서 실행되는 웹 애플리케이션 및 마이크로 서비스를 연결하는 역방향 프록시 서버를 설정합니다.

* **webui\scripts\localhost** 폴더의 **nginx.conf** 파일을 **nginx\conf** 설치 디렉터리에 복사합니다.
* **nginx**를 실행합니다.

**nginx** 실행에 대한 자세한 내용은 [Windows용 nginx](https://nginx.org/en/docs/windows.html)를 참조하세요.

### <a name="connect-to-the-dashboard"></a>대시보드에 연결

원격 모니터링 솔루션 대시보드에 액세스 하려면 http로 이동:\//localhost:9000 브라우저에서 합니다.

## <a name="clean-up"></a>정리

불필요한 요금을 방지하려면 테스트를 마친 후 Azure 구독에서 클라우드 서비스를 제거합니다. 서비스를 제거하려면 [Azure Portal](https://ms.portal.azure.com)로 이동하여 **start.cmd** 스크립트에서 만든 리소스 그룹을 삭제합니다.

또한 GitHub에서 소스 코드를 복제할 때 만들어진 원격 모니터링 리포지토리의 로컬 복사본을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

원격 모니터링 솔루션을 배포했으니, 다음 단계는 [솔루션 대시보드의 기능을 탐색](quickstart-remote-monitoring-deploy.md)하는 것입니다.
