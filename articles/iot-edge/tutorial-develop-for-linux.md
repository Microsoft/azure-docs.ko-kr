---
title: Linux 디바이스용 모듈 개발 - Azure IoT Edge | Microsoft Docs
description: 이 자습서에서는 Linux 디바이스용 Linux 컨테이너를 사용하여 IoT Edge 모듈을 개발하기 위해 개발 컴퓨터 및 클라우드 리소스를 설정하는 과정을 설명합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 11fa72f5853350c76b2a8d0aa4fd7b96b598b670
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303859"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>자습서: Linux 디바이스를 위한 IoT Edge 모듈 개발

Visual Studio Code를 사용하여 IoT Edge를 실행하는 코드를 개발하고 Linux 디바이스에 배포합니다. 

빠른 시작 문서에서는 Linux 가상 머신을 사용하여 IoT Edge 디바이스를 만들고 Azure Marketplace에서 미리 빌드한 모듈을 배포했습니다. 이 자습서에서는 고유한 코드를 개발하고 IoT Edge 디바이스에 배포하기 위한 과정을 안내합니다. 이 자습서는 특정 프로그래밍 언어 또는 Azure 서비스에 대해 좀 더 자세한 내용을 설명하는 다른 자습서를 진행하기 전에 먼저 살펴보아야 하는 유용한 내용을 제공합니다. 

이 자습서에서는 **Linux 디바이스에 C 모듈**을 배포하는 예제를 사용합니다. 이 예제는 필수 구성 요소가 가장 적기 때문에 선택한 측면이 있으므로 적절한 라이브러리를 설치하지 않았더라도 큰 문제 없이 개발 도구에 대해 알아볼 수 있습니다. 개발 개념을 이해하고 나면 기본 언어 또는 Azure 서비스를 선택하여 세부 정보를 자세히 살펴볼 수 있습니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 개발 머신을 설정합니다.
> * Visual Studio Code용 IoT Edge 도구를 사용하여 새 프로젝트를 만듭니다.
> * 프로젝트를 컨테이너로 빌드하고 Azure Container Registry에 저장합니다.
> * IoT Edge 디바이스에 코드를 배포합니다. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>주요 개념

이 자습서는 IoT Edge 모듈 개발 과정을 안내합니다. *IoT Edge 모듈* 또는 경우에 따라 간단히 *모듈*이라고 하는 이 기능은 실행 코드가 포함된 컨테이너입니다. 하나 이상의 모듈을 IoT Edge 디바이스에 배포할 수 있습니다. 모듈은 센서에서 데이터 수집, 데이터 분석 또는 데이터 정리 작업 수행, IoT Hub로 메시지를 보내기 등의 특정 작업을 수행합니다. 자세한 내용은 [Azure IoT Edge 모듈 이해](iot-edge-modules.md)를 참조하세요.

IoT Edge 모듈을 개발할 때 개발 머신과 모듈을 결과적으로 배치할 대상 IoT Edge 디바이스 간의 차이를 이해하는 것이 중요합니다. 모듈 코드를 포함하기 위해 빌드하는 컨테이너는 *대상 디바이스*의 OS(운영 체제)와 일치해야 합니다. 예를 들어, 가장 일반적인 시나리오는 Windows 머신에서 IoT Edge를 실행하는 Linux 디바이스 대상의 모듈을 개발하는 경우입니다. 이 경우 컨테이너 운영 체제는 Linux일 것입니다. 이 자습서를 진행할 때는 *개발 머신 OS*와 *컨테이너 OS* 간의 차이점에 유의해야 합니다.

이 자습서는 IoT Edge를 실행하는 Linux 디바이스를 대상으로 합니다. 개발 머신에서 Linux 컨테이너를 실행할 수 있다면 선호하는 개발 머신 운영 체제를 사용할 수 있습니다. 이 자습서에서는 Visual Studio Code를 사용할 예정이므로 이 도구로 Linux 디바이스용 개발을 진행하는 것이 좋습니다. 두 도구 간에 지원 차이가 있긴 하지만, Visual Studio를 사용할 수도 있습니다.

