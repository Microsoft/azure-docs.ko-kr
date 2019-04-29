---
title: Azure IoT Edge 개발 환경 | Microsoft Docs
description: 지원되는 시스템과 IoT Edge 모듈을 만드는 데 도움이 되는 자사 개발 도구에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3d1fb393138aeb590bc0645d6abe93652a52a5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61247554"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>IoT Edge를 위한 개발 및 테스트 환경 준비

Azure IoT Edge는 Edge에서 작동하는 디바이스로 기존 비즈니스 논리를 이동합니다. [IoT Edge 모듈](iot-edge-modules.md)로 실행하도록 애플리케이션 및 워크로드를 준비하려면 컨테이너로 빌드해야 합니다. 이 문서에서는 IoT Edge 솔루션을 성공적으로 만들 수 있도록 개발 환경을 구성하는 방법에 관한 지침을 제공합니다. 개발 환경을 설정했으면 [사용자 고유의 IoT Edge 모듈을 개발](module-development.md)하는 방법을 알아볼 수 있습니다.

모든 IoT Edge 솔루션에는 고려해야 할 두 개 이상의 컴퓨터가 있습니다. 하나는 IoT Edge 모듈을 실행하는 IoT Edge 디바이스 자체입니다. 다른 하나는 모듈을 빌드, 테스트 및 배포하는 데 사용되는 개발 컴퓨터입니다. 이 문서에서는 개발 컴퓨터에 주로 중점을 둡니다. 테스트를 위해 두 컴퓨터가 동일할 수 있습니다. 개발 컴퓨터에서 IoT Edge를 실행하고 모듈을 배포할 수 있습니다.

## <a name="operating-system"></a>운영 체제

Azure IoT Edge는 특정 [지원되는 운영 체제](support.md) 세트에서 실행됩니다. IoT Edge용으로 개발하는 경우 컨테이너 엔진을 실행할 수 있는 대부분의 운영 체제를 사용할 수 있습니다. 개발 컴퓨터에서 모듈을 컨테이너로 빌드한 후 컨테이너 레지스트리에 푸시하려면 컨테이너 엔진이 필요합니다. 