다음 표에는 Visual Studio Code 및 Visual Studio의 **Linux 컨테이너**에 대해 지원되는 개발 시나리오가 나와 있습니다.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux 디바이스 아키텍처** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure 서비스** | Azure 기능 <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **언어** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **자세한 정보** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Visual Studio 2017용 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools), [Visual Studio 2019용 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

이 자습서에서는 Visual Studio Code에 대한 개발 단계를 설명합니다. Visual Studio를 사용하는 경우는 [Visual Studio 2019를 사용한 Azure IoT Edge용 모듈 개발 및 디버그](how-to-visual-studio-develop-module.md)의 지침을 참조하세요.

## <a name="prerequisites"></a>필수 조건

개발 머신:

* 본인의 개발 선호도에 자신의 머신 또는 가상 머신을 사용할 수 있습니다.
* 컨테이너 엔진을 실행할 수 있는 대부분의 운영 체제에서 Linux 디바이스용 IoT Edge 모듈을 개발할 수 있습니다. 이 자습서에서는 Windows 머신을 사용하지만 MacOS 또는 Linux에서 알려진 차이점을 설명합니다. 
* 이 자습서의 뒷부분에 나오는 모듈 템플릿 패키지를 끌어오려면 [Git](https://git-scm.com/)을 설치하세요.  

Linux의 Azure IoT Edge 디바이스:

* 개발 머신에서 IoT Edge를 실행하지 않고, 대신 별도 디바이스를 사용하는 것이 좋습니다. 이와 같이 개발 머신과 IoT Edge 디바이스를 구분하면 실제 배포 시나리오를 정확히 반영할 수 있고 개념 차이를 유지하는 데 도움이 됩니다.
* 두 번째 디바이스를 사용할 수 없으면 빠른 시작 문서를 사용하여 [Linux 가상 머신](quickstart-linux.md)을 사용하여 Azure에서 IoT Edge 디바이스를 만듭니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)입니다. 

## <a name="install-container-engine"></a>컨테이너 엔진 설치

IoT Edge 모듈은 컨테이너로 패키지되므로, 개발 머신에 컨테이너를 빌드 및 관리하기 위한 컨테이너 엔진이 필요합니다. Docker Desktop이 많은 기능을 제공하며 컨테이너 엔진으로 널리 사용되고 있으므로 개발을 위해 이 도구를 사용하는 것이 좋습니다. Windows 디바이스에 Docker Desktop이 있으면 Linux 컨테이너와 Windows 컨테이너 간을 전환하면서 다양한 IoT Edge 디바이스 유형에 대한 모듈을 쉽게 개발할 수 있습니다. 

개발 머신에 설치하려면 다음 Docker 설명서를 참조하세요. 

* [Windows용 Docker Desktop 설치](https://docs.docker.com/docker-for-windows/install/)

  * Windows용 Docker Desktop을 설치할 때 Linux 컨테이너를 사용할지 아니면 Windows 컨테이너를 사용할지 묻는 메시지가 표시됩니다. 이러한 결정 사항은 간편한 스위치를 사용해서 언제든지 변경할 수 있습니다. 이 자습서의 모듈은 Linux 디바이스를 대상으로 하기 때문에 Linux 컨테이너를 사용합니다. 자세한 내용은 [Windows 및 Linux 컨테이너 간 전환](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)을 참조하세요.

* [Mac용 Docker Desktop 설치](https://docs.docker.com/docker-for-mac/install/)

* 여러 Linux 플랫폼의 설치 정보에 대해서는 [Docker CE 정보](https://docs.docker.com/install/)를 참조하세요.

## <a name="set-up-vs-code-and-tools"></a>VS Code 및 도구 설정

Visual Studio Code용 IoT 확장을 사용하여 IoT Edge 모듈을 개발합니다. 이러한 확장은 프로젝트 템플릿을 제공하고, 배포 매니페스트 생성을 자동화하고, IoT Edge 디바이스를 모니터링 및 관리할 수 있도록 합니다. 이 섹션에서는 Visual Studio Code 및 IoT 확장을 설치한 후 Visual Studio Code 내에서 IoT Hub 리소스를 관리하도록 Azure 계정을 설정합니다. 

1. 개발 머신에서 [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다. 

2. 설치가 완료되면 **보기** > **확장**을 선택합니다. 

3. **Azure IoT Tools**를 검색합니다. 이 도구는 실제로 IoT Edge 모듈 개발에 필요한 뿐만 아니라 IoT Hub 및 IoT 디바이스와 상호 작용하기 위한 확장 모음입니다. 

4. **설치**를 선택합니다. 포함된 각 확장은 개별적으로 설치됩니다. 

5. 확장 설치가 끝나면 **보기** > **명령 팔레트**를 선택하여 명령 팔레트를 엽니다. 

6. 명령 팔레트에서 **Azure: 로그인** 명령을 검색하여 실행합니다. 표시되는 메시지에 따라 Azure 계정에 로그인합니다. 

7. 다시 명령 팔레트에서 **Azure: IoT Hub 선택**을 선택합니다. 지시에 따라 Azure 구독 및 IoT Hub를 선택합니다. 

7. 왼쪽의 작업 표시줄에서 아이콘을 선택하거나 **보기** > **탐색기**를 선택하여 Visual Studio Code의 탐색기 섹션을 엽니다. 

8. 탐색기 섹션 맨 아래에서 축소된 **Azure IoT Hub 디바이스** 메뉴를 확장합니다. 명령 팔레트를 통해 선택한 IoT Hub와 연결된 디바이스 및 IoT Edge 디바이스가 표시됩니다. 

   ![IoT Hub의 디바이스 보기](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>새 모듈 프로젝트 만들기

Azure IoT Tools 확장에서는 Visual Studio Code에서 지원되는 모든 IoT Edge 모듈 언어를 위한 프로젝트 템플릿이 제공됩니다. 이러한 템플릿에는 작업 모듈을 배포하여 IoT Edge를 테스트하는 데 필요한 모든 파일 및 코드가 포함되어 있으며, 사용자 고유의 비즈니스 논리를 사용하여 템플릿을 사용자 지정하기 위한 시작 지점이 되기도 합니다. 

이 자습서에서는 최소의 필수 구성 요소만 설치하면 되므로 C 모듈 템플릿을 사용합니다. 

### <a name="create-a-project-template"></a>프로젝트 템플릿 만들기

Visual Studio Code 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 지시에 따라 다음 값을 사용하여 솔루션을 만듭니다. 

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값 **EdgeSolution**을 적용합니다. |
   | 모듈 템플릿 선택 | **C 모듈**을 선택합니다. |
   | 모듈 이름 제공 | 기본값인 **SampleModule**을 그대로 사용합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에 제공한 이름으로 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. <br><br> 마지막 이미지 리포지토리는 \<레지스트리 이름\>.azurecr.io/samplemodule과 같습니다. |
 
   ![Docker 이미지 리포지토리 제공](./media/tutorial-develop-for-linux/image-repository.png)

Visual Studio Code 창에 새 솔루션이 로드되면 생성된 다음 파일을 살펴보고 이해합니다. 

* **.vscode** 폴더에는 모듈 디버그에 사용되는 **launch.json**이라는 파일이 포함되어 있습니다.
* **modules** 폴더에는 솔루션의 각 모듈용 폴더가 포함되어 있습니다. 현재는 **SampleModule** 또는 모듈에 지정한 다른 이름의 폴더만 있을 것입니다. SampleModule 폴더에는 주 프로그램 코드, 모듈 메타데이터 및 여러 개의 Docker 파일이 포함되어 있습니다. 
* **.env** 파일은 컨테이너 레지스트리의 자격 증명을 저장합니다. 이러한 자격 증명은 IoT Edge 디바이스와 공유되므로 이러한 디바이스에서 컨테이너 이미지를 끌어올 수 있습니다. 
* **deployment.debug.template.json** 파일 및 **deployment.template.json** 파일은 배포 매니페스트를 만드는 데 도움이 되는 템플릿입니다. *배포 매니페스트*는 디바이스에 배포하려는 모듈, 구성 방법 및 모듈끼리 및 모듈-클라우드 간에 통신하는 방법을 정확히 정의하는 파일입니다. 템플릿 파일은 일부 값에 대해 포인터를 사용합니다. 템플릿을 실제 배포 매니페스트로 변환하는 경우 포인터가 다른 솔루션 파일에서 가져온 값으로 대체됩니다. 배포 템플릿에서 두 가지 일반적인 자리 표시자를 찾습니다. 

  * 레지스트리 자격 증명 섹션에서 솔루션을 만들 때 제공한 정보로 주소가 자동으로 채워집니다. 단, 사용자 이름 및 암호는 .env 파일에 저장된 변수를 참조합니다. 이러한 방식은 보안을 위한 것입니다. .env 파일은 Git에서 무시되지만 배포 템플릿은 무시되지 않기 때문입니다. 
  * SampleModule 섹션에서 컨테이너 이미지는 솔루션을 만들 때 이미지 리포지토리를 제공한 경우에도 채워지지 않습니다. 이 자리 표시자는 SampleModule 폴더 내에 있는 **module.json** 파일을 가리킵니다. 해당 파일로 이동하면 이미지 필드에 리포지토리가 포함되어 있을 뿐만 아니라 컨테이너의 버전 및 플랫폼으로 구성된 태그 값도 포함되어 있는 것을 볼 수 있습니다. 개발 주기의 일환으로 버전을 수동으로 반복할 수 있으며 이 섹션 뒷부분에서 소개한 전환기를 사용하여 컨테이너 플랫폼을 선택합니다. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>IoT Edge 에이전트에 레지스트리 자격 증명 제공

환경 파일은 컨테이너 레지스트리의 자격 증명을 저장하고 IoT Edge 런타임과 공유합니다. 이러한 자격 증명은 런타임에서 컨테이너 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다. 

IoT Edge 확장은 Azure에서 컨테이너 레지스트리 자격 증명을 끌어온 후 환경 파일에 채우려고 합니다. 사용자 자격 증명이 이미 포함되어 있는지 확인합니다. 그렇지 않은 경우 다음과 같이 지금 추가합니다.

1. 모듈 솔루션의 **.env** 파일을 엽니다. 
2. Azure Container Registry에서 복사한 **사용자 이름** 및 **암호** 값을 추가합니다.
3. .env 파일에 변경 내용을 저장합니다. 

### <a name="select-your-target-architecture"></a>대상 아키텍처 선택

현재, Visual Studio Code에서는 Linux AMD64 및 Linux ARM32v7 디바이스용 C 모듈을 개발할 수 있습니다. 컨테이너는 아키텍처 유형별로 다르게 빌드되고 실행되므로 각 솔루션에서 대상으로 지정할 아키텍처를 선택해야 합니다. 기본값은 Linux AMD64입니다. 

1. 명령 팔레트를 열고 **Azure IoT Edge: 에지 솔루션용 기본 대상 플랫폼 설정**을 검색하거나 창의 맨 아래에 있는 사이드바에서 바로 가기 아이콘을 선택합니다. 

   ![사이드바의 아키텍처 아이콘 선택](./media/tutorial-develop-for-linux/select-architecture.png)

2. 명령 팔레트의 옵션 목록에서 대상 아키텍처를 선택합니다. 이 자습서에서는 Ubuntu 가상 머신을 IoT Edge 디바이스로 사용할 예정이므로 기본값인 **amd64**를 그대로 둡니다. 

### <a name="review-the-sample-code"></a>샘플 코드 검토

만든 솔루션 템플릿에는 IoT Edge 모듈용 샘플 코드가 포함되어 있습니다. 이 샘플 모듈은 메시지 수신한 후 전달합니다. 파이프라인 기능은 IoT Edge의 중요한 개념인 모듈이 서로 통신하는 방식을 보여 줍니다.

각 모듈의 코드에는 여러 개의 *입력* 및 *출력* 큐가 선언될 수 있습니다. 디바이스에서 실행되는 IoT Edge 허브는 모듈 중 하나의 출력에 있는 메시지를 하나 이상의 모듈 입력으로 라우팅합니다. 입력 및 출력을 선언하는 특정 언어는 언어마다 다르지만 개념은 모든 모듈에서 동일합니다. 모듈 간 라우팅에 대한 자세한 내용은 [경로 선언](module-composition.md#declare-routes)을 참조하세요.

1. **modules/SampleModules/** 폴더 내에 있는 **main.c** 파일을 엽니다. 

2. IoT Hub C SDK는 함수 [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback)을 사용하여 모듈 입력 큐를 초기화합니다. main.c 파일에서 해당 함수를 검색합니다.

3. SetInputMessageCallback 함수 생성자를 검토하고 입력 큐 **input1**이 코드에서 초기화된 것을 확인합니다. 

   ![SetInputMessageCallback 생성자에서 입력 이름 찾기](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. 모듈 출력 큐는 비슷한 방식으로 초기화됩니다. main.c 파일에서 [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) 함수를 검색합니다. 

5. SendEventToOutputAsync 함수 생성자를 검토하고 출력 큐 **output1**이 코드에서 초기화된 것을 확인합니다. 

   ![SendEventToOutputAsync에서 출력 이름 찾기](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. **deployment.template.json** 파일을 엽니다.

7. $edgeAgent의 원하는 속성 중에서 **modules** 속성을 찾습니다. 

   여기에는 두 개의 모듈이 표시되어야 합니다. 첫 번째는 **tempSensor**로, 모듈을 테스트하는 데 사용할 수 있는 시뮬레이트된 온도 데이터를 제공하기 위해 기본적으로 모든 템플릿에 포함되어 있습니다. 두 번째는 **SampleModule**로, 이 솔루션의 일부로 생성한 모듈입니다.

7. 파일 맨 아래에서 **$edgeHub** 모듈의 원하는 속성을 찾습니다. 

   IoT Edge 허브 모듈의 기능 중 하나는 배포의 모든 모듈 간에 메시지를 라우팅하는 것입니다. **routes** 속성에서 값을 검토합니다. 첫 번째 경로 **SampleModuleToIoTHub**는 와일드카드 문자( **\*** )를 사용하여 SampleModule 모듈의 출력 큐에서 가져온 모든 메시지를 나타냅니다. 이러한 메시지는 IoT Hub를 지정하는 예약 이름인 *$upstream*으로 이동됩니다. 두 번째 경로 sensorToSampleModule은 tempSensor 모듈에서 메시지를 가져온 후 SampleModule 코드에서 초기화된 것으로 확인한 *input1* 입력 큐로 라우팅합니다. 

   ![deployment.template.json의 경로 검토](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>솔루션 빌드 및 푸시

모듈 코드 및 배포 템플릿을 검토하여 몇 가지 주요 배포 개념을 이해했습니다. 이제 SampleModule 컨테이너 이미지를 빌드하고 컨테이너 레지스트리로 푸시할 준비가 되었습니다. Visual Studio Code용 IoT Tools 확장을 사용할 경우 이 단계에서도 템플릿 파일의 정보와 솔루션 파일의 모듈 정보에 따라 배포 매니페스트를 생성합니다. 

### <a name="sign-in-to-docker"></a>Docker에 로그인

레지스트리에 저장될 컨테이너 이미지를 푸시할 수 있도록 Docker에 컨테이너 레지스트리 자격 증명을 제공합니다. 

1. **보기** > **터미널**을 선택하여 Visual Studio Code 통합 터미널을 엽니다.

2. 레지스트리를 만든 후 저장한 Azure Container Registry 자격 증명을 사용하여 Docker에 로그인합니다. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` 사용을 권장하는 보안 경고가 표시될 수 있습니다. 이 모범 사례는 프로덕션 시나리오에 권장되지만 이 자습서에는 포함되지 않습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 참조를 참조하세요.

### <a name="build-and-push"></a>빌드 및 푸시 

이제 Visual Studio Code에서 컨테이너 레지스트리에 액세스할 수 있으므로 솔루션 코드를 컨테이너 이미지로 전환해야 합니다. 

1. Visual Studio Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다. 

   ![IoT Edge 모듈 빌드 및 푸시](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   빌드 및 푸시 명령은 세 가지 작업을 시작합니다. 먼저, 배포 템플릿 및 기타 솔루션 파일의 정보로 작성된 전체 배포 매니페스트를 포함하는 **config**라는 새 폴더를 솔루션에 만듭니다. 둘째, `docker build`를 실행하여 대상 아키텍처의 적절한 dockerfile을 기준으로 컨테이너 이미지를 빌드합니다. 그런 다음, `docker push`를 실행하여 컨테이너 레지스트리에 이미지 리포지토리를 푸시합니다. 

   이 프로세스는 처음에는 몇 분 정도 걸릴 수 있지만 다음번에 명령을 실행할 때는 더 빨라집니다. 

2. 새로 만든 config 폴더에서 **deployment.amd64.json** 파일을 엽니다. 파일 이름은 대상 아키텍처를 반영하므로 다른 아키텍처를 선택한 경우에는 달라집니다.

3. 이제 자리 표시자가 있는 두 매개 변수가 적절한 값으로 채워졌는지 확인합니다. **registryCredentials** 섹션에는 .env 파일에서 끌어온 레지스트리 사용자 이름과 암호 섹션에 표시됩니다. **SampleModule**에는 module.json 파일의 이름, 버전 및 아키텍처 태그가 있는 전체 이미지 리포지토리가 포함됩니다. 

4. SampleModule 폴더에서 **module.json** 파일을 엽니다. 

5. 모듈 이미지의 버전 번호를 변경합니다. ($schema-version이 아닌 버전) 예를 들어, 모듈 코드가 약간 수정된 것처럼 패치 버전 번호를 **0.0.2**로 늘립니다. 

   >[!TIP]
   >모듈 버전을 통해 버전을 관리할 수 있으며, 프로덕션 환경에 업데이트를 밸포하기 전에 소규모 디바이스 세트에서 변경 내용을 테스트해볼 수 있습니다. 모듈을 빌드 및 푸시하기 전에 모듈 버전을 늘리지 않으면 컨테이너 레지스트리의 리포지토리를 덮어쓰게 됩니다. 

6. module.json 파일에 변경 내용을 저장합니다.

7. **deployment.template.json** 파일을 다시 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 다시 선택합니다.

8. **deployment.amd64.json** 파일을 다시 엽니다. 빌드 및 푸시 명령을 다시 실행할 때는 새 파일이 생성되지 않은 것을 알 수 있습니다. 대신, 변경 내용을 반영하도록 동일한 파일이 업데이트되었습니다. 이제 SampleModule 이미지는 컨테이너의 0.0.2 버전을 가리킵니다. 

9. 빌드 및 푸시 명령이 어떤 작업을 수행했는지를 추가로 확인하려면 [Azure Portal](https://portal.azure.com)로 이동한 후 컨테이너 레지스트리로 이동합니다. 

10. 컨테이너 레지스트리에서 **리포지토리**를 선택하고 **samplemodule**을 선택합니다. 이미지의 두 버전이 레지스트리에 푸시되었는지 확인합니다.

   ![컨테이너 레지스트리에서 두 이미지 버전 보기](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>문제 해결

모듈 이미지를 빌드 및 푸시할 때 오류가 발생하는 경우 개발 머신의 Docker 구성과 관련이 있는 경우가 자주 있습니다. 다음 사항을 확인하여 구성을 검토합니다. 

* 컨테이너 레지스트리에서 복사한 자격 증명을 사용하여 `docker login` 명령을 실행했나요? 이러한 자격 증명은 Azure에 로그인하는 데 사용하는 자격 증명과 다릅니다. 
* 컨테이너 리포지토리가 올바른가요? 컨테이너 레지스트리 이름과 모듈 이름이 올바른가요? SampleModule 폴더에서 **module.json** 파일을 열어 확인합니다. 리포지토리 값은 **\<레지스트리 이름\>.azurecr.io/samplemodule**과 같습니다. 
* 모듈에 **SampleModule** 이외의 이름을 사용한 경우 해당 이름이 솔루션 전체에서 일관되나요?
* 머신에서 빌드하는 것과 같은 유형의 컨테이너가 실행되고 있나요? 이 자습서는 Linux IoT Edge 디바이스에 대한 내용을 제공하므로 Visual Studio Code는 사이드바에 **amd64** 또는 **arm32v7**을 표시해야 하고 Docker Desktop에서는 Linux 컨테이너가 실행되고 있어야 합니다. Visual Studio Code의 C 모듈은 Windows 컨테이너를 지원하지 않습니다. 

## <a name="deploy-modules-to-device"></a>디바이스에 모듈 배포

이제 빌드한 컨테이너 이미지를 디바이스에 배포해야 하므로 컨테이너 레지스트리에 저장되어 있는지 확인합니다. IoT Edge 디바이스가 작동되고 실행 중인지 확인합니다. 

1. Visual Studio Code 탐색기에서 Azure IoT Hub 디바이스 섹션을 확장합니다. 

2. 배포하려는 IoT Edge 디바이스를 마우스 오른쪽 단추로 클릭한 다음, **단일 디바이스용 배포 만들기**를 선택합니다. 

   ![단일 디바이스용 배포 만들기](./media/tutorial-develop-for-linux/create-deployment.png)

3. 파일 탐색기에서 **config** 폴더로 이동한 후 **deployment.amd64.json** 파일을 선택합니다. 

   deployment.template.json 파일은 컨테이너 레지스트리 자격 증명 또는 모듈 이미지 값을 포함하지 않으므로 사용하지 않도록 합니다. Linux ARM32 디바이스를 대상으로 하는 경우 배포 매니페스트 이름이 deployment.arm32v7.json으로 지정됩니다. 

4. IoT Edge 디바이스에 대한 세부 정보를 확장한 다음, 디바이스의 **모듈** 목록을 확장합니다. 

5. SampleModule 및 tempSensor 모듈이 디바이스에서 실행되는 것을 확인할 때까지 새로 고침 단추를 사용하여 디바이스 보기를 업데이트합니다. 

   두 모듈이 모두 시작하는 데 몇 분 정도 걸릴 수 있습니다. IoT Edge 런타임은 새 배포 매니페스트를 받고, 컨테이너 런타임에서 모듈 이미지를 끌어온 후 각 새 모듈을 시작해야 합니다. 

   ![IoT Edge 디바이스에서 실행되는 모듈을 봅니다.](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>디바이스에서 메시지 보기

SampleModule 코드는 입력 큐의 메시지를 받고 출력 큐를 통해 메시지를 전달합니다. 배포 매니페스트는 tempSensor에서 SampleModule로 메시지를 제공한 후 SampleModule에서 IoT Hub로 메시지를 전달하는 경로를 선언했습니다. Azure IoT Tools for Visual Studio Code를 사용하여 개별 디바이스에서 IoT Hub에 도착하는 메시지를 볼 수 있습니다. 

1. Visual Studio Code 탐색기에서 모니터링하려는 IoT Edge 디바이스를 마우스 오른쪽 단추로 클릭한 다음, **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다. 

2. Visual Studio Code의 출력 창에서 IoT Hub에 메시지가 도착하는 것을 확인합니다. 

   ![디바이스에서 클라우드로 수신되는 메시지 보기](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>디바이스에서 변경 내용 보기

디바이스 자체에서 발생하는 상황을 확인하려는 경우 이 섹션의 명령을 사용하여 디바이스에서 실행되는 IoT Edge 런타임 및 모듈을 검사합니다. 

이 섹션의 명령은 개발 머신용이 아니라 IoT Edge 디바이스용입니다. IoT Edge 디바이스용으로 가상 머신을 사용하고 있는 경우 지금 연결합니다. Azure에서 가상 머신의 개요 페이지로 이동한 후 **연결**을 선택하여 보안 셸 연결에 액세스합니다. 

* 디바이스에 배포된 모든 모듈을 확인하고 해당 상태를 확인합니다.

   ```bash
   iotedge list
   ```

   4개의 모듈(두 개의 IoT Edge 런타임 모듈, tempSensor 및 SampleModule)이 표시됩니다. 4개의 모듈 모두 실행 중으로 표시됩니다.

* 특정 모듈에 대한 로그를 검사합니다.

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge 모듈은 대/소문자를 구분하지 않습니다. 

   tempSensor 및 SampleModule 로그는 처리 중인 메시지를 표시됩니다. edgeAgent 모듈은 다른 모듈을 시작하므로, 해당 로그는 배포 매니페스트 구현에 대한 정보를 포함합니다. 나열되지 않거나 실행되고 있지 않은 모듈이 있으면 edgeAgent 로그에 오류가 있을 수 있습니다. edgeHub 모듈은 모듈과 IoT Hub 간의 통신을 담당합니다. 모듈이 작동되고 실행 중이지만 IoT Hub에 메시지가 도착하지 않으면 edgeHub 로그에 오류가 있을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 개발 머신에 Visual Studio Code를 설치하고 첫 번째 IoT Edge 모듈을 배포했습니다. 기본 개념을 파악했으므로 전달된 데이터를 분석할 수 있게 모듈에 기능을 추가해 보세요. 기본 설정 언어를 선택합니다. 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)