개발 컴퓨터에서 Azure IoT Edge를 실행할 수 없는 경우 이 문서를 계속 진행하면서 로컬 테스트 및 디버그에 도움이 되는 [테스트 도구](#testing-tools)에 대해 알아보세요. 

개발 컴퓨터의 운영 체제는 IoT Edge 디바이스의 운영 체제와 일치하지 않아도 됩니다. 그러나 개발 컴퓨터와 IoT Edge 디바이스 간의 컨테이너 운영 체제는 일정해야 합니다. 예를 들어, Windows 컴퓨터에서 모듈을 개발한 후 Linux 디바이스에 배포할 수 있습니다. Windows 컴퓨터는 Linux 디바이스용 모듈을 빌드하기 위해 Linux 컨테이너를 실행해야 합니다. 

## <a name="container-engine"></a>컨테이너 엔진

IoT Edge의 중심 개념은 비즈니스 및 클라우드 논리를 컨테이너로 패키지하여 원격으로 배포할 수 있다는 것입니다. 컨테이너를 빌드하려면 개발 컴퓨터에 컨테이너 엔진이 있어야 합니다. 

프로덕션 환경에서 IoT Edge 디바이스에 대해 지원되는 유일한 컨테이너 엔진은 Moby입니다. 그러나 Docker와 같은 Open Container Initiative와 호환되는 컨테이너 엔진은 IoT Edge 모듈 이미지를 빌드할 수 있습니다. 

## <a name="development-tools"></a>개발 도구

Visual Studio와 Visual Studio Code에는 IoT Edge 솔루션 개발에 도움이 되는 추가 기능 확장이 있습니다. 이러한 확장은 새 IoT Edge 시나리오를 쉽게 만들고 배포할 수 있도록 하는 언어별 템플릿을 제공합니다. Visual Studio 및 Visual Studio Code용 Azure IoT Edge 확장은 IoT Edge 솔루션을 편리하게 코딩, 빌드, 배포 및 디버그할 수 있도록 합니다. 여러 모듈을 포함하는 전체 IoT Edge 솔루션을 만들 수 있습니다. 그러면 확장 모듈은 새 모듈이 추가될 때마다 배포 매니페스트 템플릿을 자동으로 업데이트합니다. 이러한 확장을 사용하여 Visual Studio 또는 Visual Studio Code 내에서 IoT 디바이스를 관리할 수도 있습니다. 디바이스에 모듈을 배포하고, 상태를 모니터링하고, IoT Hub에 발생하는 메시지를 확인합니다. 두 확장 모두 [IoT EdgeHub 개발 도구](#iot-edgehub-dev-tool)를 사용하여 개발 컴퓨터의 모듈 로컬 실행 및 디버그를 사용하도록 설정합니다. 

다른 편집기 또는 CLI를 사용하여 개발하려는 경우 Azure IoT Edge 개발자 도구는 명령줄에서 개발 및 테스트할 수 있도록 명령을 제공합니다. 새 IoT Edge 시나리오를 만들고, 모듈 이미지를 빌드하고, 시뮬레이터에서 모듈을 실행하고, IoT Hub로 보낸 메시지를 모니터링할 수 있습니다. 

### <a name="visual-studio-code-extension"></a>Visual Studio Code 확장

Visual Studio Code용 Azure IoT Edge 확장은 C#의 Azure 함수뿐만 아니라 C, C#, Java, Node.js 및 Python을 비롯한 프로그래밍 언어로 빌드한 IoT Edge 모듈 템플릿을 제공합니다. 

자세한 내용을 확인하고 다운로드하려면 [Visual Studio Code용 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 참조하세요.

IoT Edge 확장 외에도 개발을 위한 추가 확장을 설치하면 유용할 수 있습니다. 예를 들어 [Visual Studio Code용 Docker 지원](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)을 사용하여 이미지, 컨테이너 및 레지스트리를 관리할 수 있습니다. 또한 지원되는 모든 주요 언어 모듈에는 개발 시 도움이 될 수 있는 Visual Studio Code용 확장이 있습니다. 

#### <a name="prerequisites"></a>필수 조건

일부 언어 및 서비스용 모듈 템플릿에는 Visual Studio Code를 사용하여 개발 컴퓨터에서 프로젝트 폴더를 빌드하는 데 필요한 필수 구성 요소가 있습니다.

| 모듈 템플릿 | 필수 요소 |
| --------------- | ------------ |
| Azure 기능 | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://www.microsoft.com/net/download) |
| 자바 | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [JAVA_HOME 환경 변수 설정](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.JS](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js 모듈 생성기](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-2017-extension"></a>Visual Studio 2017 확장

Visual Studio용 Azure IoT Edge 도구는 C#으로 빌드한 IoT Edge 모듈 템플릿을 제공합니다. 

자세한 내용을 확인하고 다운로드하려면 [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)을 참조하세요.

### <a name="iot-edge-dev-tool"></a>IoT Edge 개발자 도구

Azure IoT Edge 개발자 도구는 명령줄 기능을 제공하여 IoT Edge 개발을 간소화합니다. 이 도구는 모듈을 개발, 디버그 및 테스트하기 위한 CLI 명령을 제공합니다. IoT Edge 개발자 도구는 컴퓨터에 수동으로 종속성을 설치했는지 또는 IoT Edge 개발 컨테이너를 사용하고 있는지에 관계없이 개발 시스템에 작동합니다. 

자세한 내용을 확인하고 시작하려면 [IoT Edge 개발자 도구 wiki](https://github.com/Azure/iotedgedev/wiki)를 참조하세요.

## <a name="testing-tools"></a>테스트 도구

IoT Edge 디바이스를 시뮬레이트하거나 모듈을 보다 효율적으로 디버그하기 위한 몇 가지 테스트 도구가 있습니다. 다음 표에서는 도구 간을 자세히 비교하며, 개별 섹션에서는 각 도구를 보다 구체적으로 설명합니다. 

IoT Edge 런타임만 프로덕션 환경에 배포할 수 있지만, 다음 도구를 사용하면 개발 및 테스트 목적으로 IoT Edge 디바이스를 쉽게 시뮬레이트하거나 만들 수 있습니다. 이러한 도구는 상호 배타적이지 않으며 완전한 개발 환경을 위해 함께 작동할 수 있습니다. 

| 도구 | 다른 명칭 | 지원되는 플랫폼 | 적합한 대상 |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub 개발자 도구  | iotedgehubdev | Windows, Linux, MacOS | 모듈을 디버그하는 디바이스 시뮬레이트 |
| IoT Edge 개발자 컨테이너 | microsoft/iotedgedev | Windows, Linux, MacOS | 종속성을 설치하지 않고 개발 |
| 컨테이너의 IoT Edge 런타임 | iotedgec | Windows, Linux, MacOS, ARM | 런타임을 지원하지 않을 수 있는 디바이스에서 테스트 |
| IoT Edge 디바이스 컨테이너 | toolboc/azure-iot-edge-device-container | Windows, Linux, MacOS, ARM | 많은 IoT Edge 디바이스를 포함하는 시나리오를 대규모로 테스트 |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub 개발자 도구

Azure IoT EdgeHub 개발자 도구는 로컬 개발 및 디버그 환경을 제공합니다. 이 도구는 IoT Edge 런타임 없이 IoT Edge 모듈을 시작할 수 있도록 하므로 IoT Edge 모듈 및 솔루션을 로컬로 만들고 개발하고 테스트하고 실행하고 디버그할 수 있습니다. 이미지를 컨테이너 레지스트리로 푸시한 후 테스트용 디바이스에 배포할 필요가 없습니다.

IoT EdgeHub 개발자 도구는 IoT Edge 개발자 도구뿐만 아니라 Visual Studio 및 Visual Studio Code 확장에서도 잘 작동하도록 디자인되었습니다. 외부 루프 테스트뿐만 아니라 내부 루프 개발을 지원하므로 DevOps 도구와도 통합됩니다. 

자세한 내용을 참조하고 설치하려면 [Azure IoT EdgeHub 개발자 도구](https://pypi.org/project/iotedgehubdev/)를 참조하세요.

### <a name="iot-edge-dev-container"></a>IoT Edge 개발자 컨테이너

Azure IoT Edge 개발 컨테이너는 IoT Edge 개발에 필요한 종속성이 모두 있는 Docker 컨테이너입니다. 이 컨테이너를 사용하면 C#, Python, Node.js 및 Java를 비롯하여 원하는 어떤 언어로도 쉽게 시작할 수 있습니다. Docker 또는 Moby와 같은 컨테이너 엔진을 설치하여 개발 컴퓨터로 컨테이너를 끌어오기만 하면 됩니다. 

자세한 내용은 [Azure IoT Edge 개발자 컨테이너](https://hub.docker.com/r/microsoft/iotedgedev/)를 참조하세요.

### <a name="iot-edge-runtime-in-a-container"></a>컨테이너의 IoT Edge 런타임

컨테이너의 IoT Edge 런타임은 디바이스 연결 문자열을 환경 변수로 사용하는 전체 런타임을 제공합니다. 이 컨테이너를 사용하면 MacOS와 같이 기본적으로는 런타임을 지원하지 않을 수 있는 시스템에서 IoT Edge 모듈 및 시나리오를 테스트할 수 있습니다. 배포하는 모든 모듈은 런타임 컨테이너에서 외부에서 시작됩니다. 런타임 및 배포한 모든 모듈을 동일한 컨테이너에 두려면 대신 IoT Edge 디바이스 컨테이너를 고려합니다.

자세한 내용은 [컨테이너에서 Azure IoT Edge 실행](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)을 참조하세요.

### <a name="iot-edge-device-container"></a>IoT Edge 디바이스 컨테이너

IoT Edge 디바이스 컨테이너는 컨테이너 엔진이 있는 어떤 컴퓨터에서도 실행할 준비가 된 완전한 IoT Edge 디바이스입니다. 디바이스 컨테이너에는 IoT Edge 런타임 및 컨테이너 엔진 자체가 포함됩니다. 컨테이너의 각 인스턴스는 완전 기능형 자체 프로비전 IoT Edge 디바이스입니다. 네트워크를 통해 모듈에 연결되어 있는 한, 디바이스 컨테이너는 모듈의 원격 디버깅을 지원합니다. 디바이스 컨테이너는 많은 수의 IoT Edge 디바이스를 빠르게 만들어 대규모 시나리오 또는 DevOps 파이프라인을 테스트하는 데 적합합니다. 또한 helm을 통해 kubernetes에 배포하도록 지원합니다. 

자세한 내용은 [Azure IoT Edge 디바이스 컨테이너](https://github.com/toolboc/azure-iot-edge-device-container)를 참조하세요.

## <a name="devops-tools"></a>DevOps 도구

광범위한 프로덕션 시나리오를 위한 대규모 솔루션을 개발할 준비가 완료되면 자동화, 모니터링 및 간소화된 소프트웨어 엔지니어링 프로세스를 포함하는 최신 DevOps 원리를 활용하세요. IoT Edge에는 Azure DevOps, Azure DevOps Projects 및 Jenkins를 비롯한 DevOps 도구를 지원하기 위한 확장이 있습니다. 기존 파이프라인을 사용자 지정하거나 CircleCI 또는 TravisCI 등의 다른 DevOps 도구를 사용하려는 경우 IoT Edge 개발자 도구에 포함된 CLI 기능을 사용할 수 있습니다.

자세한 내용, 지침 및 예제를 보려면 다음 페이지를 참조하세요.
* [Azure IoT Edge 연속 통합 및 지속적인 배포](how-to-ci-cd.md)
* [Azure DevOps Projects를 사용하여 IoT Edge에 대한 CI/CD 파이프라인 만들기](how-to-devops-project.md)
* [Azure IoT Edge Jenkins 플러그 인](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub 리포지토리](https://github.com/toolboc/IoTEdge-DevOps)
